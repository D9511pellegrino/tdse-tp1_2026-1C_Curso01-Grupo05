1. Eventos (Triggers - Excitaciones físicas)
Estos eventos son la entrada al modelo y reflejan exclusivamente el valor binario instantáneo (las posiciones físicas) del botón escrutado:

EV_BTN_DOWN: Evento que se dispara cuando la lectura del hardware indica que el botón está en la posición presionado (hacia abajo).

EV_BTN_UP: Evento que se dispara cuando la lectura del hardware indica que el botón está en la posición suelto (hacia arriba).

2. Variables de Control (Timers y Guards)
Para garantizar que el cambio de posición sea real (y no ruido o rebote mecánico), se requieren variables temporales que condicionarán las transiciones:

tick: Es la variable de control (timer) que se incrementa a razón de 1 cada milisegundo. Sirve para medir cuánto tiempo el botón se mantiene en una posición sin fluctuar.

DEL_BTN_NAME: Constante de tiempo que define el umbral de debouncing. Se utiliza dentro de las transiciones como un guard (condición entre corchetes): [tick >= DEL_BTN_NAME].

3. Acciones (Effects - Salidas y Modificaciones)
Son las operaciones que ejecuta el modelo Sensor cuando se cumple un evento y/o su condición temporal:

Modificación de variables de control:

tick = 0: Acción ejecutada sistemáticamente en cada transición para reiniciar el temporizador, permitiendo que comience a contar desde cero el tiempo de estabilización en el nuevo estado.

Signals (Señales hacia el modelo System):

EV_SYS_DOWN: Acción de salida (señal) enviada al modelo System para informarle de manera oficial y validada que el botón cambió a la posición down (esto ocurre al alcanzar el estado validado ST_BTN_2).

EV_SYS_UP: Acción de salida (señal) enviada al modelo System notificando que el botón pasó a la posición up de forma legítima (esto ocurre al volver al estado de reposo ST_BTN_0).
