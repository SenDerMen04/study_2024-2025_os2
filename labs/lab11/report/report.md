---
## Front matter
title: "Лабораторная работа №11"
subtitle: "Управление загрузкой системы"
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

## ПО ЛАБОРАТОРНОЙ РАБОТЕ №11

**Дисциплина:** Основы администрирования операционных систем  

**Студент:** Ко Антон Геннадьевич  
**Студ. билет №** 1132221551  
**Группа:** НПИбд-02-23  

### МОСКВА, 2024 г.

---

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
