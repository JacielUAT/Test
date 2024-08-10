
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
```swift
colocar configuración de FreeRTOS
}

### Tareas y configuración.
A continuación se describen las tareas utilizadas para el enfoque propuesto.
En la siguiente tabla se muestra el hardware utilizado.
|Nombre de Tarea|Nota|Prioridad|Stack|Parámetro|Descripción|
|udpecho_thread|:--------:|:--------:|:--------:|:--------:|:--------:|
|||:--------:|:--------:|:--------:|:--------:|

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
