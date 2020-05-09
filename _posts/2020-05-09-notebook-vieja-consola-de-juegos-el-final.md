---
layout: post
title: Notebook vieja → consola de juegos EL FINAL
date: 2020-05-09 14:52 -0300
---
La primera parte acá: [Notebook vieja → consola de juegos]({% post_url 2020-04-22-notebook-vieja-consola-de-juegos %}).

Yastá lista la consola de juegos. Tuve varias situaciones difíciles:

## 1. No podía utilizar la salida HDMI #1
La salida de HDMI conectada directa a la TV no producía ninguna salida. Tuve
que comprar [un adaptador de VGA a HDMI][1] para poder ver la salida
secundaria. Sabía que la había dejado siempre activa en la BIOS. La resolución
era pésima, pero al menos se veía la pantalla.

![VGA adapter](/assets/console/1.jpg)

![Output](/assets/console/2.jpg)

## 2. No pude ver la pantalla de booteo -> no podía ingresar a la BIOS.
La pantalla de booteo al parecer sólo aparecer en el display integrado, el cual
fue arrancado de cuajo, por lo tanto no hay salida, no hay BIOS que se pueda
ver. Por suerte mi yo del pasado, atento a mi gusto de reinstalar sistemas
operativos múltiples veces dejó el boot order en USB -> Disco 😎. Quemé el ISO
de Ubuntu en un USB y salió andando.

![Installing](/assets/console/3.jpg)

## 3. No podía utilizar la salida HDMI #2
Confiando que Ubuntu iba a identificar y activar la salida HDMI sin mucho
problema saqué el cable de la salida VGA y lo puse en el HDMI pero sin
novedades; no hay video y sin poder ver si sucede algo por la salida VGA estoy
ciego. Necesitaba otro cable HDMI. Luego de unos días llegó el cable, Ubuntu
efectivamente mostraba el menú en la salida VGA y pude configurar que las
pantallas sean mirror (HDMI como primario se lagueaba). Listo, chau VGA y
adaptador.

![Resolution](/assets/console/4.jpg)

## 3. No podía forzar el uso de la placa ATI con Steam.
Necesitaba esa placa ATI andando porque sólo se podían jugar jueguitos simil 8
bits. Ubuntu 20.04 ya tiene incluido los drivers para AMD Radeon HD 5450
(embbeded), sólo hay que decirle que use _esa placa_ para Steam.

```bash
xrandr --setprovideroffloadsink 0xd7 0x43
DRI_PRIME=1 /usr/games/steam %U
```

## 4. Steam Big Picture pierde el foco cuando los juegos se cierran.
Tener que tocar el teclado era algo que quería reducir al mínimo. Encontré que
[este bug existe desde hace un tiempo pero hay un posible workaround][2]. El
script que levanta Steam al iniciar el login quedó así:

```bash
xrandr --setprovideroffloadsink 0xd7 0x43
DRI_PRIME=1 SDL_VIDEO_MINIMIZE_ON_FOCUS_LOSS=0 /usr/games/steam %U
```

Y listo el pollo 👍.

![Hollow Night on Steam console](/assets/console/5.jpg)

[1]: https://www.youtube.com/watch?v=FUEMpHuF8Qc
[2]: https://github.com/ValveSoftware/steam-for-linux/issues/4611
