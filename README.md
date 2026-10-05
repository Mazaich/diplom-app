# Diplom App

Тестовое приложение для дипломного проекта. Простой nginx, отдающий статическую HTML-страницу.

## Что внутри

- **index.html** — сама страница. Тёмный фон, надпись «Работает — не трогаем». Ничего лишнего.
- **Dockerfile** — на базе `nginx:alpine`. Копируем страницу в `/usr/share/nginx/html/`, запускаем nginx.
- **.dockerignore** — чтобы `.git` и лишние файлы не попадали в образ.
- **.github/workflows/** — два workflow для CI/CD.

## Что делает приложение

Отдаёт одну статическую страницу на порту 80. Используется для проверки деплоя в Kubernetes — можно быстро понять, что приложение живо.

Приложение задеплоено в кластер: манифесты лежат в репозитории [diplom-k8s](https://github.com/Mazaich/diplom-k8s).

Образ хранится в Yandex Container Registry: `cr.yandex/crp3u1m6upr5da144dv4/app`.

## Сборка образа

Локально (вручную):

```
docker build -t cr.yandex/crp3u1m6upr5da144dv4/app:v1.0.0 .
docker push cr.yandex/crp3u1m6upr5da144dv4/app:v1.0.0
```
Также настроен CI/CD.

## CI/CD через GitHub Actions

Настроены два workflow:

**1. build.yaml — сборка при коммите**

- Триггер: push в ветку `main`.
- Что делает: собирает образ и пушит в Container Registry с тегом = хэш коммита.
- Цель: чтобы после каждого изменения кода была свежая версия образа.

**2. deploy.yaml — деплой по тегу**

- Триггер: push тега, начинающегося с `v` (например, `v1.0.0`).
- Что делает: собирает образ с тегом = имя тега, пушит в registry, обновляет Deployment в Kubernetes.
- Цель: автоматический деплой в кластер по релизу.

**Секреты** (в настройках репозитория → Secrets → Actions):
- `YC_SA_KEY` — JSON-ключ сервисного аккаунта Yandex Cloud.
- `YC_REGISTRY_ID` — ID Container Registry.
- `KUBE_CONFIG` — kubeconfig для доступа к кластеру, закодированный в base64.

**Где смотреть работу:**
- Вкладка [Actions](https://github.com/Mazaich/diplom-app/actions) — история запусков.
- Внутри каждого run — лог по шагам.


## Как задеплоить вручную (если CI/CD недоступен)

```
cd ~/diplom/diplom-k8s
kubectl apply -f deployment.yaml
kubectl apply -f service.yaml
```

Или только обновить образ:

```
kubectl set image deployment/nginx-app nginx=cr.yandex/crp3u1m6upr5da144dv4/app:v1.0.1
```

## Ссылки

- [diplom-terraform](https://github.com/Mazaich/diplom-terraform) — инфраструктура
- [diplom-k8s](https://github.com/Mazaich/diplom-k8s) — манифесты Kubernetes
- [diplom-notes](https://github.com/Mazaich/diplom-notes) — заметки и скриншоты
