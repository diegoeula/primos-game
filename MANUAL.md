# 🦖 PRIMOS GAME v3 — Manual de funcionalidades

Referencia completa del juego: controles, mecánicas, mundos, enemigos, jefes, power-ups, tienda, persistencia y trucos. Para guía de desarrollo ver [CLAUDE.md](CLAUDE.md); para roadmap ver [PLAN.md](PLAN.md).

---

## 🎮 Cómo se juega

### Controles por defecto

| Acción | Jugador 1 | Jugador 2 | Touch (mobile) |
|--------|-----------|-----------|-----------------|
| Izquierda | `←` | `A` | Botón **◀** |
| Derecha | `→` | `D` | Botón **▶** |
| Saltar | `Espacio` | `W` | Botón **▲** |
| Golpe / disparo | `X` | `Shift` | Botón **✊** |
| Pausa | `ESC` | — | Botón **⏸** (arriba a la derecha) |
| Volver al menú | — | — | Botón **🏠** (arriba a la derecha) |

> Las teclas son re-asignables desde el menú principal → **⚙ Teclas** y se guardan en `localStorage` por jugador.

### Navegación de menús con teclado

Cuando hay un modal abierto:
- **↑ / ↓** mueven el foco entre botones
- **Enter** activa el botón
- **ESC** cierra modal o despausa

Al abrir cualquier modal, las teclas de movimiento se **resetean** para que Nacho no salga corriendo solo cuando volvés al juego.

---

## 🧍 Mecánica del jugador

Cosas que están afinadas para que sean "perdonadoras" con un chico de 5 años:

| Mecánica | Valor | Para qué |
|----------|-------|----------|
| Velocidad horizontal | `4.2` px/frame | base; el power-up Turbo la duplica |
| Fuerza de salto | `-12.5` vy | da unos ~120 px de altura desde el piso |
| Gravedad | `+0.65` vy / frame | con cap de caída en `16` |
| **Coyote time** | `120 ms` | podés saltar APENAS te caés de una plataforma |
| **Salto variable** | sí | si soltás el botón en el aire, el salto se corta antes |
| Vidas iniciales | `3` | corazones ❤️ en el HUD |
| Invulnerabilidad post-daño | sí | parpadeo breve después de recibir golpe |
| Hitbox normal | 32×44 px | reducida a 16×22 con el power-up Chiquito |

### Pixel art

Todo se dibuja con `ctx.fillRect` a coordenadas enteras (`Math.round`) y CSS `image-rendering: pixelated`. No hay sprites externos.

---

## 💰 Sistema de monedas

Hay **dos contadores** de monedas:

| Contador | Qué representa | Dónde se ve |
|----------|----------------|-------------|
| **Score del nivel** (🪙 X/Y) | Las que llevás recogidas en el nivel actual | HUD arriba a la izquierda |
| **Wallet** (💰 X) | Total acumulado entre todas las sesiones | HUD, menú principal, pausa, tienda |

Cada vez que recogés una moneda:
1. Sube el score del nivel (+1, o +2 si tenés el power-up Plata activo)
2. Sube el wallet la misma cantidad
3. Se guarda en `localStorage` (`nacho:wallet`)

El wallet **no se pierde** al perder vidas ni al cambiar de nivel. Solo se borra con `resetNachoTodo()` desde consola o con el botón "Reiniciar progreso" del menú.

---

## 🛒 Tienda

Acceso: **menú principal** → 🛒 Tienda, o desde la **pausa** → 🛒 Tienda.

| Item | Precio | Efecto |
|------|--------|--------|
| ⭐ Estrella | 30 💰 | Invencibilidad 6 segundos |
| 🍄 Hongo | 50 💰 | +1 vida extra |
| 🪶 Pluma | 20 💰 | Doble salto |
| 💨 Turbo | 15 💰 | Velocidad x1.9 durante 5 s |
| 🦎 Chiquito | 15 💰 | Hitbox reducida 8 s |
| 🛡️ Escudo | 25 💰 | Aguanta 1 golpe |

Comprar resta del wallet y suma 1 unidad al inventario. No hay límite de cantidad por item.

---

## 🎒 Inventario

Acceso: **pausa** → 🎒 Inventario.

Cada item comprado aparece en la lista con un botón **Usar**:
1. Al tocar **Usar**, el power-up se aplica al instante al Jugador 1 (o al primer player vivo si está en 2 jugadores)
2. Se consume 1 unidad del inventario
3. El modal se cierra y el juego se despausa solo para que el chico vea el efecto

El inventario persiste en `localStorage` (`nacho:inventory`), así que comprás cuando tenés monedas y usás cuando lo necesitás.

---

## 🎉 Sala bonus

En cada **nivel de jefe** (último nivel de cada mundo) hay una **puerta dorada** parpadeante cerca del spawn.

Al tocarla:
- Te teletransporta a una **sala secreta** con 25+ monedas y plataformas de madera
- Tenés **25 segundos** o hasta juntar todas (lo que pase primero)
- Las paredes invisibles te impiden caerte
- Al salir, volvés al nivel exactamente donde estabas, con todas las monedas sumadas al wallet

Niveles con sala bonus (16 en total): N3, N6, N8, N10, N11, N12, N13, N15, N17, N19, N21, N23, N25, N27, N29, N31, N33, N35.

---

## ⚡ Power-ups (todos)

Los power-ups que se **encuentran en los niveles** o se compran en la tienda.

### Universales (en varios mundos + tienda)
| Emoji | Tipo | Efecto |
|-------|------|--------|
| ⭐ | `star` | Invencible 6 s |
| 🍄 | `mushroom` / `biscuit` | +1 vida (cambia de visual según el mundo: 🍎 manzana, ⚽ pelota, ⭐ estrella de mar, 🥕 zanahoria, etc.) |
| 🪶 | `feather` | Doble salto |
| 💨 | `turbo` | Velocidad x1.9 (5 s) |
| 🦎 | `tiny` | Hitbox 16×22 (8 s) |
| 🛡️ | `shield` | Aguanta 1 golpe |
| 🛡️ | `shield_strong` | Aguanta 3 golpes de una (Mundo 8) |

### De movimiento
| Emoji | Tipo | Efecto | Mundo |
|-------|------|--------|-------|
| 🚀 | `super_jump` | Triple salto (20 s) | 11 Espacio |
| 🌀 | `teleport` | Tecla de golpe te teletransporta ~120 px adelante (15 s) | 12 Computadora |
| 💎 | `cuarto_salto` | 3 saltos aéreos (20 s) | 14 Jardín |
| 💎 | `cinco_saltos` | 4 saltos aéreos (20 s) | 14 Jardín |
| 💨 | `mega_rapido` | Velocidad x2.5 (5 s) | 14 Jardín |
| 🦘 | `mega_salto` | Próximo salto altísimo (1 uso) | 15 Pinocho |
| 🦘 | `super_pedo` | 3 saltos aéreos (20 s) | 15 Pinocho |
| 🌊 | `submarino` | Inmune a pingüinos del agua (15 s) | 16 Ave |
| 🦅 | `volar` | Vuelo libre tipo jetpack (8 s) | 16 Ave |

### De ataque (se disparan con la tecla de golpe ✊)
Solo uno activo a la vez; si tenés varios, el código aplica el primero en una cadena de prioridad.

| Emoji | Tipo | Proyectil | Daño | Mundo |
|-------|------|-----------|------|-------|
| 👊 | `punch` | Hitbox cuerpo a cuerpo | 1 | 6 Playa |
| ⚽ | `fireball` | Pelota de fuego | 1 | 8 Fútbol |
| 🚀 | `torpedo` | Torpedo submarino | 1 | 9 Marino |
| ⚡ | `lightning` | Rayo zigzag verde | 1 | 12 Computadora |
| 💨 | `fart` | Nube tóxica | 1 | 10 Perro |
| ⚡ | `electricidad` | Rayo zigzag amarillo | 1 | 13 Pokémon |
| 💧 | `chorro_agua` | Chorro horizontal | 1 | 13 Pokémon |
| 🔥 | `fuego_pkmn` | Bola de fuego chica | 1 | 13 Pokémon |
| 🪙 | `plata` | Las monedas valen x2 (15 s) | — | 13 Pokémon |
| ❄️ | `bola_nieve` | Bola de nieve | 1 | 14 Jardín |
| 🚀 | `torpedo_super` | Torpedo plateado (pierce, atraviesa enemigos) | 1 | 15 Pinocho |
| 🦅 | `picar` | Picotazo cuerpo a cuerpo | 1 | 16 Ave |
| ⛏️ | `excavar` | Tierra rápida | 1 | 17 Excavación / 18 Excavadora |
| 🟫 | `bola_tierra` | Bola de tierra grande (entierra) | **2** | 17 Excavación / 18 Excavadora |

---

## 👾 Enemigos comunes

| Emoji | Clase | Comportamiento | HP | Mundo |
|-------|-------|----------------|----|----|
| 🐢 | `Bowser` | Patrulla horizontal | 1 | 1 Jungla |
| 🐝 | `Bee` | Vuela en patrón flotante | 1 | 2 Desierto |
| 🦂 | `Scorpion` | Patrulla con pinzas | 2 | 2 Desierto |
| 🐧 | `Penguin` | Camina y se desliza | 1 | 3 Nieve |
| 🐻‍❄️ | `PolarBear` | Lento pero fuerte | 2 | 3 Nieve |
| 🐉 | `Dragon` (común) | Dispara fuego | 2 | 4 Castillo |
| 🐢 | `TurtleNinja` (común) | Lanza shurikens | 1 | 6 Playa |
| 🦑 | `Squidward` (común) | Camina y empuja | 1 | 9 Marino |
| 🦞 | `Krabby` (subtipo) | Tira cangrejos | 1 | 9 Marino |
| 🚗 | `Car` (hazard) | Auto que va y viene | — (no muere) | 7 Ciudad |
| 🐢 | `Squirtle` | Tira agua | 1 | 13 Pokémon |
| 🐢 | `Blastoise` | Versión grande de Squirtle | 2 | 13 Pokémon |
| 🎒 | `Mochila` | Camina | 1 | 14 Jardín |
| 🧒 | `NeneSaltando` | Salta sin parar | 1 | 14 Jardín |
| 🎠 | `CaballoJuguete` | Embiste recto | 1 | 15 Pinocho |
| 🦤 | `Avestruz` | Persigue al jugador si lo ve | 1 | 16 Ave |
| 🐧 | `PinguinoAgua` | Nada en zonas con agua | 1 | 16 Ave |
| 🌭 | `Salchicha` | Rueda bajita | 1 | 17 Excavación |
| 🐕 | `PerroSalchicha` | Cuerpo largo, persigue | 1 | 17 Excavación |
| 🦕 | `Dinosaurio` | Lento pero aguanta | 2 | 18 Excavadora |

> Los enemigos se eliminan: 1) saltando encima, 2) con un power-up de ataque, o 3) con el puñetazo cuerpo a cuerpo. La estrella te hace invencible y los volás al toque.

---

## 👑 Jefes

Cada jefe tiene una barra de HP visible arriba y un ataque telegrafiado. La meta del nivel queda **bloqueada** con un candado dorado hasta que lo vencés.

| Emoji | Jefe | Mundo | HP | Particularidad |
|-------|------|-------|----|----|
| 🐢 | Bowser gigante | 1 Jungla (N3) | 3 | versión más grande del enemigo común |
| 🦂 | Rey Escorpión | 2 Desierto (N6) | 4 | pinzas que persiguen |
| 🐧 | Pingüino Rey | 3 Nieve (N8) | 4 | tira rocas de hielo |
| 👑 | Mega Bowser | 4 Castillo (N10) | 5 | **2 fases** (al 50% tira fuego) |
| 🐉 | Dragón | 5 Manzanal (N11) | 4 | aliento de fuego |
| 🐢 | Tortuga Ninja Mala | 6 Playa (N12) | 4 | shurikens en abanico |
| 🤖 | Robot | 7 Ciudad (N13) | 5 | **weak points en cabeza y patas**; el torso te daña |
| 🥅 | Arquero de River | 8 Fútbol (N15) | 4 | tira pelota de fuego |
| 🧽 | Bob Esponja | 9 Marino (N17) | 4 | torpedos |
| 🐺 | Perro Gigante | 10 Plaza Perruna (N19) | 4 | embiste y tira galletas |
| 👽 | Alien Verde | 11 Espacio (N21) | 4 | láser |
| 🤖 | Robot Maligno | 12 Computadora (N23) | 5 | glitch propio (teletransporta) |
| 🐉 | Charizard | 13 Pokémon (N25) | 5 | cono de fuego |
| 👩‍🏫 | Maestra | 14 Jardín (N27) | 5 | tira crayones |
| 🤥 | Pinocho | 15 Pinocho (N29) | 4 | nariz extensible que dispara |
| 🦜 | Loro | 16 Ave (N31) | 5 | plumas en abanico |
| 🦫 | **Topo** | 17 Excavación (N33) | 4 | excava y reaparece en posiciones aleatorias, tira tierra apuntada |
| 🚜 | **Bulldozer** | 18 Excavadora (N35) | 5 | patrulla + tira piedras + **embiste cada 4 s** (chimenea echa humo de aviso) |

---

## 🌍 Mundos y niveles (35 niveles, 18 mundos)

| # | Nivel | Mundo | Notas |
|---|-------|-------|-------|
| 1 | Tutorial | 🌴 1 Jungla | base, sin jefe |
| 2 | Explorar | 🌴 1 Jungla | plataformas móviles |
| 3 | Bosque oscuro | 🌴 1 Jungla | 🔥 Bowser gigante |
| 4 | Arenales | 🏜️ 2 Desierto | abejas |
| 5 | Trampas | 🏜️ 2 Desierto | pinchos / peligro |
| 6 | Oasis maldito | 🏜️ 2 Desierto | 🦂 Rey Escorpión |
| 7 | Hielo | ❄️ 3 Nieve | piso resbaloso + pingüinos / osos |
| 8 | Cumbre | ❄️ 3 Nieve | 🐧 Pingüino Rey |
| 9 | Puente | 🏰 4 Castillo | dragones + lava |
| 10 | Trono | 🏰 4 Castillo | 👑 Mega Bowser (2 fases) |
| 11 | Selva de Manzanos | 🍎 5 Manzanal | copas de árboles + puerta secreta original + 🐉 Dragón |
| 12 | Costa Peligrosa | 🏖️ 6 Playa | plataformas flotantes + 🐢 Tortuga Ninja |
| 13 | Metrópolis | 🏙️ 7 Ciudad | autos en movimiento + 🤖 Robot |
| 14 | La Bombonera | ⚽ 8 Fútbol | cancha + tribunas |
| 15 | Penal Final | ⚽ 8 Fútbol | 🥅 Arquero de River |
| 16 | Coral Profundo | 🌊 9 Marino | algas + calamares |
| 17 | Casa Piña | 🌊 9 Marino | 🧽 Bob Esponja |
| 18 | Plaza Tranquila | 🐕 10 Plaza Perruna | bancos + faroles |
| 19 | Furia Perruna | 🐕 10 Plaza Perruna | 🐺 Perro Gigante |
| 20 | Cinturón de Asteroides | 🚀 11 Espacio | gravedad x0.6 |
| 21 | Nave Invasora | 🚀 11 Espacio | 👽 Alien Verde |
| 22 | Bug en la RAM | 💻 12 Computadora | campo eléctrico |
| 23 | El Kernel | 💻 12 Computadora | 🤖 Robot Maligno |
| 24 | Pueblo Paleta | 🔴 13 Pokémon | bosque + Squirtles |
| 25 | Cueva Charizard | 🔴 13 Pokémon | 🐉 Charizard |
| 26 | Recreo Loco | 🎒 14 Jardín | mochilas + nenes |
| 27 | La Seño Furiosa | 🎒 14 Jardín | 👩‍🏫 Maestra |
| 28 | Taller de Juguetes | 🤥 15 Pinocho | caballitos de madera |
| 29 | Las Mentiras de Pinocho | 🤥 15 Pinocho | 🤥 Pinocho |
| 30 | Nido en el Árbol | 🦜 16 Ave | árbol + agua nadable |
| 31 | Cielo del Loro | 🦜 16 Ave | 🦜 Loro |
| 32 | Mina de Salchichas | ⛏️ 17 Excavación | salchichas + perros salchicha |
| 33 | La Madriguera del Topo | ⛏️ 17 Excavación | 🦫 Topo |
| 34 | Sitio de Construcción | 🚜 18 Excavadora | dinosaurios |
| 35 | El Bulldozer | 🚜 18 Excavadora | 🚜 Bulldozer |

---

## 👥 Modo 2 jugadores

Desde el menú: **👥 2 Jugadores**.

- Cámara compartida (auto-clamp si se separan más de 700 px)
- Cada jugador con vidas separadas (❤️ J1 / 💙 J2 en el HUD)
- Cuando uno muere, queda "eliminado"; el otro sigue solo
- Pueden rebotar uno sobre el otro
- Cada jugador usa sus propias teclas configurables

---

## ⏸ Pausa

Acceso: tecla **ESC** o botón **⏸** arriba a la derecha.

El modal de pausa tiene:
- **▶ Continuar** — vuelve al juego
- **🛒 Tienda** — abre la tienda (sin perder progreso)
- **🎒 Inventario** — usar power-ups guardados
- **🗺️ Cambiar nivel** — abre el selector de niveles
- **🏠 Menú principal** — salir al menú (perdés el progreso del nivel actual)

El juego se pausa real (timers, enemigos, proyectiles) — no se mueve nada.

---

## 🗺️ Selector de niveles

Acceso: **menú principal** → 🗺️ Niveles, o **pausa** → 🗺️ Cambiar nivel.

Muestra los 35 niveles agrupados por mundo, **todos abiertos**. Al elegir uno, se carga directo.

> El sistema de "máximo nivel desbloqueado" (`nacho:maxLevelUnlocked`) sigue funcionando para el modo de juego lineal, pero el selector ignora ese límite.

---

## ⚙ Configuración

Todo en el menú principal:

### 🧒 Personaje
- **Nombre** (input, máx 12 caracteres, default "Nacho")
- **Gorra** (botones de color)
- **Camisa** (botones de color)
- **Pantalón** (botones de color)
- Preview del personaje en canvas chico arriba

### ⚙ Teclas
Modal con tabla: cada jugador × cada acción (←, →, salto, ✊). Tocás un botón, apretás la tecla nueva, listo. Botón **🔄 Restablecer** vuelve a las defaults.

### 🚀 Velocidad
4 presets del multiplicador global de timing:

| Botón | Multiplicador |
|-------|---------------|
| 🐌 Muy Lento | x0.4 |
| 🐢 Lento | x0.6 |
| 🚶 Normal | x1.0 |
| 🚀 Rápido | x1.4 |

> Implementado con un acumulador de ticks en el `gameLoop`. Escala parejo TODO: timers, físicas, enemigos, proyectiles, partículas, plataformas móviles. A 0.4x puede haber leve stutter visual (es esperado, no es bug).

### 🏆 Ranking
Top 10 local por (score + nivel × 100). Persistido en `localStorage`. Se actualiza solo al ganar un nivel.

### 🔄 Reiniciar progreso
Borra TODO: progreso, perfil, teclas, ranking, velocidad, wallet, inventario.

---

## 💾 Persistencia (localStorage)

Todas las claves usan el prefijo `nacho:` (legacy del nombre original del juego; no se migran para no perder el progreso de quienes ya jugaron).

| Clave | Qué guarda |
|-------|-----------|
| `nacho:maxLevelUnlocked` | Nivel máximo alcanzado (modo lineal) |
| `nacho:profile` | Nombre + colores del personaje |
| `nacho:keybinds` | Teclas re-asignadas por jugador |
| `nacho:ranking` | Top 10 de scores |
| `nacho:gameSpeed` | Multiplicador 0.4 / 0.6 / 1.0 / 1.4 |
| `nacho:wallet` | Monedas acumuladas |
| `nacho:inventory` | Power-ups comprados (`{type: count}`) |

---

## 🛠️ Trucos de consola (F12 → Console)

| Comando | Qué hace |
|---------|----------|
| `resetNacho()` | Borra solo el progreso de niveles + ranking |
| `resetNachoTodo()` | Borra TODO (perfil, teclas, ranking, progreso, velocidad, wallet, inventario) |

Cualquiera de los dos recarga la página automáticamente al ejecutarse.

---

## 🧠 Notas técnicas

- **Un solo archivo**: todo (HTML + CSS + JS) vive en `index.html`. Cero dependencias, sin build step. Doble-click en el archivo abre el juego.
- **Renderer**: Canvas 2D API. Coordenadas enteras + `image-rendering: pixelated` para look de pixel art.
- **Loop**: `requestAnimationFrame` con split en `gameUpdate(dt)` + `gameDraw()`. Update se llama N veces por frame según la velocidad elegida.
- **Detección de colisiones**: AABB con separación de ejes (primero X, después Y). Evita "clavarse" en esquinas.
- **Inputs**: keyboard via `keydown/keyup`, touch via los botones flotantes abajo (auto-detect en móvil/tablet).
- **Deploy**: Vercel auto-deploy en cada push a `main`. Repo `github.com/diegoeula/primos-game`.

Para reglas no negociables del stack ver [CLAUDE.md](CLAUDE.md).
