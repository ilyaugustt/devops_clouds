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
