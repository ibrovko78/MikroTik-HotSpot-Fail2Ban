# MikroTik-HotSpot-Fail2Ban

## Защита HotSpot MikroTik от подбора паролей с Fail2Ban
- Настроить защиту хотспот микротик от подбора паролей очень легко, сейчас я вам расскажу как...

### 1. Включаем отправку логов из микротик на целевое устройство где будет установлен Fail2Ban
  * /system loggin action add name=tosyslog target=remote remote=IP remote-port=514
  * /system loggin add action=remote topics=info,hotspot
### 2. Настраиваем rsyslog на прием логов с микротик
  * nano /etc/rsyslog.d/mikrotik.conf
  * наполнить mikrotik.conf содержимым
[![copy](https://cdn.jsdelivr.net/gh/lalit2005/copii@master/assets/copy-below-code.svg)]("<https://copii.vercel.app/?ct=const%20stripe%20¬Ωœ%20require('stripe')(process.env.STRIPE_API_KEY);¬ß≈¬ß≈export%20default%20async%20(req,%20res)%20¬Ωœ>%20{¬ß≈%20%20const%20session%20¬Ωœ%20await%20stripe.checkout.sessions.create({¬ß≈%20%20%20%20payment_method_types:%20['card'],¬ß≈%20%20%20%20line_items:%20[¬ß≈%20%20%20%20%20%20{¬ß≈%20%20%20%20%20%20%20%20name:%20'Serverless%20Functions%20–%20The%20Complete%20Guide',¬ß≈%20%20%20%20%20%20%20%20description:%20'100%20page%20e-book%20on%20serverless%20functions.',¬ß≈%20%20%20%20%20%20%20%20images:%20['https://site.com/image.png'],¬ß≈%20%20%20%20%20%20%20%20amount:%20'5000',%20//%20Cents¬ß≈%20%20%20%20%20%20%20%20currency:%20'usd',¬ß≈%20%20%20%20%20%20%20%20quantity:%201¬ß≈%20%20%20%20%20%20}¬ß≈%20%20%20%20],¬ß≈%20%20%20%20success_url:%20'https://site.com/success?session_id¬Ωœ{CHECKOUT_SESSION_ID}',¬ß≈%20%20%20%20cancel_url:%20'https://site.com'¬ß≈%20%20});¬ß≈¬ß≈%20%20return%20res.status(200).json(session);¬ß≈};¬ß≈&tm=blk&lg=https://cdn.statically.io/gh/leerob/leerob.io/main/public/logos/stripe.png>")
```js
if $fromhost-ip == '192.168.1.1' then /var/log/mikrotik.log
#Replace 192.168.1.1 with the IP address of your MikroTik router.
& stop
```
3. Устанавливаем Fail2Ban, для DEBIAN apt install fail2Ban, (используйте пакетный менеджер в зависмости от вашей ОС Linux)
4.
