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
