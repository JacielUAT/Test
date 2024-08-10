
<!-- Para abrir el preview en Atom: ^ (control) + shift + M -->

# Proyecto final del curso de Sistemas operativos en tiempo Real.
[![platform][License]][License]

## Table of Contents
- [Requirements](#requirements)
- [Installation](#installation)
- [Download](#download)
- [Author](#author)
- [License](#license)
- [Otros Ejemplos](#otros-ejemplos)
  - [Bloques de códigos](#bloques-de-códigos)
  - [Resaltar texto](#resaltar-texto)
  - [Badges](#badges)
  - [Imagen](#imagen)
  - 
## Introducción
### Objetivo
En este proyecto se implementara un reloj con alarma y cronómetro, mediante el uso de FreeRTOS, generando múltiples tareas y usando IPC para sincronizarlas.
<!-- Para crear un índice -->

### Requerimientos
El sistema tendrá las siguientes funcionalidades:
* Hora (horas, minutos y segundos)
* Cronómetro (minutos, segundo y milisegundos, con una resolución de 25 milisegundos)
* Alarma
  
## Materiales
A continuación, se presenta los difentes componenete utilizados para dar solución a los solicitado por el proyecto de RTOS.

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

## Enfoque propuesto.
### Configuración de FreeRTOS
La configuración utilizada en FreeRTOS es la siguiente-
```#define configUSE_PREEMPTION                    1
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
```
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
### Tareas y configuración.
A continuación se describen las tareas utilizadas para el enfoque propuesto.
En la siguiente tabla se muestra el hardware utilizado.

|Nombre de Tarea|Nota|Stack size|Parámetro|Prioridad|Descripción|
|:--------:|:--------:|:--------:|:--------:|:--------:|:--------:|
|udpecho_thread|udpecho_thread|DEFAULT|NULL|DEFAULT|Tarea encargada de recibir la información de la nueva alarma, através de una comunicación UPD|
|taskInit|Init DSPI|100|NULL|4|NULL|Tarea encargada de inicializar el períferico de salida DSPI0 para la comunicación de la pantalla Nokia LCD|
|update_hours|Update hour|100|NULL|2|Tarea encargada de contabilizar las horas transcurridas|
|update_minutes|Update minutes|100|NULL|2|Tarea encargada de contabilizar los minutos transcurridos|
|update_seconds|Update seconds|100|NULL|2|Tarea encargada de contabilizar los segundos transcurridos|
|timer|Reloj|350|NULL|2|Tarea encargada de recibir la información de los minutos, segundos, horas y conómetro y enviar la información a desplegarce a la LCD por medio del DSPI0|
|alarm|Alarma|350|(void *)dspi_msg|3|Tarea encargada de activa la alarma| 
|cronometer|Cronometro|250|NULL|1|Tarea encargada de contabilizar los minutos, segundos y milisegundos del conómetro. 
### Mutex
En la siguiente tabla se muestras los mutex utilizados, así como la descripción de su uso.
|Mutex|Descrición
|:--------:|:--------:|
|mutexTX/mutexPRINT|Estos mutex fueron utilizados para proteger el recurso del DSPI0 al momento de enviar información a la LCD.
### Grupos de eventos
Para la activación de la alarma se utilizó un grupo de eventos evenGroupAlarm con los siguientes bits que se activan la hora seteada en la alarma es igual a la hora del reloj.
```
#define TIME_ALARM_BIT_SEG (1<<0)
#define TIME_ALARM_BIT_MIN (1<<1)
#define TIME_ALARM_BIT_HRS (1<<2)
```
Por otro lado, para saber si el conometro está en pausa o activo se utilizó un cuarto ``` TIME_CRONOMETER_BIT``` que nos permite saber el último estado del cronometro (Pausa o coteo). Esto pasa saber que hacer cuando la interrupción del SW2 ocurre.
### Semáforos binarios.
Para la sincronización de las 



## Installation
Clonar el repositorio, luego en una terminal ejecutar `pod install` para instalar las dependencias del proyecto. Por último abrir `NAME.xcworkspace` y compilar la App.

## Download
App disponible en [iTunes][AppStore] para su descarga.

## Author
Matías Correnti, [@matCorrenti][myTwitter].

## License
README is available under the MIT license. See the [LICENSE](LICENSE) file for more info.




## Otros Ejemplos

### Bloques de códigos
```swift
func texto(texto: String = "default") {
  //Comentario
  let tex: String = "Python syntax highlighting"
  let tex2: String = texto
}
```
```swift
let selector = #selector(viewDidLoad)
view.backgroundColor = .red
let toView = context.view(forKey: .to)
let view = UIView(frame: .zero)
```
~~~ swift
let text: String = "Texto"
~~~


### Resaltar texto
> *Cursiva*
>> **Negrita**
>>> ***Negrita-Cursiva***

(Se puede usar el signo `_` en lugar de `*`).


### Badges
[![AppStore][appStoreBagge]][appStore]
[![Twitter Follow][twitter]][myTwitter]
[![TestXc][testXc]][testXc]
[![CocoapodsDocs][cocoDocs]][cocoDocs]
[![Tag][tag]][tag]
[![Release][release]][release]
[![IssuesOpen][issuesOpen]][issuesOpen]
[![LicenseGitHub][licenseGitHub]][licenseGitHub]
[![DowEXTClass][dowEXTClass]][dowEXTClass]
[![CommitEXT][commitEXT]][commitEXT]


### Imagen
![Imagen de logo][logo]




<!-- Links -->
[myTwitter]:http://twitter.com/matCorrenti
[iOS_9.0]:https://img.shields.io/badge/iOS-≥_9.0-5658FE.svg?colorA=5658FE
[swift_3.0]:https://img.shields.io/badge/Swift-≥_3.0-EF5138.svg?colorA=EF5138
[xcode_3.2]:https://img.shields.io/badge/Xcode-≥_3.2-2A92F4.svg?colorA=2A92F4
[Platform]:https://img.shields.io/badge/platform-ios-lightgrey.svg
[License]:https://img.shields.io/badge/license-MIT-383838.svg


<!-- Otros -->
[appStore]:https://itunes.apple.com/ar/app/luteranos/id1137428395
[appStoreBagge]:https://img.shields.io/badge/Download_App-Luteranos-1C5FAD.svg
[twitter]:https://img.shields.io/twitter/follow/matCorrenti.svg?style=social&label=Follow&maxAge=3600
[testXc]:https://img.shields.io/badge/Xcode-≥_3.2-2A92F4.svg?colorA=0873A4
[cocoDocs]:https://img.shields.io/cocoapods/metrics/doc-percent/EXTClass.svg
[tag]:https://img.shields.io/github/tag/Saitco/EXTClass.svg
[release]:https://img.shields.io/github/release/Saitco/EXTClass.svg
[issuesOpen]:https://img.shields.io/github/issues/Saitco/README.svg
[licenseGitHub]:https://img.shields.io/github/license/Saitco/README.svg
[dowEXTClass]:https://img.shields.io/github/downloads/Saitco/README/total.svg
[commitEXT]:https://img.shields.io/github/commits-since/Saitco/EXTClass/0.3.2.svg


<!-- Links Imagenes -->
[logo]:https://github.com/Saitco/README/blob/master/img/rosaL.png "Icono de la App"
