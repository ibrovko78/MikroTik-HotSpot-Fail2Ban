# MikroTik-HotSpot-Fail2Ban

## Защита HotSpot MikroTik от подбора паролей с Fail2Ban
- Настроить защиту хотспот микротик от подбора паролей очень легко, сейчас я вам расскажу как...

### 1. Включаем отправку логов из микротик на целевое устройство где будет установлен Fail2Ban
  * Замените IP адрес 192.168.1.2 целевого устройства сбора логов на свой
  * /system loggin action add name=remote target=remote remote=192.168.1.2 remote-port=514
  * /system loggin add action=remote topics=info,hotspot
### 2. Настраиваем rsyslog на прием логов с микротик
  * nano /etc/rsyslog.d/mikrotik.conf
  * наполнить файл содержимым
```js
if $fromhost-ip == '192.168.1.1' then /var/log/mikrotik.log
# Замените IP адрес 192.168.1.1 на IP своего MikroTik.
& stop
```
  
  * открываем файл rsyslog.conf, nano /etc/rsyslog.conf
  * Добавляем внего следующие строки для приема логов с микротик на порт 514
  * Или ищем аналогичные строки в rsyslog.conf и разкомментируем их
```js
module(load="imudp")
input(type="imudp" port="514")
```

  * Перезапускаем rsyslog, systemctl restart rsyslog
  * Необходимо немного подождать пока в лог начнут поступать данные
  * Проверяем что в лог пошли данные из микротик tail -f /var/log/mikrotik.log
  
  * Не забываем настроить logrotate для /var/log/mikrotik.log
  * nano /etc/logrotate.d/mikrotik
  * наполнить файл содержимым
```js
/var/log/mikrotik.log {
    rotate 0
    size=2M
    missingok
}
```
* перезапускаем logrotate, systemctl restart logrotate

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
* В моем примере username=password поэтому failregex будет именно такой
* При необходимости доработайте failregex под свои нужды заменив несколько слов в выражении.
* nano /etc/fail2ban/filter.d/mikrotik-hotspot.conf
* наполнить файл содержимым

```js
[Definition]
failregex = ^.*hotspot,info,debug.*\(<HOST>\): login failed: user <.*> not found.*$
ignoreregex =

## пример строки из лог файла, для которой составлено failregex
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

* Создадим два скрипта для actionban и actionunban, запускаемые при срабатываении фильтра
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
* делаем скирипты исполняемыми
* chmod +x /etc/fail2ban/action.d/mikrotik-ban.sh
* chmod +x /etc/fail2ban/action.d/mikrotik-unban.sh
* перезапускаем fail2ban, systemctl restart fail2ban

### 5. Добавляем правило в файрвол MikroTik для блокировки подборщиков из добавленного address-list
* /ip firewall raw add chain=prerouting action=drop log=no src-address-list=fail2ban comment="Drop fail2ban address-list" place-before=0




