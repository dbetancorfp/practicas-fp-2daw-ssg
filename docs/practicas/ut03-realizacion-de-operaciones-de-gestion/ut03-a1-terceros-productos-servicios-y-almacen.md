# UT03-A1 - Terceros, productos, servicios y almacen

## 1. Resultado de aprendizaje, contenido y criterios de evaluación

### Resultado de aprendizaje
RA3. Realiza operaciones de gestión, consulta y análisis de la información siguiendo las especificaciones de diseño y utilizando las herramientas proporcionadas por los sistemas ERP-CRM.

###  Contenido                                                                                                                                                                                 
1. Definición de campos                                                                                                                                                                  

### Criterios de evaluación                                                                                                                                                                   
a) Se han usado herramientas/lenguajes de consulta y manipulación de datos.                                                                                                              

# 2. Agrupamiento

Individual

# 3. Contexto

Con el ERP gratuito de Odoo toca dar de alta los datos maestros de terceros (clientes y proveedores), productos, servicios y almacén con los que operará la empresa.

# 4. Objetivo de la actividad

Practicar la creación y clasificación de terceros, productos, servicios y almacén en el ERP, y el reaprovisionamiento de stock.

# 5. Enunciado de la actividad

**Realiza un informe adjuntando los pantallazos de las siguientes tareas:**

1. **Creación de los 8 terceros y sus contactos**

- Investigación: Consulta en la documentación de la aplicación Contactos cómo se estructuran las fichas maestras.
- Acción: Registra en el sistema 8 empresas (4 proveedores y 4 clientes o clientes potenciales). Basándote en el manual, asocia al menos un contacto (persona física) a cada una de estas empresas.
- Evidencia: Lista de los ocho terceros y capturas de una ficha de proveedor y otra de cliente mostrando el contacto vinculado.

2. **Etiquetar contactos y diferenciar jerarquías**

- Investigación: Busca en las guías oficiales cómo funciona el sistema de etiquetas (tags) nativo para categorizar a los partners sin necesidad de instalar ningún módulo adicional.
- Acción: Crea y aplica las etiquetas "Poco importante", "Medio importante", "Muy importante" y "VIP" desde la propia ficha del contacto. Si un tercero es VIP, asígnale también la etiqueta "Muy importante" para mantener la coherencia en las búsquedas.

3. **Asignar el Vendedor a los proveedores**

- Investigación: Revisa la documentación sobre la gestión de permisos y roles comerciales en las fichas de las empresas.
- Acción: Asigna el campo "Vendedor" a cada uno de los cuatro proveedores para identificar al responsable de la relación comercial. Demuestra mediante una prueba que este rol en la ficha maestra es independiente del usuario "Comprador" que figura en una orden de compra concreta.

4. **Configurar el Almacén Principal y las categorías de productos**

- Investigación: Dirígete a las guías de Inventario. Localiza cómo se configuran las ubicaciones logísticas y cómo se estructura el árbol de categorías del catálogo.

- Acción: Crea o renombra el almacén base como "Almacén Principal". Posteriormente, define una estructura de categorías de productos que incluya los niveles "Poco importante", "Medio importante" y "Muy importante". Aplica el concepto oficial: el almacén representa la ubicación logística, mientras que las categorías organizan los servicios y productos internamente.

5. **Crear productos, servicios y reglas de reabastecimiento**

- Investigación: Consulta en el manual cómo diferenciar el comportamiento del ERP frente a bienes físicos y servicios.
- Acción: Da de alta 10 productos físicos (tipo almacenable) y 5 servicios, asignando a todos ellos su categoría y precio de venta. Ten en cuenta que los productos físicos se recibirán en el Almacén Principal, mientras que los servicios no generan existencias.
- Tarifas: Investiga cómo configurar la pestaña de compras de los artículos para añadir precios de compra para varios proveedores. Registra variaciones en la cantidad mínima, el precio y el plazo de entrega. Si requieres aplicar un descuento, documéntate sobre cómo reflejar el precio neto en el pedido, ya que la tarifa estándar no incorpora un campo independiente de descuento.

6. **Reaprovisionamiento con dos proveedores, facturación y corrección de stock**

- Investigación: Este es el flujo logístico y contable completo. Revisa la documentación cruzada de Compras, Inventario y Facturación.
- Acción: Selecciona un mismo producto y realiza dos pedidos de compra independientes a dos proveedores distintos. Confirma y recibe la mercancía de ambos pedidos, generando y validando sus 2 facturas de proveedor correspondientes.
- Ajuste: Para finalizar, consulta en la guía de Inventario cómo realizar operaciones físicas. Ejecuta y aplica una corrección de existencias sobre el producto que acabas de reaprovisionar para documentar una posible merma.

**Comprobación.** En Compras hay dos órdenes confirmadas y dos recepciones validadas; en Contabilidad hay dos facturas de proveedor publicadas; y en Inventario el producto refleja la cantidad corregida y el movimiento de ajuste.

**Capturas obligatorias.** Las dos órdenes con proveedores distintos, las dos facturas publicadas, una recepción validada y la línea del inventario físico con cantidad contada, diferencia y motivo del ajuste.

# 6. Material proporcionado al alumnado

- **Instalación de Odoo **versión gratuita.****

# 7. Entrega

- Informe en PDF con todos los pantallazos necesarios para comprobar que se han realizado las tareas.
- Formato del documento: PDF.
- **Nombre del fichero: tuNombre_Apellido1_Apellido2_UT02-A4.pdf**

# 8. Rúbrica

<table>
<tbody>
<tr class="odd">
<td>a.1) Gestión de Terceros y Contactos (Puntos 1, 2 y 3)</td>
<td><p>(3,00 puntos)</p>
<p>Registra los 8 terceros con sus respectivos contactos asociados. Aplica correctamente la jerarquía de etiquetas (incluyendo la concurrencia VIP/Muy importante) y asigna el Vendedor documentando la diferencia con el Comprador. </p></td>
<td><p>(2,10 puntos)</p>
<p>Crea los terceros y contactos, pero presenta fallos menores en la asignación de las etiquetas múltiples o en la configuración del Vendedor en la ficha. </p></td>
<td><p>(1,50 puntos)</p>
<p>Crea los terceros de forma incompleta, faltando contactos asociados o sin aplicar correctamente el sistema nativo de etiquetas. </p></td>
<td><p>(0,00 puntos)</p>
<p>No crea el número mínimo de empresas ni clasifica los contactos mediante etiquetas o roles comerciales. </p></td>
</tr>
<tr class="even">
<td>a.2) Catálogo y Almacén (Puntos 4 y 5)</td>
<td><p>(3,00 puntos)</p>
<p>Configura el "Almacén Principal" y el árbol de categorías. Da de alta 10 productos físicos y 5 servicios, registrando correctamente tarifas de proveedor con variaciones en cantidad mínima, precio y plazo de entrega. </p></td>
<td><p>(2,10 puntos)</p>
<p>Crea los productos y el almacén, pero confunde parcialmente la categorización o falta configurar líneas de proveedor en los artículos físicos. </p></td>
<td><p>(1,50 puntos)</p>
<p>Da de alta productos, pero no distingue el comportamiento entre bienes físicos (almacenables) y servicios, o no establece tarifas de compra. </p></td>
<td><p>(0,00 puntos)</p>
<p>No configura la estructura logística ni registra los productos y servicios requeridos. </p></td>
</tr>
<tr class="odd">
<td>a.3) Reaprovisionamiento y Ajuste (Punto 6)</td>
<td><p>(4,00 puntos)</p>
<p>Ejecuta el flujo completo: dos pedidos independientes, dos recepciones validadas y dos facturas publicadas. Finaliza documentando correctamente un ajuste físico de inventario. </p></td>
<td><p>(2,80 puntos)</p>
<p>Realiza las compras y recepciones, pero agrupa proveedores incorrectamente o no publica alguna de las facturas contables. </p></td>
<td><p>(2,00 puntos)</p>
<p>Confirma los pedidos de compra, pero omite las recepciones, la facturación o la corrección final de existencias. </p></td>
<td><p>(0,00 puntos)</p>
<p>No realiza los pedidos de compra independientes ni corrige el stock en el inventario. </p></td>
</tr>
</tbody>
</table>

****

|                        |                          |                             |                             |                   |                  |
|------------------------|--------------------------|-----------------------------|-----------------------------|-------------------|------------------|
|                        | Se retrasa más de 5 días | Se retrasa entre 4 y 5 días | Se retrasa entre 2 y 3 días | Se retrasa 1 día. | Entrega en plazo |
| Entrega tarea en plazo | \- 5                     | \- 3                        | -2                          | -1                | 0                |
