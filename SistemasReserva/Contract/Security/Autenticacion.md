# Acuerdo de autenticación — SistemaReservas

**Estado:** propuesta pendiente de aprobación por el equipo.

## 1. Alcance

La consigna exige que el usuario se autentique para solicitar una reserva y que los microservicios se autentiquen entre sí. Consultar eventos y asientos es público.

## 2. Propuesta de funcionamiento

- **Usuario → Availability:** para solicitar una reserva, el usuario obtiene un token del servidor de autorización. Availability valida el token antes de aceptar la solicitud.
- **Availability → Booking (REST):** Availability presenta una credencial de servicio válida al consultar el estado de solicitudes. Booking verifica autenticidad, vigencia y permisos.
- **Booking → Payment (gRPC):** Booking se autentica como servicio ante Payment, que valida la credencial y autoriza la operación.
- **Mensajería Azure:** Availability y los consumidores usan credenciales propias para publicar o recibir mensajes. No se colocan tokens de usuario ni secretos en los mensajes.
- **Notifications:** usa su propia credencial para consumir eventos si el broker así lo requiere.

## 3. Decisiones por acordar

1. Servidor de autorización: implementación propia o producto externo; y, si aplica, proveedor de identidad (Google, GitHub, etc.).
2. Formato y validación de tokens (por ejemplo, JWT), emisor, destinatarios y permisos de cada servicio.
3. Mecanismo de autenticación servicio a servicio (por ejemplo, OAuth 2.0 Client Credentials si el servidor seleccionado lo admite).
4. Qué servicio valida cada credencial y cómo se prueban las respuestas ante ausencia o invalidez del token.
5. Servicio concreto del broker de Azure y permisos para publicar/consumir.

## 4. Seguridad mínima

- Mantener credenciales, cadenas de conexión y claves fuera de Git y de los diagramas.
- Usar HTTPS para HTTP y gRPC en los entornos que lo requieran.
- No registrar tokens completos ni secretos en los logs de auditoría.
- Denegar solicitudes no autenticadas o sin permisos en los endpoints protegidos.

## 5. Pruebas de aceptación

- Un visitante puede consultar disponibilidad sin iniciar sesión.
- Un usuario sin token válido no puede solicitar una reserva.
- Booking rechaza una consulta REST sin autorización válida.
- Payment rechaza una llamada gRPC no autorizada.
- Los servicios autorizados pueden completar el flujo de reserva.


