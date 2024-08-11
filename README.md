
<!-- Para abrir el preview en Atom: ^ (control) + shift + M -->

# Proyecto final del curso de Sistemas operativos en tiempo Real.
[![platform][License]][License]

## Table of Contents
- [Introducción](#introducción)
- [Requerimientos](#requerimientos)
- [Materiales](#materiales)
  - [Software](#software)
  - [Hardware](#hardware)
- [Enfoque propuesto](#enfoque-propuesto)
  - [Configuración de FreeRTOS](#configuración-de-freertos)
  - [Configuración del DSPI](#configuración-del-dspi)
  - [Tareas y configuración](#tareas-y-configuración)
  - [Mutex](#mutex)
  - [Grupos de eventos](#grupos-de-eventos)
  - [Semáforos binarios](#semáforos-binarios)
  - [QUEUE](#queue)
  - [Manejo de interrupciones](#manejo-de-interrupciones)
  - [Máquina de estado para la ejecución de las tareas del reloj](#máquina-de-estado-para-la-ejecución-de-las-tareas-del-reloj)
  - [Máquina de estado para la ejecución de la tarea alarma](#máquina-de-estado-para-la-ejecución-de-la-tarea-alarma)
  - [Máquina de estado para la ejecución de la tarea cronómetro](#máquina-de-estado-para-la-ejecución-de-la-tarea-cronómetro)
  - [Máquina de estado para la manipulación del sistema](#máquina-de-estado-para-la-manipulación-del-sistema)
- [Resultados](#resultados)
- [Autores](#autores)



## Introducción
### Objetivo
En este proyecto se implementará un reloj con alarma y cronómetro, mediante el uso de FreeRTOS, generando múltiples tareas y usando IPC para sincronizarlas.
<!-- Para crear un índice -->

### Requerimientos
1. El sistema tendrá las siguientes funcionalidades:
* Hora (horas, minutos y segundos)
* Cronómetro (minutos, segundo y milisegundos, con una resolución de 25 milisegundos)
* Alarma

#### Hora
2. Una tarea de segundos, deberá activarse cada 1 segundo, para incrementar un contador local y enviar el valor a la tarea de mostrar la hora.
3. Una tarea de minutos, se sincronizara con la tarea de segundo, incrementando el contador local de minutos.
4. Una tarea de horas, se sincronizará con la tarea de minutos, incrementando el contador local de horas.
5. Las variables usadas para llevar la cuenta de segundo, minutos y horas, deberán ser locales a la tarea que lleve a cabo su cuenta.
6. El paso de la información entre las tareas de segundos, minutos y horas y la de mostrar la hora, se hará utilizando un solo canal de comunicación.
7. Una tarea de mostrar la hora cada vez que se modifique el valor de segundo, minuto u hora.
8. La hora (horas, minutos y segundos) será mostrada a través de una pantalla LCD 84x48, en los renglones 2 o 3 de LCD.

#### Alarma
9. Una tarea para mostrar la alarma, la cual tendrá un valor de tiempo establecido para la alarma, con el cual, cuando coincida con el contador del reloj, se deberá mostrar en la pantalla
LCD el mensaje de ALARMA, en los renglones 4 o 5 del LCD.
10. Mientras la alarma este activada, se debe mostrar tanto el mensaje de ALARMA como la hora.
11. Mientras la alarma este activa, se deberá encender y apagar los LEDs de la pantalla LCD, en un intervalo de 1 segundo, hasta que la alarma se desactive.
12. Para desactivar la alarma, se hará mediante la pulsación del SW2 de la FRDM-K64F.
13. La detección de la pulsación del SW2 se hará mediante interrupción.
14. El valor de la alarma, se deberá poder modificar, mediante el acceso por UDP al sistema, a través del puerto 49.
#### Cronómetro
15. Una tarea para cronómetro
16. Al estar en el modo reloj, y presionar el SW3, el sistema deberá pasar al modo cronómetro, en modo espera para ser iniciado.
17. Mientras se está en modo cronómetro, se deberá mostrar tanto la hora (renglón 1 o 2) como el cronómetro (3 o 4) en el LCD.
18. En dado caso de activarse la alarma, estando en el modo cronómetro, se mostrará el mensaje de ALARMA en los renglones 5 o 6 del LCD. Para desactivar la alarma, será necesario pasar
a modo reloj.
19. El cronómetro, al pasar del modo reloj al modo cronómetro, se reiniciará a 0 todos los contadores.
20. Al estar en modo cronómetro, y presionar el SW2, el cronómetro iniciará el conteo.
21. Estando el cronómetro en modo conteo, si se presiona el SW2, el cronómetro se detendrá, y mantendrá la cuenta.
22. Si se presiona nuevamente el SW2, después de detener el conteo del cronómetro, esta iniciara nuevamente el conteo, desde el valor que se quedó en el conteo pasado.
23. Para regresar al modo reloj, se presionará el SW3, pero solo deberá actuar cuando el cronómetro está detenido.
    
## Materiales
A continuación, se presenta los diferentes componentes utilizados para dar solución al proyecto de RTOS solicitado.

### Software
En la siguiente tabla se muestra el software utilizado para implementación de proyecto solicitado.
|Platform|Language|sofware|Descripción|
|:----------:|:--------:|:---:|:---:|
|Windows/Ubuntu|C|MCUXpresso-IDE|Este IDE ofrece a los desarrolladores un entorno de desarrollo basado en Eclipse fácil de usar para MCU NXP ® basados ​​en núcleos Arm ® Cortex ® -M, incluidos sus MCU de propósito general con conexión inalámbrica y de cruce. 
|Windows/Ubuntu|C|MCUXpresso Software Development Kit (SDK)|El SDK de MCUXpresso es un paquete de habilitación de software integral diseñado para simplificar y acelerar el desarrollo de aplicaciones con dispositivos basados ​​en Arm ® Cortex ® -M de NXP.
|Windows/Ubuntu|C|lwip|lwIP es una pila TCP/IP de código abierto ampliamente utilizada diseñada para sistemas integrados. En este proyecto se utiliza para setear la alarma por medio de UDP.
### Hardware
En la siguiente tabla se muestra el hardware utilizado.
|Hardware|Descripción|
|:----------:|:--------:|
|The Freedom-K64F|Freedom-K64F es una plataforma de desarrollo de costo ultrabajo para MCU Kinetis ® K64, K63 y K24.
|Display nokia 5110|Dsiplay utilizado para mostrar el reloj, alarma y cronómetro.

## Enfoque propuesto

### Configuración de FreeRTOS
La configuración utilizada en FreeRTOS es la siguiente-
```C
#define configUSE_PREEMPTION                    1
#define configUSE_TICKLESS_IDLE                 0
#define configCPU_CLOCK_HZ                      (SystemCoreClock)
#define configTICK_RATE_HZ                      ((TickType_t)1000)
#define configMAX_PRIORITIES                    18
#define configMINIMAL_STACK_SIZE                ((unsigned short)90)
#define configMAX_TASK_NAME_LEN                 13
#define configUSE_16_BIT_TICKS                  0
#define configIDLE_SHOULD_YIELD                 1
#define configUSE_TASK_NOTIFICATIONS            1
#define configUSE_MUTEXES                       1
#define configUSE_RECURSIVE_MUTEXES             1
#define configUSE_COUNTING_SEMAPHORES           1
#define configUSE_ALTERNATIVE_API               0 /* Deprecated! */
#define configQUEUE_REGISTRY_SIZE               8
#define configUSE_QUEUE_SETS                    0
#define configUSE_TIME_SLICING                  1
#define configUSE_NEWLIB_REENTRANT              0
#define configENABLE_BACKWARD_COMPATIBILITY     0
#define configNUM_THREAD_LOCAL_STORAGE_POINTERS 5
#define configUSE_APPLICATION_TASK_TAG          0

/* Used memory allocation (heap_x.c) */
#define configFRTOS_MEMORY_SCHEME               3
/* Tasks.c additions (e.g. Thread Aware Debug capability) */
#define configINCLUDE_FREERTOS_TASK_C_ADDITIONS_H 1

/* Memory allocation related definitions. */
#define configSUPPORT_STATIC_ALLOCATION         0
#define configSUPPORT_DYNAMIC_ALLOCATION        1
/*#define configTOTAL_HEAP_SIZE                   0  not used by heap_3.c allocator */
#define configAPPLICATION_ALLOCATED_HEAP        0

/* Hook function related definitions. */
#define configUSE_IDLE_HOOK                     0
#define configUSE_TICK_HOOK                     0
#define configCHECK_FOR_STACK_OVERFLOW          0
#define configUSE_MALLOC_FAILED_HOOK            0
#define configUSE_DAEMON_TASK_STARTUP_HOOK      0

/* Run time and task stats gathering related definitions. */
#define configGENERATE_RUN_TIME_STATS           0
#define configUSE_TRACE_FACILITY                1
#define configUSE_STATS_FORMATTING_FUNCTIONS    1

/* Co-routine related definitions. */
#define configUSE_CO_ROUTINES                   0
#define configMAX_CO_ROUTINE_PRIORITIES         2

/* Software timer related definitions. */
#define configUSE_TIMERS                        1
#define configTIMER_TASK_PRIORITY               17
#define configTIMER_QUEUE_LENGTH                10
#define configTIMER_TASK_STACK_DEPTH            (configMINIMAL_STACK_SIZE * 2)

/* Define to trap errors during development. */
#define configASSERT(x) if((x) == 0) {taskDISABLE_INTERRUPTS(); for (;;);}

/* Optional functions - most linkers will remove unused functions anyway. */
#define INCLUDE_vTaskPrioritySet                1
#define INCLUDE_uxTaskPriorityGet               1
#define INCLUDE_vTaskDelete                     1
#define INCLUDE_vTaskSuspend                    1
#define INCLUDE_vTaskDelayUntil                 1
#define INCLUDE_vTaskDelay                      1
#define INCLUDE_xTaskGetSchedulerState          1
#define INCLUDE_xTaskGetCurrentTaskHandle       1
#define INCLUDE_uxTaskGetStackHighWaterMark     0
#define INCLUDE_xTaskGetIdleTaskHandle          0
#define INCLUDE_eTaskGetState                   0
#define INCLUDE_xTimerPendFunctionCall          1
#define INCLUDE_xTaskAbortDelay                 0
#define INCLUDE_xTaskGetHandle                  0
#define INCLUDE_xTaskResumeFromISR              1

```
### Configuración del DSPI
```C
dspi_master_config_t masterConfig;
masterConfig.whichCtar                                = kDSPI_Ctar0;
masterConfig.ctarConfig.baudRate                      = baudrate;
masterConfig.ctarConfig.bitsPerFrame                  = 8U;
masterConfig.ctarConfig.cpol                          = kDSPI_ClockPolarityActiveHigh;
masterConfig.ctarConfig.cpha                          = kDSPI_ClockPhaseFirstEdge;
masterConfig.ctarConfig.direction                     = kDSPI_MsbFirst;
masterConfig.ctarConfig.pcsToSckDelayInNanoSec        = 1000000000U / baudrate;
masterConfig.ctarConfig.lastSckToPcsDelayInNanoSec    = 1000000000U / baudrate;
masterConfig.ctarConfig.betweenTransferDelayInNanoSec = 1000000000U / baudrate;
masterConfig.whichPcs           = kDSPI_Pcs0;
masterConfig.pcsActiveHighOrLow = kDSPI_PcsActiveLow;

masterConfig.enableContinuousSCK        = false;
masterConfig.enableRxFifoOverWrite      = false;
masterConfig.enableModifiedTimingFormat = false;
masterConfig.samplePoint                = kDSPI_SckToSin0Clock;
uint8_t error = 0;
uint32_t srcClock_Hz = CLOCK_GetFreq(DSPI0_CLK_SRC);
DSPI_MasterInit(SPI0, &masterConfig, srcClock_Hz);
DSPI_MasterTransferCreateHandle(SPI0, &rtosDSPI_handles[0].dspiHandle, rtosDSPI_callback, NULL);
```
### Tareas y configuración
A continuación, se describen las tareas utilizadas para el enfoque propuesto.

|Nombre de Tarea|Nota|Stack size|Parámetro|Prioridad|Descripción|
|:--------:|:--------:|:--------:|:--------:|:--------:|:--------:|
|udpecho_thread|udpecho_thread|DEFAULT|NULL|DEFAULT|Tarea encargada de recibir la información de la nueva alarma, através de una comunicación UPD|
|taskInit|Init DSPI|100|NULL|4|Tarea encargada de inicializar el períferico de salida DSPI0 para la comunicación de la pantalla Nokia LCD|
|update_hours|Update hour|100|NULL|2|Tarea encargada de contabilizar las horas transcurridas|
|update_minutes|Update minutes|100|NULL|2|Tarea encargada de contabilizar los minutos transcurridos|
|update_seconds|Update seconds|100|NULL|2|Tarea encargada de contabilizar los segundos transcurridos|
|timer|Reloj|350|NULL|2|Tarea encargada de recibir la información de los minutos, segundos, horas y conómetro y enviar la información a desplegarse a la LCD por medio del DSPI0|
|alarm|Alarma|350|(void *)dspi_msg|3|Tarea encargada de activa la alarma| 
|cronometer|cronómetro|250|NULL|1|Tarea encargada de contabilizar los minutos, segundos y milisegundos del conómetro. 
### Mutex
En la siguiente tabla se muestras los mutex utilizados, así como la descripción de su uso.
|Mutex|Descrición
|:--------:|:--------:|
|mutexTX/mutexPRINT|Estos mutex fueron utilizados para proteger el recurso del DSPI0 al momento de enviar información a la LCD.
### Grupos de eventos
Para la activación de la alarma se utilizó un grupo de eventos ```evenGroupAlarm``` con los siguientes bits que se activan la hora seteada en la alarma es igual a la hora del reloj: ``` #define TIME_ALARM_BIT_SEG (1<<0),  #define TIME_ALARM_BIT_MIN (1<<1), #define TIME_ALARM_BIT_HRS (1<<2) ```

Por otro lado, para saber si el conometro está en pausa o activo se utilizó un cuarto ```TIME_CRONOMETER_BIT``` que nos permite saber el último estado del cronometro (Pausa o coteo). Esto pasa saber qué hacer cuando la interrupción del SW2 ocurre.

### Semáforos binarios
En la siguiente tabla se muestran los semáforos binarios para la sincronización de tareas, se puede apreciar el nombre del semáforo, así como la tarea que toma el semáforo y la tarea que lo libera:
|Semáforo|Task-xSemaphoreTake|Task-xSemaphoreGive|
|:--------:|:--------:|:--------:|
|semMINUTES|update_minutes|update_seconds|Este semáforo es utilizado para incrementar los minutos.
|semHOURS|update_hours|update_minutes|Este semáforo es utilizado para incrementar las horas.

### QUEUE
La tarea reloj es la encargada de desplegar la información del reloj y del cronómetro, por lo anterior de utilizó una cola ```xQueue``` en la cual las tareas ```update_hours, update_minutes, update_seconds, cronometer``` guardan información de sus tiempos. La tarea ```timer``` saca la información de la cola, donde cada valor es una estructura con la siguiente información; el ```source``` que indica la tarea que agregó dicha estructura y ```value``` que se refiere al valor de horas, segundos, minutos, milisegundos sea el caso, esto nos permite usar un mismo medio de comunicación donde puede escribir diferentes tareas.

![Procesos de la Queue](/image/queueRTOS.png)

### Manejo de interrupciones
De acuerdos a los requerimientos, se pide que el manejo del control de la alarma y cronómetro se realice por medio de interrupciones ISR. En concreto el SW2 y SW3 del k64. Por lo anterior, se realizó la siguiente configuración, así como su handlers.
```C
CLOCK_EnableClock(kCLOCK_PortA);
CLOCK_EnableClock(kCLOCK_PortC);
//SW3 CONFIGURATIONS
PORT_SetPinConfig(PORTA, 4U, &porta10_pinM9_config);
PORT_SetPinInterruptConfig(PORTA, 4, kPORT_InterruptFallingEdge);

NVIC_SetPriority(PORTA_IRQn, 0x3);
EnableIRQ(BOARD_SW_3_IRQ);
GPIO_PinInit(GPIOA, 4, &sw_config);

//SW2 CONFIGURATIONS
PORT_SetPinConfig(PORTC, 6U, &porta10_pinM9_config);
  PORT_SetPinInterruptConfig(PORTC, 6, kPORT_InterruptFallingEdge);

NVIC_SetPriority(PORTC_IRQn, 0x3);
EnableIRQ(BOARD_SW_2_IRQ);
GPIO_PinInit(GPIOC, 6, &sw_config);
```

### Máquina de estado para la ejecución de las tareas del reloj
A continuación, se presenta la lógica en forma de máquina de estados que sigue la implementación del reloj. Para el requerimiento del reloj, se utilizaron 4 tareas ```update_hours, update_minutes, update_seconds y timer``` que utilizan el tiempo de segundos, los semáforos binarios ``` semMINUTES y semHOURS ``` y la cola ```xQueue``` donde se guarda la información de las horas, segundos y milesegundos.
![Proceso del Reloj](/image/st_reloj.png)

Una vez que el calendarizador ejecuta la tarea ```timer``` este empieza a sacar información de la cola para mostrar en el display, puede mandar a mostrar información del reloj o del cronómetro. Al momento de enviar la información al display no es necesario realizar ninguna validación de si el cronómetro esta activo, ya que al momento que el conómetro no este activo nunca habrá en la información del cronómetro.
![Proceso del Reloj](/image/st_reloj3.png)

### Máquina de estado para la ejecución de la tarea alarma
Para el alarma se utilizó un grupo de eventos ```evenGroupAlarm``` que contiene 3 bits para la alarma ```TIME_ALARM_BIT_SEG, TIME_ALARM_BIT_MIN, TIME_ALARM_BIT_HRS```. Cuando el calendarizador ejecuta la tarea ```alarm``` esta valida si los bits del grupo de eventos ```evenGroupAlarm``` están activos, si esto es cierto se muestra en el display la palabra ```ALARMA``` y el ```Backlight``` de la LCD nokia 5110 preden y apaga cada 1 segundo.

![Proceso de la alarm](/image/st_alarma.png)

Un requerimiento para esta funcionalidad es agregar una nueva alarma (hh:mm:ss) desde un conexión UDP usando el puerto 49, para dar solución a este requerimiento se cuenta con la tarea ```udpecho_thread``` la cual permite captura la información de una nueva alarma que se envía por medio de una conexión UPD, actualizando la hora, minutos y segundo de la nueva alarma. Para asignar una nueva alarma se programó un script de python que abre un socket entre una computadora y la tarjeta K64 y envía la información de la nueva alarma. A continuación, se muestra el script desarrollado en python.
```python
import socket
UDP_IP = "192.168.0.102"
UDP_PORT = 49

alarm = input("¿Ingrese nueva alarma en el formato 00:00:00? ")
alarm = alarm.replace(":","")
print(alarm)
print("UDP target IP: %s" %UDP_IP)
print("UDP target PORT: %s" %UDP_PORT)
print("Message: %s" % alarm)
 
sock = socket.socket(socket.AF_INET, # Internet
					socket.SOCK_DGRAM) #UDP
sock.connect((UDP_IP, UDP_PORT))
sock.sendto(alarm.encode(), (UDP_IP,UDP_PORT)) #UDP
 
data, addr = sock.recvfrom(1024)#
print("receive message: %s" % data)
```

### Máquina de estado para la ejecución de la tarea cronómetro
Para la ejecución de la tarea de cronómetro se tiene la siguiente lógica, donde una vez que el calendarizador ejecuta la tarea de cronómetro este valida si el bit TIME_CRONOMETER_BIT en el grupo de eventos ```evenGroupAlarm``` esta activo, si esto es cierto se empieza a contar el tiempo del cronómetro y enviar la información a la cola ```xQueue```.

![Proceso del cronometro](/image/st_cronometer.png)

### Máquina de estado para la manipulación del sistema
En los puntos anteriores se explicó la lógica para la ejecución de las tareas usando semáforos binarios, colas, mutex y grupos de eventos. Ahora para cumplir, la lógica para activar y quitar el cronómetro, limpiar la alarma se debe de cumplir los siguientes requerimientos.
1. Al iniciar el sistema, se inicia con el modo reloj.
2. Estando en el modo reloj, si se activa la alarma se puede limipiar al presionar SW2.
3. Si se presiona SW3 estando en modo reloj, se activa el modo cronómetro.
4. Una vez activo el modo cronómetro, al presionar SW2 el tiempo del cronómetro empieza a contar y entra al modo conteo.
5. Estando en modo conteo, si se presiona SW2 el cronómetro se para y pasa a modo Pausa.
6. Estando en modo Pausa si se presiona SW2 el cronómetro continua en donde se quedó y pasa a modo conteo.
7. Para regresar al modo reloj, se debe de estar en el modo pausa y presionar el SW3.
5. Si la alarma se activa estando en modo conteo, pausa, cronómetro no se podrá limpiar, para limpiar se debe cumplir el punto 7 (regresar al modo reloj), el punto 2 (limpiar alarma).


A continuación, se muestra la máquina de estados hecha para cumplir la lógica de los requerimientos.

![Proceso funcionamiento](/image/st_funcionamiento.png)

## Resultados
A continuación, se presenta un video donde se explica el funcionamiento del sistemas, así como las funcionalidades que cubren que cubren los requerimientos del proyecto solicitado.
[![YouTube](http://i.ytimg.com/vi/XzANA-5TDvc/hqdefault.jpg)](https://www.youtube.com/watch?v=XzANA-5TDvc)


## Autores
Dr. Jaciel David Hernández Reséndiz, jacieldavidhernandez@gmail.com/jaciel.hernandez@cinvestav.mx

Mtr. Julio César García Méndez, jc.garcia@cinvestav.mx 

## License
README is available under the MIT license. See the [LICENSE](LICENSE) file for more info.



<!-- Links -->
[License]:https://img.shields.io/badge/license-MIT-383838.svg

