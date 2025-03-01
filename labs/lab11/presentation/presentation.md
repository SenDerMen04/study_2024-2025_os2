---
## Front matter
lang: ru-RU
title: Лабораторная работа №11
subtitle: Управление загрузкой системы
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

Целью данной работы является получение навыков работы с загрузчиком системы GRUB2.

---

## Выполнение работы

### Модификация параметров GRUB2

Запустим терминал и получим полномочия администратора:

```bash
su -
```

Далее откроем файл `/etc/default/grub` в текстовом редакторе `nano`:

```bash
nano /etc/default/grub
```

В файле `/etc/default/grub` удалим параметры `rhgb` и `quiet` из строки указания параметров запуска ядра системы `GRUB_CMDLINE_LINUX`. В этом же файле установим параметр отображения меню загрузки в течение 10 секунд:

```bash
GRUB_TIMEOUT=10
```

Сохраним изменения и закроем файл.

Теперь запишем изменения в GRUB2:

```bash
grub2-mkconfig > /boot/grub2/grub.cfg
```

После чего перезагрузим систему и убедимся, что при загрузке мы увидим прокрутку загрузочных сообщений.

---

## Устранение неполадок

Запустим перезагрузку системы:

```bash
reboot
```

Как только появится меню GRUB, выберем строку с текущей версией ядра в меню и нажмем `e` для редактирования.

Прокрутим вниз до строки, начинающейся с `linux ($root)/vmlinuz-`, и в конце этой строки введем:

```bash
systemd.unit=rescue.target
```

Нажмем `Ctrl + X` для продолжения процесса загрузки.

Введем пароль пользователя `root` при появлении запроса, после чего посмотрим список всех файлов модулей, которые загружены в настоящее время:

```bash
systemctl list-units
```

Посмотрим задействованные переменные среды оболочки:

```bash
systemctl show-environment
```

Перезагрузим систему:

```bash
systemctl reboot
```

---

## Сброс пароля root

Запустим перезагрузку системы:

```bash
reboot
```

Когда отобразится меню GRUB, выберем в меню строку с текущей версией ядра системы и нажмем `e` для редактирования.

В конце строки, загружающей ядро, введем:

```bash
rd.break
```

Нажмем `Ctrl + X` для продолжения процесса загрузки.

Этап загрузки остановился в момент загрузки `initramfs`, непосредственно перед монтированием корневой файловой системы. Чтобы получить доступ к системному образу для чтения и записи, наберем:

```bash
mount -o remount,rw /sysroot
chroot /sysroot
```

Теперь зададим новый пароль для `root`:

```bash
passwd
```

Так как на этом этапе загрузки `SELinux` еще не активирован, необходимо загрузить политику:

```bash
load_policy -i
chcon -t shadow_t /etc/shadow
```

Перезагрузим систему:

```bash
reboot
```

Войдём в систему с изменённым паролем пользователя `root`.

---

## Ответы на контрольные вопросы

1. **Какой файл конфигурации следует изменить для применения общих изменений в GRUB2?**  
   `/etc/default/grub`

2. **Как называется конфигурационный файл GRUB2, в котором вы применяете изменения для GRUB2?**  
   `/boot/grub2/grub.cfg`

3. **После внесения изменений в конфигурацию GRUB2, какую команду вы должны выполнить, чтобы изменения сохранились и воспринялись при загрузке системы?**  
   ```bash
   systemctl reboot
   ```

---

## Вывод

В ходе выполнения лабораторной работы были получены навыки работы с загрузчиком системы GRUB2.
