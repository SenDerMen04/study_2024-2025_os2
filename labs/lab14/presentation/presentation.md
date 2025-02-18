---
## Front matter
lang: ru-RU
title: Лабораторная работа №14
subtitle: Партиции, файловые системы, монтирование
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
