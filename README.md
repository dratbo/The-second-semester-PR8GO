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
        │   ├── .git/
        │   │   ├── COMMIT_EDITMSG
        │   │   ├── config
        │   │   ├── description
        │   │   ├── FETCH_HEAD
        │   │   ├── HEAD
        │   │   ├── index
        │   │   ├── ORIG_HEAD
        │   │   ├── logs/
        │   │   │   ├── HEAD
        │   │   │   └── refs/
        │   │   │       ├── heads/
        │   │   │       │   └── main
        │   │   │       └── remotes/
        │   │   │           └── origin/
        │   │   │               └── main
        │   │   ├── info/
        │   │   │   └── exclude
        │   │   ├── hooks/
        │   │   │   ├── applypatch-msg.sample
        │   │   │   ├── commit-msg.sample
        │   │   │   ├── fsmonitor-watchman.sample
        │   │   │   ├── post-update.sample
        │   │   │   ├── pre-applypatch.sample
        │   │   │   ├── pre-commit.sample
        │   │   │   ├── pre-merge-commit.sample
        │   │   │   ├── pre-push.sample
        │   │   │   ├── pre-rebase.sample
        │   │   │   ├── pre-receive.sample
        │   │   │   ├── prepare-commit-msg.sample
        │   │   │   ├── push-to-checkout.sample
        │   │   │   ├── sendemail-validate.sample
        │   │   │   └── update.sample
        │   │   ├── refs/
        │   │   │   ├── heads/
        │   │   │   │   └── main
        │   │   │   └── remotes/
        │   │   │       └── origin/
        │   │   │           └── main
        │   │   └── objects/
        │   │       ├── 12/
        │   │       │   └── 0087d250e3a35ae1522b48d1e09d6806a9b8c4
        │   │       ├── 25/
        │   │       │   └── 478bde0bf330ca914bd17c3de9ba4c4eb18db6
        │   │       ├── 41/
        │   │       │   └── 890a3c09574ec221a5179075370f7a96adab8f
        │   │       ├── 43/
        │   │       │   └── 79ee612aaaf671d8e50ed7e576caad8231931c
        │   │       ├── 49/
        │   │       │   └── c8bb3ce287e87bb29022ca6f249889f2de8ec9
        │   │       ├── 52/
        │   │       │   └── d0164e83a0c29a10be8087fd135df304173309
        │   │       ├── 56/
        │   │       │   └── b20fbd48f6111a95fdd5cc5994ac674e9674d8
        │   │       ├── 57/
        │   │       │   └── 55371b4676696ea76425245ea3f26e6c6e243f
        │   │       ├── 64/
        │   │       │   └── c30b1ac4f3502da96df4fe03440ddefb5ef68f
        │   │       ├── 79/
        │   │       │   └── 5c672711acce50b9c5adb045d8073653bf35fc
        │   │       ├── 84/
        │   │       │   └── 6b5cf027baea36de03e6aaf1da67dbe5ddb6fc
        │   │       ├── 90/
        │   │       │   └── 297db12db28b5402382d93d1997c0718a98346
        │   │       ├── 93/
        │   │       │   └── ade6176c1ae41dd213469c5a680df901096aa1
        │   │       ├── f8/
        │   │       │   └── a38309c69040f28b5a3499512c9238c6824a67
        │   │       ├── e6/
        │   │       │   └── 9de29bb2d1d6434b8b29ae775ad8c2e48c5391
        │   │       ├── e5/
        │   │       │   └── 692d2195479cc500bc93ff7b7e02b869bc06df
        │   │       ├── d2/
        │   │       │   └── 93e1bebedd1960b023c475b690e6292eb1d1ba
        │   │       ├── cf/
        │   │       │   └── b39fee3a39c1b70dfd7c6d1d69c76ba6cc9722
        │   │       ├── bc/
        │   │       │   └── 8d2641b77058f6ae51a94efebc2a2dac376745
        │   │       ├── ba/
        │   │       │   └── f69aa4261bebc60a1b7a771fa513d9a0808386
        │   │       ├── ab/
        │   │       │   └── 8a78830638d0aa77bc95ddb414bf4eee91bd91
        │   │       ├── 9d/
        │   │       │   └── a869e44a149aab89a72bfb6995aba258536eda
        │   │       ├── 8b/
        │   │       │   └── 17cd6ea9c25ed4767259f81af70bc0bf1825c6
        │   │       ├── 8a/
        │   │       │   └── 40a03d3c68256783e77961348873cfc21cd8ae
        │   │       ├── 3f/
        │   │       │   └── 808dfa814bdcae1f8334503d5a2df7f3258372
        │   │       ├── 2a/
        │   │       │   └── 8c2583b5a24b445c03b5a1ec26bb0b32c23c28
        │   │       ├── 1e/
        │   │       │   └── 267b5eedad02c945781749ee3a5fdf6f449f1a
        │   │       ├── 1a/
        │   │       │   └── 3b8e735b57865508d95ef0de4782f253bbdf54
        │   │       ├── 0d/
        │   │       │   └── fe5f4ffbc9b6822b790d6c80f3c6e804b4e18b
        │   │       ├── 05/
        │   │       │   └── db37c21d3ca6b65e4ba7a3885d0205fc27df9a
        │   │       └── 04/
        │   │           └── 6d7481699f3d65186f72d8bba26382b0b4ec5c
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

push / pull_request в main
        │
        ▼
┌─────────────────────┐
│   test-and-build    │  Go 1.23: tidy → test → build
└──────────┬──────────┘
           │ needs
           ▼
┌─────────────────────┐
│    docker-build     │  login → docker build → docker push
└─────────────────────┘
