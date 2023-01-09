# Desafío 16

## Loggers, Gzip y análisis de performance.

### Consignas

Incorporar al proyecto de servidor de trabajo la compresión gzip.

Verificar sobre la ruta /info con y sin compresión, la diferencia de cantidad de bytes devueltos en un caso y otro.

Luego implementar loggueo (con alguna librería vista en clase) que registre lo siguiente:

   - Ruta y método de todas las peticiones recibidas por el servidor (info)
   - Ruta y método de las peticiones a rutas inexistentes en el servidor (warning)

Errores lanzados por las apis de mensajes y productos, únicamente (error)
Considerar el siguiente criterio:
  - Loggear todos los niveles a consola (info, warning y error)
  - Registrar sólo los logs de warning a un archivo llamada warn.log
  - Enviar sólo los logs de error a un archivo llamada error.log

----

Luego, realizar el análisis completo de performance del servidor con el que venimos trabajando.

Vamos a trabajar sobre la ruta '/info', en modo fork, agregando ó extrayendo un console.log de la información colectada antes de devolverla al cliente. Además desactivaremos el child_process de la ruta '/randoms'

Para ambas condiciones (con o sin console.log) en la ruta '/info' OBTENER:

  - El perfilamiento del servidor, realizando el test con --prof de node.js. Analizar los resultados obtenidos luego de procesarlos con --prof-process. 
  - Utilizaremos como test de carga Artillery en línea de comandos, emulando 50 conexiones concurrentes con 20 request por cada una. Extraer un reporte con los resultados en archivo de texto.

----

Luego utilizaremos Autocannon en línea de comandos, emulando 100 conexiones concurrentes realizadas en un tiempo de 20 segundos. Extraer un reporte con los resultados (puede ser un print screen de la consola)

  - El perfilamiento del servidor con el modo inspector de node.js --inspect. Revisar el tiempo de los procesos menos performantes sobre el archivo fuente de inspección.
  - El diagrama de flama con 0x, emulando la carga con Autocannon con los mismos parámetros anteriores.
  
Realizar un informe en formato pdf sobre las pruebas realizadas incluyendo los resultados de todos los test (texto e imágenes). 

👉 Al final incluir la conclusión obtenida a partir del análisis de los datos.


## RESOLUCIÓN

Al iniciar el server, se logea como INFO del logger el puerto en el que está corriendo el servidor.
También podemos ver loggeadas como INFO todos los tipos de peticiones que se hagan a cualquier ruta.
> En caso de haber algún error o warn, se imprimirán en sus respectivos archivos 'warn.log' o 'error.log' (en la ruta raíz del proyecto)


>> AUTOCANNON - ARTILLERY

$ nodemon --inspect server.js 5050
$ node benchmark.js (o 'npm run benchmark') --> este archivo hace los test directamente en la ruta localhost:5050/info

>> APARECE LO SIGUIENTE EN CONSOLA: 
Running tests
Running 20s test @ http://localhost:5050/info (ESTA URL SALE DE LA QUE LE PASAMOS A LA FUNCIÓN "RUN" EN EL ARCHIVO BENCHMARK.JS)
100 connections


┌─────────┬────────┬────────┬─────────┬─────────┬───────────┬───────────┬─────────┐
│ Stat    │ 2.5%   │ 50%    │ 97.5%   │ 99%     │ Avg       │ Stdev     │ Max     │
├─────────┼────────┼────────┼─────────┼─────────┼───────────┼───────────┼─────────┤
│ Latency │ 125 ms │ 971 ms │ 1200 ms │ 1901 ms │ 902.48 ms │ 281.05 ms │ 2884 ms │
└─────────┴────────┴────────┴─────────┴─────────┴───────────┴───────────┴─────────┘
┌───────────┬─────────┬─────────┬─────────┬─────────┬─────────┬─────────┬─────────┐
│ Stat      │ 1%      │ 2.5%    │ 50%     │ 97.5%   │ Avg     │ Stdev   │ Min     │
├───────────┼─────────┼─────────┼─────────┼─────────┼─────────┼─────────┼─────────┤
│ Req/Sec   │ 54      │ 54      │ 110     │ 119     │ 106.6   │ 13.44   │ 54      │
├───────────┼─────────┼─────────┼─────────┼─────────┼─────────┼─────────┼─────────┤
│ Bytes/Sec │ 41.8 kB │ 41.8 kB │ 85.2 kB │ 92.2 kB │ 82.6 kB │ 10.4 kB │ 41.8 kB │
└───────────┴─────────┴─────────┴─────────┴─────────┴─────────┴─────────┴─────────┘

Req/Bytes counts sampled once per second.
# of samples: 20

2k requests in 20.14s, 1.65 MB read

// ---------------

$ npm start (o '0x server.js') // Observar package.json con los respectivos seteos en los scripts.

>> Si iniciamos el server con npm start (0x server.js) se inicia el profiling y nos genera una carpeta con el flamegraph y archivos ISOLATE con información más detallada.