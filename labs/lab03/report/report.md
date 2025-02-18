---
## Front matter
title: "Лабораторная работа №3"
subtitle: "Настройка прав доступа"
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
## ПО ЛАБОРАТОРНОЙ РАБОТЕ №3  
### Дисциплина: Основы администрирования операционных систем  

**Студент:** Ко Антон Геннадьевич  
**Студ. билет №** 1132221551  
**Группа:** НПИбд-02-23  

**Москва, 2024 г.**

---

## **Цель работы**  

Целью данной работы является получение навыков настройки базовых и специальных прав доступа для групп пользователей в операционной системе типа Linux.

---

## **Выполнение работы**  

### **1. Создание каталогов и назначение владельцев**  
Открываем терминал с учетной записью root:  
su -  
mkdir -p /data/main /data/third  
ls -Al /data  
chgrp main /data/main  
chgrp third /data/third  
chmod 770 /data/main  
chmod 770 /data/third  

Теперь владельцем /data/main является main, а /data/third — third.

---

### **2. Проверка прав доступа пользователем bob**  
В другом терминале входим под пользователем bob:  
su - bob  
cd /data/main  
touch emptyfile  
cd /data/third  
touch emptyfile  # Здесь произойдет ошибка  

Так как bob не является владельцем /data/third, файл создать нельзя.

---

### **3. Создание файлов пользователем alice**  
su - alice  
cd /data/main  
touch alice1 alice2  
ls  

Файлы alice1 и alice2 принадлежат alice.

---

### **4. Проверка прав bob на удаление файлов alice**  
su - bob  
cd /data/main  
ls  
rm -f alice*  
touch bob1 bob2  

Файлы alice1 и alice2 успешно удалены, так как bob является членом группы main.

---

### **5. Установка sticky-бита и бита идентификатора группы**  
su -  
chmod g+s,o+t /data/main  

Теперь пользователи могут удалять только свои файлы.

---

### **6. Проверка sticky-бита пользователем alice**  
su - alice  
cd /data/main  
touch alice3 alice4  
ls -Al /data  
rm -rf bob*  # Ошибка "Operation not permitted"  

Sticky-бит предотвращает удаление файлов других пользователей.

---

### **7. Настройка ACL для дополнительных прав**  
su -  
setfacl -m g:third:rx /data/main  
setfacl -m g:main:rx /data/third  
getfacl /data/main  
getfacl /data/third  

Теперь группа third имеет доступ к /data/main, а main к /data/third.

---

### **8. Установка ACL по умолчанию и проверка**  
touch /data/main/newfile1  
getfacl /data/main/newfile1  
setfacl -m d:g:third:rwx /data/main  
setfacl -m d:g:main:rwx /data/third  
touch /data/main/newfile2  
getfacl /data/main/newfile2  

---

### **9. Проверка полномочий пользователя carol**  
su - carol  
rm /data/main/newfile1  # Ошибка  
rm /data/main/newfile2  # Ошибка  
echo "Hello, world" >> /data/main/newfile1  # Ошибка  
echo "Hello, world" >> /data/main/newfile2  # Успех  

Файл newfile2 был создан после настройки ACL, поэтому доступ к нему отличается.

---

## **Ответы на контрольные вопросы**  

1. **Как установить владельца и группу для файла?**  
   chown bob:main /data/third/newfile  

2. **Как найти все файлы пользователя?**  
   find ~ -user bob -print  

3. **Как установить права 770 для каталога /data?**  
   chmod 770 /data  

4. **Как сделать файл исполняемым?**  
   chmod +x file  

5. **Как проверить, что новые файлы наследуют группу каталога?**  
   getfacl “имя каталога”  

6. **Как запретить удаление чужих файлов в общем каталоге?**  
   chmod g+s,o+t /data/main  

7. **Как добавить ACL на чтение для группы?**  
   setfacl -m g:group:r <file/dir>  

8. **Как настроить наследуемые права ACL для группы?**  
   setfacl -dm g:group:r /dir  

9. **Как сделать так, чтобы "другие" пользователи не имели прав на файлы?**  
   umask 007  

10. **Как защитить файл от случайного удаления?**  
    sudo chattr +i myfile  

---

## **Вывод**  

В ходе выполнения лабораторной работы были получены навыки настройки базовых и специальных прав доступа для групп пользователей в операционной системе типа Linux.
