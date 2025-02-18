---
## Front matter
lang: ru-RU
title: Лабораторная работа №12
subtitle: Настройки сети в Linux
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

Целью данной работы является получение навыков настройки сетевых параметров системы.

---

## Выполнение работы

### Проверка конфигурации сети

Получим полномочия администратора:

```bash
su -
```

Выведем информацию о существующих сетевых подключениях и статистику отправленных пакетов:

```bash
ip -s link
```

Проверим текущие маршруты:

```bash
ip route show
```

Выведем текущие назначения адресов для сетевых интерфейсов:

```bash
ip addr show
```

Проверим подключение к Интернету, отправив 4 пакета на IP-адрес `8.8.8.8`:

```bash
ping -c 4 8.8.8.8
```

Добавим дополнительный адрес к интерфейсу:

```bash
ip addr add 10.0.0.10/24 dev enp0s3
```

Проверим, что адрес добавился:

```bash
ip addr show
```

Выведем список всех прослушиваемых портов:

```bash
ss -tul
```

---

### Управление сетевыми подключениями с помощью `nmcli`

Выведем информацию о текущих соединениях:

```bash
nmcli connection show
```

Добавим Ethernet-соединение с именем `dhcp`:

```bash
nmcli connection add con-name "dhcp" type ethernet ifname enp0s3
```

Добавим Ethernet-соединение `static` с статическим IPv4-адресом и шлюзом:

```bash
nmcli connection add con-name "static" type ethernet ip4 10.0.0.10/24 gw4 10.0.0.1 ifname enp0s3
```

Переключимся на статическое соединение:

```bash
nmcli connection up "static"
```

Проверим успешность переключения:

```bash
nmcli connection show
ip addr
```

Вернемся к `dhcp` соединению:

```bash
nmcli connection up "dhcp"
```

---

### Изменение параметров соединения

Отключим автоподключение статического соединения:

```bash
nmcli connection modify "static" connection.autoconnect no
```

Добавим DNS-сервер в статическое соединение:

```bash
nmcli connection modify "static" ipv4.dns 10.0.0.10
nmcli connection modify "static" +ipv4.dns 8.8.8.8
```

Изменим IP-адрес статического соединения:

```bash
nmcli connection modify "static" ipv4.addresses 10.0.0.20/24
nmcli connection modify "static" +ipv4.addresses 10.20.30.40/16
```

Активируем соединение:

```bash
nmcli connection up "static"
```

Посмотрим настройки сети в `nmtui`:

```bash
nmtui
```

Переключимся на первоначальное соединение:

```bash
nmcli connection up "enp0s3"
```

---

## Ответы на контрольные вопросы

1. **Какая команда отображает только статус соединения, но не IP-адрес?**  
   ```bash
   ip link
   ```

2. **Какая служба управляет сетью в ОС типа RHEL?**  
   ```bash
   NetworkManager
   ```

3. **Какой файл содержит имя узла в ОС типа RHEL?**  
   - `/etc/hosts` – список всех хостов  
   - `/etc/hostname` – имя хоста локального устройства  

4. **Какая команда позволяет задать имя узла?**  
   ```bash
   hostnamectl set-hostname <новое имя>
   ```

5. **Какой конфигурационный файл отвечает за разрешение имен?**  
   `/etc/hosts`

6. **Какая команда показывает текущую конфигурацию маршрутизации?**  
   ```bash
   ip route show
   ```

7. **Как проверить статус службы NetworkManager?**  
   ```bash
   systemctl status NetworkManager
   ```

8. **Как изменить текущий IP-адрес и шлюз по умолчанию?**  
   ```bash
   nmcli con mod <имя соединения> ipv4.addresses "<текущий ip>,<новый ip>" gw4 <новый ip>
   ```

---

## Вывод

В ходе выполнения лабораторной работы были получены навыки настройки сетевых параметров системы.
