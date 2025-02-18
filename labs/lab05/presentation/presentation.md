---
## Front matter
lang: ru-RU
title: Лабораторная работа №5
subtitle: Управление системными службами
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

Целью данной работы является получение навыков управления системными службами операционной системы посредством systemd.

---

## **Выполнение работы**  

### **1. Управление сервисами**  
Получаем права администратора:  
sudo su  

Проверяем статус службы Very Secure FTP:  
systemctl status vsftpd  

Если служба не установлена, устанавливаем ее:  
dnf -y install vsftpd  

Запускаем службу:  
systemctl start vsftpd  

Проверяем статус службы:  
systemctl status vsftpd  

Добавляем службу в автозапуск:  
systemctl enable vsftpd  

Удаляем службу из автозапуска:  
systemctl disable vsftpd  

Просматриваем символические ссылки для автозапуска сервисов:  
ls /etc/systemd/system/multi-user.target.wants  

Снова добавляем службу в автозапуск и проверяем:  
systemctl enable vsftpd  
systemctl status vsftpd  

Просматриваем зависимости юнита:  
systemctl list-dependencies vsftpd  

Просматриваем юниты, зависящие от данного юнита:  
systemctl list-dependencies vsftpd --reverse  

---

### **2. Конфликты юнитов**  
Получаем права администратора:  
su -  

Устанавливаем iptables:  
dnf -y install iptables\*  

Проверяем статус firewalld и iptables:  
systemctl status firewalld  
systemctl status iptables  

Пробуем запустить обе службы:  
systemctl start firewalld  
systemctl start iptables  

Выводим настройки конфликтов для юнитов:  
cat /usr/lib/systemd/system/firewalld.service  
cat /usr/lib/systemd/system/iptables.service  

Останавливаем iptables:  
systemctl stop iptables  

Запускаем firewalld:  
systemctl start firewalld  

Блокируем запуск iptables:  
systemctl mask iptables  

Проверяем попытку запуска iptables (должна появиться ошибка):  
systemctl start iptables  

Пытаемся добавить iptables в автозапуск (статус будет замаскирован):  
systemctl enable iptables  

---

### **3. Изолируемые цели**  
Получаем права администратора:  
su -  

Переходим в каталог systemd и ищем список всех целей, которые можно изолировать:  
cd /usr/lib/systemd/system  
grep Isolate *.target  

Переключаем систему в режим восстановления:  
systemctl isolate rescue.target  

После ввода пароля root перезапускаем систему:  
systemctl isolate reboot.target  

---

### **4. Цель по умолчанию**  
Получаем права администратора:  
su -  

Выводим текущую цель по умолчанию:  
systemctl get-default  

Устанавливаем текстовый режим загрузки:  
systemctl set-default multi-user.target  

Перезагружаем систему:  
reboot  

После загрузки в текстовом режиме снова получаем права администратора и устанавливаем графический режим:  
systemctl set-default graphical.target  

Перезагружаем систему:  
reboot  

---

## **Ответы на контрольные вопросы**  

1. **Что такое юнит (unit)? Приведите примеры.**  
   Unit – объект, которым может управлять система systemd. Примеры: сервисы (`.service`), точки монтирования (`.mount`), таймеры (`.timer`).  

2. **Какая команда позволяет убедиться, что цель больше не включена в автозапуск?**  
   systemctl is-enabled "имя_юнита"  

3. **Какая команда отображает все загруженные сервисные юниты?**  
   systemctl list-units  

4. **Как создать зависимость (wants) для сервиса?**  
   Нужно добавить зависимость в параметр `Wants=` в файле `имя_сервиса.service`.  

5. **Как переключить текущее состояние системы в режим восстановления (rescue target)?**  
   systemctl set-default rescue.target  

6. **Почему цель может не быть изолированной?**  
   Некоторые цели являются критическими для работы systemd, и их нельзя изолировать, так как это может привести к потере важных зависимостей.  

7. **Как узнать, какие юниты зависят от определенной службы?**  
   systemctl list-dependencies "имя_юнита" --reverse  

---

## **Вывод**  

В ходе выполнения лабораторной работы были получены навыки управления системными службами операционной системы посредством systemd.

