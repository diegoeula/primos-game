# 🦖 Nacho en la Jungla

Juego de plataformas 2D estilo Mario, hecho en un único archivo HTML.

---

## 🧰 Stack

| Capa | Tecnología | Por qué |
|------|-----------|---------|
| **Estructura** | HTML5 | Un único `index.html`, sin build, sin bundler |
| **Render** | Canvas 2D API | Dibujo pixelado directo, sin librerías de juegos |
| **Lógica** | JavaScript vanilla (ES6+) | Clases, `requestAnimationFrame`, sin frameworks |
| **Estilos** | CSS3 | Layout responsive con `clamp()` y `aspect-ratio` |
| **Inputs** | Keyboard + Touch Events | Teclado en desktop, botones táctiles en mobile |
| **Hosting** | GitHub Pages | Deploy gratuito desde el repo, sin servidor |
| **Dependencias externas** | **Ninguna** | 0 npm, 0 CDN, 0 assets remotos |

### ¿Por qué vanilla y no Phaser/Godot/Unity?

- **Portabilidad**: un único `.html` que funciona abriendo el archivo, sin instalar nada.
- **Legibilidad**: un chico de 5 (y su papá) pueden ver el código completo en una sola pantalla.
- **Deploy instantáneo**: `git push` y GitHub Pages ya lo sirve.
- **Aprendizaje**: sin magia de framework, todo lo que pasa en pantalla está escrito explícitamente.

Para un juego más grande (más niveles, físicas complejas, multijugador) conviene migrar a **Phaser.js** o **Godot**.

---

## 🏗️ Arquitectura del código

Todo vive en `index.html`, dividido en tres secciones:

```
index.html
├── <style>     → CSS (layout, controles táctiles, HUD)
├── <body>      → Canvas + HUD + botones + mensajes
└── <script>    → Lógica del juego
    ├── Estado global       → objeto `game`
    ├── class Player        → Nacho (movimiento, colisión, animación)
    ├── class Coin          → monedas (animación bob + spin)
    ├── class Bowser        → enemigos (patrullaje)
    ├── class Particle      → efectos visuales
    ├── createLevel()       → define plataformas, monedas, enemigos
    ├── drawBackground()    → parallax de fondo (montañas, árboles, lianas)
    ├── drawPlatform()      → tiles de tierra y madera
    ├── Input handlers      → keyboard + touch
    └── gameLoop()          → loop principal con requestAnimationFrame
```

### Game loop (el corazón del juego)

```
requestAnimationFrame
       ↓
   gameLoop(now)
       ↓
  ┌────┴────┐
  │ UPDATE  │  → physics, input, colisiones, enemigos, partículas
  └────┬────┘
       ↓
  ┌────┴────┐
  │  DRAW   │  → background → platforms → coins → enemies → player → particles → HUD
  └────┬────┘
       ↓
  requestAnimationFrame (loop)
```

### Detección de colisiones

AABB (Axis-Aligned Bounding Box) separando ejes: primero se mueve en X y se resuelven choques horizontales, después en Y y se resuelven verticales. Esto evita el bug clásico de quedar "clavado" en esquinas.

```js
this.x += this.vx;           // mover en X
this.handleCollisionX();     // corregir colisiones horizontales
this.y += this.vy;           // mover en Y
this.handleCollisionY();     // corregir + detectar piso
```

### Controles

- **Keyboard**: `keydown` / `keyup` actualizan un objeto `keys` compartido
- **Touch**: mismos eventos sobre botones virtuales, con `preventDefault` para evitar scroll del navegador
- **Coyote time**: el jugador puede saltar hasta 120ms después de caer de una plataforma (perdona errores de timing, clave para un pibe de 5)
- **Salto variable**: si soltás el botón en el aire, el salto se corta antes (da control)

---

## 🎮 Cómo jugar

| Acción | Teclado | Touch |
|--------|---------|-------|
| Moverse | `←` `→` o `A` `D` | Botones `◀` `▶` |
| Saltar | `↑` `W` o `Espacio` | Botón `▲` |
| Reiniciar | `Enter` | Tocá la pantalla |

Derrotá a Bowser saltándole encima. Tocarlo de costado te quita una vida (tenés 3).


## 📁 Estructura del repo

```
nacho-game-v2/
├── index.html     ← todo el juego (HTML + CSS + JS)
└── README.md      ← este archivo
```

Eso es todo.

---

## 📝 Roadmap

- [ ] Sonidos (`Web Audio API`, sin archivos externos)
- [ ] Música de fondo (generada con osciladores)
- [ ] Sistema de múltiples niveles (array de configs)
- [ ] Power-ups: invencibilidad, doble salto
- [ ] Jefe final Bowser
- [ ] LocalStorage para guardar mejor puntaje
- [ ] Editor de niveles en el browser
- [ ] Migrar a Phaser.js si el proyecto crece

---

## 👨‍👦 Contexto

Proyecto hecho en casa con mi hijo de 5 años como director creativo (eligió el nombre del personaje, los colores, el escenario y el villano) y Claude como copiloto de código. La idea no es el resultado final sino el proceso de construir algo juntos.

---

Hecho con ❤️ y [Claude](https://claude.ai).