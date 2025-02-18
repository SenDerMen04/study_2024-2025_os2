---
## Front matter
title: "Лабораторная работа №10"
subtitle: "Основы работы с модулями ядра операционной системы"
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
## ПО ЛАБОРАТОРНОЙ РАБОТЕ №10
### дисциплина: Основы администрирования операционных систем

**Студент:** Ко Антон Геннадьевич  
**Студ. билет №:** 1132221551  
**Группа:** НПИбд-02-23  

**МОСКВА**  
2024 г.

## Цель работы:
Целью данной работы является получение навыков работы с утилитами управления модулями ядра операционной системы.

---

## Выполнение работы:

### Управление модулями ядра из командной строки:
Запустим терминал и получим полномочия администратора:
```bash
su -
```
Посмотрим, какие устройства имеются в нашей системе и какие модули ядра с ними связаны:
```bash
lspci -k
```
_Рис. 1. Запуск терминала и получение полномочий администратора, просмотр имеющихся устройств в системе и связанных с ними модулей ядра._

Теперь посмотрим, какие модули ядра загружены:
```bash
lsmod | sort
```
_Рис. 2. Просмотр загруженных модулей ядра._

Посмотрим, загружен ли модуль `ext4`:
```bash
lsmod | grep ext4
```
(модуль не загружен). Затем загрузим модуль:
```bash
modprobe ext4
lsmod | grep ext4
```
Просмотрим информацию о модуле:
```bash
modinfo ext4
```
_Рис. 3. Проверка загруженности модуля ext4, его загрузка и просмотр информации._

Попробуем выгрузить модуль:
```bash
modprobe -r ext4
modprobe -r ext4
```
Далее попробуем выгрузить `xfs`:
```bash
modprobe -r xfs
```
(получаем сообщение об ошибке, так как модуль используется).

_Рис. 4. Попытка выгрузки модулей ядра ext4 и xfs._

### Загрузка модулей ядра с параметрами:
Проверим наличие модуля `bluetooth`:
```bash
lsmod | grep bluetooth
```
(модуль не установлен). Загружаем его:
```bash
modprobe bluetooth
lsmod | grep bluetooth
modinfo bluetooth
```
_Рис. 5. Загрузка модуля bluetooth и просмотр информации._

Выгрузим модуль:
```bash
modprobe -r bluetooth
```
_Рис. 6. Выгрузка модуля bluetooth._

### Обновление ядра системы:
Проверим текущую версию ядра:
```bash
uname -r
```
Выведем список пакетов ядра:
```bash
dnf list kernel
```
_Рис. 7. Просмотр версии ядра и списка пакетов._

Обновим систему перед установкой нового ядра:
```bash
dnf upgrade --refresh
```
_Рис. 8. Обновление системы._

Обновим ядро и операционную систему:
```bash
dnf update kernel
dnf update
dnf upgrade --refresh
```
Перезагрузим систему и выберем новое ядро.

_Рис. 9. Обновление ядра и ОС. Перезагрузка._

Проверим установленное ядро:
```bash
uname -r
hostnamectl
```
_Рис. 10. Проверка версии ядра после обновления._

---

## Ответы на контрольные вопросы:
1. `uname -r`
2. `hostnamectl`
3. `lsmod | sort`
4. `modprobe <имя модуля> <параметры> = <значение модуля>`
5. `modprobe -r <модуль>`
6. Сперва выгружаем модуль, который использует нужный нам модуль, затем выгружаем его.
7. `modinfo <модуль>`
8. 
   1) `dnf upgrade --refresh`
   2) `dnf update kernel && dnf update && dnf upgrade --refresh`
   3) Перезагрузка системы и выбор нового ядра.

---

## Вывод:
В ходе выполнения лабораторной работы были получены навыки работы с утилитами управления модулями ядра операционной системы.
