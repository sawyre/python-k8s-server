## Тестовое задание

Простое "Hello world" приложение на python + fastapi + uvicorn. Также добавлены конфигурации для запуска приложения на k8s кластере

### Структура проекта
app  
|_ Dockerfile  
|_ requirements.txt  
|_ server.py  
iac  
|_ deployment.yml  
|_ service.yml

### Запуск
#### Без докера и бeз кластера
```commandline
cd app
pip install -r requerements.txt
uvicorn server:app --host 0.0.0.0 --port 32777
```
#### Со сборкой докера, но без кластера
```commandline
cd app
docker build -t python-server .
docker run -p 32777:8080 python-server
```
#### На кластере
Установить minikube и выполнить
```commandline
cd ./iac/k8s
minikube start
kubectl apply -f deployment.yml
kubectl apply -f service.yml
kubectl port-forward service/python-server 32777:32777
```
для запрета выхода подам в интернет
```commandline
cd ./iac/k8s
minikube start --cni=calico
kubectl apply -f deployment.yml
kubectl apply -f service.yml
kubectl apply -f egress.yml
kubectl port-forward service/python-server 32777:32777
```

### Комментарии по дополнениям к заданию
Добавил readinessProbe, чтобы следить за тем, что контейнер запустился и rollingUpdate отрабатывал корректно.  
Добавил базовую безопасность:
- запретил подам выход в интернет через egress
- запустил приложение в докере не от root пользователя, чтобы ограничить возможности манипуляции с внешними системами

### Схема реализации
![alt-текст](Схема%20реализации.png)