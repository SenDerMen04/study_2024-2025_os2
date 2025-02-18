---
## Front matter
title: "Лабораторная работа №9"
subtitle: "Управление SELinux"
author: "Ко Антон Геннадьевич"

## Generic otions
lang: ru-RU
toc-title: "Содержание"

## Bibliography
bibliography: bib/cite.bib
csl: pandoc/csl/gost-r-7-0-5-2008-numeric.csl

## Pdf output format
toc: true # Table of contents
toc-depth: 2
lof: true # List of figures
lot: true # List of tables
fontsize: 12pt
linestretch: 1.5
papersize: a4
documentclass: scrreprt
## I18n polyglossia
polyglossia-lang:
  name: russian
  options:
	- spelling=modern
	- babelshorthands=true
polyglossia-otherlangs:
  name: english
## I18n babel
babel-lang: russian
babel-otherlangs: english
## Fonts
mainfont: IBM Plex Serif
romanfont: IBM Plex Serif
sansfont: IBM Plex Sans
monofont: IBM Plex Mono
mathfont: STIX Two Math
mainfontoptions: Ligatures=Common,Ligatures=TeX,Scale=0.94
romanfontoptions: Ligatures=Common,Ligatures=TeX,Scale=0.94
sansfontoptions: Ligatures=Common,Ligatures=TeX,Scale=MatchLowercase,Scale=0.94
monofontoptions: Scale=MatchLowercase,Scale=0.94,FakeStretch=0.9
mathfontoptions:
## Biblatex
biblatex: true
biblio-style: "gost-numeric"
biblatexoptions:
  - parentracker=true
  - backend=biber
  - hyperref=auto
  - language=auto
  - autolang=other*
  - citestyle=gost-numeric
## Pandoc-crossref LaTeX customization
figureTitle: "Рис."
tableTitle: "Таблица"
listingTitle: "Листинг"
lofTitle: "Список иллюстраций"
lotTitle: "Список таблиц"
lolTitle: "Листинги"
## Misc options
indent: true
header-includes:
  - \usepackage{indentfirst}
  - \usepackage{float} # keep figures where there are in the text
  - \floatplacement{figure}{H} # keep figures where there are in the text
---

# РОССИЙСКИЙ УНИВЕРСИТЕТ ДРУЖБЫ НАРОДОВ
## Факультет физико-математических и естественных наук
### Кафедра прикладной информатики и теории вероятностей


# ОТЧЕТ
## ПО ЛАБОРАТОРНОЙ РАБОТЕ №9
### дисциплина: Основы администрирования операционных систем

**Студент:** Ко Антон Геннадьевич  
**Студ. билет №:** 1132221551  
**Группа:** НПИбд-02-23  

**МОСКВА**  
2024 г.

## Цель работы:
Целью данной работы является получение навыков работы с контекстом безопасности и политиками SELinux.

---

## Выполнение работы:

### Управление режимами SELinux:
Запустим терминал и получим полномочия администратора:
```bash
su -
```
Просмотрим текущую информацию о состоянии SELinux:
```bash
sestatus -v
```
_Рис.1. Запуск терминала и получение полномочий администратора, просмотр текущей информации о состоянии SELinux._

Посмотрим, в каком режиме работает SELinux:
```bash
getenforce
```
Изменим режим работы SELinux на разрешающий:
```bash
setenforce 0
getenforce
```
Откроем файл `/etc/sysconfig/selinux` в текстовом редакторе:
```bash
mcedit /etc/sysconfig/selinux
```
_Рис. 2. Просмотр режима работы SELinux, изменение режима работы и проверка, открытие файла в текстовом редакторе._

В открытом файле установим:
```bash
SELINUX=disabled
```
Сохраним изменения и перезагрузим систему:
```bash
reboot
```
_Рис.3. Установка в файле SELINUX=disabled, сохранение изменений и перезагрузка системы._

После перезагрузки проверим статус SELinux:
```bash
getenforce
```
Попробуем переключить режим работы:
```bash
setenforce 1
```
Система сообщает, что SELinux отключён. Откроем файл `/etc/sysconfig/selinux` и установим:
```bash
SELINUX=enforcing
```
Сохраним изменения и перезагрузим систему.

### Использование restorecon для восстановления контекста безопасности:

Просмотрим контекст безопасности файла `/etc/hosts`:
```bash
ls -Z /etc/hosts
```
Скопируем файл в домашний каталог и проверим контекст:
```bash
cp /etc/hosts ~/ 
ls -Z ~/hosts
```
Переместим файл обратно в `/etc` и снова проверим контекст:
```bash
mv ~/hosts /etc/
ls -Z /etc/hosts
```
Восстановим контекст безопасности:
```bash
restorecon -v /etc/hosts
ls -Z /etc/hosts
```
Для массового исправления контекста выполним:
```bash
touch /.autorelabel
reboot
```
_Рис. 10. Восстановление контекста безопасности._

### Настройка контекста безопасности для нестандартного расположения файлов веб-сервера:

Установим необходимое ПО:
```bash
dnf -y install httpd lynx
```
Создадим каталог и файл:
```bash
mkdir /web
cd /web
touch index.html
```
Откроем файл и добавим текст:
```bash
echo "Welcome to my web-server." > index.html
```
Изменим файл конфигурации Apache `/etc/httpd/conf/httpd.conf`, заменив `DocumentRoot "/var/www/html"` на `DocumentRoot "/web"`. Также обновим настройки доступа.

Запустим веб-сервер:
```bash
systemctl start httpd
systemctl enable httpd
```
Откроем веб-страницу в браузере:
```bash
lynx http://localhost
```
Применим новую метку контекста и восстановим контекст безопасности:
```bash
semanage fcontext -a -t httpd_sys_content_t "/web(/.*)?"
restorecon -R -v /web
setenforce 1
reboot
```
_Рис. 19. Настройка контекста безопасности для веб-сервера._

### Работа с переключателями SELinux:

Просмотрим список переключателей SELinux для службы ftp:
```bash
getsebool -a | grep ftp
```
Изменим значение переключателя:
```bash
setsebool ftpd_anon_write on
getsebool ftpd_anon_write
```
Изменим постоянное значение переключателя:
```bash
setsebool -P ftpd_anon_write on
semanage boolean -l | grep ftpd_anon
```
_Рис. 25. Работа с переключателями SELinux._

---

## Ответы на контрольные вопросы:
1. `setenforce 0`
2. `getsebool -a`
3. `audit2allow`
4. ```bash
semanage fcontext -a -t httpd_sys_content_t "/web(/.*)?"
restorecon -R -v /web
    ```
5. `/etc/sysconfig/selinux`
6. `/var/log/audit/audit.log`
7. `getsebool -a | grep ftp`
8. `ps -eZ` или `id -Z`

---

## Вывод:
В ходе выполнения лабораторной работы были получены навыки работы с контекстом безопасности и политиками SELinux.

