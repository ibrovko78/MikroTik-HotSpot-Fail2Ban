## MikroTik-HotSpot-Fail2Ban

# Защита HotSpot MikroTik от подбора паролей с Fail2Ban
- Настроить защиту хотспот микротик от подбора паролей очень легко, сейчас я вам расскажу как...

1. Включаем отправку логов из микротик на целевое устройство где будет установлен Fail2Ban
  * /system loggin action add name=tosyslog target=remote remote=IP remote-port=514
  * /system loggin add action=remote topics=info,hotspot
2. Устанавливаем Fail2Ban apt install fail2Ban (пакетный менеджер в зависмости от вашей ОС Linux)
