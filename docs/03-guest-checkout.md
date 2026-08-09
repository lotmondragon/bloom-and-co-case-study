# 03 — Checkout sin cuenta

## La decisión

No existe registro de usuario, login ni contraseña en ningún punto del
sitio. El checkout pide los datos mínimos necesarios para cumplir el
pedido — quién recibe, a dónde, cuándo, quién paga y cómo contactarlo — y
nada más. Cada compra es, desde el punto de vista del sistema, un evento
aislado.

## El trade-off, sin suavizarlo

Esto renuncia a cosas reales:

- **No hay historial de pedidos del lado de la clienta.** Si alguien
  quiere repetir un pedido de hace tres meses, no hay un botón para eso —
  tiene que recordarlo o volver a explicarlo.
- **No hay perfil de clienta recurrente.** El sistema no sabe, sin cruzar
  manualmente por correo o teléfono, que la persona que compró hoy es la
  misma que compró en diciembre. Cualquier personalización basada en
  historial de compra (descuentos por lealtad, recomendaciones) requeriría
  construir eso desde cero más adelante.
- **Cada pedido repite la captura de datos.** Una clienta frecuente
  vuelve a escribir su nombre, teléfono y dirección cada vez, en lugar de
  reutilizar algo guardado.

Ninguno de estos puntos es un detalle menor — son capacidades reales que
un sitio con cuentas de usuario sí tendría.

## Por qué se decidió así de todos modos

El perfil de compra observado (y el que describe el negocio desde antes
del sitio, ver [01-problem-space.md](01-problem-space.md)) es
mayoritariamente de regalo: alguien compra un arreglo para otra persona,
usualmente una sola vez por ocasión, sin necesidad ni expectativa de una
relación continua tipo "cuenta" con la tienda. En ese perfil, cada campo
adicional antes de poder pagar — y crear una cuenta es, como mínimo, un
correo y una contraseña más una verificación — es fricción pura frente a
un beneficio que la clienta no está pidiendo.

El costo de esa fricción no es hipotético: es la razón número uno de
abandono de carrito en e-commerce en general, y aquí no había ningún dato
que sugiriera que esta base de clientas fuera la excepción. Quitar la
cuenta de usuario no fue una simplificación técnica — fue apostar que el
beneficio de un checkout de un solo paso superaba lo que se perdía en
personalización y retención automatizada.

## Qué se conserva a pesar de no tener cuentas

Cada pedido, aunque no esté ligado a una cuenta, sí queda guardado
completo en la base de datos (ver
[02-architecture.md](02-architecture.md)) — lo que falta no es el
registro del pedido, sino la capa de identidad que uniría varios pedidos
de la misma persona automáticamente. Si en el futuro el volumen justificara
recuperar esa relación (por ejemplo, para programas de fidelidad), la ruta
más simple es identificar clientas recurrentes por correo o teléfono
post-hoc, no necesariamente forzar una cuenta desde el checkout.
