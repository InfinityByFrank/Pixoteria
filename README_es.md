# 🎴 Pixotería — Lotería mexicana multijugador para iOS y Android

🌐 [English](README.md) · **Español**

Una lotería mexicana que se juega como en la mesa: varias personas, cada quien con su teléfono, en la misma red WiFi. Un teléfono crea la partida, los demás la encuentran solos y se unen. **Sin cuentas, sin internet, sin servidores.**

<p align="center">
  <img src="assets/1-menu.png" width="200" alt="Menú principal" />
  <img src="assets/2-sala-de-espera.png" width="200" alt="Sala de espera con selección de tabla" />
  <img src="assets/3-mazo-muertos.png" width="200" alt="Baraja Día de Muertos" />
</p>

---

## 🎯 Sobre el proyecto

Quería una lotería que se jugara como la de verdad, lo que plantea tres problemas a la vez:

1. Que los teléfonos se **encuentren solos** en la red.
2. Que el juego esté **sincronizado** sin una nube que lo arbitre.
3. Que se vea y suene como lotería, con **identidad propia**, no como una plantilla.

> **Periodo:** agosto – octubre 2026 · **Plataformas:** iOS y Android (una sola base de código) · diseñada, desarrollada, ilustrada y preparada para publicar de principio a fin.

## ✨ Funciones

| | |
|---|---|
| 🧑‍🤝‍🧑 **Multijugador local** | El anfitrión crea la partida y los jugadores aparecen solos en la sala de espera |
| 🃏 **9 barajas × 54 cartas** | Tradicional, Día de Muertos, Terror, Perrault, Grimm, Carroll, Piratas, Griego, Cyberpunk y Nórdico — 486 cartas ilustradas en pixel art propio |
| 🏆 **3 patrones de victoria** | Tabla llena, línea, esquinas |
| 🔊 **Voz** | La app canta las cartas en voz alta |
| 🌍 **ES / EN** y escala de fuente accesible | |
| 🔒 **Privada por diseño** | Cero cuentas, anuncios y datos recopilados en la versión 1 |

## 🖼️ Galería

<p align="center">
  <img src="assets/4-mazo-terror.png" width="200" alt="Baraja Terror" />
  <img src="assets/5-mazo-carroll.png" width="200" alt="Baraja Carroll" />
  <img src="assets/6-mazo-grimm.png" width="200" alt="Baraja Grimm" />
</p>

## 🛠️ Tecnologías

| Tecnología | Para qué |
|---|---|
| Expo / React Native (TypeScript) | App multiplataforma |
| expo-router | Navegación |
| Zustand | Estado |
| i18next | Traducciones ES / EN (sin cadenas sueltas) |
| TCP en red local + mDNS/Bonjour | Multijugador y descubrimiento automático |
| Astro · Cloudflare | Sitio web de lanzamiento estático |

## 🧠 Decisiones técnicas clave

### Arquitectura decidida antes de escribir código
Documenté las decisiones y sus descartes en un `plan.md` desde el primer día.

- **WiFi local + TCP y no Bluetooth.** BLE está hecho para conectar un accesorio, no para sincronizar estado entre 3 o 4 dispositivos iguales. Lo descarté tras investigarlo, con una tabla comparativa de tres opciones (WiFi local, BLE, WiFi Direct/Multipeer).
- **Anfitrión autoritativo:** el host tiene el mazo, el historial y las tablas; los clientes solo pintan. Más simple y evita discrepancias, a cambio de que si el host cae la partida termina — lo dejé escrito como limitación aceptada.
- **Riesgo más grave identificado temprano:** el aislamiento de clientes en algunas redes WiFi, por eso la **Fase 0 fue una prueba de concepto** para validar lo más arriesgado primero.

### La red, de cerca
- **Descubrimiento automático con mDNS/Bonjour:** el host anuncia la partida y los clientes la ven en una lista.
- **Protocolo propio sobre TCP**, con encuadre de mensajes, versión y topes de tamaño y tiempo.
- **Separación de responsabilidades:** la capa de sockets no sabe nada del juego y la lógica del host no sabe nada de sockets, lo que permite probar la mesa con conexiones falsas.
- **Defensas del host** contra lo que llegue por el socket: validación de entrada, cupo, rechazos.
- **Condición de carrera en "¡lotería!"** si dos jugadores reclaman a la vez: el host decide el orden.
- **Bug real encontrado en dispositivo:** `expo-network` puede devolver una IP incorrecta en iOS.

### Un juego con identidad propia
- **Sistema de diseño con tokens:** ningún color, espaciado ni tipografía escritos a mano; tres temas y escala de fuente accesible.
- **Giro de identidad:** rediseño a pixel art propio después de probar con packs de arte ajeno.
- **Kit de interfaz** con marcos de 9 cortes, escalado y paleta, y una guía de los dos bugs de render que más tiempo me costaron, para no repetirlos.

### Calidad
Pruebas automatizadas del protocolo, la mesa, los patrones de victoria, el catálogo y el acceso a la tienda. Lo que se puede verificar sin dos teléfonos corre en CI; lo demás se prueba en dispositivos reales. Las reglas de arquitectura están escritas (qué carpeta depende de cuál).

### Llegar a las tiendas sin que te rechacen
Lanzamiento en tres fases — básica → anuncios → compras — para que cada fase agregue una sola declaración ante Apple y Google, en vez de defender tres en la primera revisión. Versión 1: tres barajas gratis, sin anuncios, sin compras y sin datos recopilados: cada casilla del formulario de privacidad dice "no" y es verdad. Quité las librerías de anuncios en lugar de apagarlas y bloqueé tres permisos de Android que ninguna función usaba. Política de privacidad pública en español e inglés.

## 🌐 Sitio web de lanzamiento

Sitio estático en **Astro** desplegado en **Cloudflare**, con tipografía pixel propia y sin frameworks de interfaz: galería de barajas, capturas reales, política de privacidad y soporte.

<p align="center">
  <img src="assets/landing-hero.png" width="460" alt="Portada del sitio" />
  <img src="assets/landing-capturas.png" width="460" alt="Sección de capturas del sitio" />
</p>

## 🌱 Qué aprendí / qué mejoraría

- Validar el riesgo más grande primero ahorra semanas; documentar por qué se descartó algo vale tanto como lo que se hizo; publicar en tiendas es un proyecto dentro del proyecto.
- Pendiente: migración de host y reconexión automática (hoy, si el host cae, la partida termina), conexión sin WiFi compartido (Multipeer / WiFi Direct) y los lanzamientos 2 y 3 (anuncios y compra de barajas).

## 📄 Nota

Este repositorio contiene **solo documentación**; el código fuente es privado. Con gusto lo recorro en una entrevista.

## 📬 Contacto

**Infinity dev por Frank** · [GitHub](https://github.com/TheGeistGamer) · [Infinity](https://infinitydev.agency)
