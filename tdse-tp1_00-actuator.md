# TP1 – Actividad 00
## Paso 10 y Paso 11 – Modelo Actuator (LED)

---

## Paso 10 – Eventos y Acciones del Modelo Actuator

### Descripción general

El modelo **Actuator** representa el comportamiento de un **único LED digital**, controlado por un módulo de código C del tipo **temporizado** (Update by Time Code, período = 1 ms), encargado de **actuar** sobre una salida del sistema.

El LED puede encontrarse apagado, encendido o titilando, de acuerdo a los eventos recibidos desde el modelo **System**.

---

### Estados del modelo

- **ST_LED_01_OFF**  
  Estado en el cual el LED se encuentra apagado.

- **ST_LED_01_ON**  
  Estado en el cual el LED se encuentra encendido de manera continua.

- **ST_LED_01_BLINK**  
  Estado en el cual el LED titila periódicamente utilizando un temporizador basado en el evento `tick`.

---

### Eventos (Triggers)

Los eventos que excitan al modelo Actuator son:

- **EV_LED_01_ON**  
  Solicitud de encendido del LED.

- **EV_LED_01_OFF**  
  Solicitud de apagado del LED.

- **EV_LED_01_BLINK**  
  Solicitud de activación del modo titilante.

- **tick**  
  Evento periódico generado por el sistema temporizado (cada 1 ms), utilizado para controlar el parpadeo del LED.

---

## Paso 11 – Tabla de Estados y Excitaciones del Modelo Actuator

A continuación se presenta la **tabla de Estados y Excitaciones** correspondiente al comportamiento del LED:

| Estado Actual        | Evento           | Guarda            | Acción                 | Estado Siguiente     |
|----------------------|------------------|-------------------|------------------------|----------------------|
| ST_LED_01_OFF        | EV_LED_01_ON     | –                 | LED_01_Set(ON)         | ST_LED_01_ON         |
| ST_LED_01_OFF        | EV_LED_01_BLINK  | –                 | Timer_Reset()          | ST_LED_01_BLINK      |
| ST_LED_01_ON         | EV_LED_01_OFF    | –                 | LED_01_Set(OFF)        | ST_LED_01_OFF        |
| ST_LED_01_ON         | EV_LED_01_BLINK  | –                 | Timer_Reset()          | ST_LED_01_BLINK      |
| ST_LED_01_BLINK      | EV_LED_01_OFF    | –                 | LED_01_Set(OFF)        | ST_LED_01_OFF        |
| ST_LED_01_BLINK      | EV_LED_01_ON     | –                 | LED_01_Set(ON)         | ST_LED_01_ON         |
| ST_LED_01_BLINK      | tick             | timer expired     | LED_01_Toggle()        | ST_LED_01_BLINK      |

---

### Observaciones

- El estado **ST_LED_01_BLINK** utiliza el evento periódico `tick` junto con una guarda basada en temporización.
- El parpadeo del LED se implementa sin cambiar de estado, alternando la salida mediante acciones.
- El modelo Actuator es determinístico y cumple exclusivamente la función de **actuar** sobre el sistema.

