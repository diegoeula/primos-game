# CLAUDE.md — Nacho en la Jungla

Guía rápida para que Claude colabore bien en este proyecto. Si hay conflicto con el `readme.md`, este archivo manda.

---

## Contexto

Juego de plataformas 2D hecho **en casa con un hijo de 5 años como director creativo** y Diego (papá) como developer. El chico elige nombre, colores, villano, escenario. Claude es copiloto de código.

**Objetivo**: que el proceso sea divertido y visible. El chico tiene que poder *ver* el cambio que pidió en pantalla, idealmente en menos de 1 minuto.

---

## Reglas de oro (no romper)

1. **Un solo archivo**: todo vive en `index.html` (HTML + CSS + JS juntos). No crear `main.js`, `style.css`, ni carpetas de `src/`.
2. **Cero dependencias**: sin npm, sin CDN, sin assets externos. Si algo se puede hacer con Canvas 2D o Web Audio, se hace desde cero.
3. **Sin build step**: doble-click en `index.html` tiene que abrir el juego. Nada de bundlers, transpilers, ni servidores.
4. **Preservar el "perdón" del gameplay** (clave para un pibe de 5):
   - *Coyote time* (`player.coyote`, ~120ms) — no tocar sin avisar.
   - *Salto variable* (si suelta el botón, corta el salto) — idem.
   - 3 vidas + invulnerabilidad post-daño (`invuln`).
5. **Pixel art**: `image-rendering: pixelated` + `ctx.fillRect` a coordenadas enteras (`Math.round`). No introducir sprites PNG ni suavizado.
6. **Idioma**: código, comentarios, UI e identificadores en **español**. Los nuevos también.

---

## Cómo probar

- **Windows**: doble-click en `index.html` — abre en el browser por defecto.
- **Dev loop**: editar → guardar → `F5` en el browser. No hace falta nada más.
- **Mobile**: no hay servidor local configurado. Si se necesita probar en celu, levantar un static server ad-hoc (`python -m http.server` en la carpeta).

No hay tests automatizados. La verificación es visual — **siempre recargar el browser y jugar el tramo afectado** antes de decir "listo".

---

## Mapa del código (navegación rápida)

Todo en [index.html](index.html):

| Qué | Dónde |
|-----|-------|
| Estado global `game` | [index.html:135](index.html#L135) |
| `class Player` (Nacho) | [index.html:149](index.html#L149) |
| Dibujo del personaje (gorra, cara, cuerpo, piernas) | [index.html:217](index.html#L217) |
| `class Coin` | [index.html:300](index.html#L300) |
| `class Bowser` (enemigo) | [index.html:332](index.html#L332) |
| `class Particle` | [index.html:427](index.html#L427) |
| **`createLevel()` — diseño del nivel** | [index.html:454](index.html#L454) |
| Plataformas, monedas, enemigos, meta | [index.html:455–498](index.html#L455) |
| Fondo (montañas, árboles, lianas) | [index.html:511](index.html#L511) |
| Input (teclado + touch) | [index.html:630](index.html#L630) |
| `gameLoop()` — update + draw | [index.html:682](index.html#L682) |

---

## Parámetros que se tunean seguido

Cuando el chico dice "salta más alto" / "corre más rápido" / "más monedas", ir acá:

| Parámetro | Valor actual | Dónde |
|-----------|--------------|-------|
| Velocidad de Nacho | `speed = 4.2` | [index.html:161](index.html#L161) |
| Fuerza de salto | `this.vy = -12.5` | [index.html:167](index.html#L167) |
| Gravedad | `this.vy += 0.65` | [index.html:175](index.html#L175) |
| Velocidad máxima de caída | `16` | [index.html:176](index.html#L176) |
| Coyote time | `0.12` s | [index.html:183](index.html#L183) |
| Ancho del nivel | `LEVEL_WIDTH = 2400` | [index.html:133](index.html#L133) |
| Vidas iniciales | `game.lives = 3` | [index.html:501](index.html#L501) |
| Colores de Nacho | Gorra roja `#d32f2f`, camiseta verde `#2d8f3e` | [index.html:224, 253](index.html#L224) |

**Agregar plataforma / moneda / enemigo**: meter una línea más en los arrays de `createLevel()`. Coords: `x` = píxel desde la izquierda, `y` = píxel desde arriba (0 = cielo, 450 = suelo).

---

## Estilo de colaboración con Claude

- **Explicar como para un chico de 5**: cuando el director creativo está mirando, Claude debería poder decir en voz alta *qué* está cambiando ("ahora Bowser es más rápido", "agregué 3 monedas arriba del árbol") antes de tocar código.
- **Cambios chicos y visibles**: preferir 1 cambio tocable → probar → siguiente. No grandes refactors.
- **Antes de agregar features del roadmap** (sonido, power-ups, niveles múltiples): preguntar si es lo que quiere Diego. Varias rompen las reglas de oro si se hacen mal (ej. sonido con archivos externos).
- **No inventar scope**: si pide "más monedas", agregar monedas. No reescribir la clase `Coin` "de paso".
- **Comentarios en el código**: minimizar. Los que ya están explican *por qué* (ej. "saltito permitido apenas se cae"). Mantener ese estilo, no describir lo obvio.

---

## Roadmap

Ver `readme.md` → sección "📝 Roadmap". Ideas ordenadas por impacto para un chico de 5:

1. **Sonido** (Web Audio API, sin archivos) — el feedback auditivo es enorme a esa edad.
2. **LocalStorage** para mejor puntaje — da sensación de progreso entre sesiones.
3. **Power-ups** (invencibilidad, doble salto) — fácil de entender visualmente.
4. **Múltiples niveles** (array de configs de `createLevel`) — cuando el nivel 1 se vuelva aburrido.
5. Editor de niveles / Phaser / jefe final — más adelante, cuando el proyecto pida escalar.
