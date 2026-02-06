\# Codex Task: Fix TUN mode (YouTube video load)



Сравнить реализацию VPNService/TUN пайплайна в:

\- deps/v2rayNG

\- deps/AndroidLibXrayLite-Z-NET



Цель: в AndroidLibXrayLite-Z-NET добиться поведения как у v2rayNG:

\- TUN mode: чтобы работал ipv4 и ipv6 через приложение и работал впн

\- стабильный трафик в Wi-Fi и моб. сети



Фокус проверки:

\- MTU

\- DNS (утечки/Private DNS)

\- routes 0.0.0.0/0 и ::/0

\- UDP/QUIC

\- порядок старта: VPNService -> tun2socks -> xray (или наоборот, как в v2rayNG)

Артефакты:

\- список изменённых файлов

\- причина каждой правки

\- команды сборки



