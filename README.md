# Diplom App

Тестовое приложение для дипломной работы в Yandex Cloud.

Простой nginx, отдающий статическую HTML-страницу.

## Сборка и push

```bash
docker build -t cr.yandex/<registry_id>/app:v1.0.0 .
docker push cr.yandex/<registry_id>/app:v1.0.0
