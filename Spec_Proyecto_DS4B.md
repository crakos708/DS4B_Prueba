# Spec del proyecto: DS4B Onboarding Digital de Clientes

> Documento de especificación para agentes de código (SDD). Fuente: Confluence (espacio DS4BONB) y Jira (proyecto DS4BON).

## 1. Contexto
DS4B incorpora clientes nuevos desde el registro hasta la activación del servicio. Hoy el proceso tiene fricciones y demoras que retrasan la activación. El sistema automatiza el registro, la validación de datos, la firma electrónica de términos, la activación de la cuenta y las notificaciones.

**Objetivo:** proceso ágil de incorporación (registro, verificación, activación) que elimine los puntos de fricción.

## 2. Stakeholders
| Rol | Descripción |
|---|---|
| Cliente | Se registra para adquirir un servicio de DS4B |
| Analista de Onboarding | Da seguimiento a casos de revisión manual |
| Gerente de Servicio al Cliente | Cliente interno; define reglas y prioriza |
| Equipo de Backend / TI | Integra con las plataformas que activan el servicio |
| Proveedor de Firma Electrónica | Servicio externo que certifica la firma |

## 3. Supuestos, restricciones y fuera de alcance
- **Supuestos:** el cliente tiene correo y teléfono propios y activos; el servicio a contratar ya está definido.
- **Restricciones:** firma electrónica con proveedor externo; notificaciones solo por correo y WhatsApp; el recordatorio de registro incompleto se envía una sola vez.
- **Fuera de alcance:** soporte, uso o facturación posteriores a la activación; revisión manual de casos que la validación automática no resuelva.

## 4. Modelo de dominio
| Entidad | Atributos principales |
|---|---|
| Cliente | nombre, correo (único, normalizado en minúsculas), teléfono (único) |
| SolicitudRegistro | id, cliente, fecha, canal (web/app), estado |
| Validación | solicitud, resultado (válida/inválida), datos a corregir |
| FirmaElectrónica | solicitud, fecha, método, evidencia |
| Cuenta | cliente, estado (inactiva/activa), fecha de activación |
| Notificación | destinatario, canal (correo/WhatsApp), tipo, fecha |

## 5. Proceso (BPMN)
Carriles: Cliente y Sistema DS4B.
1. Cliente diligencia el formulario de registro.
2. Sistema valida los datos. Si no son válidos, notifica el error y el cliente corrige y reintenta; si no completa, termina como *registro no completado*.
3. Si son válidos, el cliente acepta términos y firma electrónicamente.
4. Sistema activa la cuenta y el servicio contratado.
5. Sistema notifica la activación al cliente (correo y WhatsApp).
6. Si pasan 24 h sin completar, el sistema envía **un único** recordatorio.

## 6. Necesidades (épicas)
| Épica Jira | Necesidad | Título |
|---|---|---|
| DS4BON-1 | NEC001 | Registrar clientes |
| DS4BON-2 | NEC002 | Validar la información del cliente |
| DS4BON-3 | NEC003 | Formalizar la aceptación de términos |
| DS4BON-4 | NEC004 | Activar el servicio del cliente |
| DS4BON-5 | NEC005 | Notificar al cliente |
| DS4BON-6 | NEC006 | Recuperar registros incompletos |

## 7. Requisitos
Campos: Tipo, Clasificación (Obligatoria/Importante/Deseable), Prioridad de negocio (1-10), Estado de negocio (Propuesta/Aprobada/Rechazada).

### DS4BON-7 Formulario de registro
- **Tipo:** Funcional  
- **Clasificación:** Obligatoria  
- **Prioridad de negocio:** 10  
- **Estado de negocio:** Aprobada  
- **Release:** Release 1  
- **Necesidad:** NEC001 Registrar clientes
- **Criterios de aceptación:**
  1. Dado que soy un cliente nuevo, cuando abro el formulario web de registro, entonces puedo ingresar mi nombre, correo y teléfono.
  2. Dado que completé mis datos, cuando envío el formulario, entonces el sistema recibe la solicitud para continuar con la validación.

### DS4BON-8 Guardar datos de registro
- **Tipo:** Funcional  
- **Clasificación:** Obligatoria  
- **Prioridad de negocio:** 8  
- **Estado de negocio:** Propuesta  
- **Release:** Release 2  
- **Necesidad:** NEC001 Registrar clientes
- **Criterios de aceptación:**
  1. Dado que un cliente envió el formulario, cuando el sistema recibe la solicitud, entonces guarda los datos junto con la fecha y el canal de registro (web/app).
  2. Dado que existe una solicitud guardada, cuando el equipo de DS4B la consulta, entonces puede ver la fecha y el canal por el que se registró.

### DS4BON-9 Validación automática de datos
- **Tipo:** Funcional  
- **Clasificación:** Obligatoria  
- **Prioridad de negocio:** 10  
- **Estado de negocio:** Aprobada  
- **Release:** Release 1  
- **Necesidad:** NEC002 Validar la información del cliente
- **Criterios de aceptación:**
  1. Dado que envié mi correo y mi teléfono, cuando el sistema los valida, entonces verifica que tengan un formato correcto.
  2. Dado que mi correo o mi teléfono ya están registrados, cuando el sistema los valida, entonces los detecta como duplicados y el registro no continúa.

### DS4BON-10 Notificar resultado de validación en pantalla
- **Tipo:** Funcional  
- **Clasificación:** Importante  
- **Prioridad de negocio:** 8  
- **Estado de negocio:** Propuesta  
- **Release:** Release 2  
- **Necesidad:** NEC002 Validar la información del cliente
- **Criterios de aceptación:**
  1. Dado que el sistema terminó de validar mis datos, cuando la validación es exitosa, entonces veo en pantalla, en tiempo real, que puedo continuar.
  2. Dado que algún dato no pasó la validación, cuando el sistema termina de validar, entonces veo en pantalla qué dato debo corregir.

### DS4BON-11 Presentar términos y condiciones
- **Tipo:** Funcional  
- **Clasificación:** Obligatoria  
- **Prioridad de negocio:** 10  
- **Estado de negocio:** Aprobada  
- **Release:** Release 1  
- **Necesidad:** NEC003 Formalizar la aceptación de términos
- **Criterios de aceptación:**
  1. Dado que mis datos fueron validados, cuando el sistema avanza al paso de firma, entonces me muestra los términos y condiciones antes de solicitarme la firma.
  2. Dado que estoy en el paso de términos, cuando termino de leerlos, entonces puedo continuar al paso de firma.

### DS4BON-12 Firma electrónica
- **Tipo:** Funcional  
- **Clasificación:** Obligatoria  
- **Prioridad de negocio:** 10  
- **Estado de negocio:** Aprobada  
- **Release:** Release 1  
- **Necesidad:** NEC003 Formalizar la aceptación de términos
- **Criterios de aceptación:**
  1. Dado que leí los términos y condiciones, cuando los acepto mediante firma electrónica, entonces el sistema registra mi aceptación.
  2. Dado que firmé los términos, cuando el sistema registra la firma, entonces guarda la evidencia con la fecha y el método de firma utilizado.

### DS4BON-13 Activar cuenta
- **Tipo:** Funcional  
- **Clasificación:** Obligatoria  
- **Prioridad de negocio:** 10  
- **Estado de negocio:** Aprobada  
- **Release:** Release 1  
- **Necesidad:** NEC004 Activar el servicio del cliente
- **Criterios de aceptación:**
  1. Dado que mis datos fueron validados y firmé los términos, cuando el sistema detecta que ambos pasos están completos, entonces activa mi cuenta automáticamente.
  2. Dado que falta la validación o la firma, cuando el sistema revisa mi solicitud, entonces no activa mi cuenta.

### DS4BON-14 Integración con sistema backend
- **Tipo:** Funcional  
- **Clasificación:** Obligatoria  
- **Prioridad de negocio:** 8  
- **Estado de negocio:** Propuesta  
- **Release:** Release 2  
- **Necesidad:** NEC004 Activar el servicio del cliente
- **Criterios de aceptación:**
  1. Dado que la cuenta de un cliente fue activada, cuando el sistema de onboarding se comunica con el backend, entonces el servicio contratado queda habilitado.
  2. Dado que el servicio fue habilitado en el backend, cuando el cliente ingresa, entonces puede usar el servicio que contrató.

### DS4BON-15 Notificación de registro recibido
- **Tipo:** Funcional  
- **Clasificación:** Importante  
- **Prioridad de negocio:** 5  
- **Estado de negocio:** Propuesta  
- **Release:** Release 3  
- **Necesidad:** NEC005 Notificar al cliente
- **Criterios de aceptación:**
  1. Dado que envié mi solicitud de registro, cuando el sistema la recibe, entonces me envía una confirmación por correo electrónico y por WhatsApp.

### DS4BON-16 Notificación de resultado de validación
- **Tipo:** Funcional  
- **Clasificación:** Importante  
- **Prioridad de negocio:** 5  
- **Estado de negocio:** Propuesta  
- **Release:** Release 3  
- **Necesidad:** NEC005 Notificar al cliente
- **Criterios de aceptación:**
  1. Dado que el sistema terminó de validar mis datos, cuando el resultado está disponible, entonces me lo notifica por correo electrónico y por WhatsApp.
  2. Dado que la validación indicó que debo corregir información, cuando recibo la notificación, entonces el mensaje me indica que debo corregir mis datos.

### DS4BON-17 Notificación de cuenta activada
- **Tipo:** Funcional  
- **Clasificación:** Obligatoria  
- **Prioridad de negocio:** 10  
- **Estado de negocio:** Aprobada  
- **Release:** Release 1  
- **Necesidad:** NEC005 Notificar al cliente
- **Criterios de aceptación:**
  1. Dado que mi cuenta fue activada, cuando el sistema completa la activación, entonces me envía el aviso por correo electrónico y por WhatsApp.

### DS4BON-18 Recordatorio de registro incompleto
- **Tipo:** Funcional  
- **Clasificación:** Importante  
- **Prioridad de negocio:** 5  
- **Estado de negocio:** Propuesta  
- **Release:** Release 2  
- **Necesidad:** NEC006 Recuperar registros incompletos
- **Criterios de aceptación:**
  1. Dado que pasaron 24 horas desde el inicio de mi solicitud, cuando aún no he completado el proceso, entonces el sistema me envía un recordatorio por correo y WhatsApp.
  2. Dado que ya recibí un recordatorio por esa solicitud, cuando sigue pasando el tiempo sin avance, entonces el sistema no me envía otro.
  3. Dado que completé todo el proceso antes de las 24 horas, cuando se cumple ese plazo, entonces el sistema no me envía recordatorio.

### DS4BON-19 Disponibilidad 24 horas
- **Tipo:** No funcional  
- **Clasificación:** Obligatoria  
- **Prioridad de negocio:** 10  
- **Estado de negocio:** Aprobada  
- **Release:** Release 1  
- **Necesidad:** Técnica / no funcional
- **Criterios de aceptación:**
  1. Dado que quiero registrarme, cuando intento acceder al sistema a cualquier hora del día, entonces el sistema está disponible las 24 horas.

### DS4BON-20 Seguridad de la información
- **Tipo:** No funcional  
- **Clasificación:** Obligatoria  
- **Prioridad de negocio:** 10  
- **Estado de negocio:** Aprobada  
- **Release:** Release 1  
- **Necesidad:** Técnica / no funcional
- **Criterios de aceptación:**
  1. Dado que ingresé mis datos personales, cuando el sistema los almacena, entonces quedan guardados cifrados.
  2. Dado que alguien intenta acceder a la información de un cliente, cuando no se autentica de forma segura, entonces el sistema no le da acceso.

### DS4BON-21 Usabilidad del registro
- **Tipo:** No funcional  
- **Clasificación:** Importante  
- **Prioridad de negocio:** 8  
- **Estado de negocio:** Propuesta  
- **Release:** Release 2  
- **Necesidad:** Técnica / no funcional
- **Criterios de aceptación:**
  1. Dado que soy un cliente sin experiencia técnica, cuando realizo el registro completo, entonces logro terminarlo en menos de 5 minutos.

### DS4BON-22 Tiempo de respuesta de validación
- **Tipo:** No funcional  
- **Clasificación:** Importante  
- **Prioridad de negocio:** 5  
- **Estado de negocio:** Propuesta  
- **Release:** Release 3  
- **Necesidad:** Técnica / no funcional
- **Criterios de aceptación:**
  1. Dado que envié mis datos, cuando el sistema los valida automáticamente, entonces el resultado está disponible en menos de 5 segundos.

### DS4BON-23 Diseño responsivo
- **Tipo:** No funcional  
- **Clasificación:** Importante  
- **Prioridad de negocio:** 5  
- **Estado de negocio:** Propuesta  
- **Release:** Release 3  
- **Necesidad:** Técnica / no funcional
- **Criterios de aceptación:**
  1. Dado que uso un computador, una tablet o un móvil, cuando abro el sistema de registro, entonces puedo completar mi registro desde cualquiera de ellos.

### DS4BON-24 Recuperación ante fallos del sistema
- **Tipo:** No funcional  
- **Clasificación:** Importante  
- **Prioridad de negocio:** 8  
- **Estado de negocio:** Propuesta  
- **Release:** Release 2  
- **Necesidad:** Técnica / no funcional
- **Criterios de aceptación:**
  1. Dado que ya envié mis datos, cuando el sistema sufre una interrupción, entonces mi solicitud se conserva sin pérdida de información.
  2. Dado que el sistema se restableció, cuando vuelvo a ingresar, entonces retomo mi proceso desde el último paso completado.

### DS4BON-25 Autorización para el tratamiento de datos
- **Tipo:** No funcional  
- **Clasificación:** Obligatoria  
- **Prioridad de negocio:** 10  
- **Estado de negocio:** Propuesta  
- **Release:** Release 2  
- **Necesidad:** Técnica / no funcional
- **Criterios de aceptación:**
  1. Dado que voy a enviar el formulario, cuando el sistema me presenta la política de tratamiento de datos, entonces debo aceptarla para continuar.
  2. Dado que no acepto la política, cuando intento enviar el formulario, entonces el sistema no procesa mi registro.

### DS4BON-26 Compatibilidad con navegadores
- **Tipo:** No funcional  
- **Clasificación:** Importante  
- **Prioridad de negocio:** 8  
- **Estado de negocio:** Propuesta  
- **Release:** Release 3  
- **Necesidad:** Técnica / no funcional
- **Criterios de aceptación:**
  1. Dado que uso Chrome, Edge, Firefox o Safari en su versión reciente, cuando completo el registro, entonces todas las funciones operan igual en cada uno.

### DS4BON-27 Mensajes de error comprensibles
- **Tipo:** No funcional  
- **Clasificación:** Deseable  
- **Prioridad de negocio:** 3  
- **Estado de negocio:** Propuesta  
- **Release:** Release 3  
- **Necesidad:** Técnica / no funcional
- **Criterios de aceptación:**
  1. Dado que cometí un error al diligenciar el formulario, cuando el sistema me lo informa, entonces el mensaje usa lenguaje sencillo e indica cómo corregirlo.

## 8. Plan de releases
| Release | Contenido |
|---|---|
| Release 1 | 7, 9, 11, 12, 13, 17, 19, 20 |
| Release 2 | 8, 10, 14, 18, 21, 24, 25 |
| Release 3 | 15, 16, 22, 23, 26, 27 |

## 9. Reglas de negocio
- RN1: el correo se compara en minúsculas y sin espacios al inicio ni al final (origen: error DS4BON-28).
- RN2: la cuenta solo se activa con validación **y** firma completas.
- RN3: el recordatorio se envía una única vez a las 24 h, y no se envía si el proceso ya terminó.
- RN4: la firma guarda evidencia con fecha y método.

## 10. Trazabilidad
Cada requisito se prueba con sus criterios de aceptación (Modelo en V). Ver hoja *Casos de prueba* del Excel: `CP-xx` ↔ `DS4BON-xx`.
