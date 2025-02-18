---
## Front matter
lang: ru-RU
title: Лабораторная работа №16
subtitle: Программный RAID
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

Цель данной работы заключается в освоении работы с RAID-массивами при помощи утилиты `mdadm`.

---

## Выполнение работы

### Создание виртуальных носителей

Добавляем три диска размером 512 MiB к виртуальной машине.

---

### Создание RAID 1

Получаем полномочия администратора и проверяем наличие дисков:

```bash
su -
fdisk -l | grep /dev/sd
```

Создаем разделы на каждом диске:

```bash
sfdisk /dev/sdd <<EOF
;
EOF
sfdisk /dev/sde <<EOF
;
EOF
sfdisk /dev/sdf <<EOF
;
EOF
```

Устанавливаем тип разделов в `Linux raid autodetect`:

```bash
sfdisk --change-id /dev/sdd 1 fd
sfdisk --change-id /dev/sde 1 fd
sfdisk --change-id /dev/sdf 1 fd
```

Создаем массив RAID 1:

```bash
mdadm --create --verbose /dev/md0 --level=1 --raid-devices=2 /dev/sdd1 /dev/sde1
```

Проверяем состояние массива:

```bash
cat /proc/mdstat
mdadm --query /dev/md0
mdadm --detail /dev/md0
```

Форматируем RAID и монтируем:

```bash
mkfs.ext4 /dev/md0
mkdir /data
mount /dev/md0 /data
```

Добавляем в `/etc/fstab`:

```bash
echo '/dev/md0 /data ext4 defaults 1 2' >> /etc/fstab
```

---

### Имитация сбоя диска и замена

```bash
mdadm /dev/md0 --fail /dev/sde1
mdadm /dev/md0 --remove /dev/sde1
mdadm /dev/md0 --add /dev/sdf1
```

Проверяем состояние массива:

```bash
mdadm --detail /dev/md0
```

Удаляем массив и очищаем метаданные:

```bash
umount /dev/md0
mdadm --stop /dev/md0
mdadm --zero-superblock /dev/sdd1
mdadm --zero-superblock /dev/sde1
mdadm --zero-superblock /dev/sdf1
```

---

### RAID 1 с горячим резервом

Создаем RAID 1 с двумя дисками и добавляем третий диск как `hot spare`:

```bash
mdadm --create --verbose /dev/md0 --level=1 --raid-devices=2 /dev/sdd1 /dev/sde1
mdadm --add /dev/md0 /dev/sdf1
```

Проверяем состояние массива:

```bash
cat /proc/mdstat
mdadm --query /dev/md0
mdadm --detail /dev/md0
```

Имитируем сбой:

```bash
mdadm /dev/md0 --fail /dev/sde1
```

Проверяем пересборку массива:

```bash
mdadm --detail /dev/md0
```

Удаляем массив и очищаем метаданные:

```bash
umount /dev/md0
mdadm --stop /dev/md0
mdadm --zero-superblock /dev/sdd1
mdadm --zero-superblock /dev/sde1
mdadm --zero-superblock /dev/sdf1
```

---

### Преобразование RAID 1 в RAID 5

Создаем RAID 1 и добавляем диск:

```bash
mdadm --create --verbose /dev/md0 --level=1 --raid-devices=2 /dev/sdd1 /dev/sde1
mdadm --add /dev/md0 /dev/sdf1
```

Преобразуем RAID 1 в RAID 5:

```bash
mdadm --grow /dev/md0 --level=5
mdadm --grow /dev/md0 --raid-devices=3
```

Проверяем состояние массива:

```bash
mdadm --detail /dev/md0
```

Удаляем массив и очищаем метаданные:

```bash
umount /dev/md0
mdadm --stop /dev/md0
mdadm --zero-superblock /dev/sdd1
mdadm --zero-superblock /dev/sde1
mdadm --zero-superblock /dev/sdf1
```

---

## Ответы на контрольные вопросы

1. **Что такое RAID?**  
   RAID (Redundant Array of Independent Disks) – технология организации хранения данных на нескольких дисках для повышения отказоустойчивости и/или производительности.

2. **Какие типы RAID существуют?**  
   - RAID 0: без резервирования, высокая скорость, низкая надежность.  
   - RAID 1: зеркалирование, высокая надежность.  
   - RAID 5: чередование с четностью, хорошая балансировка между скоростью и надежностью.  
   - RAID 6: двойная четность, повышенная надежность.

3. **Как работает RAID 0, 1, 5, 6?**  
   - **RAID 0**: данные разбиваются на блоки и записываются на несколько дисков (высокая скорость, отсутствие отказоустойчивости).  
   - **RAID 1**: полное зеркалирование данных на два диска (надежность, но меньший объем).  
   - **RAID 5**: данные и контрольные суммы распределены по дискам (оптимальный баланс скорости и надежности).  
   - **RAID 6**: две контрольные суммы, выдерживает выход из строя двух дисков.

---

## Вывод

В ходе выполнения лабораторной работы мы освоили создание, управление и мониторинг RAID-массивов с помощью `mdadm`.
