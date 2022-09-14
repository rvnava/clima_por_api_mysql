# flow5.1-g10
Quinto ejercicio adicional de flujos pormedio de Node-RED. En este caso se obtendrá la información de temperatura y humedad por medio del API de OpenWeatherMap, la información obtenida se irá guardando en la base de datos de MySQL, además se cambiarán los graficos los cuales se harán con Grafana.

## Introducción
Este quinto flujo realizado con [Node-RED](https://nodered.org/), el cual aparte del  uso de los nodos "MQTT", "JSON", "function", "gauge" y "template", se guardarán los datos en la base de datos recibidos por medio del nodo MQTT en MySQL posteriormente se mostrar en una interfaz gráfica hecha en Grafana. 

Para esta práctica se usarán los siguientes nodos:

 - MQTT. Se usa la obtener los mensajes que se envíen a los servidores localhost (pruebas en servidor local) y broker.hivemq.com (pruebas en servidor en internet)
 - JSON. Se usa darle formato al mensaje recibido por el nodo MQTT
 - function. Se usa para filtrar parte del mensaje recibido
 - gauge. Se usa para visualizar en forma especifica los valores recibidos de acuerdo al filtro del nodo function conectado a él
 - template. Se usa para generar un gráfico histórico de los valores obtenidos
 - debug. Se usa para visualizar las acciones del los nodos conectados e él
 - http. Se usa para realizar peticiones al API de [OpenWeatherMap](https://openweathermap.org)


## Material necesario

 - Sofware
	 - Equipo o Máquina virtual con [Ubuntu](https://ubuntu.com/) con una versión mínima 20.04
	 - [Node-RED](https://nodered.org/) (notas para [instalación](https://github.com/nodesource/distributions/blob/master/README.md))
	 - [Git](https://git-scm.com/) (notas para [instalación](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git))
 - Hardware
	 - PC o LapTop con Linux, Windows
	 - Mínimo 8 Gb en RAM
	 - Disco duro con mínimo 100 Gb de espacio libre

## Material de referencia

 - [Node-Red](https://nodered.org/)
 - [Git](https://git-scm.com/)

## Instrucciones

**Requerimientos previos**
	 - Tener instalado el sofware necesario listado en el apartado 

**Material necesario**
	 - El programa "node-red" debe estár ejecución
	 - Tener la aplicación de Node-RED abierta en un navegador con la dirección "http://localhost:1880"
	 - Ejecutar en una terminal el comando nslookup broker.hivemq.com para obtener la dirección IP del servidror público y copiar la ip "Address" (18.158.239.107)
	 
 - Arrastrar los nodos necesarios para obtener el flujo similar al de la siguiente imagen

![](https://github.com/rvnava/flow5-g10/blob/main/Flujo-broker-publico.png?raw=true)

 - Conectar el nodo **json** con los 2 nodos **function**
 - Conectar cada nodo **function** con cada nodo **gauge**
 - Conectar cada nodo **function** con el nodo **chart**

 - Doble clic nodo broker
        En la sección servidor->agregar nuevo broker mqtt
        click en el botón de edición
        En el campo Server colocar la IP
        Oprimir Add
        En el campo Topic poner **codigoIot/Mor/mqtt/flow4** (para pruebas con servidor público) o **codigoIoT/mosquitto** (para pruebas locales)
        clic en Done

 - Doble clic nodo json
        Action: Siempre convertir a objeto JavaScript
        clic en Done

 - Doble clic en el primer nodo function
        Cambiar el nombre a Temperatura
        en la pestaña On Message escribir el siguiente código
        
		msg.payload = msg.payload.temp;
		msg.topic = "Temperatura";

        return msg;

 - Doble clic en el segundo nodo function
        Cambiar el nombre a Humedad
        en la pestaña On Message escribir el siguiente código
        
        msg.payload = msg.payload.hum;
		msg.topic = "Humedad";

        return msg;

 - En la pestaña dashboard
        Generar nuevo tab
        Renombrar en el nodo edit Flow 4 - MQTT
        Señalar pestaña 4
        Agregar nuevo grupo
        Cambiar el nombre a temperatura
        Agregar nuevo grupo
        Cambiar el nombre a humedad

 - Doble clic en el nodo gauge que está conectado al nodo de temperatura
        Seleccionar grupo temperatura
        Label: Temperatura
        Units: grados centigrados (C)
        Rangos 2 a 38, seleccionar de acuerdo a donde nos encontremos
        Cambiar color azul-frio 2-18
        Verde 18-26
        clic en Done

 - Doble clic en el nodo gauge que está conectado al nodo humedad
        Seleccionar grupo humedad
        Label: Humedad relativa
        Tipo : level
        Units: %
        min 0 max 100 (de acuerdo al rango del sensor)
        clic en Done

 - Conectar los nodos **function** temperatura y humedad al nodo **chart**
 - Agregar un nuevo grupo para el nodo histórico llamado HistoricoLocal
 - Dobleclic en el nodo chart
        Label: Historico local
        x-axis: 20 min
        y-xis: 0 -100

 - Agregar un enviador en el tema
        codigoIoT/flow5/mqtt/clima
 - Broker publico deberá conectarse a la ip del servicio OpenWeatherMap
        35.156.177.225
 - Crear un JSON que contenga temperatura y humedad obtenidas por API
 - Enviar las siguientes variables: id, temp, hum

 - En el Nodo function JSON publico agregar un código simiar al siguiente
	msg.payload = '{"id":"Raul Vargas, Toluca","temp":' + global.get("tempAPI") +',"hum":' + global.get ("humAPI") +'}';
	return msg;

 - Modificar los nodos function que grafican localmente la información obtenida por API

	Al nodo Nodo function Temperatua API agregar el siguiente código
	global.set ("tempAPI", msg.payload.main.temp);
	msg.payload = msg.payload.main.temp;
	msg.topic = "Temperatura";
	return msg;

	Al Nodo function Humedad API agregar el siguiente código
	msg.payload = msg.payload.main.humidity;
	global.set ("humAPI", msg.payload);
	return msg;


 - Oprimir Deploy
  

## Resultados

Los resultados de la ejecución se pueden visualizar en la dirección del [dashboard](http://localhost:1880/ui/) de **Node-RED**

La siguiente imagen muestra el flujo realizado en **Node-Red**
![](https://github.com/rvnava/flow5-g10/blob/main/Flujo-broker-publico.png?raw=true)

La siguiente imagen muestra el Dashboard
![](https://github.com/rvnava/flow5-g10/blob/main/Dashboard-Broker-publico.png?raw=true)

## Evidencias

 - [Repositorio en GitHub](https://github.com/rvnava/flow5-g10.git)
 - Video en youtube 
	 - No disponible
 - blogs
	 - No disponible
 - Foros
	- No disponible
 - Redes sociales
	- No disponible
	- 
## Créditos
 
 - [LinkedIn](www.linkedin.com/in/raúl-vargas-nava-aa646925)

