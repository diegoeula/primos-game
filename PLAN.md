# PLAN — Niveles, mundos, power-ups, features

Checklist vivo. Ver [CLAUDE.md](CLAUDE.md) para reglas no negociables del stack.

---

## Arquitectura (pre-requisito de todo)

- [x] Array `LEVELS = [...]` con configs por nivel
- [x] Cada nivel: `{ theme, platforms, coins, enemies, goal, boss?, powerups? }`
- [x] `createLevel(index)` carga el config
- [x] `game.currentLevel` + al ganar, avanza al siguiente
- [x] `localStorage` para recordar progreso (clave `nacho:maxLevelUnlocked`, reset con `resetNacho()` en consola)

---

## Niveles / Mundos

| # | Nivel | Mundo | Mecánica nueva | Jefe |
|---|-------|-------|----------------|------|
| 1 | Tutorial | 🌴 1. Jungla | — (base actual) | |
| 2 | Explorar | 🌴 1. Jungla | plataformas móviles | |
| 3 | Bosque oscuro | 🌴 1. Jungla | | 🔥 Bowser gigante |
| 4 | Arenales | 🏜️ 2. Desierto | 🐝 abejas picadoras | |
| 5 | Trampas | 🏜️ 2. Desierto | pinchos / peligro | |
| 6 | Oasis maldito | 🏜️ 2. Desierto | | 🦂 **Rey Escorpión** |
| 7 | Hielo | ❄️ 3. Nieve | piso resbaloso + 🐧 pingüinos y 🐻‍❄️ osos polares | |
| 8 | Cumbre | ❄️ 3. Nieve | | 🐧 **Pingüino Rey** |
| 9 | Puente | 🏰 4. Castillo | mix + dragones + lava | |
| 10 | Trono | 🏰 4. Castillo | | 👑 **Bowser Mega Gigante** (2 fases) |
| 11 | Selva de Manzanos | 🍎 5. Manzanal | copas de árboles (caer = muerte) + 🚪 puerta secreta | 🐉 **Dragón** (con fuego) |
| 12 | Costa Peligrosa | 🏖️ 6. Playa | plataformas flotantes + power-up puñetazo | 🐢 **Tortuga Ninja Mala** |
| 13 | Metrópolis | 🏙️ 7. Ciudad | autos en movimiento + edificios | 🤖 **Robot** (tira autos + lanzallamas, débil en cabeza y piernas) |
| 14 | La Bombonera | ⚽ 8. Fútbol | cancha + tribunas | |
| 15 | Penal Final | ⚽ 8. Fútbol | | 🥅 **Arquero de River** |
| 16 | Coral Profundo | 🌊 9. Marino | algas + Calamardos | |
| 17 | Casa Piña | 🌊 9. Marino | | 🧽 **Bob Esponja** |
| 18 | Plaza Tranquila | 🐕 10. Plaza Perruna | bancos + faroles | |
| 19 | Furia Perruna | 🐕 10. Plaza Perruna | | 🐺 **Perro Gigante** |
| 20 | Cinturón de Asteroides | 🚀 11. Espacio | gravedad x0.6 + asteroides | |
| 21 | Nave Invasora | 🚀 11. Espacio | | 👽 **Alien Verde** |
| 22 | Bug en la RAM | 💻 12. Computadora | pixel + campo eléctrico | |
| 23 | El Kernel | 💻 12. Computadora | | 🤖 **Robot Maligno** (glitch propio) |
| 24 | Pueblo Paleta | 🔴 13. Pokémon | bosque + Squirtles + Blastoise | |
| 25 | Cueva Charizard | 🔴 13. Pokémon | | 🐉 **Charizard** (cono de fuego) |
| 26 | Recreo Loco | 🎒 14. Jardín | bloques juguete + mochilas + nenes saltando | |
| 27 | La Seño Furiosa | 🎒 14. Jardín | | 👩‍🏫 **Maestra** (tira crayones) |
| 28 | Taller de Juguetes | 🤥 15. Pinocho | madera vieja + caballitos | |
| 29 | Las Mentiras de Pinocho | 🤥 15. Pinocho | | 🤥 **Pinocho** (nariz extensible) |
| 30 | Nido en el Árbol | 🦜 16. Ave | árbol + agua nadable + pingüinos + avestruces | |
| 31 | Cielo del Loro | 🦜 16. Ave | plataformas de nube | 🦜 **Loro** (plumas en abanico) |
| 32 | Mina de Salchichas | ⛏️ 17. Excavación | cueva + 🌭 salchichas + 🐕 perros salchicha | |
| 33 | La Madriguera | ⛏️ 17. Excavación | | 🦫 **Topo** (sale de la tierra, tira tierra) |
| 34 | Sitio de Construcción | 🚜 18. Excavadora | obra + 🦕 dinosaurios | |
| 35 | El Bulldozer | 🚜 18. Excavadora | | 🚜 **Bulldozer** (embiste + tira piedras) |

---

## Power-ups

- [x] ⭐ Estrella — invencibilidad 6s
- [x] 🍄 Hongo — +1 vida (en mundo manzanal se ve como 🍎 manzana)
- [x] 🪶 Pluma — doble salto
- [x] 💨 Turbo — velocidad x1.9 durante 5s
- [x] 🦎 Chiquito — hitbox 16x22 durante 8s
- [x] 🛡️ Escudo — aguanta 1 golpe (fuego/enemigos) y refleja autos (mundos 5 y 7)
- [x] 👊 Puñetazo — permite atacar con botón de golpe por 12s (mundo 6)

---

## Framework de jefes

- HP 2–5 golpes según mundo
- Ataque **telegrafiado** (aviso visual ~0.5s antes)
- Derrota = partícula grande + abre la puerta al próximo mundo
- **⚠️ Regla nueva global**: la bandera está *bloqueada* hasta que se vence al jefe del nivel (candado visual en la meta)
- Jefe final N10: **2 fases**, cambia patrón al 50% HP
- Jefe N13 (Robot): **puntos débiles** = cabeza + patas (el torso daña)

---

## Fases de implementación

- [x] **Fase 1 — Cimientos**: refactor a `LEVELS[]`, progresión, localStorage
- [x] **Fase 2 — Mundo 1 completo**: N2 plataformas móviles + N3 jefe Bowser gigante
- [x] **Fase 3 — Power-ups base**: clase `PowerUp` + estrella + hongo
- [x] **Fase 4 — Mundo 2** (Desierto): N4–N6 + abejas + Rey Escorpión
- [x] **Fase 5 — Mundo 3** (Nieve): N7 hielo + pluma + pingüinos/osos polares + N8 Pingüino Rey
- [x] **Fase 6 — Mundo 4 + pulido** (Castillo): N9 mix + dragones + lava + N10 Mega Bowser + turbo + chiquito
- [x] **Fase 7 — Bandera bloqueada por jefe**: regla global, candado visual en meta, aviso al jugador
- [x] **Fase 8 — Mundo 5 Selva de Manzanos** (N11): treetops sin suelo + puerta secreta + tesoro + 🛡️ escudo + 🐉 Dragón jefe
- [x] **Fase 9 — Mundo 6 Playa** (N12): playa + plataformas flotantes + 👊 puñetazo + 🐢 Tortuga Ninja
- [x] **Fase 10 — Mundo 7 Ciudad** (N13): edificios + 🚗 autos como hazard + 🤖 Robot con weak points
- [x] **Fase 11 — Ranking de puntos**: scoreboard local con top 10 (localStorage)
- [x] **Fase 12 — Configurar teclas**: modal de settings + persistencia
- [x] **Fase 13 — Menú principal + personalización**: pantalla inicio, nombre del personaje, colores (gorra/camisa/pantalón)
- [x] **Fase 14 — 2 jugadores**: player1 + player2, inputs separados, cámara compartida, vidas separadas
- [x] **Fase 15 — Andamios power-ups nuevos**: timers + triple salto + disparos con ✊ + teleport corto + visuales de vidas por mundo
- [x] **Fase 16 — Mundo 8 Fútbol** (N14-N15): cancha de Boca + Goalkeeper de River + pelota de fuego + escudo fuerte
- [x] **Fase 17 — Mundo 9 Marino** (N16-N17): bajo del mar + Calamardo + Bob Esponja + torpedo + pedos
- [x] **Fase 18 — Mundo 10 Perro** (N18-N19): plaza + Perro Gigante que embiste + galletas (zanahorias)
- [x] **Fase 19 — Mundo 11 Espacio** (N20-N21): asteroides + gravedad x0.6 + Alien Verde + triple salto
- [x] **Fase 20 — Mundo 12 Computadora** (N22-N23): grid digital + campo eléctrico + Robot Maligno + rayos + teleport
- [x] **Fase 21 — Andamios power-ups M13-16**: timers + maxAirJumps extendido + vuelo libre + agua nadable + coin multiplier + projectile pierce
- [x] **Fase 22 — Mundo 13 Pokémon** (N24-N25): bosque + Squirtle/Blastoise + Charizard + electricidad/chorro_agua/fuego_pkmn/plata + vidas pokeball
- [x] **Fase 23 — Mundo 14 Jardín** (N26-N27): aula + Mochila/NeneSaltando + Maestra + cuarto_salto/cinco_saltos/mega_rapido/bola_nieve + vidas crayón
- [x] **Fase 24 — Mundo 15 Pinocho** (N28-N29): taller + CaballoJuguete + PinochoBoss + torpedo_super/mega_salto/super_pedo + vidas nariz
- [x] **Fase 25 — Mundo 16 Ave** (N30-N31): cielo+árbol+agua nadable + Avestruz/PinguinoAgua + Loro + submarino/picar/volar + vidas huevo
- [x] **Fase 26 — Selector de niveles + rename a Primos Game v3**: modal con 31 niveles agrupados por 16 mundos (todos abiertos) + title/banner/header renombrados; localStorage sigue con prefijo `nacho:` (no se pierde progreso)
- [x] **Fase 27 — Velocidad regulable del juego**: 4 botones en el menú (🐌 Muy Lento 0.4x / 🐢 Lento 0.6x / 🚶 Normal 1.0x / 🚀 Rápido 1.4x) persistidos en `nacho:gameSpeed`. Implementado con acumulador de ticks en `gameLoop` (escala parejo todo: timers, físicas, enemigos, proyectiles, partículas, plataformas móviles). Split de `gameLoop` en `gameUpdate(dt)` + `gameDraw()`.
- [x] **Fase 28 — Wallet + Tienda + Inventario + Pausa con menú + Bonus**: combo de 4 features:
  - **Wallet persistente** (`nacho:wallet`): monedas se acumulan entre sesiones. Display 💰 en HUD, menú principal, pausa y tienda.
  - **Tienda** (`#shop-modal`): 6 power-ups comprables (estrella, hongo, pluma, turbo, chiquito, escudo) con precios fijos en monedas; acceso desde menú principal y pausa.
  - **Inventario** (`#inventory-modal`, `nacho:inventory`): los power-ups comprados se guardan; se usan con "Usar" desde el modal (disponible en pausa).
  - **Pausa con menú** (`#pause-modal`): al pausar (ESC o ⏸) aparece un modal con Continuar / Tienda / Inventario / Cambiar nivel / Menú principal. La pantalla queda jugable visualmente detrás.
  - **Navegación con teclado**: ↑/↓ mueven foco entre botones del modal abierto, Enter activa. Las teclas de movimiento se limpian al abrir cualquier modal (no quedan "pegadas").
  - **Sala bonus** en los 16 niveles de jefe: puerta dorada cerca del spawn, teletransporta a una sala con 25+ monedas; volvés solo al juntar todas o pasados 25s. Snapshot/restore del estado del nivel.
- [x] **Fase 29 — Mundos 17 (Excavación) + 18 (Excavadora)** (N32-N35):
  - 3 enemigos nuevos: 🌭 Salchicha (rueda bajita), 🐕 Perro Salchicha (cuerpo largo que persigue), 🦕 Dinosaurio (slow tanky, hp 2)
  - 2 jefes nuevos: 🦫 Topo (excava y reaparece, tira tierra), 🚜 Bulldozer (patrulla, tira piedras, embiste cada 4s)
  - 2 power-ups nuevos: ⛏️ Excavar (proyectil rápido de tierra) y 🟫 Bola de Tierra (proyectil grande con `damage: 2` que entierra al enemigo común instantáneo y daña 2 al jefe)
  - Backgrounds: cueva con estalactitas/lámparas/vetas de mineral · obra con grúas/edificios en construcción/conos
  - Boss levels (33 y 35) agregados a `BOSS_LEVEL_INDICES` para que tengan sala bonus
  - Refactor menor: proyectiles ahora usan `pr.damage` (default 1) para soportar bola_tierra haciendo daño doble

---

## Decisiones del director creativo ✅

### 🏜️ Mundo 2 — Desierto
- Cactus y arena naranja · 🐝 abejas · 🦂 Rey Escorpión

### ❄️ Mundo 3 — Nieve
- Montañas + muñecos de nieve · 🐧 pingüinos + 🐻‍❄️ osos polares · 🐧 Pingüino Rey

### 🏰 Mundo 4 — Castillo
- Castillo oscuro con relámpagos y dragones · 👑 Bowser Mega Gigante (fase 2 tira fuego)
- Power-ups: 💨 Turbo + 🦎 Chiquito

### 🍎 Mundo 5 — Selva de Manzanos
- Copas de árboles como plataformas (caerse = perder vida, no hay suelo)
- Vidas se ven como 🍎 manzanas
- 🚪 Puerta/pasadizo secreto → teletransporta a la arena del jefe con cascada de monedas
- 🐉 Jefe: **Dragón** que tira fuego
- 🛡️ Power-up: **Escudo** para aguantar el fuego

### 🏖️ Mundo 6 — Playa
- Playa con plataformas flotantes
- 👊 Power-up: **Puñetazo** (tecla de ataque + botón táctil)
- 🐢 Jefe: **Tortuga Ninja Mala**

### 🏙️ Mundo 7 — Ciudad
- Edificios grises + autos de fondo
- 🚗 Autos en movimiento como obstáculos
- 🛡️ Power-up: **Escudo** que refleja los autos que tira el jefe
- 🤖 Jefe: **Robot** con cabeza y patas como puntos débiles (el torso daña), tira autos + fuego de las manos

---

## Features globales (pendientes)

### Menú principal
- Campo de nombre del personaje
- Sliders/botones para color de gorra, camisa, pantalón
- Botón "Jugar" / "2 jugadores" / "Config" / "Ranking"

### 2 jugadores
- Player1 (flechas) y Player2 (WASD por defecto)
- Cámara compartida (o split?)
- Cada uno con sus vidas, colores distintos
- Pueden rebotar uno sobre el otro (cooperativo)

### Configurar teclas
- Modal con inputs para cada acción (izq / der / salto / ataque)
- Por jugador (Player1 / Player2)
- Persistir en `localStorage`

### Ranking
- Top 10 por nivel completado + score total
- Persistir en `localStorage`
- Mostrar nombre + puntaje + nivel
