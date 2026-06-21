# PXE-SERVVV Docker

PXE-SERVVV — Docker-панель управления PXE-сервером на базе iVentoy.

## Состав

- Flask web panel: порт 8080
- iVentoy GUI: порт 26000
- ISO-хранилище: `data/iso`
- Docker network mode: `host`
- PXE DHCP pool по умолчанию: `10.70.70.200-10.70.70.219`

## Установка

```bash
sudo apt update
sudo apt install -y docker.io docker-compose-v2 git
