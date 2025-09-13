# 3 Лабораторная обычная:
# Задание:
1. Написать “плохой” CI/CD файл, который работает, но в нем есть не менее пяти “bad practices” по написанию CI/CD
2. Написать “хороший” CI/CD, в котором эти плохие практики исправлены
3. В Readme описать каждую из плохих практик в плохом файле, почему она плохая и как в хорошем она была исправлена, как исправление повлияло на результат
## Плохой ci/cd:
```
name: Bad CI/CD

on:
  push:
    branches:
      - main

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v3

      - name: Install deps
        run: npm install

      - name: Test
        run: npm run test || true

      - name: Build
        run: npm run build

      - name: Deploy
        run: echo "Deploying to production"
```
## "Bad practices" в этом ci/cd:
1. Использование npm install может подтянуть разные версии пакетов - Это ведёт к нестабильным сборкам.
2. npm run test || true - игнорируются ошибки тестов, пайплайн всегда проходит - а возмжоно баги попадут в production.
3. Отсутствие кэширования зависимостей - каждый раз зависимости скачиваются заново - медленно, особенно в больших проектах.
4. build, deploy объединены - если билд упадёт, всё равно может выполниться deploy - нет ответственности.
5. Деплой всегда выполняется - В файле deploy запускается при любом пуше в main. Если случайно закоммитить баг — он попадёт в прод.

## Хороший ci/cd:
```
name: Good CI/CD

on:
  push:
    branches:
      - main
  pull_request:

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3

      - name: Use Node.js
        uses: actions/setup-node@v4
        with:
          node-version: 18
          cache: 'npm'

      - name: Install deps
        run: npm ci

      - name: Run tests
        run: npm test

      - name: Build
        run: npm run build

  deploy:
    needs: build
    if: github.ref == 'refs/heads/main'
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Deploy
        run: echo "Deploying to production"
```

Изменения:
1. В good ci/cd используется npm ci. Эта команда устанавливает зависимости строго по package-lock.json.
2. Cтоит просто npm test без || true. Если тесты упали — пайплайн падает, и деплой не выполняется.
3. Добавлен шаг actions/setup-node с опцией cache: 'npm', что позволяет сохранять и переиспользовать зависимости между билдами.
4. Также build и deploy сделал отдельными. Deploy зависит от успешного завершения build.
5. В good ci/cd теперь стоит условие if: github.ref == 'refs/heads/main'. Деплой запускается только для основной ветки.



   

