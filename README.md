# Laboratory work IV

Данная лабораторная работа посвещена изучению систем непрерывной интеграции на примере сервиса Travis CI

```ch
$ open https://travis-ci.org
```

## Tasks

1. Авторизоваться на сервисе Travis CI с использованием GitHub аккаунта
2. Создать публичный репозиторий с названием lab04 на сервисе GitHub
3. Ознакомиться со ссылками учебного материала
4. Включить интеграцию сервиса Travis CI с созданным репозиторием
5. Получить токен для Travis CLI с правами repo и user
6. Получить фрагмент вставки значка сервиса Travis CI в формате Markdown
7. Выполнить инструкцию учебного материала
8. Составить отчет и отправить ссылку личным сообщением в Slack

Tutorial

```ch
$ export GITHUB_USERNAME=<имя_пользователя>

$ export GITHUB_TOKEN=<полученный_токен>
```

```ch
$ cd ${GITHUB_USERNAME}/workspace

$ pushd .

$ source scripts/activate
```

```ch
$ \curl -sSL https://get.rvm.io | bash -s -- --ignore-dotfiles
```

Вывод:
```ch
Turning on ignore dotfiles mode.
Downloading https://github.com/rvm/rvm/archive/master.tar.gz
Installing RVM to /home/misha/.rvm/
Installation of RVM in /home/misha/.rvm/ is almost complete:

  * To start using RVM you need to run `source /home/misha/.rvm/scripts/rvm`
    in all your open shell windows, in rare cases you need to reopen all shell windows.
Thanks for installing RVM 🙏
```

```ch
$ echo "source $HOME/.rvm/scripts/rvm" >> scripts/activate

$ . scripts/activate

$ rvm autolibs disable

$ rvm install ruby-2.4.2
```
Вывод:

```ch
ruby-2.4.2 - #removing src/ruby-2.4.2..
Installing Ruby from source to: /home/misha/.rvm/rubies/ruby-2.4.2, this may take a while depending on your cpu(s)...
ruby-2.4.2 - #downloading ruby-2.4.2, this may take a while depending on your connection...
ruby-2.4.2 - #extracting ruby-2.4.2 to /home/misha/.rvm/src/ruby-2.4.2.....
ruby-2.4.2 - #configuring..................................................................
ruby-2.4.2 - #post-configuration..
ruby-2.4.2 - #compiling......................................................................................................|
ruby-2.4.2 - #installing...........
ruby-2.4.2 - #making binaries executable..
ruby-2.4.2 - #downloading rubygems-3.0.9
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100  865k  100  865k    0     0   695k      0  0:00:01  0:00:01 --:--:--  696k
No checksum for downloaded archive, recording checksum in user configuration.
ruby-2.4.2 - #extracting rubygems-3.0.9.....
ruby-2.4.2 - #removing old rubygems........
ruby-2.4.2 - #installing rubygems-3.0.9................................................................
ruby-2.4.2 - #gemset created /home/misha/.rvm/gems/ruby-2.4.2@global
ruby-2.4.2 - #importing gemset /home/misha/.rvm/gemsets/global.gems..........................................................
ruby-2.4.2 - #generating global wrappers........
ruby-2.4.2 - #gemset created /home/misha/.rvm/gems/ruby-2.4.2
ruby-2.4.2 - #importing gemsetfile /home/misha/.rvm/gemsets/default.gems evaluated to empty gem list
ruby-2.4.2 - #generating default wrappers........
ruby-2.4.2 - #adjusting #shebangs for (gem irb erb ri rdoc testrb rake).
Install of ruby-2.4.2 - #complete
```

```ch
$ rvm use 2.4.2 --default
```

Вывод:

```ch
Using /home/misha/.rvm/gems/ruby-2.4.2
```

```ch
$ git clone https://github.com/${GITHUB_USERNAME}/lab03 projects/lab04
```

Вывод:

```ch
Клонирование в «projects/lab04»...
remote: Enumerating objects: 91, done.

remote: Counting objects: 100% (91/91), done.
remote: Compressing objects: 100% (63/63), done.
remote: Total 91 (delta 22), reused 91 (delta 22), pack-reused 0 (from 0)
Получение объектов: 100% (91/91), 28.25 КиБ | 14.13 МиБ/с, готово.
Определение изменений: 100% (22/22), готово.
```

```ch
$ cd projects/lab04

$ git remote remove origin

$ git remote add origin https://github.com/misakontileev-stack/lab_04_1.git
```

```ch
$ git clone https://github.com/${GITHUB_USERNAME}/lab03 projects/lab04
$ cd projects/lab04
$ git remote remove origin
$ git remote add origin https://github.com/${GITHUB_USERNAME}/lab04
```

```ch
$ cat > .travis.yml <<EOF
language: cpp
EOF
```

```ch
$ cat >> .travis.yml <<EOF

script:
- cmake -H. -B_build -DCMAKE_INSTALL_PREFIX=_install
- cmake --build _build
- cmake --build _build --target install
EOF
```

```ch
$ cat >> .travis.yml <<EOF

addons:
  apt:
    sources:
      - george-edison55-precise-backports
    packages:
      - cmake
      - cmake-data
EOF
```

```ch
$ mkdir -p .github/workflows

$ nano .github/workflows/ci.yml
```

В файл вставляем такой текст:

```ch
name: C++ CI

on:
  push:
    branches: [ master ]
  pull_request:
    branches: [ master ]

jobs:
  build:
    runs-on: ${{ matrix.os }}

    strategy:
      matrix:
        os: [ubuntu-latest, windows-latest]

    steps:
    - name: Checkout repository
      uses: actions/checkout@v3

    - name: Install dependencies (Ubuntu)
      if: matrix.os == 'ubuntu-latest'
      run: |
        sudo apt update
        sudo apt install -y build-essential cmake

    - name: Configure CMake
      run: cmake -S . -B build

    - name: Build
      run: cmake --build build --config Release
```

```ch
$ git add .

$ git commit -m "Make ci.yml"
```

У меня с первого раза не заработало, поэтому на GitHub коммиты будут другие

```ch
$ git push origin master
```

