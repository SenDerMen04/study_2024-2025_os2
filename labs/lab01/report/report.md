---
## Front matter
title: "Шаблон отчёта по лабораторной работе"
subtitle: "Установка и конфигурация операционной системы на виртуальную машину"
author: "Антон Геннадьевич Ко"

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
mainfont: Arial
romanfont: Arial
sansfont: Arial
monofont: Arial
mathfont: Arial
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

## Цель работы  

Целью данной работы является приобретение практических навыков установки операционной системы на виртуальную машину, настройки минимально необходимых для дальнейшей работы сервисов.

---

## Выполнение работы  

1. **Скачивание и установка виртуальной машины**  
   - [VirtualBox](https://www.virtualbox.org/)  

2. **Скачивание дистрибутива Linux Rocky**  
   - [Rocky Linux](https://rockylinux.org/download)  

3. **Установка Linux версии Red Hat (64-bit) на виртуальную машину**  
   - Указан объем памяти: **4096 МБ**  
   - Размер виртуального жесткого диска: **40 ГБ**  
   - Добавлен скаченный ранее оптический привод  

4. **Запуск виртуальной машины и установка ОС**  
   - Выбор языка интерфейса: **английский**  
   - Настройка установки:  
     - **Место установки** — параметры по умолчанию  
     - **Выбор программ** — *Server with GUI* + *Development Tools*  
     - **Отключение KDUMP**  
     - **Включение сети**  
     - Имя узла: **agko.localdomain**  
   - **Установка пароля** для root и администратора  
   - Запуск установки и завершение  

5. **Подключение образа диска Дополнительной гостевой ОС**  

---

## Домашнее задание  

1. **Версия ядра Linux**  
   ```bash
   dmesg | grep "linux version"
   ``
## 2. Частота процессора  

  ```bash
  dmesg | grep -I "MHz"
  ```
## 3. Модель процессора 
  
  ```bash 
  dmesg | grep "CPU0"  
  ```
## 4. Объем доступной оперативной памяти  
  
  ```bash
  free -m  
  ```

## 5. Тип обнаруженного гипервизора  
  ```bash
  dmesg | grep -I "hypervisor detected"  
  ```
## 6. Тип файловой системы корневого раздела  
  ```bash
  dmesg | grep -I "filesystem"  

## 7. Последовательность монтирования файловых систем  
dmesg | grep -i "mount"  

---

## Ответы на контрольные вопросы  

### 1. Что содержит информация об учетной записи пользователя?  
- Идентификатор учетной записи пользователя и ее имя  
- Идентификатор основной группы пользователя и ее название  

### 2. Основные команды Linux  
- **Справка по команде:**  
  info "название команды"  
  "название команды" --help  
- **Перемещение по файловой системе:**  
  cd "путь"  
- **Просмотр содержимого каталога:**  
  ls или dir  
- **Определение объема каталога:**  
  du -sh "путь"  
- **Создание каталога:**  
  mkdir "название"  
- **Удаление каталога:**  
  rmdir "название"  
- **Создание файла:**  
  touch "название"  
  cat > "название"  
- **Удаление файла:**  
  rm "название"  
- **Изменение прав доступа:**  
  chmod "права" "файл"  
- **Просмотр истории команд:**  
  history  

### 3. Файловые системы  

| Название | Максимальный размер файла | Максимальное число файлов | Максимальный размер тома |
|----------|---------------------------|---------------------------|--------------------------|
| NTFS     | 2⁶⁴ байт                   | 2³² - 1                   | 256 ТБ                   |
| EXT4     | 2⁴⁴ байт                   | 2³² - 1                   | 1048576 ТБ                |

### 4. Команда для проверки файловой системы  
dmesg | grep "filesystem"  

### 5. Команда для завершения процесса  
pkill "название процесса"  

---

## Вывод  

В ходе выполнения лабораторной работы были приобретены практические навыки установки операционной системы на виртуальную машину и настройки минимально необходимых для дальнейшей работы сервисов.
