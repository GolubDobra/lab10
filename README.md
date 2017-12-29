[![Build Status](https://travis-ci.org/GolubDobra/lab10.svg?branch=master)](https://travis-ci.org/GolubDobra/lab10)
the demo application redirects data from stdin to a file **log.txt** using a package **print**.

## Laboratory work X
Данная лабораторная работа посвещена изучению систем управления пакетами на примере **Hunter**

```ShellSession
$ open https://github.com/ruslo/hunter
```

## Tasks

- [X] 1. Создать публичный репозиторий с названием **lab10** на сервисе **GitHub**
- [X] 2. Сгенирировать токен для доступа к сервису **GitHub** с правами **repo**
- [X] 3. Выполнить инструкцию учебного материала
- [X] 4. Ознакомиться со ссылками учебного материала
- [X] 5. Составить отчет и отправить ссылку личным сообщением в **Slack**

## Tutorial
Устанавливание значение для сервиса **GitHub**
```ShellSession
$ export GITHUB_USERNAME=<имя пользователя> # Устанавливаем значение переменной окружения GITHUB_USERNAME
$ export GITHUB_TOKEN=<токен> # Устанавливаем значение переменной окружения GITHUB_TOKEN
```
Устанавливаем **hub**
```ShellSession
$ cd ${GITHUB_USERNAME}/workspace
$ pushd .
$ source scripts/activate
$ go get github.com/github/hub
```
Настройка конфигурационного файла для **hub**
```ShellSession
$ mkdir ~/.config # Создаем директорию config
$ cat > ~/.config/hub <<EOF # Записываем свои данные в hub
github.com:
- user: ${GITHUB_USERNAME}
  oauth_token: ${GITHUB_TOKEN}
  protocol: https
EOF
$ git config --global hub.protocol https # Устанавливаем значение hub.protocol
```
Работа с пакетом из предыдущей лабы
```ShellSession
$ wget https://github.com/${GITHUB_USERNAME}/lab09/archive/v0.1.0.0.tar.gz # Скачиваем пакет 9-ой лабы
$ export PRINT_SHA1=`openssl sha1 v0.1.0.0.tar.gz | cut -d'=' -f2 | cut -c2-41` 
$ echo $PRINT_SHA1 # Выводим хэш-сумму
$ rm -rf v0.1.0.0.tar.gz # Удаляем пакет
```
Работа с репозиторием ***Hunter***
```ShellSession
$ git clone https://github.com/ruslo/hunter projects/hunter
$ cd projects/hunter && git checkout v0.19.137 # Меняем директорию на projects/hunter и переключаемся на ветвь v0.19.137
$ git remote show #Выводим ветки удаленного репозитория
origin
$ hub fork # Делаем fork репозитория hunter
$ git remote show #Выводим ветки удаленного репозитория
$ git remote show ${GITHUB_USERNAME} #Выводим всю информацию о нашей ветке
```

Настройка ***Hunter***
```ShellSession
$ mkdir cmake/projects/print # Создаем директорию 
$ cat > cmake/projects/print/hunter.cmake <<EOF # Добавляем изменения в hunter.cmake(Настраиваем конфигурационный файл утилиты hunter для пакета print)
include(hunter_add_version)
include(hunter_cacheable)
include(hunter_cmake_args)
include(hunter_download)
include(hunter_pick_scheme)
#Указываем данные о версии
hunter_add_version(
    PACKAGE_NAME #имя пакета
    print
    VERSION #версия пакета
    "0.1.0.0"
    URL #ссылка на пакет пред.лабы
    "https://github.com/${GITHUB_USERNAME}/lab09/archive/v0.1.0.0.tar.gz"
    SHA1 #хэш-сумма
    ${PRINT_SHA1}
)
#Указываем версию по умолчанию
hunter_pick_scheme(DEFAULT url_sha1_cmake)
#Указываем hunter_cmake_args
hunter_cmake_args( 
    print
    CMAKE_ARGS
    BUILD_EXAMPLES=NO
    BUILD_TESTS=NO
)

hunter_cacheable(print)

hunter_download(PACKAGE_NAME print) 
EOF
```
Записываем данные о пакете *print* в *default.cmake*
```ShellSession
$ cat >> cmake/configs/default.cmake <<EOF
hunter_config(print VERSION 0.1.0.0)
EOF
```
Отправляем изменения на **GitHub** сервер
```ShellSession
$ git add . # Отследить изменения всех файлов
$ git commit -m"added print package" # Сохранить изменения
$ git push ${GITHUB_USERNAME} master # Загружаем репозиторий ветки ${GITHUB_USERNAME} в удаленный репозиторий ветки master
$ git tag v0.19.137.1 # Добавляем тэг 
$ git push ${GITHUB_USERNAME} master --tags # Отправляем на удаленную ветку теги локальной ветки
$ cd .. # Выходим из директории
```
Инициализация директории **lab10**
```ShellSession
$ export HUNTER_ROOT=`pwd`/hunter # Экспортируем переменную HUNTER_ROOT
$ mkdir lab10 && cd lab10 # Создаем директорию lab10 и переходим в нее
$ git init # Инициализируем локальный репозиторий
$ git remote add origin https://github.com/${GITHUB_USERNAME}/lab10 #Устанавливаем связь с удаленным репозиторием десятой лабораторной работы
```
Работаем с файлом *demo.cpp*
```ShellSession
$ mkdir sources
$ cat > sources/demo.cpp <<EOF #Создание и изменение файла demo.cpp
#include <print.hpp>

int main(int argc, char** argv) {
	std::string text;
	while(std::cin >> text) {
		std::ofstream out("log.txt", std::ios_base::app);
		print(text, out);
		out << std::endl;
	}
}
EOF
```
Работа с *HunterGate.cmake*
```ShellSession
$ wget https://github.com/hunter-packages/gate/archive/v0.8.1.tar.gz # Скачиваем пакет
$ tar -xzvf v0.8.1.tar.gz gate-0.8.1/cmake/HunterGate.cmake # Разархивируем его
$ mkdir cmake #Создаем директорию cmake
$ mv gate-0.8.1/cmake/HunterGate.cmake cmake # Переносим gate-0.8.1/cmake/HunterGate.cmake в директорию cmake
$ rm -rf gate*/ #Удаляем директорию gate-0.8.1
$ rm *.tar.gz #Удаляем пакет v0.8.1.tar.gz
```
Работа с *CMakeLists.txt*
```ShellSession
#Записываем информацию о версии cmake и указываем стандарт CMAKE_CXX
$ cat > CMakeLists.txt <<EOF
cmake_minimum_required(VERSION 3.0)

set(CMAKE_CXX_STANDARD 11)
EOF
```
Загрузка пакета
```
$ wget https://github.com/${GITHUB_USERNAME}/hunter/archive/v0.19.137.1.tar.gz #Скачиваем пакет
$ export HUNTER_SHA1=`openssl sha1 v0.19.137.1.tar.gz | cut -d'=' -f2 | cut -c2-41` #Экспортируем контрольную сумму
$ echo $HUNTER_SHA1 # хэш-сумма SHA1,в дальнейшем будем использовать SHA256
$ rm -rf v0.19.137.1.tar.gz #Удаляем
```
Работа с *CMakeLists.txt*
```ShellSession
$ cat >> CMakeLists.txt <<EOF #Подключение HunterGate.cmake 
include(cmake/HunterGate.cmake)
HunterGate( #Настройки HunterGate
    URL "https://github.com/${GITHUB_USERNAME}/hunter/archive/v0.19.137.1.tar.gz"
    SHA1 "${HUNTER_SHA1}"
)
EOF
```
Работа с *CMakeLists.txt*
```ShellSession
$ cat >> CMakeLists.txt <<EOF

project(demo) 
hunter_add_package(print) #Добавляем пакет print
find_package(print) #Находим пакет print

add_executable(demo \${CMAKE_CURRENT_SOURCE_DIR}/sources/demo.cpp) #Добавляем исполняемый файл, который содержит список исходных файлов
target_link_libraries(demo print) # создаём целевую библиотеку

install(TARGETS demo RUNTIME DESTINATION bin)
EOF
```
Редактирование *.gitignore*
```ShellSession
$ cat > .gitignore <<EOF
*build*/
*install*/
*.swp
EOF
```
Редактирование *README.md*
```ShellSession
$ cat > README.md <<EOF
[![Build Status](https://travis-ci.org/${GITHUB_USERNAME}/lab10.svg?branch=master)](https://travis-ci.org/${GITHUB_USERNAME}/lab10)
the demo application redirects data from stdin to a file **log.txt** using a package **print**.
EOF
```
Редактирование *.travis.yml*
```ShellSession
# -H. установка директории, в которой сгенерируется файл CMakeLists.txt
# -B_build указывает директорию для собираемых файлов
$ cat > .travis.yml <<EOF
language: cpp
script:   
- cmake -H. -B_build
- cmake --build _build
EOF
```
Работа с сервисом *Travis*
```ShellSession
$ travis lint # предупреждения travis
```
Отправляем последние изменения на ***GitHub*** сервер
```ShellSession
$ git add . # # Отследить изменения всех файлов
$ git commit -m"first commit" # Сохранить изменения
$ git push origin master # Загрузка файлов на сервер
```
Работа с ***Travis***
```ShellSession
$ travis login --auto # Авторизация через GitHub
$ travis enable # Включение проекта
```
Сборка проекта утилитой **hunter** и проверяем работу программы.
```ShellSession
# -H. установка директория, в который сгенерируется файл CMakeLists.txt
# -B_build указывает директорию для собираемых файлов
$ cmake -H. -B_build -DCMAKE_INSTALL_PREFIX=_install
$ cmake --build _build --target install
$ mkdir artifacts && cd artifacts # Создаем директорию и переходим в нее
$ echo "text1 text2 text3" | ../_install/bin/demo # Изменяем файл log.txt
$ cat log.txt #Вывод на экран содержимое файла
text1
text2
text3
```

## Report

```ShellSession
$ popd
$ export LAB_NUMBER=10
$ git clone https://github.com/tp-labs/lab${LAB_NUMBER} tasks/lab${LAB_NUMBER}
$ mkdir reports/lab${LAB_NUMBER}
$ cp tasks/lab${LAB_NUMBER}/README.md reports/lab${LAB_NUMBER}/REPORT.md
$ cd reports/lab${LAB_NUMBER}
$ edit REPORT.md
$ gistup -m "lab${LAB_NUMBER}"
```

## Links

- [hub](https://hub.github.com/)
- [polly](https://github.com/ruslo/polly)
- [conan](https://conan.io)

```
Copyright (c) 2017 Братья Вершинины
```
