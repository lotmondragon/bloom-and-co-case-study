# 07 — Aprendizajes

## El contrato de precios debió existir desde el primer commit

La versión inicial del checkout aceptaba del navegador los precios,
subtotales y el total ya calculados, y los guardaba tal cual en el
pedido — el servidor solo confiaba en lo que el cliente decía haber
calculado. Funcionalmente pasaba cualquier prueba manual normal, porque en
el uso normal el navegador calcula bien. El problema es que nada impide
que una petición hecha a mano contra esa misma ruta mande cualquier precio
que quiera; "funciona en el flujo normal" y "es seguro" no son la misma
propiedad, y la primera no implica la segunda.

Esto se corrigió después, como respuesta a una revisión dedicada, no como
parte del diseño original. La corrección en sí fue directa (ver
[05-payment-flow.md](05-payment-flow.md)): el servidor pasó a ser la única
fuente de verdad para cualquier número que afecte cuánto se cobra. Pero el
orden en que ocurrió — construir primero la versión que confía en el
cliente, corregirla después — es exactamente al revés de cómo debería
plantearse cualquier ruta que mueve dinero. En un proyecto que vuelva a
tocar pagos, ese contrato ("el cliente manda intención, el servidor
calcula hechos") es lo primero que definiría, no un fix posterior.

## Faltó una suite de pruebas alrededor de la lógica de precios

La lógica de cálculo de subtotal, envío, recargos y descuentos se probó
manualmente durante el desarrollo, pero no quedó cubierta por pruebas
automatizadas. Es la parte del código con menor tolerancia a errores
silenciosos: un error de redondeo o una condición mal invertida en el
cálculo de un descuento no truena nada visiblemente — simplemente cobra el
monto equivocado, y nadie se entera hasta que alguien lo nota a mano. Esta
es exactamente la clase de lógica (pura, sin dependencias externas, con
entradas y salidas claras) más barata de cubrir con pruebas y más cara de
dejar sin cubrir.

## Las credenciales de "modo prueba" de un proveedor no siempre son intercambiables entre sí

El procesador de pagos ofrece más de un tipo de credencial que se ve
idéntica a simple vista (mismo prefijo, mismo panel) pero que se comporta
distinto según el contexto desde el que se usa. Diagnosticar por qué una
credencial que parecía correcta era rechazada específicamente al llamarse
desde un servidor (y no desde una máquina local) tomó bastante más tiempo
del que debería haber tomado, porque el error devuelto no apunta de forma
obvia a "estás usando el tipo de credencial equivocado para este
contexto". La lección práctica: al integrar un procesador de pagos nuevo,
vale la pena presupuestar tiempo explícito para mapear qué tipo de
credencial de prueba corresponde a qué escenario, antes de asumir que
"dice modo prueba" es suficiente información.

## Qué sí volvería a hacer igual

No todo en la lista de "qué cambiaría" es autocrítica sobre decisiones de
arquitectura — el checkout sin cuenta (ver
[03-guest-checkout.md](03-guest-checkout.md)) y el CMS headless (ver
[04-headless-cms.md](04-headless-cms.md)) siguen pareciendo las decisiones
correctas para el tamaño y el perfil de este negocio específico. Los tres
puntos de arriba son sobre secuencia y disciplina de proceso, no sobre la
forma final del sistema.
