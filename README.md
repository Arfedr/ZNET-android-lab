# ZNET Android Lab

Лаборатория для сравнения реализации VPN/TUN пайплайна (v2rayNG) и переноса/починки его в AndroidLibXrayLite-Z-NET.

## Состав
- deps/v2rayNG — референс (как должно работать)
- deps/AndroidLibXrayLite-Z-NET — цель (куда переносим фиксы)

## Как открыть в Android Studio
Открывать в двух окнах:
1) deps/v2rayNG
2) deps/AndroidLibXrayLite-Z-NET

Цель: полноценный VPN (TUN) через xray-core + tun2socks, чтобы работали все приложения.
