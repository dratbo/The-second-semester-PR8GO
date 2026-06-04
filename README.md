<h1 align="center"> Привет! Я <a target="_blank"> Кармеев Артур из группы ЭФМО-01-25 </a> 
<img src="https://github.com/blackcater/blackcater/raw/main/images/Hi.gif" height="32"/></h1>
<h3 align="center"> Данная практика была интересной 🤔 </h3>

<h3 align="center"> Практическая работа №8: Настройка GitHub Actions / GitLab CI для деплоя приложения </h3>


Структура работы:

    └── pr8/
        ├── pz8-docker/
        │   ├── README.md
        │   ├── deploy/
        │   │   └── docker-compose.yml
        │   ├── .git/ тут куча файлов, всё стёр
        │   │   
        │   ├── services/
        │   │   └── tasks/
        │   │       ├── .dockerignore
        │   │       ├── Dockerfile
        │   │       ├── go.mod
        │   │       ├── go.sum
        │   │       ├── tasks.exe
        │   │       └── cmd/
        │   │           └── tasks/
        │   │               └── main.go
        │   └── .github/
        │       └── workflows/
        │           └── ci.yml
        └── .idea/
            ├── .gitignore
            ├── modules.xml
            ├── pr7.iml
            ├── vcs.xml
            └── workspace.xml


## 1. Тема и цель практической работы

Цель работы: освоить базовый CI/CD для микросервиса tasks: автоматическая проверка кода, сборка приложения, сборка Docker-образа, формирование тегов и публикация образа в container registry.

Репозиторий: https://github.com/dratbo/The-second-semester-PR8GO (мы как раз тут🤔)
Платформа CI/CD: GitHub Actions
Структура проекта: pz8-docker/ (взял просто 7-ую практику)


## 2. Структура pipeline

`push / pull_request в main` -->  `test-and-build` (Go 1.23: tidy → test → build) --> `docker-build` (login → docker build → docker push)

P.S. `docker-build` не запуститься если не выполнится `test-and-build`

- `test-and-build` - проверка и сборка Go-приложения
- `docker-build` - Сборка Docker-образа и публикация в GHCR (при `push`)


## 3. Выбранная платформа

Использована GitHub Actions. Файл workflow: `.github/workflows/ci.yml` в корне репозитория (каталог `pz8-docker` на GitHub).

## 4. Полный YAML-файл pipeline

```
name: CI Pipeline

on:
  push:
    branches: [ "main", "master" ]
  pull_request:
    branches: [ "main", "master" ]

jobs:
  test-and-build:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout repository
        uses: actions/checkout@v4

      - name: Setup Go
        uses: actions/setup-go@v5
        with:
          go-version: '1.23'

      - name: Show Go version
        run: go version

      - name: Download dependencies
        run: go mod tidy
        working-directory: ./services/tasks

      - name: Run tests
        run: go test ./...
        working-directory: ./services/tasks

      - name: Build application
        run: go build ./...
        working-directory: ./services/tasks

  docker-build:
    runs-on: ubuntu-latest
    needs: test-and-build

    steps:
      - name: Checkout repository
        uses: actions/checkout@v4

      - name: Set up Docker Buildx
        uses: docker/setup-buildx-action@v3

      - name: Login to registry (тут я полез выполнять 7-ой шаг, уже пожалел, но что то вышло)
        if: github.event_name == 'push'
        run: echo "${{ secrets.REGISTRY_PASSWORD }}" | docker login -u "${{ secrets.REGISTRY_USERNAME }}" --password-stdin ghcr.io 
      - name: Build image
        run: |
          docker build \
            -t ghcr.io/dratbo/techip-tasks:${{ github.sha }} \
            -t ghcr.io/dratbo/techip-tasks:latest \
            .
        working-directory: ./services/tasks

      - name: Push image
        if: github.event_name == 'push'
        run: |
          docker push ghcr.io/dratbo/techip-tasks:${{ github.sha }}
          docker push ghcr.io/dratbo/techip-tasks:latest
```


## 5. Пояснение шагов pipeline

- Job `test-and-build`

| Шаг | Действие | 
|-----|--------|
| Checkout repository | Клонирование репозитория на runner | 
| Setup Go | Установка Go 1.23 | 
| Show Go version | Вывод версии компилятора в лог |
| Download dependencies | `go mod tidy` в `services/tasks` |
| Run tests | `go test ./...` — проверка пакетов (тестовых файлов может не быть) |
| Build application | `go build ./...` — проверка компиляции |

- Job `docker-build`

| Шаг | Действие | 
|-----|--------|
| Checkout repository | Повторный checkout (отдельный runner) | 
| Set up Docker Buildx | Подготовка среды для сборки образов | 
| Login to registry | Вход в `ghcr.io` (только при `push`, через secrets) |
| Build image | Сборка образа с двумя тегами (SHA и `latest`) |
| Push image | Отправка образа в GitHub Container Registry (только при `push`) |

<table cellpadding="10">
  <tr>
    <td><img width="974" height="517" alt="image" src="https://github.com/user-attachments/assets/703a1765-b41d-4aa3-95cf-405b403b14c2" /></td>
  </tr>
</table>

<table cellpadding="10">
  <tr>
    <td><img width="974" height="473" alt="image" src="https://github.com/user-attachments/assets/b7205fb4-cbd9-4b66-8b47-d99d5df2b4c5" /></td>
  </tr>
</table>


## 6. Формирование тега Docker-образа

Используются два тега

| Тег | Значение | Назначение |
|-----|--------|--------------|
| `ghcr.io/dratbo/techip-tasks:${{ github.sha }}` | полный hash коммита | однозначная связь образа с версией кода |
| `ghcr.io/dratbo/techip-tasks:latest` | последняя успешная сборка в `main` | удобно для pull/deploy |

`${{ github.sha }}` — встроенная переменная GitHub Actions (аналог commit hash в GitLab: `$CI_COMMIT_SHORT_SHA`).

Полное имя образа: registry `ghcr.io` + namespace `dratbo` + имя `techip-tasks` + тег.


## 7. Где хранятся секреты

Секреты не хранятся в ci.yml, в коде и в Git.

| Секрет | Где хранится | Назначение |
|-----|--------|--------------|
| `REGISTRY_USERNAME` | GitHub → Settings → Secrets and variables → Actions | логин для `docker login` (логин GitHub) |
| `REGISTRY_PASSWORD` | там же | Personal Access Token с правом `write:packages` |

В workflow используются только ссылки: `${{ secrets.REGISTRY_USERNAME }}`, `${{ secrets.REGISTRY_PASSWORD }}`.

Для опционального деплоя по SSH (шаг 8) дополнительно использовались бы `SSH_HOST`, `SSH_USER`, `SSH_PRIVATE_KEY` — в том же разделе Secrets, не в репозитории.

<table cellpadding="10">
  <tr>
    <td><img width="1889" height="957" alt="image" src="https://github.com/user-attachments/assets/6a37a452-7a1a-46b0-b14d-62436345f55e" /></td>
  </tr>
</table>

<table cellpadding="10">
  <tr>
    <td><img width="974" height="501" alt="image" src="https://github.com/user-attachments/assets/f38637f7-8bee-4832-b4f0-a6ca48811f15" /></td>
  </tr>
</table>


## 8. Результат выполнения pipeline

<table cellpadding="10">
  <tr>
    <td><img width="1891" height="962" alt="image" src="https://github.com/user-attachments/assets/3213a634-0a49-4956-b405-7b55380083c6" /></td>
  </tr>
</table>

Успешный запуск workflow «CI Pipeline»: job `test-and-build` и `docker-build` завершены успешно (зелёная отметка, статус Success).


## 9. Публикация образа в registry

Registry: GitHub Container Registry (`ghcr.io`).

Образ: `ghcr.io/dratbo/techip-tasks` с тегами `<SHA коммита>` и `latest`.

Процесс в CI:

- `docker login` в `ghcr.io` с использованием secrets;
- `docker build` с тегами;
- `docker push` обоих тегов.


<table cellpadding="10">
  <tr>
    <td><img width="1882" height="973" alt="image" src="https://github.com/user-attachments/assets/2d8caf75-03a9-4f28-b02b-639d29411d01" /></td>
  </tr>
</table>

Пакет `techip-tasks` в GitHub Packages / успешный push.

Локально на ПК образ после CI не появляется — он хранится в registry и доступен через `docker pull ghcr.io/dratbo/techip-tasks:latest`.


## 10. Контрольные вопросы :weary: 

1. Чем CI отличается от CD?

CI (Continuous Integration) — автоматическая проверка кода при каждом изменении: зависимости, тесты, сборка. Цель — быстро узнать, что коммит «ломает» проект.
CD (Continuous Delivery / Deployment) — доставка или развёртывание уже проверенного артефакта: публикация образа в registry, выкладка на сервер (`docker pull`, `compose up -d`).
Различие: CI отвечает за качество и сборку, CD — за доставку к пользователю/на сервер. CI обычно идёт первым; CD — после успешного CI.

2. Почему pipeline должен запускать тесты?

Чтобы до merge/deploy поймать ошибки в логике и регрессии. Тесты в CI дают единый стандарт: проверка на чистом runner, а не только «у меня на ноутбуке работает». Даже при отсутствии `*_test.go` шаг `go test ./...` подтверждает, что пакеты компилируются и тестируемы; при появлении тестов они сразу войдут в pipeline.

3. Зачем нужен автоматический build?

Чтобы каждый коммит проходил ту же сборку, что и на production: фиксированная версия Go, те же команды (`go build`, `docker build`). Это отсекает ситуации «забыли собрать», «собрали старой версией Go», «на сервере другие флаги». Build в CI — обязательный gate перед образом и деплоем.

4. Почему важно собирать Docker-образ в CI, а не только локально?

Локальная сборка зависит от ОС, кэша и настроек разработчика. В CI образ собирается в предсказуемой среде (Linux, тот же Dockerfile) и становится единым артефактом для registry и серверов. Так исключают «у меня работает, на VPS — нет» и обеспечивают повторяемость релиза.

5. Что такое CI secrets?

Это секретные переменные в настройках CI (у вас — GitHub Actions → Settings → Secrets): пароли registry, PAT, SSH-ключи. В workflow они доступны как `${{ secrets.ИМЯ }}`, в логах не показываются. Отделяют конфиденциальные данные от кода pipeline.

6. Почему нельзя хранить токены и SSH-ключи в репозитории?

Репозиторий часто публичный, его клонируют, видна история Git — секреты останутся в коммитах навсегда. Риск утечки, кражи доступа к registry и VPS. Правило: секреты только в Secrets CI или защищённых хранилищах, не в `.yml` и не в `.env` в Git.

7. Для чего нужен тег Docker-образа?

Тег (версия образа) связывает образ с конкретным коммитом (`${{ github.sha }}`) или помечает «последний» (`latest`). Без тега сложно понять, что развёрнуто, откатиться и воспроизвести релиз. В вашей работе: `ghcr.io/dratbo/techip-tasks:<SHA>` и `latest`.

8. Что делает job docker-build?

Запускается после успешного `test-and-build`. Клонирует репозиторий, настраивает Buildx, при `push` логинится в `ghcr.io`, собирает образ из `services/tasks` с тегами SHA и `latest`, пушит образ в registry. Это этап упаковки и публикации приложения в Docker-формате.

9. Почему в multi-service проекте важен working-directory?

В монорепозитории несколько сервисов (`services/tasks`, `services/auth` и т.д.). Без `working-directory: ./services/tasks` команды `go test`, `go build`, `docker build` выполнятся не в той папке — не найдут `go.mod` или `Dockerfile`. Явный каталог задаёт контекст для каждого сервиса в общем pipeline.

10. Какие риски возникают при полностью автоматическом деплое?

- выкладка сломанной версии без ручной проверки;
- неверный тег или `latest` перезапишет рабочий сервис;
- деплой затронет не тот сервер или compose-проект (как с телеграм-ботом на том же VPS);
- утечка SSH-ключа при ошибке в secrets;
- простой при неудачном `compose up` или конфликте портов;
- нет отката, если не хранятся старые теги образов.
Поэтому в учебной практике деплой часто опционален, а на production добавляют staging, ручное подтверждение и откат.
