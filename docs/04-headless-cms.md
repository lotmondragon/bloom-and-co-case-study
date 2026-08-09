# 04 — CMS headless para autonomía de la clienta

## El problema que resuelve

Las dos dueñas del negocio son quienes deciden, semana a semana, qué
arreglos están disponibles, a qué precio y con qué fotos. Esa cadencia —
cambios frecuentes, de contenido, hechos por personas sin conocimiento
técnico — es exactamente el caso que un catálogo definido en código no
resuelve bien: cada cambio de precio o cada arreglo nuevo habría requerido
tocar el repositorio y desplegar de nuevo, con un desarrollador de por
medio.

## La alternativa descartada

Definir el catálogo directamente en el código (un archivo con la lista de
arreglos, sus precios y sus descripciones) hubiera sido más simple de
construir en un primer momento: sin un servicio externo que integrar, sin
esquema que diseñar, sin curva de aprendizaje para nadie. Se descartó
porque esa simplicidad inicial se paga después, todas las semanas, en
forma de dependencia total del desarrollador para el mantenimiento más
básico del negocio — cambiar un precio o subir una foto nueva.

## Por qué headless específicamente

Un CMS headless separa el contenido (qué arreglos existen, sus datos) de
la presentación (cómo se ve la página). Eso permite dos cosas a la vez:
las dueñas administran el contenido desde un panel visual pensado para
gente no técnica, y el sitio sigue siendo una aplicación Next.js normal
que controla por completo su propio diseño, rendimiento y comportamiento —
no hay que ceder el frontend a las plantillas de un CMS tradicional para
ganar esa autonomía de contenido.

## Qué autonomía gana el negocio, en concreto

- Agregar un arreglo nuevo, retirar uno agotado, o marcar cuáles aparecen
  como destacados en la portada — todo sin pedirle nada a un
  desarrollador.
- Cambiar un precio con efecto inmediato en el sitio.
- Subir fotos de producto directamente, en cuanto existan (al momento de
  escribir esto, el catálogo todavía usa arte de placeholder porque las
  fotos reales no se han tomado — ver las capturas en el README).

## El costo de esta decisión

Cada arreglo del catálogo depende de una plataforma externa: si ese
servicio tiene una interrupción, el catálogo no se puede actualizar (y en
el peor caso, no se puede ni leer) hasta que se restablezca. Es un
trade-off aceptado conscientemente: la disponibilidad de un proveedor
externo especializado en este problema es, en la práctica, más alta que
la que hubiera dado cualquier alternativa casera, y el beneficio de
autonomía para el negocio es diario, no ocasional.
