---
layout: post
title:  "Desarrollando con VS Code y SSH en la Raspberry Pi"
date:   2020-02-25 00:00:00 -0600
categories: development
tags: raspberrypi vscode
---

Hace un par de semanas compré el modelo más reciente de Raspberry Pi, estaba muy interesado en comprobar su rendimiento; sus mejoras en procesador y memoria hacen que muchas personas la propongan como un reemplazo de escritorio viable. Opté por la versión con 4gb de RAM y empecé a instalar paquetes de software de productividad y desarrollo, la verdad quedé gratamente sorprendido con el desempeño de esta nueva versión; sin embargo, no todo fue precisamente como lo que yo esperaba.

![RPI](/assets/img/rpi-vnc.png)

Normalmente mi rpi está en mi escritorio y me conecto por medio de VNC para trabajar en ella desde alguna otra computadora, utilizando este esquema me fue posible instalar y utilizar sin problemas paquetes como [Ultimaker Cura](https://ultimaker.com/software/ultimaker-cura), sin embargo, al hacer comparaciones de rendimiento directamente en la rpi vs utilizando acceso remoto en VNC, era claro que usarla directamente era mucho más rápido y fluido. Un poco resignado a que siempre iba a tener que iba a tener que pagar esa penalidad en rendimiento cuando quisiera hacer trabajar de forma remota, decidí poner en pausa mis experimentos para dedicarme de lleno a un proyecto que debo trabajar muy pronto con uno de mis clientes.

Ahora que mi proyecto está prácticamente terminado, me doy cuenta que es algo que puede servir a varias personas y debería compartirlo en un blog; una excelente excusa para empezar a escribir nuevamente y revivir mi antiguo sitio, después de revisar opciones decidí que [Github Pages](https://pages.github.com/) era buena opción, al leer un poco más me di cuenta que para sacar ventaja completa de Github Pages era indispensable que aprendiera a usar [jekyll](https://jekyllrb.com/) y que si quería que mis *commits* tuvieran la [verificación de firma](https://help.github.com/es/github/authenticating-to-github/about-commit-signature-verification) iba a necesitar toda una serie de herramientas que no tenía en mi estación de trabajo Windows.

Si bien era posible usar [WSL](https://docs.microsoft.com/en-us/windows/wsl/install-win10) no quería desaprovechar la excusa para ponerme a probar cosas nuevas con mi rpi. Lo primero que necesitaba hacer era permitir la interfaz SSH, lo cual es extremadamente fácil gracias a la herramienta de configuración:

![rpi-config](/assets/img/rpi-config.png)

Luego, siguiendo esta [guía](https://code.visualstudio.com/docs/remote/ssh), en un par de horas pude fácilmente:
- Conectar el VS Code en mi laptop Windows al Raspbian de mi rpi.
- Instalar ruby, bundler y jekyll desde la terminal de VS Code
- Mapear el puerto 4000 para verificar mis cambios
- Usar las herramientas en de línea de comandos desde la terminal
- Desarrollar forma remota con un rendimiento prácticamente idéntico al de hacerlo localmente

Y acá me encuentro, escribiendo este mismo post, desde mi laptop a través de SSH directamente en mi rpi!

![code-ssh](/assets/img/code-ssh.png)

Me sorprendió la facilidad y el nivel de integración, VS Code es una herramienta muy poderosa y estoy usándola cada vez más, no se pierdan los próximos artículos donde les estaré mostrando muchos escenarios de desarrollo a punta de VS Code.

Hasta la próxima!