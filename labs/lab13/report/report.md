---
## Front matter
title: "Лабораторная работа №13"
subtitle: "Фильтр пакетов"
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

## ПО ЛАБОРАТОРНОЙ РАБОТЕ №13

**Дисциплина:** Основы администрирования операционных систем  

**Студент:** Ко Антон Геннадьевич  
**Студ. билет №** 1132221551  
**Группа:** НПИбд-02-23  

### МОСКВА, 2024 г.

---

## Цель работы

Целью данной работы является получение навыков настройки пакетного фильтра в Linux.

---

## Выполнение работы

### Управление брандмауэром с помощью `firewall-cmd`

Получим полномочия администратора:

```bash
su -
```

Определим текущую зону по умолчанию и доступные зоны:

```bash
firewall-cmd --get-default-zone
firewall-cmd --get-zones
```

Просмотр доступных служб:

```bash
firewall-cmd --get-services
firewall-cmd --list-services
```

Сравним вывод информации:

```bash
firewall-cmd --list-all
firewall-cmd --list-all --zone=public
```

Добавим сервер VNC в конфигурацию брандмауэра:

```bash
firewall-cmd --add-service=vnc-server
firewall-cmd --list-all
systemctl restart firewalld
```

Сделаем службу `vnc-server` постоянной:

```bash
firewall-cmd --add-service=vnc-server --permanent
firewall-cmd --reload
```

Добавим порт `2022` TCP в конфигурацию межсетевого экрана:

```bash
firewall-cmd --add-port=2022/tcp --permanent
firewall-cmd --reload
firewall-cmd --list-all
```

---

### Управление брандмауэром с помощью `firewall-config`

Установим графический интерфейс `firewall-config`:

```bash
yum install firewall-config
firewall-config
```

Выберем `Permanent`, затем включим службы `http`, `https`, `ftp` и добавим порт `2022/udp`.

Перезагрузим конфигурацию:

```bash
firewall-cmd --reload
```

---

### Самостоятельная работа

Создадим конфигурацию межсетевого экрана для служб:

```bash
firewall-cmd --add-service=telnet --permanent
firewall-config
```

В `firewall-config` включим службы `imap`, `pop3`, `smtp`.

Перезагрузим конфигурацию:

```bash
firewall-cmd --reload
```

---

## Ответы на контрольные вопросы

1. **Какая служба должна быть запущена перед началом работы с `firewall-config`?**  
   ```bash
   systemctl start firewalld
   ```

2. **Как добавить UDP-порт 2355 в брандмауэр?**  
   ```bash
   firewall-cmd --add-port=2355/udp --permanent
   ```

3. **Как показать конфигурацию брандмауэра во всех зонах?**  
   ```bash
   firewall-cmd --list-all-zones
   ```

4. **Как удалить службу `vnc-server` из конфигурации брандмауэра?**  
   ```bash
   firewall-cmd --remove-service=vnc-server
   ```

5. **Как активировать новую конфигурацию?**  
   ```bash
   firewall-cmd --reload
   ```

6. **Как проверить, что новая конфигурация активна?**  
   ```bash
   firewall-cmd --list-all --zone=<zone-name>
   ```

7. **Как добавить интерфейс `eno1` в зону `public`?**  
   ```bash
   firewall-cmd --zone=public --change-interface=eno1
   ```

8. **Если не указана зона, в какую зону будет добавлен интерфейс?**  
   В зону по умолчанию (`firewall-cmd --get-default-zone`).

---

## Вывод

В ходе выполнения лабораторной работы были получены навыки настройки пакетного фильтра в Linux.
