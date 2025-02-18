---
## Front matter
title: "Лабораторная работа №4"
subtitle: "Работа с программными пакетами"
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

---

# ОТЧЕТ  
## ПО ЛАБОРАТОРНОЙ РАБОТЕ №4  
### Дисциплина: Основы администрирования операционных систем  

**Студент:** Ко Антон Геннадьевич  
**Студ. билет №** 1132221551  
**Группа:** НПИбд-02-23  

**Москва, 2024 г.**

---

## **Цель работы**  

Целью данной работы является получение навыков работы с репозиториями и менеджерами пакетов.

---

## **Выполнение работы**  

### **1. Работа с репозиториями**  
Переход в режим суперпользователя и просмотр репозиториев:  
su -  
cd /etc/yum.repos.d  
ls  

Просмотр содержимого файлов репозиториев:  
cat rocky-addons.repo  
cat rocky-devel.repo  
cat rocky-extras.repo  
cat rocky.repo  

---

### **2. Просмотр списка репозиториев и пакетов**  
dnf repolist  
dnf search user  

---

### **3. Установка и удаление пакета nmap**  
dnf search nmap  
dnf info nmap  
dnf install nmap  
dnf install nmap\*  

Удаление:  
dnf remove nmap  
dnf remove nmap\*  

---

### **4. Работа с группами пакетов**  
dnf groups list  
LANG=C dnf groups list  
dnf groups info "RPM Development Tools"  
dnf groupinstall "RPM Development Tools"  
dnf groupremove "RPM Development Tools"  

---

### **5. История использования dnf**  
dnf history  
dnf history undo 6  

---

### **6. Работа с rpm-пакетом lynx**  
dnf list lynx  
dnf install lynx --downloadonly  

Поиск скачанного пакета и его установка:  
find /var/cache/dnf/ -name lynx*  
cd /var/cache/dnf/...  
rpm -Uhv lynx-.rpm  
which lynx  
rpm -qf $(which lynx)  

Получение информации о пакете lynx:  
rpm -qi lynx  
rpm -ql lynx  
rpm -qd lynx  
man lynx  

Вывод конфигурационных файлов и скриптов пакета:  
rpm -qc lynx  
rpm -q --scripts lynx  

Запуск lynx в отдельном терминале и удаление пакета:  
lynx  
rpm -e lynx  
ls  

---

### **7. Работа с пакетом dnsmasq**  
dnf list dnsmasq  
dnf install dnsmasq  
which dnsmasq  
rpm -qf $(which dnsmasq)  

Получение информации о dnsmasq:  
rpm -qi dnsmasq  
rpm -ql dnsmasq  
rpm -qd dnsmasq  
man dnsmasq  
rpm -qc dnsmasq  

Вывод скриптов установки и удаление пакета:  
rpm -q --scripts dnsmasq  
rpm -e dnsmasq  

---

## **Ответы на контрольные вопросы**  

1. **Какая команда позволяет искать пакет rpm, содержащий файл useradd?**  
   yum search useradd  

2. **Какие команды показывают имя группы dnf, содержащей инструменты безопасности, и список ее содержимого?**  
   yum info gcl  

3. **Как установить rpm, загруженный из интернета, не входящий в репозитории?**  
   yum install  

4. **Как убедиться, что загруженный rpm не содержит вредоносного кода?**  
   rpm -q --scripts  

5. **Какая команда показывает всю документацию в rpm?**  
   rpm -qd  

6. **Какая команда показывает, к какому пакету rpm принадлежит файл?**  
   rpm -qf $(which файл)  

---

## **Вывод**  

В ходе выполнения лабораторной работы были получены навыки работы с репозиториями и менеджерами пакетов.
