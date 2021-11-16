## Trabajo Práctico 6 - Construcción de Imágenes de Docker

### 1- Objetivos de Aprendizaje
 - Adquirir conocimientos para construir y publicar imágenes de Docker.
 - Familiarizarse con el vocabulario.

### 2- Unidad temática que incluye este trabajo práctico
Este trabajo práctico corresponde a la unidad Nº: 3

### 3- Consignas a desarrollar en el trabajo práctico:
 - En los puntos en los que se pida alguna descripción, realizarlo de la manera más clara posible.

### 4- Desarrollo:

#### 1- Conceptos de Dockerfiles
  - Leer https://docs.docker.com/engine/reference/builder/ (tiempo estimado 2 horas)
  - Describir las instrucciones
     - **FROM [--platform=<platform>] <image> [AS <name>]**

     Un Dockerfile debe comenzar con una instrucción FROM. 

     Lo que hace es inicializar un nuevo escenario de build y setear la `base image` para las siguientes instrucciones.

     `FROM` puede aparecer varias venes en un único docker file para crear multiples imagenes o usae un escenario de build como dependecia de otro.

     La flag --platform puede setearse si FROM referencia una imagen multiplataforma.

     - **RUN**

     Tiene dos formas:
     
     -shell: ``RUN <command>`` (el comando correrá en el shell).
     
     -exec: ``RUN ["executable, "param1", "param2"]``.

     Esta instrcucción ejecutará cualquier comando en una nueva capa en el tope (parte superior) de la imahen actual y commiteará los resultados para que sean usados en el siguiente paso.

      
     - **ADD**

      Tiene dos formas:
     
     -``ADD [--chown=<user>:<group>] <src>... <dest>``.
     
     -``ADD [--chown=<user>:<group>] ["<src>",... "<dest>"]``.

     Copia nuevos archivos, directorios o archivos remoto desde una fuente `<src>` y los añade a el filesystem de la imagen en la ruta destino `<dest>`.
     `<src>` debe estar dentro del context del build.
    

     - **COPY**

      Tiene dos formas:

     -``COPY [--chown=<user>:<group>] <src>... <dest>``.
     
     -``COPY [--chown=<user>:<group>] ["<src>",... "<dest>"]``.

     Copia nuevos archivos o directorios desde el path fuente `<src>` y los añade al filesystem del container en el path destido `<dest>`.
     Misma finalidad que `ADD` pero soporta unicamente copiar desde un directorio local del host y no ofrece al funcionalidad de desempaquetar el contido desde un src comprimido.



     - **EXPOSE <port> [<port>/<protocol>...]**

     Informa  que el container escucha en un puerto de la red específico en tiempo de ejcutción. No es que publica en el puerto, funciona como un tipo de documentación entre la persona que construye la imagen y la que ejecuta el container.

     - **CMD**

     Tiene tres formas:
     
     -shell: ``CMD command param1 param2``.
     
     -exec: ``CMD ["executable","param1","param2"]``.

     -default parameters to entrypoint: ``CMD ["param1","param2"]``.

     Solo puede haber una en un Dockerfile, su propósito es proveer un contenerdor de ejecución por defecto, puede ser incluido u omitido del ejecutable, en el ultimo caso se debe especificar un ENTRYPOINT.


     - **ENTRYPOINT**
     Tiene dos formas:
     
     -shell: ``ENTRYPOINT command param1 param2``.
     
     -exec: ``RUN ["executable, "param1", "param2"]``.

     Esta instrucción te permite configurar el contenedor que va a correr como un ejecutable. 

     Los argumentos pasados en el comando `run` (en la linea de comandos) serán agregados después de todos los elementos en un `exec` desde el `ENTRYPOINT`. Esto permite pasar los elementos al entry point.

     El ENTRYPOINT especifica el ejecutable que usará el contenedor y CMD se corresponde con los parámetros a usar con dicho ejecutable.


     <!-- http://marker.to/iZpLmB -->

#### 2- Generar imagen de docker
   - Clonar/Actualizar el repositorio de https://github.com/alexisfr/ing-soft-3-2020 
   - El código se encuentra en la carpeta `./proyectos/spring-boot`
   - Se puede copiar al repositorio personal en una carpeta `trabajo-practico-06/spring-boot`
   - Compilar la salida con:
```bash
cd proyectos/spring-boot
mvn clean package spring-boot:repackage  
```
   - Agregar un archivo llamado **Dockerfile** (en el directorio donde se corrió el comando mvn)
```Dockerfile
FROM java:8-jre-alpine

RUN apk add --no-cache bash

WORKDIR /app

COPY target/*.jar ./spring-boot-application.jar

ENV JAVA_OPTS="-Xms32m -Xmx128m"
EXPOSE 8080

ENTRYPOINT exec java $JAVA_OPTS -Djava.security.egd=file:/dev/./urandom -jar spring-boot-application.jar
```
   - Generar la imagen de docker con el comando build
```bash
docker build -t test-spring-boot .
```
  - Ejecutar el contenedor
```bash
docker run -p 8080:8080 test-spring-boot
```
  - Capturar y mostrar la salida.

  <details>
  <summary>Output</summary>
  > docker run -p 8080:8080 test-spring-boot

  .   ____          _            __ _ _
 /\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
 \\/  ___)| |_)| | | | | || (_| |  ) ) ) )
  '  |____| .__|_| |_|_| |_\__, | / / / /
 =========|_|==============|___/=/_/_/_/
 :: Spring Boot ::        (v2.0.2.RELEASE)

2021-10-04 00:00:03.696  INFO 1 --- [           main] s.actuator.SampleActuatorApplication     : Starting SampleActuatorApplication v2.0.2 on d2b31717df46 with PID 1 (/app/spring-boot-application.jar started by root in /app)
2021-10-04 00:00:03.700  INFO 1 --- [           main] s.actuator.SampleActuatorApplication     : No active profile set, falling back to default profiles: default
2021-10-04 00:00:03.799  INFO 1 --- [           main] ConfigServletWebServerApplicationContext : Refreshing org.springframework.boot.web.servlet.context.AnnotationConfigServletWebServerApplicationContext@2280cdac: startup date [Mon Oct 04 00:00:03 GMT 2021]; root of context hierarchy
2021-10-04 00:00:05.363  INFO 1 --- [           main] trationDelegate$BeanPostProcessorChecker : Bean 'org.springframework.transaction.annotation.ProxyTransactionManagementConfiguration' of type [org.springframework.transaction.annotation.ProxyTransactionManagementConfiguration$$EnhancerBySpringCGLIB$$6f737d73] is not eligible for getting processed by all BeanPostProcessors (for example: not eligible for auto-proxying)
2021-10-04 00:00:05.731  INFO 1 --- [           main] o.s.b.w.embedded.tomcat.TomcatWebServer  : Tomcat initialized with port(s): 8080 (http)
2021-10-04 00:00:05.766  INFO 1 --- [           main] o.apache.catalina.core.StandardService   : Starting service [Tomcat]
2021-10-04 00:00:05.767  INFO 1 --- [           main] org.apache.catalina.core.StandardEngine  : Starting Servlet Engine: Apache Tomcat/8.5.31
2021-10-04 00:00:05.781  INFO 1 --- [ost-startStop-1] o.a.catalina.core.AprLifecycleListener   : The APR based Apache Tomcat Native library which allows optimal performance in production environments was not found on the java.library.path: [/usr/lib/jvm/java-1.8-openjdk/jre/lib/amd64/server:/usr/lib/jvm/java-1.8-openjdk/jre/lib/amd64:/usr/lib/jvm/java-1.8-openjdk/jre/../lib/amd64:/usr/java/packages/lib/amd64:/usr/lib64:/lib64:/lib:/usr/lib]
2021-10-04 00:00:05.879  INFO 1 --- [ost-startStop-1] o.a.c.c.C.[Tomcat].[localhost].[/]       : Initializing Spring embedded WebApplicationContext
2021-10-04 00:00:05.880  INFO 1 --- [ost-startStop-1] o.s.web.context.ContextLoader            : Root WebApplicationContext: initialization completed in 2081 ms
2021-10-04 00:00:06.757  INFO 1 --- [ost-startStop-1] o.s.b.w.servlet.ServletRegistrationBean  : Servlet dispatcherServlet mapped to [/]
2021-10-04 00:00:06.767  INFO 1 --- [ost-startStop-1] o.s.b.w.servlet.FilterRegistrationBean   : Mapping filter: 'characterEncodingFilter' to: [/*]
2021-10-04 00:00:06.768  INFO 1 --- [ost-startStop-1] o.s.b.w.servlet.FilterRegistrationBean   : Mapping filter: 'hiddenHttpMethodFilter' to: [/*]
2021-10-04 00:00:06.768  INFO 1 --- [ost-startStop-1] o.s.b.w.servlet.FilterRegistrationBean   : Mapping filter: 'httpPutFormContentFilter' to: [/*]
2021-10-04 00:00:06.769  INFO 1 --- [ost-startStop-1] o.s.b.w.servlet.FilterRegistrationBean   : Mapping filter: 'requestContextFilter' to: [/*]
2021-10-04 00:00:06.769  INFO 1 --- [ost-startStop-1] o.s.b.w.servlet.FilterRegistrationBean   : Mapping filter: 'httpTraceFilter' to: [/*]
2021-10-04 00:00:06.770  INFO 1 --- [ost-startStop-1] o.s.b.w.servlet.FilterRegistrationBean   : Mapping filter: 'webMvcMetricsFilter' to: [/*]
2021-10-04 00:00:06.922  INFO 1 --- [           main] o.s.w.s.handler.SimpleUrlHandlerMapping  : Mapped URL path [/**/favicon.ico] onto handler of type [class org.springframework.web.servlet.resource.ResourceHttpRequestHandler]
2021-10-04 00:00:07.156  INFO 1 --- [           main] s.w.s.m.m.a.RequestMappingHandlerAdapter : Looking for @ControllerAdvice: org.springframework.boot.web.servlet.context.AnnotationConfigServletWebServerApplicationContext@2280cdac: startup date [Mon Oct 04 00:00:03 GMT 2021]; root of context hierarchy
2021-10-04 00:00:07.253  INFO 1 --- [           main] s.w.s.m.m.a.RequestMappingHandlerMapping : Mapped "{[/],methods=[GET],produces=[application/json]}" onto public java.util.Map<java.lang.String, java.lang.String> sample.actuator.SampleController.hello()
2021-10-04 00:00:07.255  INFO 1 --- [           main] s.w.s.m.m.a.RequestMappingHandlerMapping : Mapped "{[/],methods=[POST],produces=[application/json]}" onto public java.util.Map<java.lang.String, java.lang.Object> sample.actuator.SampleController.olleh(sample.actuator.SampleController$Message)
2021-10-04 00:00:07.256  INFO 1 --- [           main] s.w.s.m.m.a.RequestMappingHandlerMapping : Mapped "{[/foo]}" onto public java.lang.String sample.actuator.SampleController.foo()
2021-10-04 00:00:07.260  INFO 1 --- [           main] s.w.s.m.m.a.RequestMappingHandlerMapping : Mapped "{[/error],produces=[text/html]}" onto public org.springframework.web.servlet.ModelAndView org.springframework.boot.autoconfigure.web.servlet.error.BasicErrorController.errorHtml(javax.servlet.http.HttpServletRequest,javax.servlet.http.HttpServletResponse)
2021-10-04 00:00:07.261  INFO 1 --- [           main] s.w.s.m.m.a.RequestMappingHandlerMapping : Mapped "{[/error]}" onto public org.springframework.http.ResponseEntity<java.util.Map<java.lang.String, java.lang.Object>> org.springframework.boot.autoconfigure.web.servlet.error.BasicErrorController.error(javax.servlet.http.HttpServletRequest)
2021-10-04 00:00:07.305  INFO 1 --- [           main] o.s.w.s.handler.SimpleUrlHandlerMapping  : Mapped URL path [/webjars/**] onto handler of type [class org.springframework.web.servlet.resource.ResourceHttpRequestHandler]
2021-10-04 00:00:07.305  INFO 1 --- [           main] o.s.w.s.handler.SimpleUrlHandlerMapping  : Mapped URL path [/**] onto handler of type [class org.springframework.web.servlet.resource.ResourceHttpRequestHandler]
2021-10-04 00:00:07.763  INFO 1 --- [           main] o.s.b.a.e.web.EndpointLinksResolver      : Exposing 15 endpoint(s) beneath base path '/actuator'
2021-10-04 00:00:07.779  INFO 1 --- [           main] s.b.a.e.w.s.WebMvcEndpointHandlerMapping : Mapped "{[/actuator/auditevents],methods=[GET],produces=[application/vnd.spring-boot.actuator.v2+json || application/json]}" onto public java.lang.Object org.springframework.boot.actuate.endpoint.web.servlet.AbstractWebMvcEndpointHandlerMapping$OperationHandler.handle(javax.servlet.http.HttpServletRequest,java.util.Map<java.lang.String, java.lang.String>)
2021-10-04 00:00:07.781  INFO 1 --- [           main] s.b.a.e.w.s.WebMvcEndpointHandlerMapping : Mapped "{[/actuator/beans],methods=[GET],produces=[application/vnd.spring-boot.actuator.v2+json || application/json]}" onto public java.lang.Object org.springframework.boot.actuate.endpoint.web.servlet.AbstractWebMvcEndpointHandlerMapping$OperationHandler.handle(javax.servlet.http.HttpServletRequest,java.util.Map<java.lang.String, java.lang.String>)
2021-10-04 00:00:07.782  INFO 1 --- [           main] s.b.a.e.w.s.WebMvcEndpointHandlerMapping : Mapped "{[/actuator/health],methods=[GET],produces=[application/vnd.spring-boot.actuator.v2+json || application/json]}" onto public java.lang.Object org.springframework.boot.actuate.endpoint.web.servlet.AbstractWebMvcEndpointHandlerMapping$OperationHandler.handle(javax.servlet.http.HttpServletRequest,java.util.Map<java.lang.String, java.lang.String>)
2021-10-04 00:00:07.783  INFO 1 --- [           main] s.b.a.e.w.s.WebMvcEndpointHandlerMapping : Mapped "{[/actuator/conditions],methods=[GET],produces=[application/vnd.spring-boot.actuator.v2+json || application/json]}" onto public java.lang.Object org.springframework.boot.actuate.endpoint.web.servlet.AbstractWebMvcEndpointHandlerMapping$OperationHandler.handle(javax.servlet.http.HttpServletRequest,java.util.Map<java.lang.String, java.lang.String>)
2021-10-04 00:00:07.784  INFO 1 --- [           main] s.b.a.e.w.s.WebMvcEndpointHandlerMapping : Mapped "{[/actuator/shutdown],methods=[POST],produces=[application/vnd.spring-boot.actuator.v2+json || application/json]}" onto public java.lang.Object org.springframework.boot.actuate.endpoint.web.servlet.AbstractWebMvcEndpointHandlerMapping$OperationHandler.handle(javax.servlet.http.HttpServletRequest,java.util.Map<java.lang.String, java.lang.String>)
2021-10-04 00:00:07.785  INFO 1 --- [           main] s.b.a.e.w.s.WebMvcEndpointHandlerMapping : Mapped "{[/actuator/configprops],methods=[GET],produces=[application/vnd.spring-boot.actuator.v2+json || application/json]}" onto public java.lang.Object org.springframework.boot.actuate.endpoint.web.servlet.AbstractWebMvcEndpointHandlerMapping$OperationHandler.handle(javax.servlet.http.HttpServletRequest,java.util.Map<java.lang.String, java.lang.String>)
2021-10-04 00:00:07.785  INFO 1 --- [           main] s.b.a.e.w.s.WebMvcEndpointHandlerMapping : Mapped "{[/actuator/env/{toMatch}],methods=[GET],produces=[application/vnd.spring-boot.actuator.v2+json || application/json]}" onto public java.lang.Object org.springframework.boot.actuate.endpoint.web.servlet.AbstractWebMvcEndpointHandlerMapping$OperationHandler.handle(javax.servlet.http.HttpServletRequest,java.util.Map<java.lang.String, java.lang.String>)
2021-10-04 00:00:07.786  INFO 1 --- [           main] s.b.a.e.w.s.WebMvcEndpointHandlerMapping : Mapped "{[/actuator/env],methods=[GET],produces=[application/vnd.spring-boot.actuator.v2+json || application/json]}" onto public java.lang.Object org.springframework.boot.actuate.endpoint.web.servlet.AbstractWebMvcEndpointHandlerMapping$OperationHandler.handle(javax.servlet.http.HttpServletRequest,java.util.Map<java.lang.String, java.lang.String>)
2021-10-04 00:00:07.787  INFO 1 --- [           main] s.b.a.e.w.s.WebMvcEndpointHandlerMapping : Mapped "{[/actuator/info],methods=[GET],produces=[application/vnd.spring-boot.actuator.v2+json || application/json]}" onto public java.lang.Object org.springframework.boot.actuate.endpoint.web.servlet.AbstractWebMvcEndpointHandlerMapping$OperationHandler.handle(javax.servlet.http.HttpServletRequest,java.util.Map<java.lang.String, java.lang.String>)
2021-10-04 00:00:07.787  INFO 1 --- [           main] s.b.a.e.w.s.WebMvcEndpointHandlerMapping : Mapped "{[/actuator/loggers],methods=[GET],produces=[application/vnd.spring-boot.actuator.v2+json || application/json]}" onto public java.lang.Object org.springframework.boot.actuate.endpoint.web.servlet.AbstractWebMvcEndpointHandlerMapping$OperationHandler.handle(javax.servlet.http.HttpServletRequest,java.util.Map<java.lang.String, java.lang.String>)
2021-10-04 00:00:07.788  INFO 1 --- [           main] s.b.a.e.w.s.WebMvcEndpointHandlerMapping : Mapped "{[/actuator/loggers/{name}],methods=[GET],produces=[application/vnd.spring-boot.actuator.v2+json || application/json]}" onto public java.lang.Object org.springframework.boot.actuate.endpoint.web.servlet.AbstractWebMvcEndpointHandlerMapping$OperationHandler.handle(javax.servlet.http.HttpServletRequest,java.util.Map<java.lang.String, java.lang.String>)
2021-10-04 00:00:07.790  INFO 1 --- [           main] s.b.a.e.w.s.WebMvcEndpointHandlerMapping : Mapped "{[/actuator/loggers/{name}],methods=[POST],consumes=[application/vnd.spring-boot.actuator.v2+json || application/json]}" onto public java.lang.Object org.springframework.boot.actuate.endpoint.web.servlet.AbstractWebMvcEndpointHandlerMapping$OperationHandler.handle(javax.servlet.http.HttpServletRequest,java.util.Map<java.lang.String, java.lang.String>)
2021-10-04 00:00:07.790  INFO 1 --- [           main] s.b.a.e.w.s.WebMvcEndpointHandlerMapping : Mapped "{[/actuator/heapdump],methods=[GET],produces=[application/octet-stream]}" onto public java.lang.Object org.springframework.boot.actuate.endpoint.web.servlet.AbstractWebMvcEndpointHandlerMapping$OperationHandler.handle(javax.servlet.http.HttpServletRequest,java.util.Map<java.lang.String, java.lang.String>)
2021-10-04 00:00:07.791  INFO 1 --- [           main] s.b.a.e.w.s.WebMvcEndpointHandlerMapping : Mapped "{[/actuator/threaddump],methods=[GET],produces=[application/vnd.spring-boot.actuator.v2+json || application/json]}" onto public java.lang.Object org.springframework.boot.actuate.endpoint.web.servlet.AbstractWebMvcEndpointHandlerMapping$OperationHandler.handle(javax.servlet.http.HttpServletRequest,java.util.Map<java.lang.String, java.lang.String>)
2021-10-04 00:00:07.792  INFO 1 --- [           main] s.b.a.e.w.s.WebMvcEndpointHandlerMapping : Mapped "{[/actuator/metrics/{requiredMetricName}],methods=[GET],produces=[application/vnd.spring-boot.actuator.v2+json || application/json]}" onto public java.lang.Object org.springframework.boot.actuate.endpoint.web.servlet.AbstractWebMvcEndpointHandlerMapping$OperationHandler.handle(javax.servlet.http.HttpServletRequest,java.util.Map<java.lang.String, java.lang.String>)
2021-10-04 00:00:07.793  INFO 1 --- [           main] s.b.a.e.w.s.WebMvcEndpointHandlerMapping : Mapped "{[/actuator/metrics],methods=[GET],produces=[application/vnd.spring-boot.actuator.v2+json || application/json]}" onto public java.lang.Object org.springframework.boot.actuate.endpoint.web.servlet.AbstractWebMvcEndpointHandlerMapping$OperationHandler.handle(javax.servlet.http.HttpServletRequest,java.util.Map<java.lang.String, java.lang.String>)
2021-10-04 00:00:07.794  INFO 1 --- [           main] s.b.a.e.w.s.WebMvcEndpointHandlerMapping : Mapped "{[/actuator/scheduledtasks],methods=[GET],produces=[application/vnd.spring-boot.actuator.v2+json || application/json]}" onto public java.lang.Object org.springframework.boot.actuate.endpoint.web.servlet.AbstractWebMvcEndpointHandlerMapping$OperationHandler.handle(javax.servlet.http.HttpServletRequest,java.util.Map<java.lang.String, java.lang.String>)
2021-10-04 00:00:07.795  INFO 1 --- [           main] s.b.a.e.w.s.WebMvcEndpointHandlerMapping : Mapped "{[/actuator/httptrace],methods=[GET],produces=[application/vnd.spring-boot.actuator.v2+json || application/json]}" onto public java.lang.Object org.springframework.boot.actuate.endpoint.web.servlet.AbstractWebMvcEndpointHandlerMapping$OperationHandler.handle(javax.servlet.http.HttpServletRequest,java.util.Map<java.lang.String, java.lang.String>)
2021-10-04 00:00:07.796  INFO 1 --- [           main] s.b.a.e.w.s.WebMvcEndpointHandlerMapping : Mapped "{[/actuator/mappings],methods=[GET],produces=[application/vnd.spring-boot.actuator.v2+json || application/json]}" onto public java.lang.Object org.springframework.boot.actuate.endpoint.web.servlet.AbstractWebMvcEndpointHandlerMapping$OperationHandler.handle(javax.servlet.http.HttpServletRequest,java.util.Map<java.lang.String, java.lang.String>)
2021-10-04 00:00:07.798  INFO 1 --- [           main] s.b.a.e.w.s.WebMvcEndpointHandlerMapping : Mapped "{[/actuator],methods=[GET],produces=[application/vnd.spring-boot.actuator.v2+json || application/json]}" onto protected java.util.Map<java.lang.String, java.util.Map<java.lang.String, org.springframework.boot.actuate.endpoint.web.Link>> org.springframework.boot.actuate.endpoint.web.servlet.WebMvcEndpointHandlerMapping.links(javax.servlet.http.HttpServletRequest,javax.servlet.http.HttpServletResponse)
2021-10-04 00:00:07.886  INFO 1 --- [           main] o.s.j.e.a.AnnotationMBeanExporter        : Registering beans for JMX exposure on startup
2021-10-04 00:00:07.889  INFO 1 --- [           main] o.s.j.e.a.AnnotationMBeanExporter        : Bean with name 'dataSource' has been autodetected for JMX exposure
2021-10-04 00:00:07.900  INFO 1 --- [           main] o.s.j.e.a.AnnotationMBeanExporter        : Located MBean 'dataSource': registering with JMX server as MBean [com.zaxxer.hikari:name=dataSource,type=HikariDataSource]
2021-10-04 00:00:07.943  INFO 1 --- [           main] o.s.b.w.embedded.tomcat.TomcatWebServer  : Tomcat started on port(s): 8080 (http) with context path ''2021-10-04 00:00:07.949  INFO 1 --- [           main] s.actuator.SampleActuatorApplication     : Started SampleActuatorApplication in 4.616 seconds (JVM running for 5.678)
  
</details>

  - Verificar si retorna un mensaje (correr en otro terminal o browser)
```bash
curl -v localhost:8080
```
<details>
  <summary>Output</summary>
  > wsl curl -v localhost:8080
*   Trying 127.0.0.1:8080...
* TCP_NODELAY set
* Connected to localhost (127.0.0.1) port 8080 (#0)
> GET / HTTP/1.1
> Host: localhost:8080
> User-Agent: curl/7.68.0
> Accept: */*
>
* Mark bundle as not supporting multiuse
< HTTP/1.1 200
< Content-Type: application/json;charset=UTF-8
< Transfer-Encoding: chunked
< Date: Mon, 04 Oct 2021 00:06:15 GMT
<
* Connection #0 to host localhost left intact
{"message":"Spring boot says hello from a Docker container"}
</details>

#### 3- Dockerfiles Multi Etapas
Se recomienda crear compilaciones de varias etapas para todas las aplicaciones (incluso las heredadas). En resumen, las compilaciones de múltiples etapas:

- Son independientes y auto descriptibles
- Resultan en una imagen de Docker muy pequeña
- Puede ser construido fácilmente por todas las partes interesadas del proyecto (incluso los no desarrolladores)
- Son muy fáciles de entender y mantener.
- No requiere un entorno de desarrollo (aparte del código fuente en sí)
- Se puede empaquetar con pipelines muy simples

Las compilaciones de múltiples etapas también son esenciales en organizaciones que emplean múltiples lenguajes de programación. La facilidad de crear una imagen de Docker por cualquier persona sin la necesidad de JDK / Node / Python / etc. no puede ser sobrestimado.

- Modificar el dockerfile para el proyecto Java anterior de la siguiente forma
```dockerfile
FROM maven:3.5.2-jdk-8-alpine AS MAVEN_TOOL_CHAIN
COPY pom.xml /tmp/
RUN mvn -B dependency:go-offline -f /tmp/pom.xml -s /usr/share/maven/ref/settings-docker.xml
COPY src /tmp/src/
WORKDIR /tmp/
RUN mvn -B -s /usr/share/maven/ref/settings-docker.xml package

FROM java:8-jre-alpine

EXPOSE 8080

RUN mkdir /app
COPY --from=MAVEN_TOOL_CHAIN /tmp/target/*.jar /app/spring-boot-application.jar

ENV JAVA_OPTS="-Xms32m -Xmx128m"

ENTRYPOINT exec java $JAVA_OPTS -Djava.security.egd=file:/dev/./urandom -jar /app/spring-boot-application.jar

HEALTHCHECK --interval=1m --timeout=3s CMD wget -q -T 3 -s http://localhost:8080/actuator/health/ || exit 1
```
- Construir nuevamente la imagen
```bash
docker build -t test-spring-boot .
```
- Analizar y explicar el nuevo Dockerfile, incluyendo las nuevas instrucciones.

```dockerfile
#Especifica como imagen base "maven:3.5.2-jdk-8-alpine" y la nombra MAVEN_TOOL_CHAIN
FROM maven:3.5.2-jdk-8-alpine AS MAVEN_TOOL_CHAIN
#Copia el archivo pom.xml a la carpeta /tmp/ del contexto de la imagen
COPY pom.xml /tmp/
#Ejecuta el comando mvn dependency:go-offline en modo no interactivo (-B), el cual resuelve todas las dependencias del proyecto. Usa el parámetro -f para indicar la ubicación del archivo pom.xml y -s para indicar el path del archivo de configuraciones del usuario.
RUN mvn -B dependency:go-offline -f /tmp/pom.xml -s /usr/share/maven/ref/settings-docker.xml
#Copia el contenido de src en el contexto de la imagen.
COPY src /tmp/src/
#Especifica el directorio de trabajo
WORKDIR /tmp/
#Ejecuta el comando mvn package para compilar el proyecto
RUN mvn -B -s /usr/share/maven/ref/settings-docker.xml package

#Acá se lo puede considerar como que empieza otra sección lógica en el Dockerfile, al tener varios FROM lo que conseguimos es una compilación en etapas donde iremos haciendo uso de los resultados de las secciones anteriores para conformar nuestra imagen final. Este FROM define la imagen que los contenedores eventualmente ejecutarán. La imagen presedente se utiliza únicamente como una herramienta práctica para la creación.

FROM java:8-jre-alpine

#Expongo en el puerto 8080
EXPOSE 8080

#Creo un subdirectorio llamado app
RUN mkdir /app
#Copio de la imagen anterior (que nombramos MAVEN_TOLL_CHAIN) los ejecutables especificados.
COPY --from=MAVEN_TOOL_CHAIN /tmp/target/*.jar /app/spring-boot-application.jar

ENV JAVA_OPTS="-Xms32m -Xmx128m"

ENTRYPOINT exec java $JAVA_OPTS -Djava.security.egd=file:/dev/./urandom -jar /app/spring-boot-application.jar

#Le dice a docker como saber si la app es "saludable"
HEALTHCHECK --interval=1m --timeout=3s CMD wget -q -T 3 -s http://localhost:8080/actuator/health/ || exit 1
```

*La principal diferencia entre este dockerfile y el anterior es que con este, cuando ejecutemos el build, primero se contruye la applicación (primer sección lógica) y después se usan los ejecutables obtenidos para la creación de la imagen. En el caso anterior la construcción de la aplicación iba por nuestra cuenta y traíamos al contexto de la imagen a contruir los ejecutables .jar ya existentes en nuestro filesystem local.*


#### 4- Python Flask
  - Utilizar el código que se encuentra en la carpeta `./proyectos/python-flask`
  - Se puede copiar al repositorio personal en una carpeta `trabajo-practico-06/python-flask`
  - Correr el comando
```bash
cd ./proyectos/python-flask
docker-compose up -d
```
  - Explicar que sucedió!
  - ¿Para qué está la key `build.context` en el docker-compose.yml?

  Lo sucedido es que en el docker-compose se encuentra:
  
  ```docker
   build:
      context: ./
  ```
  Esto indica que se debe construir una imagen de acuerdo a lo especificado en el archivo `Dockerfile` y se usa como contexto el directorio actual.


#### 5- Imagen para aplicación web en Nodejs
  - Crear una la carpeta `trabajo-practico-06/nodejs-docker`
  - Generar un proyecto siguiendo los pasos descriptos en el trabajo práctico 5 para Nodejs
  - Escribir un Dockerfile para ejecutar la aplicación web localizada en ese directorio
    - Idealmente que sea multistage, con una imagen de build y otra de producción.
    - Usar como imagen base **node:13.12.0-alpine**
    - Ejecutar **npm install** dentro durante el build.
    - Exponer el puerto 3000
  - Hacer un build de la imagen, nombrar la imagen **test-node**.
  - Ejecutar la imagen **test-node** publicando el puerto 3000.
  - Verificar en http://localhost:3000 que la aplicación está funcionando.
  - Proveer el Dockerfile y los comandos ejecutados como resultado de este ejercicio.

  **Dockerfile**

  ```dockerfile
  FROM node:13.12.0-alpine AS dependencias
  COPY package.json /tmp/
  WORKDIR /tmp/
  RUN npm install
 
  FROM dependencias
  COPY --from=DEPENDENCIAS /tmp/node_modules ./node_modules
  COPY . .
  EXPOSE 3000
  CMD npm run start
  ```

  **Comandos**

  ```bash
   > docker build -t test-node .
   > docker run -p 3000:3000 test-node
  ```

  **Verificación en browser**

  ![screenshot](./TP06-images/cap_05.png)


#### 6- Publicar la imagen en Docker Hub.
  - Crear una cuenta en Docker Hub si no se dispone de una.
  - Registrase localmente a la cuenta de Docker Hub:
```bash
docker login
```
  - Crear un tag de la imagen generada en el ejercicio 3. Reemplazar <mi_usuario> por el creado en el punto anterior.
```bash
docker tag test-node <mi_usuario>/test-node:latest
```
  - Subir la imagen a Docker Hub con el comando
```bash
docker push <mi_usuario>/test-node:latest
``` 
  - Como resultado de este ejercicio mostrar la salida de consola, o una captura de pantalla de la imagen disponible en Docker Hub.

  Salida

  ```bash
  > docker push narananf/test-node:latest
The push refers to repository [docker.io/narananf/test-node]
8fece29fb949: Pushed
43fa3061ba42: Pushed
2b5e02abf147: Pushed
5f70bf18a086: Pushed
225ed854bdff: Pushed
65d358b7de11: Mounted from library/node
f97384e8ccbc: Mounted from library/node
d56e5e720148: Mounted from library/node
beee9f30bc1f: Mounted from library/node
latest: digest: sha256:15b99dc8efefb4968e652ec67cd70f457037f967bd264b3524cd7e250c49572b size: 2200
  ```
