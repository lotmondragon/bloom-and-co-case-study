# 05 — Flujo de pago

## Por qué redirección y no un widget embebido

El procesador de pagos usado ofrece dos modos de integración: un checkout
alojado, al que se redirige a la clienta para capturar los datos de pago,
o un widget embebido que captura esos datos dentro del propio sitio. Se
eligió el modo de redirección por una razón concreta de superficie de
responsabilidad: los datos de tarjeta nunca tocan el servidor ni el
frontend de este proyecto en ningún momento — los captura enteramente la
página alojada del procesador. Eso reduce drásticamente el alcance de
cualquier auditoría de seguridad o de cumplimiento sobre este código,
porque la parte más sensible del flujo (captura de datos de pago) ni
siquiera es parte de la aplicación.

## Por qué el webhook es la fuente de verdad, no el redirect del navegador

Después de pagar, el procesador redirige el navegador de vuelta al sitio.
Es tentador usar ese momento — "el navegador volvió a la página de éxito"
— como la señal de que el pago se completó. Es la señal equivocada: ese
redirect lo controla completamente el navegador de la clienta, que puede
cerrarse, perder conexión, o (en el peor caso) ser manipulado para visitar
esa URL sin haber pagado nada. Confiar en el redirect del navegador para
marcar un pedido como pagado sería equivalente a dejar que el cliente
decida si pagó.

La confirmación real llega por un canal distinto: una notificación
asíncrona que el procesador de pagos manda directamente de servidor a
servidor, fuera de la sesión del navegador, en cuanto el estado del pago
cambia. Solo esa notificación puede mover un pedido de "pendiente" a
"pagado". El servidor que la recibe, antes de confiar en ella, vuelve a
consultarle al procesador de pagos el estado real de ese pago — nunca
actualiza un pedido solo porque llegó una notificación con la forma
correcta; la notificación es un aviso de que hay que ir a verificar, no la
verificación en sí.

(La validación de que esa notificación efectivamente viene del procesador
de pagos y no de un tercero — la implementación de la firma — es
deliberadamente el único detalle que no se describe aquí: es superficie
de seguridad real del sitio en producción.)

## Qué significa esto para la página de confirmación

Como la notificación es asíncrona, puede llegar antes, durante o después
de que la clienta regrese a la página de confirmación. La página está
diseñada para funcionar correctamente en cualquiera de los tres casos:
muestra el resumen del pedido leyendo su estado actual de la base de
datos, sea cual sea — "pendiente" o "pagado" — en lugar de asumir que, por
haber llegado ahí, el pago ya se procesó. Si la notificación todavía no
llega, la clienta ve un estado consistente con la realidad, no una
promesa que el sistema todavía no puede respaldar.

## Qué pasa si la notificación nunca llega

El procesador de pagos reintenta notificaciones no confirmadas — no es un
evento de una sola oportunidad. Si a pesar de eso un pedido se queda
indefinidamente en estado pendiente mientras el pago sí se realizó, es un
caso operativo, no un fallo de diseño del flujo: se resuelve verificando
manualmente el pago en el panel del procesador y actualizando el pedido a
mano. Ese es, de hecho, el primer procedimiento documentado en el runbook
interno del proyecto (no publicado aquí).

## Por qué el pedido nunca confía en un número que venga del navegador

Antes de que existiera esta salvaguarda, la ruta que crea un pedido
recibía del navegador los precios y el total ya calculados, y simplemente
los guardaba. El problema es estructural: cualquier petición hecha a mano
contra esa misma ruta (sin pasar por la interfaz del sitio) puede mandar
cualquier número que quiera en esos campos. Nada en el protocolo HTTP
distingue una petición que viene del checkout real de una construida a
propósito con un precio manipulado.

La corrección fue tratar el checkout como un formulario de intención, no
de hechos: el navegador manda identificadores de producto y cantidades, y
el servidor —nunca el cliente— vuelve a resolver cada precio contra el
catálogo, revalida cualquier código de descuento y la zona de envío contra
las reglas vigentes en el servidor, y solo entonces calcula el total que
efectivamente se cobra y se guarda. El navegador puede seguir mostrando su
propio cálculo para que la clienta vea un resumen antes de pagar, pero ese
número es cosmético — el que cuenta es el que el servidor vuelve a
calcular desde cero.
