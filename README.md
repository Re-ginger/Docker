1. Обновила список доступных пакетов и их версий с репозиториев
```bash
sudo apt update
```
2. С помощью команд установила ключевые пакеты и сертификаты для использования репозиториев с HTTPS
 ```bash
 sudo apt install apt-transport-https ca-certificates curl software-properties-common
```
3. Добавила GPG ключа Docker для доверенной установки пакетов Docker:
```bash
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add
```
4. Добавила репозитория Docker:
```bash
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu focal stable"
```
5. Повторное обновила список пакетов
```bash
sudo apt update
```
6. Проверила наличие докера
```bash
 apt-cache policy docker-ce
```
Показала, что Docker еще не установлен в системе, но доступен для установки.
7. Установка Docker
```bash
 sudo apt install docker-ce
```
8. Проверила статус docker
```bash
 sudo systemctl status docker
● docker.service - Docker Application Container Engine
     Loaded: loaded (/lib/systemd/system/docker.service; enabled; vendor preset: enabled)
     Active: active (running) since Mon 2024-08-26 17:36:01 MSK; 29min ago
TriggeredBy: ● docker.socket
       Docs: https://docs.docker.com
   Main PID: 2851341 (dockerd)
```
8. Указала образ для создания и запустила контейнер 
```bash
sudo docker run --name my-postgres -e POSTGRES_PASSWORD=test -p 5432:5432 -d postgres
```
9. Проверила, что контейнер запущен
```bash
# sudo docker ps
CONTAINER ID   IMAGE      COMMAND                  CREATED          STATUS          PORTS                                       NAMES
cf32b2131375   postgres   "docker-entrypoint.s…"   23 minutes ago   Up 23 minutes   0.0.0.0:5432->5432/tcp, :::5432->5432/tcp   my-postgres
```
10. Подключилась к PostgreSQL через Docker:
```bash
docker exec -it my-postgres psql -U postgres
```
11. Далее через Dbevear подключилась к базе данных 
![image](https://github.com/user-attachments/assets/dc83bf2a-fc0e-4bc7-af83-0814484eb870)
