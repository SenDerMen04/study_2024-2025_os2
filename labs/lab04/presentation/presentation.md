---
## Front matter
lang: ru-RU
title: Лабораторная работа №4
subtitle: Работа с программными пакетами
author:
  - Ко А.Г.
institute:
  - Российский университет дружбы народов, Москва, Россия
date: 18 февраля 2025

## i18n babel
babel-lang: russian
babel-otherlangs: english

## Formatting pdf
toc: false
toc-title: Содержание
slide_level: 2
aspectratio: 169
section-titles: true
theme: metropolis
header-includes:
 - \metroset{progressbar=frametitle,sectionpage=progressbar,numbering=fraction}
---

# Информация

## Докладчик

:::::::::::::: {.columns align=center}
::: {.column width="70%"}

  * Ко Антон Геннадьевич
  * Студент
  * Российский университет дружбы народов
  * [1132221551@pfur.ru](mailto:1132221551@pfur.ru)
  * <https://github.com/SenDerMen04>

:::
::::::::::::::

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
