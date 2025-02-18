---
## Front matter
lang: ru-RU
title: Лабораторная работа №13
subtitle: Фильтр пакетов
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
