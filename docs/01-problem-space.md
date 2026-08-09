# 01 — El problema de negocio

## Cómo vendía el negocio antes

Todo el ciclo de venta ocurría dentro de una app de mensajería. Una
clienta interesada mandaba un mensaje, típicamente con una foto de
referencia de algo que había visto en redes o que alguien le recomendó.
Desde ahí:

1. Una de las dueñas confirmaba a mano si ese arreglo (o algo parecido)
   estaba disponible esa semana, según qué flor hubiera en inventario.
2. El precio se cotizaba en el momento, por chat, ajustado según tamaño,
   flores disponibles y fecha de entrega.
3. La entrega se negociaba por mensajes: zona, fecha, ventana de horario.
4. El pago se resolvía aparte — una transferencia bancaria o un link de
   pago generado manualmente, mandado por el mismo chat.
5. No había un registro estructurado de nada de esto más allá del propio
   historial de mensajes.

Esto no era un flujo improvisado por falta de herramientas: era, de hecho,
el modelo de negocio completo, y funcionaba razonablemente bien a bajo
volumen porque cada cliente recibía atención personalizada.

## Qué lo limitaba

- **Disponibilidad nunca era visible por adelantado.** Una clienta no
  podía saber qué arreglos existían ni qué costaban sin iniciar una
  conversación primero. Eso descarta a cualquier persona que solo quiere
  comparar opciones antes de decidir.
- **Cada venta requería a alguien disponible en tiempo real.** Si nadie
  contestaba el chat en las siguientes horas, la venta se enfriaba o se
  perdía directamente. El negocio no podía vender mientras dormía.
- **El cobro era un paso manual y frágil.** No había una relación
  automática entre "este pedido" y "este pago" — dependía de que alguien
  llevara la cuenta mentalmente o en una libreta aparte. Un pago que
  llegaba sin contexto (por ejemplo, un monto que no coincidía con lo
  cotizado) requería investigación manual.
- **No existía ningún dato reutilizable.** No había forma de ver, por
  ejemplo, cuáles arreglos se vendían más, ni de mandar un recordatorio
  automático de una fecha especial — todo vivía disperso en conversaciones
  individuales.

## Qué se decidió resolver (y qué no)

El objetivo del sitio no fue eliminar la mensajería del negocio — es una
parte genuina de cómo esta clienta se relaciona con su base, y sigue
siendo el canal correcto para pedidos personalizados (ver
[06-custom-orders.md](06-custom-orders.md)). El objetivo fue sacar del chat
específicamente la parte que era pura fricción operativa: mostrar un
catálogo con precio fijo, dejar que la clienta arme un pedido sin esperar
respuesta de nadie, y cobrar de forma que el pago quede ligado
automáticamente al pedido correcto, sin intervención manual.

Eso definió el alcance: catálogo con precios fijos y gestionable sin
depender de un desarrollador (ver
[04-headless-cms.md](04-headless-cms.md)), checkout de una sola sesión sin
fricción de registro (ver [03-guest-checkout.md](03-guest-checkout.md)), y
un flujo de pago donde la confirmación del pedido no depende de que
alguien la haga a mano (ver [05-payment-flow.md](05-payment-flow.md)).
