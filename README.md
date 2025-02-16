## MikroTik-HotSpot-Fail2Ban

# Защита HotSpot MikroTik от подбора паролей с Fail2Ban

1. Включаем отправку логов из микротик на целевое устройство где будет установлен Fail2Ban
  * /system loggin action add name=tosyslog target=remote remote=IP remote-port=514
  * /system loggin add action=remote topics=info,hotspot
2. 
