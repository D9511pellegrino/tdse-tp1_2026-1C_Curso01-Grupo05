Eventos (Triggers - Señales de entrada)
Estos eventos son la entrada al modelo System y provienen de los modelos Sensor (o confirmaciones de hardware), reflejando los cambios de estado físicos del entorno validados:

EV_SYS_CAM_01_DETECT: Evento que se dispara cuando el sensor de la cámara detecta la presencia de un vehículo en la zona de ingreso.

EV_SYS_CAM_01_NOT_DETECT: Evento que se dispara cuando la cámara deja de detectar el vehículo (ej. el vehículo retrocede y abandona la zona antes de sacar ticket).

EV_SYS_BTN_01_DOWN: Evento proveniente del modelo Sensor del botón (ya filtrado sin rebotes) que notifica que el usuario ha presionado el botón para solicitar un ticket.

EV_SYS_BTN_01_UP: Evento que notifica que el usuario ha liberado el botón.

EV_SYS_TICKET_01_PRINT: Evento de confirmación proveniente del hardware o del subsistema de impresión, indicando que el ticket se ha emitido correctamente.

EV_SYS_BARRIER_01_UP: Evento de confirmación (sensor de fin de carrera) que indica que la barrera ha alcanzado su posición máxima superior (abierta).

EV_SYS_BARRIER_01_DOWN: Evento de confirmación que indica que la barrera ha alcanzado su posición máxima inferior (cerrada).

EV_SYS_COIL_01_DETECT: Evento proveniente de la bobina de inducción indicando que el vehículo está físicamente pasando por debajo de la barrera.

EV_SYS_COIL_01_NOT_DETECT: Evento que indica que el vehículo ha liberado la zona de la bobina (pasó completamente).

every 1s / every 0s: Eventos temporales generados por el motor de la máquina de estados para evaluar condiciones periódicamente.

Variables de Control (Timers y Guards)
Para gestionar los tiempos de procesos mecánicos (como la impresión) y coordinar el flujo, se requieren variables y condiciones que habilitan las transiciones:

Tick: Es una variable numérica de control utilizada como temporizador regresivo. Sirve para medir el tiempo necesario para la impresión del ticket o la pausa antes de levantar la barrera.

[Tick > 0]: Guard (condición lógica) que obliga a la máquina a permanecer en el ciclo de espera de impresión mientras el tiempo no se haya agotado.

[Tick == 0]: Guard que habilita la transición hacia el estado de levantar la barrera únicamente cuando el temporizador ha llegado a cero, asegurando que la impresión finalizó.

Acciones (Effects - Salidas y Modificaciones)
Son las operaciones que ejecuta el modelo System cuando se cumple un evento y/o su condición (guard). En este modelo, las acciones incluyen cálculos internos y señales hacia los actuadores:

Modificación de variables de control:

Tick--: Acción ejecutada en bucle (cada 1s) para decrementar la variable de control, descontando el tiempo restante del proceso en curso (ej. impresión).

Signals (Señales hacia el modelo Actuator):
(Nota: Aunque en la tabla reducida se omitieron con "-", estas son las acciones de salida implícitas al transitar hacia nuevos estados para comandar los dispositivos).

EV_ACT_CAM_ON: Acción de salida enviada al actuador de la cámara para encenderla/activar su procesamiento al detectar un vehículo.

EV_ACT_PRINT_TICKET: Acción de salida que ordena al actuador de la impresora iniciar la emisión del papel.

EV_ACT_BARRIER_UP: Acción de salida enviada al motor de la barrera para que comience a subir.

EV_ACT_BARRIER_DOWN: Acción de salida enviada al motor de la barrera para que comience a bajar y asegurar el perímetro.

# System Statechart - State Transition Table

Lógica de procesamiento basada en el flujo de control del expendedor de tickets (Entry). 
Este módulo procesa los eventos provenientes de los sensores y coordina las acciones de los actuadores (Cámara, Impresora, Barrera).

| Current State | Event | [Guard] | Next State | Actions |
| :--- | :--- | :--- | :--- | :--- |
| **ST_SYS_IDLE** | EV_SYS_CAM_01_DETECT | - | **ST_SYS_CAM_01_ON** | - |
| **ST_SYS_CAM_01_ON** | EV_SYS_CAM_01_NOT_DETECT | - | **ST_SYS_IDLE** | - |
| **ST_SYS_CAM_01_ON** | EV_SYS_BTN_01_DOWN | - | **ST_SYS_BTN_01_DOWN** | - |
| **ST_SYS_BTN_01_DOWN** | EV_SYS_BTN_01_UP | - | **ST_SYS_CAM_01_ON** | - |
| **ST_SYS_BTN_01_DOWN** | EV_SYS_TICKET_01_PRINT | - | **ST_SYS_TICKET_01_P...** | - |
| **ST_SYS_TICKET_01_P...** | every 1s | [Tick > 0] | **ST_SYS_TICKET_01_P...** | Tick-- |
| **ST_SYS_TICKET_01_P...** | EV_SYS_BARRIER_01_UP | [Tick == 0] | **ST_SYS_BARRIER_01_UP** | - |
| **ST_SYS_BARRIER_01_UP** | EV_SYS_COIL_01_DETECT | - | **ST_SYS_COIL_01_OFF** | - |
| **ST_SYS_COIL_01_OFF** | EV_SYS_COIL_01_NOT_DETECT | - | **ST_SYS_BARRIER_01_UP** | - |
| **ST_SYS_COIL_01_OFF** | EV_SYS_BARRIER_01_DOWN | - | **ST_SYS_BARRIER_01_DOWN** | - |
| **ST_SYS_BARRIER_01_DOWN** | every 0s | - | **ST_SYS_IDLE** | - |

## Descripción del Modelado
El diagrama representa el ciclo de vida del ingreso a un estacionamiento:
1. **Detección**: El sistema sale de reposo (`IDLE`) cuando la cámara detecta un vehículo.
2. **Validación**: Se espera a que el usuario presione el botón (`BTN_01_DOWN`).
3. **Procesamiento**: Se imprime el ticket con un control de tiempo (`Tick`).
4. **Acceso**: Se levanta la barrera y se detecta el paso del vehículo mediante la bobina de inducción (`COIL`).
5. **Cierre**: Una vez que el vehículo termina de pasar, la barrera baja y el sistema vuelve a `IDLE`.
