# rubiCanon

Teclado de 36 teclas en dos bloques + 3 botones sueltos y 2 ruedas tipo ratón, con una SuperMini nRF52840 y firmware [ZMK](https://zmk.dev).

La configuración está en [config/boards/shields/rubiCanon/](config/boards/shields/rubiCanon/). Este archivo es solo documentación y no afecta al firmware.

## Disposición física

Números = posición de la tecla en el keymap (es lo que usan los `key-positions` de los combos).

```
┌─────┬─────┬─────┬─────┬─────┐   ┌─────┬─────┬─────┬─────┬─────┐
│  0  │  1  │  2  │  3  │  4  │   │  5  │  6  │  7  │  8  │  9  │
├─────┼─────┼─────┼─────┼─────┤   ├─────┼─────┼─────┼─────┼─────┤
│  10 │  11 │  12 │  13 │  14 │   │  15 │  16 │  17 │  18 │  19 │
├─────┼─────┼─────┼─────┼─────┤   ├─────┼─────┼─────┼─────┼─────┤
│  20 │  21 │  22 │  23 │  24 │   │  25 │  26 │  27 │  28 │  29 │
└─────┴─────┼─────┼─────┼─────┤   ├─────┼─────┼─────┼─────┴─────┘
            │  30 │  31 │  32 │   │  33 │  34 │  35 │
            └─────┴─────┴─────┘   └─────┴─────┴─────┘

  BTN: 36   ·   S1: 37   ·   S2: 38
```

- Matriz 4 filas × 10 columnas, `col2row`. La fila de pulgares usa las columnas C2–C7.
- 36 = botón `BTN1` (P1.04), 37 = botón `S1` (`RC01`, P1.07), 38 = botón `S2` (`RC02`, P1.11). Los tres van a GND (entradas directas, no están en la matriz).

### Ruedas (encoders)

| Rueda | Pines A/B | Sentido horario | Sentido antihorario |
|---|---|---|---|
| Izquierda (RE1) | P0.09 / P0.10 (pines NFC) | Vol+ | Vol- |
| Derecha (RE2) | P1.01 / P1.02 | PgUp | PgDn |

Son ruedas de ratón de 12 pulsos / 24 clicks: `steps = <48>`, `triggers-per-rotation = <24>` → 1 evento por click.

### Otros componentes

| Componente | Qué es |
|---|---|
| `RST1` | Botón de reset de la placa. Una pulsación reinicia el teclado; dos pulsaciones rápidas lo ponen en modo de flasheo. |
| `Status1` | Interruptor de encendido: corta la batería (`BAT+`) de la placa. |
| `Batt1` | Conector de la batería LiPo. |

> Con el interruptor apagado la batería queda desconectada de la placa, así que **no se carga** aunque enchufes el USB. Para cargar, déjalo encendido.

## Capas

Los nombres son keycodes de ZMK (distribución US). Con el sistema en español, `;` escribe `ñ`, `/` escribe `-`, etc. `▽` = transparente (usa la tecla de la capa inferior).

### Capa 0 — base

```
┌─────┬─────┬─────┬─────┬─────┐   ┌─────┬─────┬─────┬─────┬─────┐
│  Q  │  W  │  E  │  R  │  T  │   │  Y  │  U  │  I  │  O  │  P  │
├─────┼─────┼─────┼─────┼─────┤   ├─────┼─────┼─────┼─────┼─────┤
│  A  │  S  │  D  │  F  │  G  │   │  H  │  J  │  K  │  L  │  ;  │
├─────┼─────┼─────┼─────┼─────┤   ├─────┼─────┼─────┼─────┼─────┤
│  Z  │  X  │  C  │  V  │  B  │   │  N  │  M  │  ,  │  .  │  /  │
└─────┴─────┼─────┼─────┼─────┤   ├─────┼─────┼─────┼─────┴─────┘
            │ LCtl│ LAlt│ MO1 │   │ MO2 │ Spc │ RAlt│
            └─────┴─────┴─────┘   └─────┴─────┴─────┘

  BTN: Caps   ·   S1: Mute   ·   S2: Play/Pausa
```

### Capa 1 — números y F (mantener `MO1`)

```
┌─────┬─────┬─────┬─────┬─────┐   ┌─────┬─────┬─────┬─────┬─────┐
│  1  │  2  │  3  │  4  │  5  │   │  6  │  7  │  8  │  9  │  0  │
├─────┼─────┼─────┼─────┼─────┤   ├─────┼─────┼─────┼─────┼─────┤
│  F1 │  F2 │  F3 │  F4 │  F5 │   │  F6 │  F7 │  F8 │  F9 │ F12 │
├─────┼─────┼─────┼─────┼─────┤   ├─────┼─────┼─────┼─────┼─────┤
│  F9 │ F10 │ F11 │ F12 │  \  │   │  /  │  7  │  8  │  9  │  0  │
└─────┴─────┼─────┼─────┼─────┤   ├─────┼─────┼─────┼─────┴─────┘
            │  ▽  │  ▽  │  ▽  │   │ MO3 │  ▽  │  ▽  │
            └─────┴─────┴─────┘   └─────┴─────┴─────┘

  BTN: ExtPwr   ·   S1: ▽   ·   S2: ▽
```

`ExtPwr` = alterna la alimentación externa.

### Capa 2 — Bluetooth y navegación (mantener `MO2`)

```
┌─────┬─────┬─────┬─────┬─────┐   ┌─────┬─────┬─────┬─────┬─────┐
│ BT0 │ BT1 │ BT2 │ BT3 │BTCLR│   │ PgUp│  ↑  │ PgDn│  ▽  │ LGUI│
├─────┼─────┼─────┼─────┼─────┤   ├─────┼─────┼─────┼─────┼─────┤
│  !  │  '  │  '  │  =  │  `  │   │  ←  │  ↓  │  →  │  ▽  │  ▽  │
├─────┼─────┼─────┼─────┼─────┤   ├─────┼─────┼─────┼─────┼─────┤
│  ▽  │  ▽  │  ▽  │  ▽  │  ▽  │   │ Vol+│ Home│ Vol-│  ▽  │ OTog│
└─────┴─────┼─────┼─────┼─────┤   ├─────┼─────┼─────┼─────┴─────┘
            │  ▽  │  ▽  │ MO3 │   │  ▽  │  ▽  │  ▽  │
            └─────┴─────┴─────┘   └─────┴─────┴─────┘

  BTN: BLE   ·   S1: ▽   ·   S2: ▽
```

`BT0`–`BT3` = seleccionar perfil Bluetooth, `BTCLR` = borrar el perfil actual, `OTog` = alternar USB/BLE, `BLE` = forzar salida BLE.

### Capa 3 (`MO1` + `MO3` o `MO2` + `MO3`)

```
┌─────┬─────┬─────┬─────┬─────┐   ┌─────┬─────┬─────┬─────┬─────┐
│  Q  │  W  │  E  │  R  │  T  │   │  Y  │  ↑  │  I  │  O  │  P  │
├─────┼─────┼─────┼─────┼─────┤   ├─────┼─────┼─────┼─────┼─────┤
│  A  │  S  │  D  │  F  │  G  │   │  ←  │  ↓  │  →  │  L  │  ;  │
├─────┼─────┼─────┼─────┼─────┤   ├─────┼─────┼─────┼─────┼─────┤
│  Z  │  X  │  C  │  V  │  B  │   │  N  │  M  │  ,  │  .  │  /  │
└─────┴─────┼─────┼─────┼─────┤   ├─────┼─────┼─────┼─────┴─────┘
            │ LCtl│ LAlt│  ▽  │   │  ▽  │ Spc │ RAlt│
            └─────┴─────┴─────┘   └─────┴─────┴─────┘

  BTN: BLE   ·   S1: ▽   ·   S2: ▽
```

## Combos (solo en capa 0)

Pulsar las dos teclas a la vez:

| Teclas (posiciones) | Resultado |
|---|---|
| Q + W (0, 1) | Esc |
| A + S (10, 11) | Tab |
| Z + X (20, 21) | Shift izq. |
| O + P (8, 9) | Backspace |
| L + ; (18, 19) | Enter |
| . + / (28, 29) | Shift der. |
| Q + A (0, 10) | Supr |
| R + T (3, 4) | `KP_EQUAL` (¿ abrir) |
| Y + U (5, 6) | `MINUS` (? cerrar) |
| K + L (17, 18) | `'` (acento), timeout 150 ms |
| MO2 + Espacio (33, 34) | GUI izq. |

## Guía Bluetooth (para no complicarse)

### Las teclas que necesitas

Todas están en la **capa 2**: mantén pulsada la tecla `MO2` (pulgar derecho, la más interior, posición 33) y, sin soltarla, pulsa:

| Tecla (en la capa base) | Qué hace en la capa 2 |
|---|---|
| Q | Perfil Bluetooth 0 |
| W | Perfil Bluetooth 1 |
| E | Perfil Bluetooth 2 |
| R | Perfil Bluetooth 3 |
| T | **Borra** el perfil que está seleccionado ahora |
| / (abajo a la derecha) | Cambia entre USB y Bluetooth |
| BTN1 (botón suelto) | Fuerza la salida por Bluetooth |

**¿Qué es un perfil?** Es una "ranura" donde el teclado recuerda un dispositivo. Hay 4: puedes tener el portátil en el 0, el móvil en el 1, la tablet en el 2... y saltar de uno a otro con `MO2` + Q/W/E/R.

### Conectar un dispositivo nuevo

1. Mantén `MO2` y pulsa una tecla de perfil **libre** (por ejemplo `MO2` + W para el perfil 1).
2. En el ordenador o móvil, abre los ajustes de Bluetooth y busca dispositivos.
3. Aparecerá **rubiCanon**. Púlsalo para emparejar.
4. Listo. La próxima vez se reconecta solo al encender el teclado (si ese perfil está seleccionado).

> Si el perfil ya tenía otro dispositivo guardado, el teclado no se dejará emparejar con uno nuevo. Bórralo primero (ver abajo) o usa otro perfil.

### Cambiar de dispositivo

Mantén `MO2` y pulsa la tecla del perfil al que quieras pasar (Q, W, E o R). El teclado se desconecta del actual y se conecta al otro.

### Borrar un perfil

1. Selecciona el perfil que quieres borrar (`MO2` + Q/W/E/R).
2. Pulsa `MO2` + T.
3. **Importante:** en el ordenador/móvil, ve a los ajustes de Bluetooth y **olvida / elimina "rubiCanon"** también. Si solo lo borras en un lado, no volverán a entenderse.
4. Ya puedes emparejar de nuevo ese perfil.

### Si tengo el cable USB puesto y no escribe por Bluetooth

Con el USB conectado (aunque sea solo para cargar), el teclado escribe **por el cable**, no por Bluetooth. Pulsa `MO2` + BTN1 para forzar Bluetooth, o `MO2` + / para alternar entre USB y Bluetooth.

### Si nada funciona: borrado total

Cuando el teclado se conecta y desconecta todo el rato, o no aparece en la lista, lo más rápido es empezar de cero:

1. Descarga el firmware: en GitHub, pestaña **Actions** → última ejecución con ✔ → al final, en **Artifacts**, descarga `firmware`.
2. Conecta el teclado por USB y **pulsa dos veces rápido el botón de reset (`RST1`)** de la PCB. Aparecerá una unidad USB en el ordenador (como un pendrive).
3. Arrastra a esa unidad el archivo **`settings_reset-nice_nano_v2-zmk.uf2`**. La unidad desaparece sola: es normal. Esto borra todos los perfiles del teclado.
4. Vuelve a pulsar `RST1` dos veces y arrastra **`rubiCanon-nice_nano_v2-zmk.uf2`** (el firmware normal).
5. En **todos** tus dispositivos, elimina "rubiCanon" de la lista de Bluetooth.
6. Empareja de nuevo siguiendo "Conectar un dispositivo nuevo".

> Si en Windows sigue desconectándose después de esto, añade `CONFIG_BT_CTLR_PHY_2M=n` a [rubiCanon.conf](config/boards/shields/rubiCanon/rubiCanon.conf) y vuelve a compilar.
