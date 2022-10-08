# Laboratorio 3 de Robótica
## Universiad Nacional de Colombia
## 2022-2
***
### Autores
- Andrés Holguín Restrepo 
- Julián Andrés Caipa Prieto
### Profesores encargados
- Ing. Ricardo Emiro Ramírez H.
- Ing. Jhoan Sebastian Rodriguez R.
***
## Introducción
El laboratorio #3 busca generar un acercamiento a la programación de señales de entrada y salida asociadas a componentes reales involucrados al controlador de un sistema robotizado, además de implementar tareas interactuando con dichos componentes para facilitar la operación del robot y generar espacios de trabajo más sencillos para el operario.

Se tienen los siguientes resultados de aprendizaje:
-	Utilizar diversas funciones de RAPID	.
-	Utilizar el modulo de entradas y salidas digitales dispuesto en el controlador IRC5.
	
El trabajo de esta práctica se desarrollará sobre dos ejes temáticos:
-	Programación intermedia en RAPID: Manejo de bucles y condicionales.
-	Manejo del módulo de Entradas y salidas digitales: Comunicaciones por señales digitales.

La idea principal del laboratorio es programar dos señales de entrada en dos botones de la caja del controlador, uno que cumpla la función de situar el robot con movimientos de acercamiento para montaje/desmontaje de herramienta, y otro para situar el robot en posición de trabajo o posición ‘’Home’’; y además programar dos señales de salida en dos pilotos de la caja, uno que indique que el robot está ejecutando una rutina, y otro que indique que el robot está quieto y es seguro para montaje/desmontaje de herramienta.
 
La idea principal del laboratorio es programar dos señales de entrada en dos botones de la caja del controlador, uno que cumpla la función de situar el robot con movimientos de acercamiento para montaje/desmontaje de herramienta (de ahora en adelante M/D), y otro para situar el robot en posición de trabajo o posición ‘’Home’’; y además programar dos señales de salida en dos pilotos de la caja, uno que indique que el robot está ejecutando una rutina, y otro que indique que el robot está quieto y es seguro para M/D  de herramienta.

## Definición de rutinas
Debido a que parte del ejercicio de programación es aplicar una trayectoria con el robot que ejecute una rutina de escritura, y además es requerido programar otro par de rutinas, una que deje todos los ángulos articulares en cero, como posición ‘’Home’’, y otro que acerque a una posición para montar y desmontar la herramienta, así pues, el primer paso es formular las rutinas. 
Para la rutina de dirigir a ‘’Home’’ simplemente se crea un target que ubique todas las articulaciones del robot en 0 grados. Luego se crea una rutina de movimiento de tipo articular que guíe el robot desde la posición actual hasta dicho target. Este target es de tipo posición de ejes, por lo cual simplemente se ajusta el robot y se almacena su posición con todas las articulaciones en 0. 

Foto de target 0 y rutina.

Otra trayectoria para definir es la que genere la rutina de escritura, para lo cual se utilizará una pieza que contiene las letras a dibujar (AJA), y en este caso solo se va a utilizar la letra ‘J’. Se ubica la pieza en el espacio de trabajo para generar un objeto de trabajo, ubicada con origen en (500,200,0) y se hace uso del sistema de creación de sistema de coordenadas por el método de tres puntos, seleccionando desde la esquina inferior derecha y girando entre esquinas en sentido antihorario. Con esto el sistema coordenado queda definido con orientación del eje ‘z’ normal a la superficie. Y luego se hace uso de trayectoria automática para crear los targets que definen la letra y luego se ajusta dichos targets para que se orienten igual que el sistema coordenado del objeto de trabajo, y no se presenten errores sobre los movimientos del robot entre targets (tipo lineal). 

Foto definición de la J

Por último, se requiere definir la rutina que llevará el robot hacia la posición de M/D de la herramienta. Para ello existen varias formas, primero se verá la más sencilla y luego se desarrollará sobre las otras. La manera más sencilla de definir una posición adecuada real para M/D es simplemente tomar el brazo robótico en la vida real y ubicarlo en una posición que permita al operario montar y desmontar la herramienta de trabajo, teniendo en cuenta las condiciones de seguridad adecuadas (el robot debe estar detenido completamente, preferiblemente en parada de emergencia, antes de que el operario se acerque a una distancia prudente y pueda montar la herramienta para verificar que en dicha posición es posible).

Foto posición

Una vez realizado esto, del Flex Pendant se extraen los ángulos y se define un target de tipo articular en RobotStudio y se crea una rutina vacía, a la cual se le lleva inicialmente el target de ‘’Home’’, dado que la idea inicial es que el robot vaya a esta posición antes de dirigirse a la posición de M/D, y luego se añade la posición definida, teniendo en cuenta que ambos movimientos serán de tipo articular. 

Foto rutina montaje

Ahora bien, otra forma posible es mover el robot en RobotStudio y buscar una posición aproximada sobre la cuál se sabe que será posible realizar el M/D, teniendo en cuenta las dimensiones del espacio real de trabajo, y definir de igual forma un target articular y la rutina, pero esto no es tan recomendado, debido a que puede resultar una posición no adecuada. 

> Para más información sobre la creación de targets, rutinas, y la corrección de errores, véase el laboratorio 1, disponible en: [Lab1AJA] (https://github.com/jcaipap/Lab1_Robotica_Caipa_Holguin)

Con las rutinas ya definidas, es prudente crear un Main y añadirlas, con eso a la hora de trabajar el código RAPID, será más sencillo debido a que todo ya estaría sobre la rutina principal de ejecución y solo se procede a escribir la parte de las señales de entrada y salida. Esto es bastante sencillo, se crea una rutina vacía y se le cambia el nombre a ‘Main’, y esta debería cambiar su logo de rutina usual a uno similar al de la interfaz de RAPID. Luego se da clic derecho sobre esta rutina y se selecciona ‘’Insertar llamada a procedimiento’’ y se introducen las rutinas en el orden que se desean ejecutar, en este caso será:
1. Posición M/D.
2. Home
3. Rutina de dibujo
4. Home
5. Posición M/D
6. Home

Resultando finalmente el Main como se puede apreciar en el siguiente código:
```
PROC main()
        ToolQ;
        ToHome;
        Security;
        Path_40;
        ToHome;
        ToolQ;
        ToHome;
    ENDPROC
```

Finalmente se ejecuta la rutina Main para verificar su correcto funcionamiento antes de proceder al resto del trabajo.

Video main

## Identificaión del panel
Debido a que se usará el panel de botones que tiene el controlador IRC5 del laboratorio, es necesario hacer una identificación de este. La disposición del panel es la siguiente:

Foto panel

Con la disposición ya entendida, se procede a realizar algo que facilitará posteriormente el proceso de asignación de señales de entrada y salida y es que, las señales se pueden nombrar como uno desee cuando se trabaja en el RobotStudio, sin embargo, a la hora de pasar el código al robot, es necesario que las señales lleven el mismo nombre que tienen en el controlador de este, por lo que es mejor inicialmente identificarlas de forma adecuada para evitar futuros errores en el trabajo de las señales. Para ello hay que tener en cuenta dos cosas, primero, los botones y pilotos que están conectados del panel, para ello simplemente se abre el panel y se ve la disposición de los cables, observando cuales de ellos están conectados. Para el presente caso se tienen los siguientes botones y pilotos conectados:

Foto señalando en el panel

Luego, se debe identificar el nombre que tienen ellos en el controlador, para ello se va al Flex Pendant (en la vida real) y se va al menú principal, en la opción de ‘’Inputs and Outputs’’, en donde se pueden ver todas las entradas y salidas, y simplemente se va a ‘’View’’ y se selecciona ‘’Digital Inputs’’ para ver cuales son los nombres asignados a los botones disponibles, ya que estos actúan como entradas, y ‘’Digital Outputs’’ para ver cuales son los nombres asignados a los pilotos disponibles, ya que estos actúan como salidas. Los nombres para los botones son XXXXXXXXXXXX y los nombres para los pilotos son XXXXXXXXX. En este caso se van a usar dos botones y dos pilotos como lo indicado previamente. 

Con la identificación del panel ya realizada, es posible proceder a la definición de señales y asignación de condicionales con estas para ejecutar lo requerido. 
