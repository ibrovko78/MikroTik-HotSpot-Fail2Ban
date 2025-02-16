# MikroTik-HotSpot-Fail2Ban

## Защита HotSpot MikroTik от подбора паролей с Fail2Ban
- Настроить защиту хотспот микротик от подбора паролей очень легко, сейчас я вам расскажу как...

### 1. Включаем отправку логов из микротик на целевое устройство где будет установлен Fail2Ban
  * Замените IP адрес 192.168.1.2 целевого устройства сбора логов на свой
  * /system loggin action add name=tosyslog target=remote remote=192.168.1.2 remote-port=514
  * /system loggin add action=remote topics=info,hotspot
### 2. Настраиваем rsyslog на прием логов с микротик
  * nano /etc/rsyslog.d/mikrotik.conf
  * наполнить файл содержимым

```js
if $fromhost-ip == '192.168.1.1' then /var/log/mikrotik.log
# Замените IP адрес 192.168.1.1 на IP своего MikroTik.
& stop
```
  * Перезапускаем rsyslog, systemctl restart rsyslog
  * Проверяем что в лог пошли данные из микротик cat /var/log/mikrotik.log

### 3. Устанавливаем Fail2Ban и sshpass 
### 4. Настраиваем Fail2Ban
  * nano /etc/fail2ban/jail.local
  * наполнить файл содержимым

```js
# Do all your modifications to the jail's configuration in jail.local!
[mikrotik-hotspot]
enabled  = true
filter   = mikrotik-hotspot
action   = mikrotik
logpath  = /var/log/mikrotik.log
maxretry = 3
bantime  = 15m
findtime = 600
```
* Создадим фильтр mikrotik-hotspot для Fail2Ban
* nano /etc/fail2ban/filter.d/mikrotik-hotspot.conf
* наполнить файл содержимым

```js
[Definition]
failregex = ^.*hotspot,info,debug.*\(<HOST>\): login failed: user <.*> not found.*$
ignoreregex =

## Feb/09/2025 13:26:32 hotspot,info,debug 989898 (172.23.139.178): login failed: user <989898> not found
```
* Создадим правило действия при срабатывании фильтра
* nano /etc/fail2ban/action.d/mikrotik.conf
* наполнить файл содержимым

```js
[Definition]
actionstart =
actionstop =
actioncheck =
actionban = /etc/fail2ban/action.d/mikrotik-ban.sh <ip>
actionunban = /etc/fail2ban/action.d/mikrotik-unban.sh <ip>
```
* Создадим два скрипта для actionban и actionunban, запускаемые при срабатываении действия
* nano /etc/fail2ban/action.d/mikrotik-ban.sh
* Замените 192.168.1.1 на IP адрес вашего микротик
* наполнить файл содержимым
```js
#!/bin/bash
IP=$1
USER="admin"
PASS="пароль микротик"
ROUTER_IP="192.168.1.1"

sshpass -p "$PASS" ssh -o StrictHostKeyChecking=no $USER@$ROUTER_IP "/ip firewall address-list add address=$IP list=fail2ban"
```
* nano /etc/fail2ban/action.d/mikrotik-unban.sh
* Замените 192.168.1.1 на IP адрес вашего микротик
* наполнить файл содержимым
```js
#!/bin/bash
IP=$1
USER="admin"
PASS="пароль микротик"
ROUTER_IP="192.168.1.1"

sshpass -p "$PASS" ssh -o StrictHostKeyChecking=no $USER@$ROUTER_IP "/ip firewall address-list remove [find address=$IP list=fail2ban]"
```
