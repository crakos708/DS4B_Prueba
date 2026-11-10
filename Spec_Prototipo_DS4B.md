# Spec de generación del prototipo: DS4B Onboarding Digital de Clientes

> Extraído del Spec general. Metodología: User Centered Design.

## 1. Usuario principal
**Cliente nuevo de DS4B** (persona sin experiencia técnica que quiere contratar un servicio sin trámites presenciales).

## 2. Historias a prototipar (mejor priorizadas)
| Clave | Historia | Prioridad |
|---|---|---|
| DS4BON-7 | Formulario de registro | 10 |
| DS4BON-9 | Validación automática de datos | 10 |
| DS4BON-11 | Presentar términos y condiciones | 10 |
| DS4BON-12 | Firma electrónica | 10 |
| DS4BON-13 | Activar cuenta | 10 |
| DS4BON-17 | Notificación de cuenta activada | 10 |

## 3. Pantallas y flujo
1. **Registro (7):** campos nombre, correo, teléfono; botón *Enviar*.
2. **Validación (9):** se ejecuta al enviar. Verifica formato y duplicados. Si falla, muestra qué dato corregir y vuelve al paso 1.
3. **Términos (11):** texto de términos; *Continuar* se habilita al terminar de leer (scroll al final).
4. **Firma (12):** el cliente escribe su nombre completo como firma y confirma; se guarda fecha y método ("firma escrita").
5. **Cuenta activada (13 y 17):** muestra la cuenta activa y el aviso enviado por correo y WhatsApp (simulado).

## 4. Reglas de comportamiento
- Correo: formato válido; se normaliza a minúsculas y sin espacios antes de comparar (RN1).
- Teléfono: solo dígitos, entre 7 y 15.
- Duplicados: lista en memoria; precargado `ana.perez@correo.com` para demostrar el rechazo.
- La cuenta solo se activa si hay validación y firma completas (RN2).
- Sin backend: todo en memoria, sin persistencia.

## 5. Requisitos no funcionales aplicados
Responsivo (DS4BON-23), mensajes de error claros (DS4BON-27), registro en menos de 5 minutos (DS4BON-21).

## 6. Criterios de aceptación a demostrar
- **DS4BON-7**
  - Dado que soy un cliente nuevo, cuando abro el formulario web de registro, entonces puedo ingresar mi nombre, correo y teléfono.
  - Dado que completé mis datos, cuando envío el formulario, entonces el sistema recibe la solicitud para continuar con la validación.
- **DS4BON-9**
  - Dado que envié mi correo y mi teléfono, cuando el sistema los valida, entonces verifica que tengan un formato correcto.
  - Dado que mi correo o mi teléfono ya están registrados, cuando el sistema los valida, entonces los detecta como duplicados y el registro no continúa.
- **DS4BON-11**
  - Dado que mis datos fueron validados, cuando el sistema avanza al paso de firma, entonces me muestra los términos y condiciones antes de solicitarme la firma.
  - Dado que estoy en el paso de términos, cuando termino de leerlos, entonces puedo continuar al paso de firma.
- **DS4BON-12**
  - Dado que leí los términos y condiciones, cuando los acepto mediante firma electrónica, entonces el sistema registra mi aceptación.
  - Dado que firmé los términos, cuando el sistema registra la firma, entonces guarda la evidencia con la fecha y el método de firma utilizado.
- **DS4BON-13**
  - Dado que mis datos fueron validados y firmé los términos, cuando el sistema detecta que ambos pasos están completos, entonces activa mi cuenta automáticamente.
  - Dado que falta la validación o la firma, cuando el sistema revisa mi solicitud, entonces no activa mi cuenta.
- **DS4BON-17**
  - Dado que mi cuenta fue activada, cuando el sistema completa la activación, entonces me envía el aviso por correo electrónico y por WhatsApp.

## 7. Restricciones técnicas
Un solo archivo HTML autocontenido (HTML + CSS + JS), sin dependencias externas, accesible (etiquetas, foco visible, contraste), tema claro y oscuro, adaptable a móvil.

## 8. Prompt sugerido para el agente
> Genera un prototipo web de un solo archivo HTML para el flujo de onboarding digital de DS4B descrito en este Spec. Implementa las 5 pantallas, las reglas de la sección 4 y demuestra los criterios de la sección 6. Sin librerías externas.
