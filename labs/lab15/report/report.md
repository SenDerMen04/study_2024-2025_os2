---
## Front matter
title: "Лабораторная работа №15"
subtitle: "Управление логическими томами"
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

## ПО ЛАБОРАТОРНОЙ РАБОТЕ №15

**Дисциплина:** Основы администрирования операционных систем  

**Студент:** Ко Антон Геннадьевич  
**Студ. билет №** 1132221551  
**Группа:** НПИбд-02-23  

### МОСКВА, 2024 г.

---

## Цель работы

Целью данной работы является получение навыков управления логическими томами.

---

## Выполнение работы

### Создание физического тома

Отмонтируем ранее использованные точки монтирования:

```bash
umount /mnt/data
umount /mnt/data-ext
```

Проверим, что диски не подмонтированы:

```bash
mount
```

Создадим новую разметку для `/dev/sdb` и `/dev/sdc`, удалив старые партиции:

```bash
fdisk /dev/sdb
```

```bash
p  # Просмотр текущей разметки
o  # Создание новой таблицы разделов DOS
p  # Проверка удаления партиций
w  # Сохранение изменений
```

Обновим таблицу разделов:

```bash
partprobe /dev/sdb
```

Создадим новый основной раздел с типом LVM:

```bash
fdisk /dev/sdb
n  # Новый раздел
p  # Основной раздел
+100M  # Размер
w  # Запись изменений
```

Установим раздел как физический том:

```bash
pvcreate /dev/sdb1
pvs  # Проверка
```

---

### Создание группы томов и логических томов

```bash
vgcreate vgdata /dev/sdb1
vgs  # Проверка
lvcreate -n lvdata -l 50%FREE vgdata
lvs  # Проверка
```

Создадим файловую систему:

```bash
mkfs.ext4 /dev/vgdata/lvdata
```

Создадим точку монтирования и добавим запись в `/etc/fstab`:

```bash
mkdir -p /mnt/data
echo '/dev/vgdata/lvdata /mnt/data ext4 defaults 1 2' >> /etc/fstab
mount -a
```

---

### Изменение размера логических томов

Добавим новый раздел и расширим группу томов:

```bash
fdisk /dev/sdb
n  # Новый раздел
+100M
w  # Запись изменений
```

Создадим новый физический том и расширим `vgdata`:

```bash
pvcreate /dev/sdb2
vgextend vgdata /dev/sdb2
```

Расширим `lvdata`:

```bash
lvextend -r -l +50%FREE /dev/vgdata/lvdata
```

Проверим доступное пространство:

```bash
df -h
```

Уменьшим логический том на 50МБ:

```bash
lvreduce -r -L -50M /dev/vgdata/lvdata
```

Проверим изменения:

```bash
lvs
df -h
```

---

## Ответы на контрольные вопросы

1. **Какой тип раздела используется в разделе GUID для работы с LVM?**  
   `GPT`

2. **Как создать группу томов `vggroup`, содержащую `/dev/sdb3`, с экстентом 4 MiB?**  
   ```bash
   vgcreate -s 4M vggroup /dev/sdb3
   ```

3. **Как показать сводку физических томов?**  
   ```bash
   pvs
   ```

4. **Как добавить весь диск `/dev/sdd` в группу томов?**  
   ```bash
   vgextend vggroup /dev/sdd
   ```

5. **Как создать логический том `lvvol1` размером 6 MiB?**  
   ```bash
   lvcreate -n lvvol1 -L 6M vggroup
   ```

6. **Как увеличить `lvvol1` на 100 МБ?**  
   ```bash
   lvextend -r -L +100M /dev/vggroup/lvvol1
   ```

7. **Какой первый шаг для увеличения логического тома, если нет доступного места?**  
   Создать новый физический том и расширить группу томов.

8. **Какой параметр `lvextend` автоматически изменяет размер файловой системы?**  
   `-r`

9. **Как отобразить доступные логические тома?**  
   ```bash
   lvs
   ```

10. **Как проверить целостность файловой системы `lvdata`?**  
    ```bash
    fsck /dev/vgdata/lvdata
    ```

---

## Вывод

В ходе выполнения лабораторной работы были получены навыки управления логическими томами в LVM.
