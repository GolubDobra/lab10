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
$ export GITHUB_USERNAME=GolubDobra # Устанавливаем значение переменной окружения GITHUB_USERNAME
$ export GITHUB_TOKEN=6ab2fa01395a5ed63746f15179f386cb9454d599 # Устанавливаем значение переменной окружения GITHUB_TOKEN
```
Устанавливаем **hub**
```ShellSession
$ cd ${GITHUB_USERNAME}/workspace
$ pushd .
~/GolubDobra/workspace ~/GolubDobra/workspace
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
--2018-01-07 04:06:22--  https://github.com/GolubDobra/lab09/archive/v0.1.0.0.tar.gz
Resolving github.com (github.com)... 192.30.253.112, 192.30.253.113
Connecting to github.com (github.com)|192.30.253.112|:443... connected.
HTTP request sent, awaiting response... 302 Found
Location: https://codeload.github.com/GolubDobra/lab09/tar.gz/v0.1.0.0 [following]
--2018-01-07 04:06:23--  https://codeload.github.com/GolubDobra/lab09/tar.gz/v0.1.0.0
Resolving codeload.github.com (codeload.github.com)... 192.30.253.121, 192.30.253.120
Connecting to codeload.github.com (codeload.github.com)|192.30.253.121|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: unspecified [application/x-gzip]
Saving to: ‘v0.1.0.0.tar.gz’

v0.1.0.0.tar.gz                       [     <=>                                                    ] 473,44K   390KB/s    in 1,2s    

2018-01-07 04:06:25 (390 KB/s) - ‘v0.1.0.0.tar.gz’ saved [484803]
$ export PRINT_SHA1=`openssl sha1 v0.1.0.0.tar.gz | cut -d'=' -f2 | cut -c2-41` 
$ echo $PRINT_SHA1 # Выводим хэш-сумму
feabd55227870adfe44776d09e09789860e73e35
$ rm -rf v0.1.0.0.tar.gz # Удаляем пакет
```
Работа с репозиторием ***Hunter***
```ShellSession
$ git clone https://github.com/ruslo/hunter projects/hunter
Cloning into 'projects/hunter'...
remote: Counting objects: 29695, done.
remote: Compressing objects: 100% (39/39), done.
remote: Total 29695 (delta 36), reused 41 (delta 21), pack-reused 29630
Receiving objects: 100% (29695/29695), 5.53 MiB | 678.00 KiB/s, done.
Resolving deltas: 100% (18582/18582), done.
Checking connectivity... done.
$ cd projects/hunter && git checkout v0.19.137 # Меняем директорию на projects/hunter и переключаемся на ветвь v0.19.137
$ git remote show #Выводим ветки удаленного репозитория
origin
$ hub fork # Делаем fork репозитория hunter
Updating GolubDobra
From https://github.com/ruslo/hunter
 * [new branch]      master     -> GolubDobra/master
 * [new branch]      pr.new.toolchain.id -> GolubDobra/pr.new.toolchain.id
 * [new branch]      testing-packages -> GolubDobra/testing-packages
new remote: GolubDobra
$ git remote show #Выводим ветки удаленного репозитория
GolubDobra
origin
$ git remote show ${GITHUB_USERNAME} #Выводим всю информацию о нашей ветке
* remote GolubDobra
  Fetch URL: https://github.com/GolubDobra/hunter.git
  Push  URL: https://github.com/GolubDobra/hunter.git
  HEAD branch: master
  Remote branches:
    master              tracked
    pr.new.toolchain.id tracked
    testing-packages    tracked
  Local ref configured for 'git push':
    master pushes to master (up to date)
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
[detached HEAD 78e14b0] added print package
 2 files changed, 28 insertions(+)
 create mode 100644 cmake/projects/print/hunter.cmake
$ git push ${GITHUB_USERNAME} master # Загружаем репозиторий ветки ${GITHUB_USERNAME} в удаленный репозиторий ветки master
$ git tag v0.19.137.1 # Добавляем тэг 
$ git push ${GITHUB_USERNAME} master --tags # Отправляем на удаленную ветку теги локальной ветки
Username for 'https://github.com': GolubDobra
Password for 'https://GolubDobra@github.com': 
Counting objects: 8, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (6/6), done.
Writing objects: 100% (8/8), 889 bytes | 0 bytes/s, done.
Total 8 (delta 4), reused 0 (delta 0)
remote: Resolving deltas: 100% (4/4), completed with 4 local objects.
To https://github.com/GolubDobra/hunter.git
 * [new tag]         v0.19.137.1 -> v0.19.137.1
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
--2018-01-07 04:24:18--  https://github.com/hunter-packages/gate/archive/v0.8.1.tar.gz
Resolving github.com (github.com)... 192.30.253.113, 192.30.253.112
Connecting to github.com (github.com)|192.30.253.113|:443... connected.
HTTP request sent, awaiting response... 302 Found
Location: https://codeload.github.com/hunter-packages/gate/tar.gz/v0.8.1 [following]
--2018-01-07 04:24:19--  https://codeload.github.com/hunter-packages/gate/tar.gz/v0.8.1
Resolving codeload.github.com (codeload.github.com)... 192.30.253.120, 192.30.253.121
Connecting to codeload.github.com (codeload.github.com)|192.30.253.120|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 335960 (328K) [application/x-gzip]
Saving to: ‘v0.8.1.tar.gz’

v0.8.1.tar.gz                     100%[===========================================================>] 328,09K   304KB/s    in 1,1s    

2018-01-07 04:24:21 (304 KB/s) - ‘v0.8.1.tar.gz’ saved [335960/335960]
$ tar -xzvf v0.8.1.tar.gz gate-0.8.1/cmake/HunterGate.cmake # Разархивируем его
gate-0.8.1/cmake/HunterGate.cmake
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
$ wget https://github.com/${GITHUB_USERNAME}/hunter/archive/v0.19.137.1.tar.gz    # Скачиваем пакет

--2018-01-07 04:29:58--  https://github.com/GolubDobra/hunter/archive/v0.19.137.1.tar.gz
Resolving github.com (github.com)... 192.30.253.113, 192.30.253.112
Connecting to github.com (github.com)|192.30.253.113|:443... connected.
HTTP request sent, awaiting response... 302 Found
Location: https://codeload.github.com/GolubDobra/hunter/tar.gz/v0.19.137.1 [following]
--2018-01-07 04:29:59--  https://codeload.github.com/GolubDobra/hunter/tar.gz/v0.19.137.1
Resolving codeload.github.com (codeload.github.com)... 192.30.253.120, 192.30.253.121
Connecting to codeload.github.com (codeload.github.com)|192.30.253.120|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: unspecified [application/x-gzip]
Saving to: ‘v0.19.137.1.tar.gz’

v0.19.137.1.tar.gz                    [     <=>                                                    ]   1,06M   812KB/s    in 1,3s    

2018-01-07 04:30:01 (812 KB/s) - ‘v0.19.137.1.tar.gz’ saved [1114226]
$ export HUNTER_SHA1=`openssl sha1 v0.19.137.1.tar.gz | cut -d'=' -f2 | cut -c2-41` #Экспортируем контрольную сумму
$ echo $HUNTER_SHA1 # хэш-сумма SHA1,в дальнейшем будем использовать SHA256
feabd55227870adfe44776d09e09789860e73e35
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
Hooray, .travis.yml looks valid :)
```
Отправляем последние изменения на ***GitHub*** сервер
```ShellSession
$ git add . # # Отследить изменения всех файлов
$ git commit -m"first commit" # Сохранить изменения
[master (root-commit) a04ff6c] first commit
 6 files changed, 546 insertions(+)
 create mode 100644 .gitignore
 create mode 100644 .travis.yml
 create mode 100644 CMakeLists.txt
 create mode 100644 README.md
 create mode 100644 cmake/HunterGate.cmake
 create mode 100644 sources/demo.cpp

$ git push origin master # Загрузка файлов на серверUsername for 'https://github.com': GolubDobra
Password for 'https://GolubDobra@github.com': 
Counting objects: 10, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (7/7), done.
Writing objects: 100% (10/10), 5.62 KiB | 0 bytes/s, done.
Total 10 (delta 0), reused 0 (delta 0)
To https://github.com/GolubDobra/lab10
 * [new branch]      master -> master
```
Работа с ***Travis***
```ShellSession
$ travis login --auto # Авторизация через GitHub
Successfully logged in as GolubDobra!
$ travis enable # Включение проекта
Detected repository as GolubDobra/lab10, is this correct? |yes| yes
GolubDobra/lab10: enabled :)
```
Сборка проекта утилитой **hunter** и проверяем работу программы.
```ShellSession
# -H. установка директория, в который сгенерируется файл CMakeLists.txt
# -B_build указывает директорию для собираемых файлов
$ cmake -H. -B_build -DCMAKE_INSTALL_PREFIX=_install
-- The C compiler identification is GNU 5.4.0
-- The CXX compiler identification is GNU 5.4.0
-- Check for working C compiler: /usr/bin/cc
-- Check for working C compiler: /usr/bin/cc -- works
-- Detecting C compiler ABI info
-- Detecting C compiler ABI info - done
-- Detecting C compile features
-- Detecting C compile features - done
-- Check for working CXX compiler: /usr/bin/c++
-- Check for working CXX compiler: /usr/bin/c++ -- works
-- Detecting CXX compiler ABI info
-- Detecting CXX compiler ABI info - done
-- Detecting CXX compile features
-- Detecting CXX compile features - done
-- [hunter] Calculating Toolchain-SHA1
-- [hunter] Calculating Config-SHA1
-- [hunter] HUNTER_ROOT: /home/GolubDobra/GolubDobra/workspace/projects/hunter
-- [hunter] [ Hunter-ID: xxxxxxx | Toolchain-ID: e1266bb | Config-ID: 83da1ac ]
-- [hunter] PRINT_ROOT: /home/GolubDobra/GolubDobra/workspace/projects/hunter/_Base/xxxxxxx/e1266bb/83da1ac/Install (ver.: 0.1.0.0)
-- [hunter] Building print
loading initial cache file /home/GolubDobra/GolubDobra/workspace/projects/hunter/_Base/xxxxxxx/e1266bb/83da1ac/cache.cmake
loading initial cache file /home/GolubDobra/GolubDobra/workspace/projects/hunter/_Base/xxxxxxx/e1266bb/83da1ac/Build/print/args.cmake
-- The C compiler identification is GNU 5.4.0
-- The CXX compiler identification is GNU 5.4.0
-- Check for working C compiler: /usr/bin/cc
-- Check for working C compiler: /usr/bin/cc -- works
-- Detecting C compile features
-- Detecting C compile features - done
-- Check for working CXX compiler: /usr/bin/c++
-- Check for working CXX compiler: /usr/bin/c++ -- works
-- Detecting CXX compile features
-- Detecting CXX compile features - done
-- Configuring done
-- Generating done
-- Build files have been written to: /home/GolubDobra/GolubDobra/workspace/projects/hunter/_Base/xxxxxxx/e1266bb/83da1ac/Build/print/Build
Scanning dependencies of target print-Release
[  6%] Creating directories for 'print-Release'
[ 12%] Performing download step (download, verify and extract) for 'print-Release'
-- downloading...
     src='https://github.com/GolubDobra/lab09/archive/v0.1.0.0.tar.gz'
     dst='/home/GolubDobra/GolubDobra/workspace/projects/hunter/_Base/Download/print/0.1.0.0/d1fdd7c/v0.1.0.0.tar.gz'
     timeout='none'
-- [download 0% complete]

...

-- [download 100% complete]
-- downloading... done
-- verifying file...
     file='/home/GolubDobra/GolubDobra/workspace/projects/hunter/_Base/Download/print/0.1.0.0/d1fdd7c/v0.1.0.0.tar.gz'
-- verifying file... done
-- extracting...
     src='/home/GolubDobra/GolubDobra/workspace/projects/hunter/_Base/Download/print/0.1.0.0/d1fdd7c/v0.1.0.0.tar.gz'
     dst='/home/GolubDobra/GolubDobra/workspace/projects/hunter/_Base/xxxxxxx/e1266bb/83da1ac/Build/print/Source'
-- extracting... [tar xfz]
-- extracting... [analysis]
-- extracting... [rename]
-- extracting... [clean up]
-- extracting... done
[ 18%] No patch step for 'print-Release'
[ 25%] No update step for 'print-Release'
[ 31%] Performing configure step for 'print-Release'
loading initial cache file /home/GolubDobra/GolubDobra/workspace/projects/hunter/_Base/xxxxxxx/e1266bb/83da1ac/cache.cmake
loading initial cache file /home/GolubDobra/GolubDobra/workspace/projects/hunter/_Base/xxxxxxx/e1266bb/83da1ac/Build/print/args.cmake
-- The C compiler identification is GNU 5.4.0
-- The CXX compiler identification is GNU 5.4.0
-- Check for working C compiler: /usr/bin/cc
-- Check for working C compiler: /usr/bin/cc -- works
-- Detecting C compile features
-- Detecting C compile features - done
-- Check for working CXX compiler: /usr/bin/c++
-- Check for working CXX compiler: /usr/bin/c++ -- works
-- Detecting CXX compile features
-- Detecting CXX compile features - done
-- Configuring done
-- Generating done
-- Build files have been written to: /home/GolubDobra/GolubDobra/workspace/projects/hunter/_Base/xxxxxxx/e1266bb/83da1ac/Build/print/Build/print-Release-prefix/src/print-Release-build
[ 37%] Performing build step for 'print-Release'
Scanning dependencies of target print
[ 50%] Building CXX object CMakeFiles/print.dir/sources/print.cpp.o
[100%] Linking CXX static library libprint.a
[100%] Built target print
[ 43%] Performing install step for 'print-Release'
[100%] Built target print
Install the project...
-- Install configuration: "Release"
-- Installing: /home/GolubDobra/GolubDobra/workspace/projects/hunter/_Base/xxxxxxx/e1266bb/83da1ac/Build/print/Install/lib/libprint.a
-- Installing: /home/GolubDobra/GolubDobra/workspace/projects/hunter/_Base/xxxxxxx/e1266bb/83da1ac/Build/print/Install/include
-- Installing: /home/GolubDobra/GolubDobra/workspace/projects/hunter/_Base/xxxxxxx/e1266bb/83da1ac/Build/print/Install/include/print.hpp
-- Installing: /home/GolubDobra/GolubDobra/workspace/projects/hunter/_Base/xxxxxxx/e1266bb/83da1ac/Build/print/Install/cmake/print-config.cmake
-- Installing: /home/GolubDobra/GolubDobra/workspace/projects/hunter/_Base/xxxxxxx/e1266bb/83da1ac/Build/print/Install/cmake/print-config-release.cmake
loading initial cache file /home/GolubDobra/GolubDobra/workspace/projects/hunter/_Base/xxxxxxx/e1266bb/83da1ac/Build/print/args.cmake
[ 50%] Completed 'print-Release'
[ 50%] Built target print-Release
Scanning dependencies of target print-Debug
[ 56%] Creating directories for 'print-Debug'
[ 62%] Performing download step (download, verify and extract) for 'print-Debug'
-- verifying file...
     file='/home/GolubDobra/GolubDobra/workspace/projects/hunter/_Base/Download/print/0.1.0.0/d1fdd7c/v0.1.0.0.tar.gz'
-- verifying file... done
-- extracting...
     src='/home/GolubDobra/GolubDobra/workspace/projects/hunter/_Base/Download/print/0.1.0.0/d1fdd7c/v0.1.0.0.tar.gz'
     dst='/home/GolubDobra/GolubDobra/workspace/projects/hunter/_Base/xxxxxxx/e1266bb/83da1ac/Build/print/Source'
-- extracting... [tar xfz]
-- extracting... [analysis]
-- extracting... [rename]
-- extracting... [clean up]
-- extracting... done
[ 68%] No patch step for 'print-Debug'
[ 75%] No update step for 'print-Debug'
[ 81%] Performing configure step for 'print-Debug'
loading initial cache file /home/GolubDobra/GolubDobra/workspace/projects/hunter/_Base/xxxxxxx/e1266bb/83da1ac/cache.cmake
loading initial cache file /home/GolubDobra/GolubDobra/workspace/projects/hunter/_Base/xxxxxxx/e1266bb/83da1ac/Build/print/args.cmake
-- The C compiler identification is GNU 5.4.0
-- The CXX compiler identification is GNU 5.4.0
-- Check for working C compiler: /usr/bin/cc
-- Check for working C compiler: /usr/bin/cc -- works
-- Detecting C compile features
-- Detecting C compile features - done
-- Check for working CXX compiler: /usr/bin/c++
-- Check for working CXX compiler: /usr/bin/c++ -- works
-- Detecting CXX compile features
-- Detecting CXX compile features - done
-- Configuring done
-- Generating done
-- Build files have been written to: /home/GolubDobra/GolubDobra/workspace/projects/hunter/_Base/xxxxxxx/e1266bb/83da1ac/Build/print/Build/print-Debug-prefix/src/print-Debug-build
[ 87%] Performing build step for 'print-Debug'
Scanning dependencies of target print
[ 50%] Building CXX object CMakeFiles/print.dir/sources/print.cpp.o
[100%] Linking CXX static library libprintd.a
[100%] Built target print
[ 93%] Performing install step for 'print-Debug'
[100%] Built target print
Install the project...
-- Install configuration: "Debug"
-- Installing: /home/GolubDobra/GolubDobra/workspace/projects/hunter/_Base/xxxxxxx/e1266bb/83da1ac/Build/print/Install/lib/libprintd.a
-- Up-to-date: /home/GolubDobra/GolubDobra/workspace/projects/hunter/_Base/xxxxxxx/e1266bb/83da1ac/Build/print/Install/include
-- Up-to-date: /home/GolubDobra/GolubDobra/workspace/projects/hunter/_Base/xxxxxxx/e1266bb/83da1ac/Build/print/Install/include/print.hpp
-- Installing: /home/GolubDobra/GolubDobra/workspace/projects/hunter/_Base/xxxxxxx/e1266bb/83da1ac/Build/print/Install/cmake/print-config.cmake
-- Installing: /home/GolubDobra/GolubDobra/workspace/projects/hunter/_Base/xxxxxxx/e1266bb/83da1ac/Build/print/Install/cmake/print-config-debug.cmake
loading initial cache file /home/GolubDobra/GolubDobra/workspace/projects/hunter/_Base/xxxxxxx/e1266bb/83da1ac/Build/print/args.cmake
[100%] Completed 'print-Debug'
[100%] Built target print-Debug
-- [hunter] Build step successful (dir: /home/GolubDobra/GolubDobra/workspace/projects/hunter/_Base/xxxxxxx/e1266bb/83da1ac/Build/print)
-- [hunter] Cache saved: /home/GolubDobra/GolubDobra/workspace/projects/hunter/_Base/Cache/raw/a72a8b50460042ed76eb2a9bbac35104010660f0.tar.bz2
-- Configuring done
-- Generating done
-- Build files have been written to: /home/GolubDobra/GolubDobra/workspace/projects/lab10/_build

$ cmake --build _build --target install
Scanning dependencies of target demo
[ 50%] Building CXX object CMakeFiles/demo.dir/sources/demo.cpp.o
[100%] Linking CXX executable demo
[100%] Built target demo
Install the project...
-- Install configuration: ""
-- Installing: /home/GolubDobra/GolubDobra/workspace/projects/lab10/_install/bin/demo
$ mkdir artifacts && cd artifacts # Создаем директорию и переходим в нее
$ echo "text1 text2 text3" | ../_install/bin/demo # Изменяем файл log.txt
$ cat log.txt #Вывод на экран содержимое файла
text1
text2
text3
$ git push origin master
Username for 'https://github.com': GolubDobra
Password for 'https://GolubDobra@github.com': 
Everything up-to-date
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
