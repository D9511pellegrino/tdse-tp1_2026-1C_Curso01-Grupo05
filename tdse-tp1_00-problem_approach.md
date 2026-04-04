1. Solución de COMA Electronics:
La solución integral "Intelligent Parking Management System" de COMA Electronics comprende la infraestructura completa de un estacionamiento automatizado. Esta arquitectura incluye el Servidor del Sistema (Parking System Server), las Máquinas de Entrada y Salida (Entry/Exit Machines), y las Estaciones de Pago (Toll Computer / Automatic Pay Station). Sin embargo, para los fines y alcance de este proyecto, el desarrollo se restringe exclusivamente al modelado de la Máquina de Entrada, denominada Parking Ticket Dispenser Machine (Entry).

2. Implementación de la Parking Ticket Dispenser Machine (Entry):
El objetivo principal es diseñar, verificar, validar y depurar el modelo de comportamiento de esta máquina. Para ello, se adoptará una arquitectura de software modular dividida funcionalmente en tres tareas elementales: escrutar, procesar y actuar. La sincronización e integración de estos módulos se realizará mediante el paso de mensajes.

Dado que el desarrollo se llevará a cabo a nivel de prototipo (sin el hardware final), se utilizarán componentes de reemplazo: las entradas digitales (cámara, bobina sensora y botón) se emularán mediante interruptores (dip switches) y pulsadores, mientras que las salidas digitales (barrera, display e impresora) se representarán mediante LEDs.

Un requerimiento crítico del proyecto es que el firmware debe operar bajo un esquema de ejecución cíclica de tareas con una base de tiempo de 1 milisegundo (Update by Time Code, period = 1mS). Es mandatorio garantizar un comportamiento cooperativo entre los módulos; por lo tanto, el uso de rutinas bloqueantes es inaceptable, ya que ningún proceso debe monopolizar el uso de la CPU.

3. Modelado de los módulos de código en C:
Para estructurar el comportamiento del sistema bajo las restricciones temporales impuestas, la lógica se dividirá en tres Máquinas de Estado (Statecharts) independientes:

Sensor Statechart (Módulo para escrutar): Encargado de adquirir las señales del entorno físico. Su función principal es leer los pulsadores y switches, aplicando los filtros por software necesarios (como rutinas de debouncing o anti-rebote) para entregar eventos limpios y validados al sistema.

System Statechart (Módulo para procesar): Constituye el núcleo lógico de la aplicación. Recibe los eventos validados del módulo Sensor, evalúa el estado actual del sistema y toma decisiones, despachando las órdenes y mensajes correspondientes hacia el actuador.

Actuator Statechart (Módulo para actuar): Recibe las señales de control provenientes del módulo System y gestiona el comportamiento de las salidas físicas. Este módulo utiliza los temporizadores (ticks de 1mS) para generar los efectos visuales en los LEDs, simulando el tiempo físico que tardaría en subir una barrera o imprimir un ticket.
