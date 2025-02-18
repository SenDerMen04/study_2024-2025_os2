---
## Front matter
title: "Лабораторная работа №14"
subtitle: "Партиции, файловые системы, монтирование"
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

## ПО ЛАБОРАТОРНОЙ РАБОТЕ №14

**Дисциплина:** Основы администрирования операционных систем  

**Студент:** Ко Антон Геннадьевич  
**Студ. билет №** 1132221551  
**Группа:** НПИбд-02-23  

### МОСКВА, 2024 г.

---

## Цель работы

Целью данной работы является получение навыков создания разделов на диске и файловых систем, а также навыков монтирования файловых систем.

---

## Выполнение работы

### Создание виртуальных носителей

Добавим к виртуальной машине два диска размером 512 МБ через VirtualBox.

---

### Создание разделов MBR с помощью `fdisk`

```bash
su -
fdis --list
fdisk /dev/sdb
```

Создание нового раздела:

```bash
n  # Новый раздел
p  # Основной раздел
+100M  # Размер 100 MiB
w  # Запись изменений
```

Обновление таблицы разделов ядра:

```bash
partprobe /dev/sdb
```

---

### Создание логических разделов

```bash
fdisk /dev/sdb
n  # Новый раздел
e  # Расширенный раздел
n  # Логический раздел
+101M  # Размер 101 MiB
w  # Запись изменений
partprobe /dev/sdb
```

---

### Создание раздела подкачки

```bash
fdisk /dev/sdb
t  # Изменение типа
6  # Номер раздела
82  # Код для swap
w  # Запись изменений
partprobe /dev/sdb
mkswap /dev/sdb6
swapon /dev/sdb6
free -m
```

---

### Создание разделов GPT с помощью `gdisk`

```bash
gdisk -l /dev/sdc
gdisk /dev/sdc
n  # Новый раздел
+100M  # Размер 100 MiB
w  # Запись изменений
partprobe /dev/sdc
```

---

### Форматирование файловых систем

Форматирование XFS:

```bash
mkfs.xfs /dev/sdb1
xfs_admin -L xfsdisk /dev/sdb1
```

Форматирование EXT4:

```bash
mkfs.ext4 /dev/sdb5
tune2fs -L ext4disk /dev/sdb5
tune2fs -o acl,user_xattr /dev/sdb5
```

---

### Ручное монтирование

```bash
mkdir -p /mnt/tmp
mount /dev/sdb5 /mnt/tmp
umount /dev/sdb5
```

---

### Монтирование через `/etc/fstab`

```bash
blkid /dev/sdb1
vim /etc/fstab
UUID=<значение_UUID> /mnt/data xfs defaults 1 2
mount -a
df -h
```

---

## Ответы на контрольные вопросы

1. **Какой инструмент используется для создания разделов GUID?**  
   `gdisk`

2. **Какой инструмент применяется для создания разделов MBR?**  
   `fdisk`

3. **Какая файловая система используется по умолчанию для RHEL?**  
   `XFS`

4. **Какой файл отвечает за автоматическое монтирование?**  
   `/etc/fstab`

5. **Как избежать автоматического монтирования?**  
   `mount /dev/sdb5 /mnt/tmp`

6. **Как форматировать раздел swap?**  
   `mkswap /dev/sdb6`

7. **Как проверить, будет ли автоматическое монтирование работать?**  
   `df -h`

8. **Какая файловая система создаётся по умолчанию при `mkfs`?**  
   `swap`

9. **Как отформатировать раздел в EXT4?**  
   ```bash
   mkfs.ext4 /dev/sdb<number>
   tune2fs -L ext4disk /dev/sdb<number>
   tune2fs -o acl,user_xattr /dev/sdb<number>
   ```

10. **Как найти UUID всех устройств?**  
    ```bash
    blkid
    ```

---

## Вывод

В ходе выполнения лабораторной работы были получены навыки создания разделов на диске, работы с файловыми системами и монтирования.
