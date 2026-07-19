# Asteroids

Clon del clásico arcade **Asteroids** implementado en canvas HTML5 puro, sin dependencias ni bundler.

## Demo:

[Asteroids demo](https://klerith.github.io/claude-asteroids/)

## Descripción

Nave espacial en un campo de asteroides con envolvimiento de bordes (el espacio es toroidal). Destruye asteroides para sumar puntos: los grandes se parten en medianos, los medianos en pequeños. Incluye power-ups especiales y tipos de asteroides únicos como la estrella fugaz.

## Tecnologías

- **HTML5 Canvas** — renderizado 2D
- **JavaScript (ES6+)** — lógica del juego en un solo archivo `game.js`
- Sin frameworks, sin bundler, sin dependencias

## Cómo correr

Abre `index.html` directamente en el navegador (doble clic), o usa un servidor local:

```bash
npx serve .
```

Luego visita `http://localhost:3000`.

## Controles

| Tecla     | Acción     |
| --------- | ---------- |
| `←` `→`   | Rotar nave |
| `↑`       | Propulsar  |
| `Espacio` | Disparar   |
| `S`       | Activar escudo temporal |

## Puntuación

| Asteroide | Puntos |
| --------- | ------ |
| Grande    | 20     |
| Mediano   | 50     |
| Pequeño   | 100    |

## Características

- 3 vidas con invencibilidad temporal al reaparecer (parpadeo)
- Asteroides se parten en fragmentos más pequeños al ser destruidos
- Partículas de explosión al destruir asteroides
- Escudo temporal activable (ver [Modificaciones](#modificaciones))
- Power-up de disparo triple recogible al destruir asteroides (ver [Modificaciones](#modificaciones))

## Modificaciones

### Escudo Temporal

Power-up defensivo activable manualmente. Rodea la nave con un círculo de energía azul que **absorbe un único impacto** de asteroide.

- **Activación:** tecla `S`, en cualquier momento durante la partida.
- **Duración:** ~5 segundos, o hasta recibir un golpe (lo que ocurra primero).
- **Disponibilidad:** una vez por nivel — se recarga automáticamente al pasar de nivel.
- **Al absorber un golpe:** el asteroide impactado se destruye (y se parte en fragmentos, si corresponde) sin sumar puntos; el escudo se consume y la nave no pierde vidas ni invencibilidad.
- El HUD muestra el estado del escudo bajo el puntaje: `ESCUDO: LISTO` (disponible), `ESCUDO: ACTIVO` (protegiendo) o `ESCUDO: —` (agotado este nivel).

El estado del escudo vive encapsulado en la clase `Ship` (`shieldReady`, `shieldTimer`, `tryShield()`), pensado como patrón reutilizable para futuros power-ups por-nivel sin necesidad de refactor.

### Disparo Triple

Power-up ofensivo que se **recoge volando sobre él**, como en los shoot 'em up clásicos — no se activa por tecla. Mientras está activo, cada disparo lanza **3 balas en abanico** en vez de una, ideal para limpiar grupos de asteroides.

- **Aparición:** al destruir un asteroide hay una probabilidad de que suelte una cápsula amarilla con una `3`, que queda flotando y derivando por el espacio (envuelve bordes como todo lo demás).
- **Recogida:** toca la cápsula con la nave para activar el disparo triple. Si la cápsula no se recoge, desaparece sola tras ~12 segundos (parpadea antes de expirar).
- **Duración del efecto:** ~8 segundos tras recogerla.
- **Disponibilidad:** máximo 1 cápsula por nivel. Mientras haya una en pantalla no aparecen más; en cuanto se recoge, expira o ya se usó ese nivel, tampoco vuelven a soltarse.
- **Exclusión mutua:** no puede recogerse/activarse mientras el escudo temporal está activo, y viceversa — solo un power-up extra a la vez.
- El HUD muestra el estado bajo el del escudo: `TRIPLE: BUSCAR ¤` (hay cápsulas disponibles este nivel), `TRIPLE: ACTIVO` (disparando en abanico) o `TRIPLE: —` (agotado este nivel).

Sigue el mismo patrón que el escudo (`tripleReady`, `tripleTimer`, `tryTripleShot()`), encapsulado en `Ship`; la única diferencia es el disparador: colisión nave↔`PowerUp` en vez de tecla.
