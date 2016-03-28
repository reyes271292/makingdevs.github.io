---
layout: post
title: "Logger_logback"
date: 2016-03-28 09:13:56 -0600
comments: true
author: Juan Francisco Reyes Silva
published: true
comments: true
tags: groovy
categories:
- groovy
---

En el proceso de desarrollo de software se implementan bitácoras o mejor conocidas como loggin que permiten tener información de salida útil al desarrollador en cuanto al correcto flujo de la aplicación.

Este registro se divide en secciones las cuales son: Logger, Formatter y Handler.

<!-- more -->
* Logger: 
Es el responsable de captar el mensaje y pasarlo al marco de registro.

**Nivel** |	**Descripción**
  --- | ---
FATAL |	Errores graves que causan la terminación prematura 
ERROR |	Otros errores de ejecución o condiciones inesperadas
WARNING |	Se utiliza para situaciones que podrían ser potencialmente dañinas
INFO |	Eventos interesantes de tiempo de ejecución (inicio / apagado)
DEBUG |	Información detallada sobre el flujo a través del sistema
TRACE |	Información más detallada

* Formatter:
Su función es dar formato a la salida. toma el objeto binario y realiza la conversión a una representación de cadena

* Appender o Handler: 
Se le entrega el mensaje con formato al Appender, el cual puede ser visualizado en diferentes formas como son: consola, archivo, base de datos, servicios de mensajería, escribir en un socket.

#Logback
Este logger pretende ser el sucesor de log4j, fue diseñado por Ceki Gülcü, fundador de log4j's. 

Para poder usar este logger, se requiere del módulo logback es por ello que se usa MVNRepository para obtenerlo, este proyecto se enfoca en un server con jetty, que realizara el bitacorado de las peticiones al servidor.

La configuración del archivo gradle queda de la siguiente manera:
``` groovy
subprojects{
  apply plugin:'groovy'

  repositories{
    jcenter()
  }

  dependencies{
  	compile 'org.codehaus.groovy:groovy-all:2.4.4'
    compile 'ch.qos.logback:logback-classic:1.1.6'
  }  
  
}

project(":webserver"){
  apply plugin: 'war'
  apply plugin: 'jetty'
  
  dependencies{
    testCompile 'org.eclipse.jetty.aggregate:jetty-all-server:8.1.19.v20160209'

  }
  httpPort = 1234
  
}
```

Para que gradle reconozca el sub proyecto denominado webserver, se crea el archivo settings.gradle, dando como resultado la siguiente configuración:
``` groovy
include "webserver"
```

La estructura general del proyecto queda de la siguiente forma:

* build.gradle

* README.md

* settings.gradle

* webserver/src/main/resources/logback.groovy

* webserver/src/main/webapp/WEB-INF/groovy/MessagesLogger.groovy

* webserver/src/main/webapp/WEB-INF/web.xml

El archivo groovy que se estará manejando para obtener la bitácora de las peticiones que se hacen al servidor, será en la ruta webserver/src/main/resources denominado _logback.groovy_ el cual contiene el patrón de salida del logger además de especificar donde se mostrara dicha información.

``` groovy
import static ch.qos.logback.classic.Level.INFO
import static ch.qos.logback.classic.Level.DEBUG

import ch.qos.logback.classic.encoder.PatternLayoutEncoder
import ch.qos.logback.core.ConsoleAppender

appender("CONSOLE", ConsoleAppender) {
  encoder(PatternLayoutEncoder) {
    pattern = "%d{HH:mm:ss.SSS} [%thread] %highlight(%-5level) %cyan(%logger{15}) - %msg %n"
  }
}

appender("FILE", FileAppender) {
  file = "example_logger.log"
  append = true
  encoder(PatternLayoutEncoder) {
    pattern = "%d{HH:mm:ss.SSS} %-5level %logger{30} %msg %n"
  }
}
root(DEBUG, ["CONSOLE","FILE"])
``` 


Ahora el archivo _MessagesLogger.groovy_ tendrá como función el obtener las peticiones que se hacen al servidor, es decir, sea una petición por GET o POST, regresando un mensaje en formato JSON. Tomar en cuenta que se crea ahí una instancia de la clase Logger que esta previamente creada en el directorio resources. Además de contar con el nivel del logger, respetando la configuración que se hizo en el archivo logback.groovy.

``` groovy
import javax.servlet.http.HttpServletResponse

import org.slf4j.Logger
import org.slf4j.LoggerFactory

Logger log = LoggerFactory.getLogger(getClass())

log.debug "*"*40
log.debug request.properties.toString()
log.error "Error"
log.warn "Warning"
log.info "Info"
log.trace "Trace"

def method = request.method

try{

  def contentType = headers.find{ k,v -> k.toLowerCase() == 'content-type' }?.value

  if(method.toLowerCase()=="post"){
    json([method:"POST"])
  }
  if(method.toLowerCase()=="get"){
    json([method:"GET"])
  }

}
catch(RuntimeException e){
  response.contentType='application/json'
  response.setStatus(HttpServletResponse.SC_BAD_REQUEST,e.message)
}
catch(Exception e){
  response.contentType='application/json'
  response.setStatus(HttpServletResponse.SC_NOT_FOUND,e.message)
}
``` 

Una vez terminado de configurar el archivo anterior, se procede a iniciar el servidor jetty de la siguiente forma:

``` groovy 
gradle :webserver:jettyRun
``` 

Para poder ver la salida tanto a consola como al archivo especificado se hace una petición mediante el navegador Firefox.

``` groovy
curl -i http://localhost:1234/webserver/MessagesLogger.groovy 
``` 

Ahora para verificar que realmente está en función el logger logback, se verifica la consola donde se inició el servidor jetty así como la ruta donde está el proyecto.

{% img left /images/logback.png 'Logback' 'images' %}

## Acerca del patrón de salida
* %d{HH:mm:ss.SSS} Es la hora que se realizo el proceso
* [%thread] Indica el thread que inicio la tarea
* %highlight(%-5level) Brinda el color acorde al nivel de logger que se esta usando
* %cyan(%logger{15}) Muestra el nombre de la clase que esta tomando el logger, en color azul
* %msg Muestra el msn que se manda acorde al nivel que se esta llamando
* %n Indica el final de la línea 

