---
## Front matter
lang: ru-RU
title: Лабораторная работа №15
subtitle: Управление логическими томами
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
