# UT04-A1 - Exploración de tablas, consultas y vistas en Odoo

## 1. Resultado de aprendizaje, contenido y criterios de evaluación

### Resultado de aprendizaje

**RA4.** Adapta sistemas ERP-CRM identificando los requerimientos de un supuesto empresarial y utilizando las herramientas proporcionadas por los mismos.

### Contenido

- Tablas y vistas que es preciso adaptar.
- Consultas necesarias para obtener información.

### Criterios de evaluación
- CE4b. Se han adaptado definiciones de campos, tablas y vistas de la base de datos del ERP-CRM.
- CE4c. Se han adaptado consultas.
- CE4h. Se han realizado pruebas.
- CE4i. Se han documentado las operaciones realizadas y las incidencias observadas.


## 2. Agrupamiento

Actividad individual.

Se permitirá la consulta entre compañeros para resolver dudas técnicas, pero cada estudiante deberá realizar sus propias consultas, modificaciones y documentación.


## 3. Contexto

La empresa ficticia **RetroByte Canarias**, dedicada a la comercialización de videojuegos y consolas retro, utiliza Odoo para gestionar clientes, productos y ventas.

La dirección necesita conocer cómo se almacena la información empresarial antes de encargar modificaciones en su sistema.

Has sido contratado como técnico de sistemas ERP para investigar la estructura de su base de datos y preparar consultas que permitan obtener información útil para la toma de decisiones.

## 4. Objetivo de la actividad

Analizar la estructura de almacenamiento de Odoo, identificar las relaciones entre sus modelos de datos y utilizar SQL y las herramientas del ERP para consultar información empresarial.

Al finalizar, el alumnado deberá ser capaz de interpretar tablas, elaborar consultas y adaptar una vista de consulta sin comprometer la integridad del sistema.

## 5. Enunciado de la actividad

La empresa RetroByte Canarias necesita conocer cómo se almacenan los clientes y productos en Odoo y obtener información mediante consultas SQL.

### Pasos previos

Antes de empezar, comprueba que:

- Odoo responde en `http://127.0.0.1:8069/`.
- Has iniciado sesión con una cuenta autorizada y has activado el modo de desarrollador.
- Puedes acceder a PostgreSQL con el usuario `odoo`.

#### Paso 1 — Comprobar que Odoo está disponible

1. Abre `http://127.0.0.1:8069/` en el navegador.
2. Verifica que carga la página inicial de Odoo.
3. Abre `http://127.0.0.1:8069/web/login`.
4. Verifica que aparece el formulario de inicio de sesión con los campos **Correo electrónico** y **Contraseña**.

#### Paso 2 — Comprobar los permisos de la cuenta y activar el modo de desarrollador

1. Inicia sesión en Odoo con la cuenta de prácticas.
2. Abre el menú de aplicaciones (icono de cuadrícula) y elige **Ajustes**.
3. En **Ajustes > Opciones generales**, desplázate hasta **Herramientas de desarrollador**.
4. Pulsa **Activar modo de desarrollador**.
5. Comprueba que la URL incluye `debug=1` y que en la barra superior aparece el botón **Abrir las herramientas de desarrollador**.

**Resultado esperado:** la cuenta puede abrir Ajustes y activar el modo de desarrollador en Odoo 17 Community (la instancia informa de la versión `Odoo 17.0-20260908 (Edición Community)`).

**Por qué se hace:** el modo de desarrollador permite inspeccionar los modelos y las vistas de Odoo. Es útil como complemento de PostgreSQL para relacionar los nombres técnicos de los modelos con las tablas, pero no sustituye la ejecución de las consultas SQL solicitadas.

**Límite de seguridad:** activar el modo de desarrollador solo cambia las herramientas visibles durante la sesión. No guardes ninguna configuración ni edites ningún registro.

Realiza las siguientes tareas:

### Parte A. Explorar las tablas

1. Accede a PostgreSQL mediante la herramienta facilitada.
2. Localiza las tablas `res_partner` y `product_template`.
3. Identifica en cada tabla su clave primaria y tres campos relevantes.
4. Anota qué información almacena cada tabla.

####  Inspeccionar los modelos desde Odoo (comprobación complementaria)

a. Con el modo de desarrollador activo, abre **Ajustes**.</br>
b. Despliega **Técnico** y elige **Modelos**.</br>
c. Abre el modelo `res.partner`.</br>
d. En la pestaña **Campos**, comprueba los campos `email` (**Correo electrónico**), `name` (**Nombre**) y `phone` (**Teléfono**).</br>
e. Vuelve al listado de modelos y abre `product.template`.</br>
f. En la pestaña **Campos**, comprueba `name` (**Nombre**), `list_price` (**Precio de venta**), `default_code` (**Referencia interna**) y `barcode` (**Código de barras**).

**Resultado esperado:** `res.partner` aparece como el modelo **Contacto** y `product.template` como **Producto**; ambos son de tipo **Objeto base**. Los campos enumerados se muestran en sus definiciones técnicas.

**Por qué se hace:** Odoo muestra los nombres técnicos del modelo y de sus campos, que ayudan a interpretar las consultas. Es una comprobación complementaria: el nombre de las tablas (`res_partner`, `product_template`) y su clave primaria hay que confirmarlos en PostgreSQL.

**Límite de seguridad:** abre solo pantallas de consulta. No crees ni edites modelos, campos, vistas ni registros.

#### Punto A1 y A2  - Acceder a PostgreSQL

a. Conéctate a PostgreSQL con el usuario `odoo` mediante `psql` o la herramienta facilitada.</br>
b. Ejecuta `\l` para listar las bases de datos disponibles.</br>
c. Identifica la base de datos de Odoo del entorno (`postgree`).</br>
d. Conéctate a ella con `\c postgree`.</br>
e. Ejecuta `\dt res_partner` y `\dt product_template`.

**Resultado esperado:** las tablas `public.res_partner` y `public.product_template` existen en `postgree` y su propietario es el usuario `odoo`.

**Comprobación estructural opcional:** `\d product_template` muestra el índice `product_template_pkey` como **PRIMARY KEY** sobre `id`, y que `product_product.product_tmpl_id` referencia `product_template(id)`. `\d res_partner` muestra que muchas otras tablas lo referencian mediante `res_partner(id)`.

**Límite de seguridad:** en esta parte no se ejecuta ninguna sentencia que modifique datos o estructura.

#### Punto A3 y A4 -  Confirmar claves primarias y campos relevantes 

Ejecuta estas consultas de metadatos en la base `postgree`:

```sql
SELECT
  tc.table_name,
  kcu.column_name AS clave_primaria
FROM information_schema.table_constraints AS tc
JOIN information_schema.key_column_usage AS kcu
  ON tc.constraint_name = kcu.constraint_name
 AND tc.table_schema = kcu.table_schema
WHERE tc.constraint_type = 'PRIMARY KEY'
  AND tc.table_schema = 'public'
  AND tc.table_name IN ('res_partner', 'product_template')
ORDER BY tc.table_name;
```

```sql
SELECT
  table_name,
  column_name,
  data_type,
  is_nullable
FROM information_schema.columns
WHERE table_schema = 'public'
  AND (
    table_name = 'res_partner'
    AND column_name IN ('id', 'name', 'email', 'phone')
    OR table_name = 'product_template'
    AND column_name IN ('id', 'name', 'list_price', 'default_code')
  )
ORDER BY table_name, column_name;
```

**Resultado esperado:**

| Tabla | Clave primaria | Campos relevantes |
|---|---|---|
| `res_partner` | `id` (`integer`, no nulo) | `name` (`character varying`), `email` (`character varying`) y `phone` (`character varying`); los tres admiten nulos. |
| `product_template` | `id` (`integer`, no nulo) | `name` (`jsonb`, no nulo), `list_price` (`numeric`, admite nulos) y `default_code` (`character varying`, admite nulos). |

**Qué almacena cada tabla:** `res_partner` guarda contactos (clientes, proveedores, empresas y usuarios) y sus datos de identificación y comunicación. `product_template` guarda las plantillas de producto, incluida la referencia interna y el precio de venta.

**Dato importante para la Parte B:** en Odoo 17, `product_template.name` es de tipo `jsonb` (un objeto con una traducción por idioma), no texto simple. Las consultas de productos tendrán que extraer el valor del idioma antes de mostrar el nombre.

**Límite de seguridad:** ambas consultas acceden solo a `information_schema`; no modifican datos ni estructura.

### Parte B. Realizar consultas SQL

Escribe y ejecuta estas tres consultas:

1. Mostrar el nombre y el correo electrónico de los contactos.
2. Mostrar el nombre y el precio de venta de los productos, ordenados por precio descendente.
3. Mostrar únicamente los productos cuyo precio sea superior a 50 €.

Guarda las consultas SQL y comprueba que devuelven resultados.

#### Identificar los idiomas del nombre de producto (preparación)

Ejecuta:

```sql
SELECT DISTINCT clave
FROM product_template
CROSS JOIN LATERAL jsonb_object_keys(name) AS clave
ORDER BY clave;
```

**Resultado esperado:** los nombres de producto contienen las claves `en_US` y `es_ES`.

**Por qué se hace:** el campo `name` es un objeto JSON con traducciones. Para mostrar un nombre legible se usará `name ->> 'es_ES'`, y se recurrirá a `name ->> 'en_US'` cuando no haya traducción en español.

**Límite de seguridad:** la consulta solo lee claves del JSON; no altera productos ni traducciones.

#### Punto B1 - Consultar nombres y correos de los contactos

```sql
SELECT
  name,
  email
FROM res_partner
ORDER BY name;
```

**Resultado esperado:** la consulta devuelve los contactos con su correo (en el entorno de referencia, 11 registros, entre ellos `Administrator`, `My Company SLU` y `OdooBot`).

**Por qué se hace:** muestra el nombre y el correo electrónico de los registros de contacto. No se añade filtro porque el enunciado no lo pide; por eso aparecen también usuarios técnicos almacenados en `res_partner`.

#### Punto B2 - Consultar productos ordenados por precio de venta

```sql
SELECT
  COALESCE(name ->> 'es_ES', name ->> 'en_US') AS nombre,
  list_price AS precio_venta
FROM product_template
ORDER BY list_price DESC NULLS LAST;
```

**Resultado esperado:** la consulta devuelve los productos ordenados de mayor a menor precio (en el entorno de referencia, tres productos `DUA Valoración IVA 21 %`, `10 %` y `4 %`, todos con precio `1.00`).

**Por qué se hace:** ordena por `list_price` de mayor a menor. `COALESCE` toma primero la traducción española del nombre y, si falta, la inglesa. `NULLS LAST` deja al final los productos sin precio.

#### Punto B3 - Consultar productos con precio superior a 50 €3)

```sql
SELECT
  COALESCE(name ->> 'es_ES', name ->> 'en_US') AS nombre,
  list_price AS precio_venta
FROM product_template
WHERE list_price > 50
ORDER BY list_price DESC;
```

**Resultado esperado:** solo aparecen productos con `list_price` mayor que 50. Si todos los productos existentes cuestan menos (como en el entorno de referencia, donde cuestan `1.00`), la consulta devuelve **0 filas**: el resultado es correcto y debe explicarse en la documentación.

**Límite de seguridad (pasos 7 a 9):** son sentencias `SELECT`; no modifican información.

### Parte C. Crear una vista SQL

1. Crea una vista SQL llamada `vista_productos_retrobyte` que muestre los nombres y precios de los productos.
2. Ejecuta una consulta `SELECT` sobre la vista.
3. Comprueba que devuelve la información esperada.

#### Comprobar que la vista no existe

```sql
\dv vista_productos_retrobyte
```

**Resultado esperado:** PostgreSQL responde `Did not find any relation named "vista_productos_retrobyte"`.

**Por qué se hace:** crear una vista con un nombre ya ocupado daría error o llevaría a usar `CREATE OR REPLACE VIEW`, que reemplazaría una definición existente. La comprobación confirma que se puede crear la vista nueva sin sobrescribir ningún objeto.

#### Punto C1 - Crear la vista SQL de productos

```sql
CREATE VIEW vista_productos_retrobyte AS
SELECT
  COALESCE(name ->> 'es_ES', name ->> 'en_US') AS nombre,
  list_price AS precio_venta
FROM product_template;
```

**Resultado esperado:** PostgreSQL responde `CREATE VIEW`.

**Por qué se hace:** la vista reúne los nombres legibles y los precios de venta en una consulta reutilizable. La transformación del JSON queda guardada en la definición de la vista, por lo que quien la consulte no tiene que repetirla.

**Alcance del cambio:** es la única operación de definición de estructura de la actividad. No crea ni actualiza datos de negocio y se realiza sobre la base de prácticas.

#### Punto C2 y C3 - Verificar el contenido de la vista

```sql
SELECT *
FROM vista_productos_retrobyte
ORDER BY precio_venta DESC NULLS LAST;
```

**Resultado esperado:** la vista devuelve los mismos productos y precios que la consulta B2 (en el entorno de referencia, las tres filas `DUA Valoración IVA …` con precio `1.00`).

**Por qué se hace:** prueba que la vista existe, se puede consultar y devuelve los nombres legibles y precios esperados.

**Límite de seguridad:** es una sentencia `SELECT`; no modifica información.

## 6. Material proporcionado al alumnado

- Instalación de Odoo 17 Community en Docker.
- Base de datos de pruebas con clientes, productos y pedidos.
- Acceso de consulta a PostgreSQL.
- Herramienta de administración de PostgreSQL o terminal `psql`.
- Guía básica de SQL.
- Documentación del modo desarrollador y del ORM de Odoo.
- Estructura inicial de un módulo personalizado.

## 7. Entrega

Se entregará un archivo ZIP denominado `UT4-A1_Apellidos_Nombre.zip` que contendrá:

Entrega:

- **Archivo `consultas.sql` con las consultas y la creación de la vista.**
	- Crea `consultas.sql` con, en este orden: las tres consultas de la Parte B (pasos 7, 8 y 9), la sentencia `CREATE VIEW` (paso 11) y la consulta de verificación de la vista (paso 12). Añade un comentario `--` encima de cada una indicando a qué apartado corresponde.
	
- **Una captura de pantalla de los resultados de la vista.**
	- Haz una captura de pantalla del resultado del paso 12 en la que se vean la consulta y las filas devueltas. Guárdala en `evidencias/`.

- **Archivo estructura.md: Una breve explicación de la diferencia entre una tabla y una vista SQL.**
	- Redacta la diferencia entre tabla y vista SQL usando los objetos de esta práctica.
	**Ejemplo de explicación:** una **tabla** almacena físicamente registros, como `res_partner` y `product_template`. Una **vista** almacena solo una definición de consulta: `vista_productos_retrobyte` no duplica los productos, sino que, cada vez que se consulta, presenta los datos actuales de `product_template` con el nombre extraído del JSON y el precio de venta.

- **Archivo codigo,md: código de la adaptación de la vista de Odoo.**
-
- **Archivo`incidencias.md`: operaciones realizadas, dificultades y soluciones.**

## 8. Rúbrica

| Criterio / Apartado | Excelente | Bien | Regular | Mal |
| :--- | :--- | :--- | :--- | :--- |
| **Parte A: Exploración de tablas (CE3a / CE4b)**<br>*(2 puntos)* | Identifica correctamente las tablas `res_partner` y `product_template`, extrae sus claves primarias y campos solicitados mediante SQL. Explica con precisión técnica qué almacena cada una y reconoce las estructuras de datos (ej. tipo JSON en Odoo). *(2 puntos)* | Localiza las tablas y campos básicos en PostgreSQL, pero la explicación de lo que almacena cada una o la identificación de claves/tipos de datos es incompleta o poco detallada. *(1,4 puntos)* | Accede a las tablas pero comete errores importantes al identificar las claves primarias o campos relevantes, sin detallar correctamente su estructura o contenido. *(1 punto)* | No identifica las tablas requeridas en la base de datos ni sus campos, o no realiza la exploración solicitada. *(0 puntos)* |
| **Parte B: Consultas SQL (CE3a / CE4c)**<br>*(3 puntos)* | Implementa las 3 consultas de forma impecable obteniendo los resultados exactos. Maneja correctamente la extracción de campos JSON (`name ->> 'es_ES'`), el uso de `COALESCE` para idiomas, filtros (`WHERE`) y ordenaciones (`DESC NULLS LAST`). *(3 puntos)* | Las consultas son funcionales y devuelven datos, pero presentan fallos menores de sintaxis lógica (ej. no procesar correctamente el tipo JSON, omisión de `COALESCE`, o errores leves en la ordenación). *(2,1 puntos)* | Las consultas tienen errores de sintaxis que requieren corrección manual para poder ejecutarse, o la lógica de filtrado/ordenación es incorrecta en más de una de las sentencias solicitadas. *(1,5 puntos)* | No entrega las consultas, contienen errores críticos que impiden su ejecución o no tienen ninguna relación con lo solicitado. *(0 puntos)* |
| **Parte C: Creación y verificación de Vista SQL (CE4c)**<br>*(2 puntos)* | Crea la vista `vista_productos_retrobyte` con la sintaxis correcta sin sobreescribir datos, ejecuta la consulta de verificación sobre ella con éxito y aporta la captura de pantalla evidenciando los resultados esperados. *(2 puntos)* | Crea la vista SQL correctamente con la transformación del JSON, pero omite la consulta de verificación posterior o no adjunta la captura de pantalla demostrando su funcionamiento. *(1,4 puntos)* | Intenta crear la vista pero la sentencia contiene errores de sintaxis SQL, o la definición de la vista no extrae los campos o idiomas correctos desde `product_template`. *(1 punto)* | No realiza la creación de la vista SQL ni aporta evidencias de este apartado. *(0 puntos)* |
| **Adaptación de la vista de Odoo (CE4b)**<br>*(2 puntos)* | Entrega el archivo `codigo.md` con el código XML o Python exacto para la adaptación de la vista del ERP-CRM, respetando la integridad del sistema y sin alterar el núcleo original de Odoo. *(2 puntos)* | Entrega el código de la adaptación de la vista, pero el formato presenta pequeños errores o faltan atributos menores que dificultan su integración directa en el entorno de Odoo. *(1,4 puntos)* | El código de la vista es incompleto, no respeta la arquitectura MVC de Odoo, o presenta errores estructurales que romperían la interfaz web al aplicarse. *(1 punto)* | No entrega la adaptación de la vista de Odoo, o el código entregado es completamente ajeno a las tecnologías del ERP. *(0 puntos)* |
| **Documentación, pruebas e incidencias (CE4h / CE4i / CE3f)**<br>*(1 punto)* | Entrega los archivos `estructura.md` (con una explicación técnica y clara de la diferencia entre tabla y vista) e `incidencias.md` detallado. El ZIP cumple estrictamente con la nomenclatura y estructura requeridas. *(1 punto)* | Entrega toda la documentación, pero la diferencia entre tabla y vista es superficial, no detalla suficientemente las incidencias, o hay fallos menores en el empaquetado del archivo ZIP. *(0,7 puntos)* | Falta alguno de los archivos de texto requeridos (`estructura.md` o `incidencias.md`), o la justificación técnica de las operaciones es inexistente. El ZIP está mal nombrado. *(0,5 puntos)* | No documenta las operaciones realizadas ni entrega los archivos de texto solicitados. *(0 puntos)* |

<br>

**Penalización por retraso en la entrega:**

| | Se retrasa más de 5 días | Se retrasa entre 4 y 5 días | Se retrasa entre 2 y 3 días | Se retrasa 1 día | Entrega en plazo |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **Puntuación** | - 5 puntos | - 3 puntos | - 2 puntos | - 1 punto | 0 puntos |
