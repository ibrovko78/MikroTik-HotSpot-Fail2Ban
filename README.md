## MikroTik-HotSpot-Fail2Ban

# Защита HotSpot MikroTik от подбора паролей с Fail2Ban
- Настроить защиту хотспот микротик от подбора паролей очень легко, сейчас я вам расскажу как...

1. Включаем отправку логов из микротик на целевое устройство где будет установлен Fail2Ban
  * /system loggin action add name=tosyslog target=remote remote=IP remote-port=514
  * /system loggin add action=remote topics=info,hotspot
2. Настраиваем rsyslog на прием логов с микротик
  * nano /etc/rsyslog.d/mikrotik.conf
  * наполнить mikrotik.conf содержимым
    if $fromhost-ip == '192.168.1.1' then /var/log/mikrotik.log
    `# Replace 192.168.1.1 with the IP address of your MikroTik router.`
    & stop
  * 
3. Устанавливаем Fail2Ban, для DEBIAN apt install fail2Ban, (используйте пакетный менеджер в зависмости от вашей ОС Linux)
4.
