# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Comandos

No hay build, lint ni tests: proyecto estático sin dependencias ni bundler.

- Correr: abrir `index.html` en navegador, o servidor local: `npx serve .` → `http://localhost:3000`

## Arquitectura

Clon de Asteroids en HTML5 Canvas puro, todo en un único archivo `game.js` (sin módulos, sin build step). `index.html` solo crea el `<canvas id="canvas">` (800×600) y carga `game.js`.

Estructura de `game.js` (orden secuencial, no hay módulos):

1. **Input**: `keys` (estado continuo) y `justPressed` (edge-trigger, vía `pressed(code)`) alimentados por listeners de `keydown`/`keyup`.
2. **Utils**: `wrap` (espacio toroidal — todo objeto que sale de un borde reaparece del opuesto), `dist`, `rand`, `randInt`.
3. **Clases de entidades**: `Bullet`, `Asteroid`, `Ship`, `Particle` — cada una con `update(dt)` y `draw()`. `Asteroid.split()` devuelve los dos fragmentos de tamaño menor (tamaño 1 no se parte). Tablas `RADII`/`SPEEDS`/`POINTS` indexadas por tamaño (1=pequeño, 2=mediano, 3=grande).
4. **Estado global del juego**: variables sueltas (`ship`, `bullets`, `asteroids`, `particles`, `score`, `lives`, `level`, `state`) reinicializadas en `initGame()`. `state` es una máquina de 3 estados: `'playing'` | `'dead'` | `'gameover'`.
5. **`update(dt)`**: dispatch según `state`. En `'playing'` hace, en orden: leer disparo, actualizar entidades, colisión bala↔asteroide (con partición y suma de puntaje), colisión nave↔asteroide (respetando invencibilidad temporal), y avance de nivel cuando `asteroids.length === 0`.
6. **`draw()` / HUD**: dibuja en orden partículas → asteroides → balas → nave → HUD; overlay de "GAME OVER" cuando corresponde.
7. **Loop principal**: `requestAnimationFrame` clásico con `dt` clamped a 0.05s para evitar saltos tras tabs inactivas.

Todas las coordenadas envuelven vía `wrap()` (mundo toroidal); las colisiones son circulares (`dist < suma de radios`).
