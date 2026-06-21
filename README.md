# PXE-SERVVV Docker

PXE-SERVVV is a Docker-based web management panel for a PXE server built around iVentoy.

PXE-SERVVV — Docker-панель управления PXE-сервером на базе iVentoy.

---

## English

### Overview

PXE-SERVVV provides a web interface for managing ISO images and basic PXE/iVentoy service operations. The project uses Docker with host networking because PXE, DHCP, TFTP, and iVentoy services must bind directly to the server network interface.

### Components

* Flask web panel: port `8080`
* iVentoy GUI: port `26000`
* ISO storage on host: `data/iso`
* ISO storage inside container: `/srv/pxe/iventoy-iso` and `/opt/iventoy/iso`
* Docker network mode: `host`
* Default PXE server IP: `10.70.70.1/24`
* Default DHCP pool for PXE clients: `10.70.70.200-10.70.70.219`

### Download

Download the release archive from:

```text
https://github.com/notfounddd/pxe-servvv-docker/releases/tag/release
```

Extract the project to `/opt/pxe-servvv-docker`.

Example:

```bash
sudo mv /home/kali/Downloads/pxe-servvv-docker /opt/pxe-servvv-docker
```

### Install Docker

Variant 1:

```bash
sudo apt update
sudo apt install -y docker.io docker-compose
```

Variant 2:

```bash
sudo apt update
sudo apt install -y docker.io docker-compose-v2
```

Enable and start Docker:

```bash
sudo systemctl enable docker
sudo systemctl start docker
```

Check Docker:

```bash
sudo docker --version
sudo docker compose version
```

If `docker compose version` does not work, use `docker-compose` commands instead of `docker compose`.

### Prepare project files

Go to the project directory:

```bash
cd /opt/pxe-servvv-docker
```

Set executable permissions and fix possible Windows line endings:

```bash
sudo chmod +x host-setup.sh docker-entrypoint.sh scripts/pxe-servvv-*
sudo sed -i 's/\r$//' host-setup.sh docker-entrypoint.sh scripts/pxe-servvv-*
```

Fix iVentoy permissions:

```bash
sudo chmod -R a+rX iventoy
sudo chmod +x iventoy/iventoy.sh iventoy/lib/iventoy
sudo find iventoy/lib -type f -exec chmod a+rx {} \;
```

### Configure PXE network interface

Run the host setup script before building and starting the container:

```bash
cd /opt/pxe-servvv-docker
sudo ./host-setup.sh
```

The script will:

1. list available network interfaces;
2. let you choose the PXE interface;
3. assign a PXE IP address, for example `10.70.70.1/24`;
4. generate `.env`;
5. create a persistent systemd service for restoring the PXE IP address after reboot.

Check the selected interface IP:

```bash
ip -br addr
```

### Build and start

For Docker Compose V2:

```bash
cd /opt/pxe-servvv-docker
sudo docker compose -f docker-compose.yml build --no-cache
sudo docker compose -f docker-compose.yml up -d --force-recreate
```

For legacy Docker Compose:

```bash
cd /opt/pxe-servvv-docker
sudo docker-compose -f docker-compose.yml build --no-cache
sudo docker-compose -f docker-compose.yml up -d --force-recreate
```

### Check the container

```bash
sudo docker ps
```

Check logs:

```bash
sudo docker logs -f pxe-servvv
```

Check ports:

```bash
sudo ss -tulpen | grep -E ':8080|:26000|:16000|:67|:69|:4011'
```

Minimum expected ports:

```text
8080  - PXE-SERVVV web panel
26000 - iVentoy GUI
```

After iVentoy PXE is started, these ports should also appear:

```text
67
69
4011
16000
```

### Open PXE-SERVVV panel

Open in browser:

```text
http://10.70.70.1:8080
```

Default credentials:

```text
admin / admin
```

### First iVentoy start

Open iVentoy GUI:

```text
http://10.70.70.1:26000
```

Set the following parameters:

```text
Server IP: 10.70.70.1
DHCP Server Mode: Internal
IP Pool Begin: 10.70.70.200
IP Pool End: 10.70.70.219
```

Click `Start` / `Save` in the iVentoy GUI.

### Check iVentoy status

```bash
sudo docker exec -it pxe-servvv /usr/local/sbin/pxe-servvv-iventoy-control status
```

Expected result after PXE is running:

```json
{"systemd":"container","web_ready":true,"boot_ready":true,"real_status":"running"}
```

If the status is:

```json
{"systemd":"container","web_ready":true,"boot_ready":false,"real_status":"gui_only"}
```

it means that the iVentoy GUI is available, but PXE services are not started yet. Open the iVentoy GUI and click `Start` / `Save`.

### Upload ISO images

Upload ISO images through the PXE-SERVVV panel:

```text
http://10.70.70.1:8080
```

ISO files are stored on the host in:

```text
/opt/pxe-servvv-docker/data/iso
```

Inside the container they are available as:

```text
/srv/pxe/iventoy-iso
/opt/iventoy/iso
```

After uploading the first ISO image, open the iVentoy GUI and use the refresh/update function if the image does not appear automatically.

---

## Русский

### Описание

PXE-SERVVV — это Docker-панель управления PXE-сервером на базе iVentoy. Панель позволяет загружать ISO-образы, управлять базовыми действиями iVentoy и проверять состояние PXE-сервера.

Проект использует Docker в режиме `host`, потому что PXE, DHCP, TFTP и iVentoy должны напрямую работать с сетевым интерфейсом сервера.

### Состав

* Flask web panel: порт `8080`
* iVentoy GUI: порт `26000`
* Хранилище ISO на хосте: `data/iso`
* Хранилище ISO внутри контейнера: `/srv/pxe/iventoy-iso` и `/opt/iventoy/iso`
* Docker network mode: `host`
* IP PXE-сервера по умолчанию: `10.70.70.1/24`
* DHCP pool по умолчанию для PXE-клиентов: `10.70.70.200-10.70.70.219`

### Скачивание

Скачать архив релиза:

```text
https://github.com/notfounddd/pxe-servvv-docker/releases/tag/release
```

Распаковать проект в `/opt/pxe-servvv-docker`.

Пример:

```bash
sudo mv /home/kali/Downloads/pxe-servvv-docker /opt/pxe-servvv-docker
```

### Установка Docker

Вариант 1:

```bash
sudo apt update
sudo apt install -y docker.io docker-compose
```

Вариант 2:

```bash
sudo apt update
sudo apt install -y docker.io docker-compose-v2
```

Включить и запустить Docker:

```bash
sudo systemctl enable docker
sudo systemctl start docker
```

Проверить Docker:

```bash
sudo docker --version
sudo docker compose version
```

Если `docker compose version` не работает, используйте команды `docker-compose`.

### Подготовка файлов проекта

Перейти в каталог проекта:

```bash
cd /opt/pxe-servvv-docker
```

Выдать права скриптам и исправить возможные Windows-переносы строк:

```bash
sudo chmod +x host-setup.sh docker-entrypoint.sh scripts/pxe-servvv-*
sudo sed -i 's/\r$//' host-setup.sh docker-entrypoint.sh scripts/pxe-servvv-*
```

Исправить права iVentoy:

```bash
sudo chmod -R a+rX iventoy
sudo chmod +x iventoy/iventoy.sh iventoy/lib/iventoy
sudo find iventoy/lib -type f -exec chmod a+rx {} \;
```

### Конфигурация сетевого интерфейса PXE

Перед сборкой и запуском контейнера выполнить:

```bash
cd /opt/pxe-servvv-docker
sudo ./host-setup.sh
```

Скрипт:

1. найдёт сетевые интерфейсы;
2. предложит выбрать интерфейс для PXE;
3. назначит PXE IP, например `10.70.70.1/24`;
4. создаст `.env`;
5. создаст systemd-сервис для восстановления IP после перезагрузки.

Проверить IP на выбранном интерфейсе:

```bash
ip -br addr
```

### Сборка и запуск

Для Docker Compose V2:

```bash
cd /opt/pxe-servvv-docker
sudo docker compose -f docker-compose.yml build --no-cache
sudo docker compose -f docker-compose.yml up -d --force-recreate
```

Для старого Docker Compose:

```bash
cd /opt/pxe-servvv-docker
sudo docker-compose -f docker-compose.yml build --no-cache
sudo docker-compose -f docker-compose.yml up -d --force-recreate
```

### Проверка контейнера

```bash
sudo docker ps
```

Проверка логов:

```bash
sudo docker logs -f pxe-servvv
```

Проверка портов:

```bash
sudo ss -tulpen | grep -E ':8080|:26000|:16000|:67|:69|:4011'
```

Минимально должны появиться порты:

```text
8080  - панель PXE-SERVVV
26000 - iVentoy GUI
```

После запуска PXE в iVentoy должны появиться:

```text
67
69
4011
16000
```

### Переход в панель PXE-SERVVV

Открыть в браузере:

```text
http://10.70.70.1:8080
```

Данные для входа по умолчанию:

```text
admin / admin
```

### Первый запуск iVentoy

Открыть iVentoy GUI:

```text
http://10.70.70.1:26000
```

Установить параметры:

```text
Server IP: 10.70.70.1
DHCP Server Mode: Internal
IP Pool Begin: 10.70.70.200
IP Pool End: 10.70.70.219
```

Нажать `Start` / `Save` в панели iVentoy.

### Проверка iVentoy

```bash
sudo docker exec -it pxe-servvv /usr/local/sbin/pxe-servvv-iventoy-control status
```

Ожидаемый результат после запуска PXE:

```json
{"systemd":"container","web_ready":true,"boot_ready":true,"real_status":"running"}
```

Если статус:

```json
{"systemd":"container","web_ready":true,"boot_ready":false,"real_status":"gui_only"}
```

это означает, что iVentoy GUI доступен, но PXE-службы ещё не запущены. Нужно открыть iVentoy GUI и нажать `Start` / `Save`.

### Загрузка ISO-образов

Загрузка ISO выполняется через основную панель:

```text
http://10.70.70.1:8080
```

ISO-файлы хранятся на хосте:

```text
/opt/pxe-servvv-docker/data/iso
```

Внутри контейнера они доступны как:

```text
/srv/pxe/iventoy-iso
/opt/iventoy/iso
```

После загрузки первого ISO-образа открыть iVentoy GUI и нажать кнопку обновления/refresh, если образ не появился автоматически.
::: 
