<div style="text-align: right">08/06/2021</div>

# Sincronización Manual
#### Este instructivo lo guiará para su correcta configuración.

### Recomendaciones previas al proceso
- Hasta no terminar el proceso el usuario involucrado no debería operando con la aplicación Movil.
- Luego de presionar el botón de la sincronización manual el usuario debería esperar unos minutos para dejar que el servicio finalice.
- Antes de comenzar con el flujo de trabajo normal, se aconseja reiniciar la aplicación.
- No deberían quedar configuración de procesamientos viejos sobre el archivo `db.json`.

## Objetivo
Esta herramienta esta pensada para forzar la sincronización en los casos límites cuando el servicio no responde para una cierta solicitud.

## Configuración
#### El json para la sincronización manual debe ir en el archivo `~/jsons/mobile/db.json` que se encuentra en la raíz de la webApi. El json llamado `forceSync` es un arreglo para cada usuario.

Se compone con las siguientes propiedades:

| Nombre         |Tipo     | Descripción     |
| :------------- | :---------- | :----------- |
|  id | string   | Identificador único    |
| retry   | boolean | Reprocesamiento |
| userName   | string | Nombre de usuario |
| solicitud.filters.id   | string | (Opcional) Id de la solicitud a procesar |
| solicitud.filters.idEstadoSolicitud   | string | (Opcional) El estado en el que se encuentra la solicitud |
| solicitud.filters.numeroSolicitud   | string | (Opcional) El número de la solicitud|
| solicitud.recovery.all   | boolean | Si es <span style="color:blue">true</span> significa que se sincronizarán todos los registros |
| solicitud.recovery.expand   | json | Especificar el [exapnd](#Valores-posibles-para-el-expand) para alguna sección. |

### Algunas aclaraciones:
En el objeto `filters` debe ir alguna de los dos filtros disponibles: el `id` o el par `{idEstadoSolicitud, numeroSolicitud}`. Esto es porque a veces soporte no cuenta con el id de la solicitud, supongamos el caso en el que la solicitud nunca llegó a sincronizar, entonces solo le queda configurar el par `{idEstadoSolicitud, numeroSolicitud}` esta información la puede proveer el usuario del dispositivo que la visualiza. Entonces para los casos que se conoce el id se debería usar el filtro `id` caso contrario el par `{idEstadoSolicitud, numeroSolicitud}`.\
Si se contesta <span style="color:blue">false</span> en `solicitud.recovery.all` entonces debe ir un [expand válido](#Valores-posibles-para-el-expand), caso contrario puede quedar en `{}`.
<br>
#### **NOTA**: En producción no debería existir el caso de que un LP tengo más de una solicitud con el mismo número en su aplicación mobile, por ello el filtro par `{idEstadoSolicitud, numeroSolicitud}` debería ser válido, si esto no ocurre, el LP tiene que corregir el error del número de solicitud antes de realizar este procedimiento, puesto que no hay garantía de que el botón aparezca en la solicitud que desean recuperar por la ambiguedad del caso, salvo que se conozca su id.

<br/>

### Valores posibles para el expand
El expand es un objeto que le dice al ORM del mobile que datos debe recuperar de SQLite. Sólo se permitiran expandir siempre y cuando existan sus relaciones, por ejemplo la Solicitud tiene una relación uno a uno con la carátula. A continuación, se listan los expand posibles clasificados por sección:

|id| Sección         |Expand|
|:--| :------------- | :---------- |
|0|Carátula|`{Caratula: { informacionesLp: {}, seguros: {}, HorariosContacto: {}}`|
|A|DatosPA|`{PropuestoAsegurado: {DomicilioParticular: {},DomicilioFiscal: {},FotoDocumento: {}}}`|
|B|Beneficiario|`{beneficiarios: {}}`|
|C|ModalidadPagoBeneficio|`{ModalidadPagoBeneficio: {}}`|
|D|Tomador| `{Tomador: { TributosProvincia: {}, TomadorPersonaFisica: {DomicilioParticular: {} }, TomadorEmpresa: { RepresentanteLegal: { DomicilioParticular: {} } }, DomicilioCorrespondencia: {}, DomicilioFiscal: {} }}`|
|E|Cobertura| `{SeccionCobertura: {}}`|
|F|ModalidaPdago| `{ModalidaPdago: {}} }`|
|G|Pagador|`{Pagador: {TributosProvincia: {},PagadorEmpresa: {}}}`|
|H|OtrasActDelPA| `{OtrasActDelPA:{}}`|
|I|AntecedentesFamiliaresPA| `{AntecedentesFamiliaresPA: {}}`|
|J|InfoMedicaDelPA| `{InfoMedicaDelPA: {DatosUltimoConsultado: {MotivoConsultaMedica: {}}}}`|
|K|ContexturaPA|`{ContexturaPA: {}}`|
|L|UsoNicotina|`{UsoNicotina: {}}`|
|M|AntecedentesMedicosPA|`{AntecedentesMedicosPA: {ProblemaInfarto: {},ProblemaHipertension: {},ProblemaCancer: {},ProblemaDiabetes: {},ProblemaRespiratorio: {},ProblemaSistemaNervioso: {},EnfermedadMental: {},TrastornoHepatico: {},ProblemaRenal: {},EnfermedadesTransmisionSexual: {},ProblemaOjosNarizGarganta: {},ProblemasOseos: {},PiensaSolicitarMedico: {},EstuvoInternado: {},SeHaSometidoAEstudiosDiagnostico: {},EstaEmbarazada: {},HaUsadoDrogas: {},SeguroVidaRechazado: {},BeneficiosPorDiscapacidad: {}}}`|
|XVI|CuestionarioXVI|`{CuestionarioXVI: {}}`|
|XVII|CuestionarioXVII|`{CuestionarioXVII: {}}`|
|XIII|CuestionarioXIII|`{CuestionarioXIII: {}}`|
|XIV|CuestionarioXIV|`{CuestionarioXIV: {DomMedico: {}}}`|
|XV|CuestionarioXV|`{CuestionarioXV: {ConsumoDrogas: {}}}`|
<br>

## Flujo del proceso
![No se encontró la imagen](img\ProcesamientoManual.png)
<br>

1. [<span style="color:green">soporte</span>]: Seguramente hay una comunicación previa para conocer el incidente, en ese momento se debe obtener la información para configurar correctamente el json `solicitud.filters`.
2. [<span style="color:green">soporte</span>]: Con la información recuperada se debe configurar el json `forceSync`.
3. [<span style="color:green">soporte</span>]: Se le debe pedir al usuario que reinicie la aplicación.
4. [<span style="color:blue">usuario</span>]: Debe reiniciar la aplicación.
5. [<span style="color:blue">usuario</span>]: Debe ir hacia la solicitud que informó para su sincronización manual y en el menú contextual le debería aparecer el botón para dicha tarea. **Finalmente lo presiona**.
6. [<span style="color:blue">usuario</span>]: Pese a que el proceso termina en pocos segundos, la sincronización esta trabajando en modo *background*, por eso se recomenda que espere al menos unos 5 minutos antes de continuar con sus tareas. También es recomendable, luego de esperar dicho tiempo, volver a reiniciar la aplicación.

---
## Ejemplos
* El usuario *X100000* informó que una solicitud de su dispositivo no sincronizó, su número es el *D100201*. Haciendo una consulta en la base de datos no se encontró dicha solicitud, por tanto, no podemos recuperar su id. El LP nos informa que la solicitud esta en el estado de *Nuevas*.
    * Para este caso hipotético el archivo `db.json` nos quedaría de la siguinete manera:\
    <code style="font-size: 12px;">
    {\
    &nbsp;&nbsp;...,\
    &nbsp;&nbsp;<span style="color:green">"forceSync"</span>: [\
    &nbsp;&nbsp;{\
    &nbsp;&nbsp;&nbsp;&nbsp;<span style="color:green">"id"</span>:  "DC300ADC-5CB0-4BE0-A49B-002E140E9DCC",\
    &nbsp;&nbsp;&nbsp;&nbsp;<span style="color:green">"retry"</span>: <span style="color:blue">false</span>,\
    &nbsp;&nbsp;&nbsp;&nbsp;<span style="color:green">"userName"</span>: "X100000",\
    &nbsp;&nbsp;&nbsp;&nbsp;<span style="color:green">"solicitud"</span>: {\
    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="color:green">"filters"</span>: {\
    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="color:green">"idEstadoSolicitud"</span>: "1",\
    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="color:green">"numeroSolicitud"</span>: "D100201"\
    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;},\
    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="color:green">"recovery"</span>: {\
    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="color:green">"all"</span>: <span style="color:blue">true</span>,\
    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="color:green">expand"</span>: {}\
    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;}\
    &nbsp;&nbsp;&nbsp;&nbsp;}\
    &nbsp;&nbsp;}]\
    }
    </code>
* El usuario *X100000* informó que una solicitud de su dispositivo no sincronizó, su número es el *D100201*. Haciendo una consulta en la base de datos se recuperó el *id*.
    * Para este caso hipotético el archivo `db.json` nos quedaría de la siguinete manera:\
    <code style="font-size: 12px;">
    {\
    &nbsp;&nbsp;...,\
    &nbsp;&nbsp;<span style="color:green">"forceSync"</span>: [\
    &nbsp;&nbsp;{\
    &nbsp;&nbsp;&nbsp;&nbsp;<span style="color:green">"id"</span>:  "79D33512-7DD6-4FB6-9A9F-9C5C4AC080A8",\
    &nbsp;&nbsp;&nbsp;&nbsp;<span style="color:green">"retry"</span>: <span style="color:blue">false</span>,\
    &nbsp;&nbsp;&nbsp;&nbsp;<span style="color:green">"userName"</span>: "X100000",\
    &nbsp;&nbsp;&nbsp;&nbsp;<span style="color:green">"solicitud"</span>: {\
    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="color:green">"filters"</span>: {\
    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="color:green">"id"</span>: "2874DDF3-0F7D-4DA2-8CD9-C98274CD508A"\
    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;},\
    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="color:green">"recovery"</span>: {\
    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="color:green">"all"</span>: <span style="color:blue">true</span>,\
    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="color:green">expand"</span>: {}\
    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;}\
    &nbsp;&nbsp;&nbsp;&nbsp;}\
    &nbsp;&nbsp;}]\
    }
    </code>
#### **NOTAR QUE**: En cada caso se actualiza el id principal para el registro de *resyncronize*, si usted no lo hace y ya usó dicho id para el mismo LP, debe colocar la marca de retry en <span style="color:blue">true</span>, en caso contrario la configuración no tendrá efecto ya que ese id fue procesado OK anteriormente.

* El usuario *X100000* informó que cierta información no sincronizó, son datos sensibles de la sección *Modalidad de Pago* que se deben recuperar, el número de la solicitud es *D101023*. Haciendo una consulta en la base de datos se recuperó el *id*.
    * Para este caso hipotético el archivo `db.json` nos quedaría de la siguinete manera, sabiendo qué solo queremos recuperar la sección F:\
    <code style="font-size: 12px;">
    {\
    &nbsp;&nbsp;...,\
    &nbsp;&nbsp;<span style="color:green">"forceSync"</span>: [\
    &nbsp;&nbsp;{\
    &nbsp;&nbsp;&nbsp;&nbsp;<span style="color:green">"id"</span>:  "AF66B245-0C56-4BA4-B268-C44444265597",\
    &nbsp;&nbsp;&nbsp;&nbsp;<span style="color:green">"retry"</span>: <span style="color:blue">false</span>,\
    &nbsp;&nbsp;&nbsp;&nbsp;<span style="color:green">"userName"</span>: "X100000",\
    &nbsp;&nbsp;&nbsp;&nbsp;<span style="color:green">"solicitud"</span>: {\
    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="color:green">"filters"</span>: {\
    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="color:green">"id"</span>: "C74C9EBF-DD01-4DFE-B45B-8BC770FCEB21"\
    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;},\
    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="color:green">"recovery"</span>: {\
    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="color:green">"all"</span>: <span style="color:blue">false</span>,\
    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="color:green">expand"</span>: {ModalidaPdago: {}} }\
    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;}\
    &nbsp;&nbsp;&nbsp;&nbsp;}\
    &nbsp;&nbsp;}]\
    }
    </code>
