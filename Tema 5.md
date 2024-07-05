# Tema 5


## Introducción

- Dispositivos de E/S o periféricos: permiten la comunicación del computador con su entorno
- Las conexiones entre los periféricos, el procesador y la memoria se llevan a cabo mediante los buses
- Para realizar la conexión entre la CPU y los periféricos se necesita de módulos de E/S debido a que:
    - Velocidad de transferencia periféricos << velocidad CPU o memoria
    - Ancho de palabra no coincide con ancho de palabra CPU
    - Existen una amplia variedad de dispositivos &rarr; muy distintas formas de funcionamiento
      //Foto de la 4

## Buses

- Bus: vía de comunicación que conecta 2 o más dispositivos
- Es un medio de transmisión compartido
- En un mismo instante de tiempo se permite la recepción por varios dispositivos, pero solamente uno puede transmitir &rarr; se necesitan mecanismos de arbitraje
  // foto de la 5

## Módulos E/S

- Actúan como interfaz entre la CPU y uno o más dispositivos periféricos
    - Controlador de E/S: módulo de E/S básico
    - Procesador de E/S: módulo de E/S con capacidad para ejecutar programas
- Funciones:
    - Control y temporización
    - Comunicación con el procesador
    - Comunicación con los dispositivos
    - Almacenamiento temporal de datos
    - Detección de errores

## Direccionamiento de los módulos de E/S

- La comunicación con los dispositivos de E/S se realiza mediante los registros
- Dos aproximaciones para los registros:
    - E/S asignada en memoria:
        - Un único espacio de direcciones en el sistema
        - Parte del espacio de direcciones se reserva para E/S
        - Mismas instrucciones para acceso a memoria y a dispositivos de E/S &rarr; mismos modos de direccionamiento disponibles
    - E/S aislada:
        - Dos espacios de direcciones diferentes: memoria y E/S
        - Se utiliza una señal de control (M/IO) para indicar si la dirección en el bus de direcciones es de E/S o de memoria, o bien se usa un bus de E/S propio
        - Instrucciones especiales para operaciones de E/S

## Gestión de la E/S

La comunicación entre los módulos de E/S y el procesador se puede hacer utilizando:
- E/S programada: La CPU toma la iniciativa de contactar con el dispositivo de E/S y gestiona las transferencias de datos
- E/S mediante interrupciones: Los dispositivos de E/S realizan una petición de interrupción al procesador
- Acceso directo a memoria: Realizada por un controlador especializado que se encarga de la transferencia de datos sin intervención del procesador

### E/S programada

- CPU tiene control completo y directo sobre la operación de E/S
    - Envío de comandos de lectura y escritura
    - Comprobación del estado del dispositivo
    - Transferencia de datos
- Encuesta (polling): cada vez que la CPU envía un comando al dispositivo de E/S espera a que la operación sobre su estado
- Velocidad de procesador &rarr; Velocidad E/S &rarr; gran desperdicio de ciclos de reloj en las encuestas
- Es la técnica de gestión más simple y más ineficiente desde el punto de vista del procesador

## E/S con interruptores

- El dispositivo avisa al procesador cuando está lista para la transferencia
- El aviso se produce activando una línea de petición de interrupción
- Interrupción: suceso inesperado que afecta al procesador. Al contrario que las excepciones:
    - Son suceso externos
    - Se pueden producir en cualquier momento
- La E/S con interrupciones permite al procesador trabajar en otro proceso mientras se espera por una operación de E/S

- Pasos en el procesamiento de una interrupción:
    - El dispositivo envía una señal de interrupción al procesador
    - El procesador termina la ejecución de la instrucción en curso
    - El procesador envía una señal de reconocimiento al dispositivo que originó la interrupción
    - El procesador salva el PC y los registros necesarios en la pila
    - El procesador carga en el PC la dirección de la rutina de tratamiento de la interrupción solicitada (ISR, Interrupt Service Routine) y ejecuta esa rutina
    - Una vez que la rutina termina, el procesador restaura el estado guardado en la pila y retorna al programa que se estaba ejecutando
- Si más de un dispositivo puede generar la petición de una interrupción habrá que:
    - Identificar el dispositivo que generó la interrupción
    - Establecer prioridades
    - Proteger los servicios de interrupción de otras interrupciones
### Identificación del dispositivo
- La identificación del dispositivo se puede realizar mediante:
    - Líneas de interrupción dedicadas: limita el número de dispositivos que se pueden conectar
    - Líneas de interrupción compartidas: la identificación del dispositivo se puede hacer usando:
        - Encuesta software: se consulta el valor del registro de estado de los diferentes dispositivos
        - Encadenamiento: la línea de reconocimiento de interrupción se conecta en cadena a los distintos dispositivos
        - Arbitraje de bus: solo con un módulo puede activar la línea de interrupción en un instante dado.

- Encuesta software
    - Cuando se activa la línea de interrupción la CPU ejecuta una rutina genérica de manejo de interrupciones
    - La rutina se encarga de interrogar a los diferentes dispositivos comprobando sus registros de estado
    - Una vez identificado el dispositivo se ejecuta la rutina de tratamiento específica
- Encadenamiento:
    - Cuando se activa la línea de interrupción, la CPU activa la línea de reconocimiento de interrupción que se conecta en cadena a todos los módulos de E/S
    - El primer módulo que recibe la señal y ha generado una interrupción deja de propagar la señal y envía la dirección de su rutina de tratamiento por el bus de datos

- Diferentes modos de direccionamiento para especificar la dirección:
    - Direccionamiento absoluto: se manda la dirección completa
    - Direccionamiento relativo: se envía parte de la dirección
    - Direccionamiento relativo indirecto (interrupciones vectorizadas): manda la posición relativa a una tabla de direcciones residente en memoria
- Arbitraje de bus:
    1. El módulo de E/S se hace con el bus antes de activar la línea de interrupción
    2. La CPU activa la línea de reconocimiento de interrupción
    3. El módulo de E/S sitúa la dirección de la rutina de tratamiento en el bus de datos
### Prioridades

- Líneas de interrupción dedicadas: el procesador selecciona la línea con mayor prioridad
- Líneas de interrupción compartidas:
    - Consulta software: el orden en el que se consulta establece la prioridad
    - Encadenamiento: el orden en que se encadenan establece la prioridad
    - Arbitraje de bus: en el propio mecanismo de arbitraje se pueden establecer prioridades
### Interrupciones anidadas
- Sistema de interrupciones único: La ejecución del programa de servicio de una interrupción continua hasta el final antes de aceptar una segunda petición de interrupción
- Sistema de interrupciones multinivel
    - A cada causa de interrupción se le asigna un nivel de prioridad
    - Una interrupción se atiende si su nivel es superior al de la interrupción cuyo programa de servicio se está ejecutando
- La forma de ignorar interpretaciones es mediante el enmascaramiento
- Las excepciones más críticas no son enmascarables
- Para el manejo de interrupciones y excepciones el MIPS utiliza el coprocesador 0
    - Registro EPC (*Exception Program Counter*): dirección de la instrucción que se estaba ejecutando cuando se produjo la excepción
    - Registro Status: máscara de interrupciones y bits de autorización
    - Registro Cause: motivo de la excepción o interrupción

## Acceso directo a memoria
- E/S mediante interrupciones ineficientes para grandes transferencias de datos -> Acceso directo a memoria
- El acceso directo a memoria (*DMA, Direct Memory Access*) se realiza con un controlador especializado que transfiere los datos entre dispositivos y memoria sin intervención del procesador

Pasos en una transferencia DMA:
1. El procesador inicia el DMA proporcionando:
    - Identidad del dispositivo
    - Operación a realizar
    - Dirección de memoria fuente o destino de los datos a transferir
      Número de bytes a transferir
2. El DMA inicia la operación:
    - Obtiene el acceso al bus e inicia la transferencia
    - Si se requiere más de una transferencia por el bus el controlador de DMA se encarga de realizarlas sin molestar al procesador
- 3º Una vez finalizada la transferencia el DMA interrumpe al procesador

- Opciones para el acceso al bus por parte del DMA:
    - Por **ráfagas**: cuando el DMA toma el control del bus no lo libera hasta haber transmitido todo el bloque de datos
    - Por **robo de ciclos**: el DMA retiene el control del bus solo un ciclo, transmite una palabra y lo libera
    - DMA **transparente**: solo roba ciclos cuando la CPU no usa el bus

## Procesadores de E/S

- Procesador de E/S: controlador inteligente que permite la ejecución de órdenes de E/S sin intervención de la CPU