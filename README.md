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


![Foto de target 0 y rutina](https://github.com/aholguinr/Lab3_Robotica_Caipa_Holguin/blob/main/imagenes/home.png?raw=true)


Otra trayectoria para definir es la que genere la rutina de escritura, para lo cual se utilizará una pieza que contiene las letras a dibujar (AJA), y en este caso solo se va a utilizar la letra ‘J’. Se ubica la pieza en el espacio de trabajo para generar un objeto de trabajo, ubicada con origen en (500,200,0) y se hace uso del sistema de creación de sistema de coordenadas por el método de tres puntos, seleccionando desde la esquina inferior derecha y girando entre esquinas en sentido antihorario. Con esto el sistema coordenado queda definido con orientación del eje ‘z’ normal a la superficie. Y luego se hace uso de trayectoria automática para crear los targets que definen la letra y luego se ajusta dichos targets para que se orienten igual que el sistema coordenado del objeto de trabajo, y no se presenten errores sobre los movimientos del robot entre targets (tipo lineal). 


![Foto definición de la J](https://github.com/aholguinr/Lab3_Robotica_Caipa_Holguin/blob/main/imagenes/J.png?raw=true)

Por último, se requiere definir la rutina que llevará el robot hacia la posición de M/D de la herramienta. Para ello existen varias formas, primero se verá la más sencilla y luego se desarrollará sobre las otras. La manera más sencilla de definir una posición adecuada real para M/D es simplemente tomar el brazo robótico en la vida real y ubicarlo en una posición que permita al operario montar y desmontar la herramienta de trabajo, teniendo en cuenta las condiciones de seguridad adecuadas (el robot debe estar detenido completamente, preferiblemente en parada de emergencia, antes de que el operario se acerque a una distancia prudente y pueda montar la herramienta para verificar que en dicha posición es posible).

![Foto rutina montaje](https://github.com/aholguinr/Lab3_Robotica_Caipa_Holguin/blob/main/imagenes/montR.png?raw=true)

Una vez realizado esto, del Flex Pendant se extraen los ángulos y se define un target de tipo articular en RobotStudio y se crea una rutina vacía, a la cual se le lleva inicialmente el target de ‘’Home’’, dado que la idea inicial es que el robot vaya a esta posición antes de dirigirse a la posición de M/D, y luego se añade la posición definida, teniendo en cuenta que ambos movimientos serán de tipo articular. 

![Foto rutina montaje RS](https://github.com/aholguinr/Lab3_Robotica_Caipa_Holguin/blob/main/imagenes/mont.png?raw=true)



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

## Identificaión del panel
Debido a que se usará el panel de botones que tiene el controlador IRC5 del laboratorio, es necesario hacer una identificación de este. La disposición del panel es la siguiente:

![Foto panel](https://github.com/aholguinr/Lab3_Robotica_Caipa_Holguin/blob/main/imagenes/panel.png?raw=true)

Con la disposición ya entendida, se procede a realizar algo que facilitará posteriormente el proceso de asignación de señales de entrada y salida y es que, las señales se pueden nombrar como uno desee cuando se trabaja en el RobotStudio, sin embargo, a la hora de pasar el código al robot, es necesario que las señales lleven el mismo nombre que tienen en el controlador de este, por lo que es mejor inicialmente identificarlas de forma adecuada para evitar futuros errores en el trabajo de las señales. Para ello hay que tener en cuenta dos cosas, primero, los botones y pilotos que están conectados del panel, para ello simplemente se abre el panel y se ve la disposición de los cables, observando cuales de ellos están conectados. Para el presente caso se tienen los siguientes botones y pilotos conectados:


![Foto señalando en el panel](https://github.com/aholguinr/Lab3_Robotica_Caipa_Holguin/blob/main/imagenes/panelS.png?raw=true)

Luego, se debe identificar el nombre que tienen ellos en el controlador, para ello se va al Flex Pendant (en la vida real) y se va al menú principal, en la opción de ‘’Inputs and Outputs’’, en donde se pueden ver todas las entradas y salidas, y simplemente se va a ‘’View’’ y se selecciona ‘’Digital Inputs’’ para ver cuales son los nombres asignados a los botones disponibles, ya que estos actúan como entradas, y ‘’Digital Outputs’’ para ver cuales son los nombres asignados a los pilotos disponibles, ya que estos actúan como salidas. Los nombres para los botones son DI_01 y DI_02, y los nombres para los pilotos son DO_01 y DO_03. En este caso se van a usar dos botones y dos pilotos como lo indicado previamente. 

Con la identificación del panel ya realizada, es posible proceder a la definición de señales y asignación de condicionales con estas para ejecutar lo requerido. 

## Definición de señales
Para poder configurar las señales se deben ubicar sobre el controlador virtual de RobotStudio, ya que es similar a lo que sucede en el controlador real, y es necesario que estén en código para su correcto funcionamiento en simulación.

Primero se debe abrir la pestaña de Controlador>Configuración>I/O System. Al dar doble clic se abre la pestaña que almacena las entradas y salidas del controlador, incluyendo las señales que son las de interés para el programa.

![Foto configuración](https://github.com/aholguinr/Lab3_Robotica_Caipa_Holguin/blob/main/imagenes/1.png?raw=true)

Se da clic en la que dice Signal, lo cual despliega todas las señales disponibles del controlador, se da clic derecho y Nuevo Signal lo cual muestra el menú de editor de instancias, se escribe el nombre, en este caso vamos a crear la primera salida digital, por lo cual se escribe ‘’DO_01‘’, y se cambia el tipo de señal a ‘’Digital Output’’, y se da clic en aceptar. 

![Foto panel digital](https://github.com/aholguinr/Lab3_Robotica_Caipa_Holguin/blob/main/imagenes/2.png?raw=true)

> Nota: Saldrá una advertencia que indica que las señales creadas no serán útiles o activas hasta reiniciar el controlador. 

Posteriormente se crean de igual forma las otras tres señales y se reinicia el controlador para que entren en funcionamiento.

Ahora bien, para hacer uso de las señales, simplemente se deben crear unas instrucciones especiales llamadas instrucciones de acción. El problema con dichas funcionalidades es que son un poco simples, y a la hora del código es más complicado trabajar con ellas, por lo cual primero se explicará el procedimiento sencillo sobre las rutinas ya creadas, y luego se va a observar cómo escribirlas en código y hacer un código más complejo a fin de cumplir los requerimientos solicitados. Con este procedimiento inicial se crean automáticamente instrucciones en código que ejecutan lo esperado y es posible modificar como cualquier código RAPID.

Para crear las instrucciones de acción de forma sencilla, se va a Simulación>Simulador de E/S.

![Foto E/S](https://github.com/aholguinr/Lab3_Robotica_Caipa_Holguin/blob/main/imagenes/3.png?raw=true)

Allí se debe verificar que se esté en el controlador, y se debe crear una lista de usuario que contenga las señales recién creadas para poder utilizarlas fácilmente. Para ello se da clic en Filtro>Lista de usuarios, y luego a la derecha en editar listas. Se da en nueva lista, se le asigna un nombre y en la tabla de señales se seleccionan las creadas y de clic en la flecha central para ubicarlo en la tabla de señales de la lista y se da en aceptar. 

![Foto Lista](https://github.com/aholguinr/Lab3_Robotica_Caipa_Holguin/blob/main/imagenes/4.png?raw=true)

Ahora debería verse le panel de E/S de la siguiente manera:

![Foto panel E/S activo](https://github.com/aholguinr/Lab3_Robotica_Caipa_Holguin/blob/main/imagenes/5.png?raw=true)

Ahora nuevamente se vuelve a la pestaña de Posición Inicial>Trayectorias y Puntos, y en el árbol de procesos se abre el controlador y las trayectorias, y se va la trayectoria definida previamente como Main. En esta se da clic derecho y se da en ‘’Insertar instrucción de acción’’.  En este caso se van a utilizar dos tipos de instrucciones de acción, la instrucción ‘’WaitDI’’, la cual espera a que la señal de entrada cambie de estado para continuar ejecutando el Main, y la instrucción ‘’SetDO’’, la cual modifica una señal de salida asignando un estado específico cuando se llega a dicha instrucción en el Main. Inicialmente vamos a crear una ‘’WaitDI’’ con la señal DI_01, ya que esta está asignada al botón que hace que el robot vaya a la posición M/D de herramienta, para ello se busca la opción de WaitDI en el menú de opciones de acción (Plantilla de instrucciones), se selecciona la señal de interés (DI_01) y luego se selecciona el estado de interés en ‘Value’, que en este caso es ‘1’, ya que se pretende que funcione cuando se perciba que se presionó el botón, es decir, cuando se tenga el estado lógico en alto. La instrucción se crea con un icono de rayo que permite diferenciar que es de acción, y se sube hasta el primer lugar en la lista para que sea lo primero que se ejecute. 

![Foto edición acción](https://github.com/aholguinr/Lab3_Robotica_Caipa_Holguin/blob/main/imagenes/6.png?raw=true)

Ahora bien, se reinicia el controlador por seguridad, y cuando esto se complete, se exporta a RAPID con Posición Inicial>Sincronizar>Sincronizar con RAPID, y ya esto debería crear el código en RAPID, y para comprobar se ejecuta el Main, y este no debería continuar hasta en el panel E/S dar clic sobre el botón de Entradas>DI_01.

## Señales en el código RAPID y condicionales

Ahora, como se mencionó anteriormente, es preferible configurar dichas instrucciones en el código Main sobre RAPID, para lo cual se va a la pestaña RAPID y luego en el árbol se busca el Module1, que es dónde se almacena el código, y luego se busca la rutina ‘’PROC main()’’.  Luego simplemente se escriben las instrucciones:

```
SetDO DO_0X, #;
```

En donde la X se cambia por el número respectivo de la señal, y el # se cambia por el estado que se le asigna a la señal. Y se tiene:

```
WaitDI DI_0X, 1;
```

Y aquí se hace de la misma forma, pero en este caso siempre es 1 el valor de interés, debido a que se trata de un botón.

Luego de esto, se crean todas las líneas de código respectivas y se ubican sobre el Main.

## Resultado final
Con todo y lo anterior, la idea es que el código resulte como:

```
PROC main()
        SetDO DO_03,1;
        
        WaitDI DI_02,1;
        SetDO DO_03,0;
        SetDO DO_01,1;
        ToolQ;
        SetDO DO_01,0;
        SetDO DO_03,1;
        
        WaitDI DI_01,1;
        SetDO DO_03,0;
        SetDO DO_01,1;
        ToHome;
        Security;
        Path_40;
        ToHome;
        SetDO DO_01,0;
        SetDO DO_03,1;
        
        WaitDI DI_02,1;
        SetDO DO_03,0;
        SetDO DO_01,1;
        ToolQ;
        SetDO DO_01,0;
        SetDO DO_03,1;
        
        WaitDI DI_01,1;
        SetDO DO_03,0;
        SetDO DO_01,1;
        ToHome;
        SetDO DO_01,0;
        SetDO DO_03,1;
    ENDPROC
```

Ahora bien, en la vida real se supone que el robot, se enciende el piloto rojo que indica que el robot está quieto, se presiona el botón para enviar el robot a M/D, y se apaga el piloto rojo y se enciende el piloto verde que indica que el robot está en movimiento, procedimiento llamado ‘’Cambio de pilotos’’ de aquí en adelante. Luego, el operario debe instalar la herramienta y cuando se retire se puede presionar el botón destinado a las demás funcionalidades, ocurre el cambio de pilotos hasta llegar a la posición de Home, luego a la de seguridad y luego se ejecuta la rutina de trazado y se retorna a la posición de seguridad y a Home, y al llegar allí ocurre de nuevo cambio de pilotos. Se presiona el botón para M/D, ocurre cambio de pilotos hasta que llega a la posición para desmontar, se cambian de nuevo indicando que es seguro para que el operario proceda a retirar la herramienta y luego se pulsa el botón de las otras funcionalidades para enviar el robot a Home. Por último, el robot se mueve hasta Home, hay cambio de pilotos y se termina toda la rutina. 

En el repositorio se tiene el video que muestra el robot en funcionamiento en el main.

