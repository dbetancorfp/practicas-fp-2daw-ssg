# UT03-A1-modificacion_modulos

<a class="md-button md-button--primary" href="ut03-a1-modificacion-modulos.pdf">&#x2B07; Descargar pr&aacute;ctica en PDF</a>


## 1. Resultado de aprendizaje, contenido y criterios de evaluación

<table class="curricular-table">
<tbody>
  <tr>
    <th scope="row" class="curricular-table__label">Resultados de aprendizaje</th>
    <td class="curricular-table__content"><p>RA3. Realiza operaciones de gestión, consulta y análisis de la información siguiendo las especificaciones de diseño y utilizando las herramientas proporcionadas por los sistemas ERP-CRM.</p></td>
  </tr>
  <tr>
    <th scope="row" class="curricular-table__label">Contenido</th>
    <td class="curricular-table__content"><ol>
<li>Definición de campos</li>
<li>Consultas de acceso a datos</li>
<li>Interfaces de entrada de datos y de procesos. Formularios</li>
<li>Informes y listados de la aplicación</li>
</ol></td>
  </tr>
  <tr>
    <th scope="row" class="curricular-table__label">Criterios de evaluación</th>
    <td class="curricular-table__content"><p>a) Se han usado herramientas/lenguajes de consulta y manipulación de datos.
b) Se han generado formularios.
d) Se han exportado datos e informes.
g) Se han documentado las operaciones e incidencias.</p></td>
  </tr>
</tbody>
</table>

## 2. Agrupamiento

Individual

## 3. Contexto
Esta guía crea un módulo pequeño llamado `sale_quotation_creator`. Mostrará la persona que **creó** el presupuesto tanto en el formulario y la vista de lista (árbol) de Odoo, como en el PDF estándar de presupuesto/pedido. Además, comprobaremos esta información accediendo directamente a la base de datos y exportaremos los resultados.

## 4. Objetivo de la actividad
Practicar el acceso directo a la base de datos del ERP mediante PgAdmin, la creación de vistas y menús personalizados, la introducción de datos y la exportación a CSV.

Al terminar:

* En **Ventas > Pedidos > Presupuestos**, cada presupuesto mostrará el campo **Creado por**.
* Al pulsar **Imprimir > Presupuesto / Pedido**, el PDF incluirá **Creado por: Nombre del usuario** junto a los datos superiores.
* No se escribe código Python ni se añade ningún campo ni columna propios a PostgreSQL.
* Se reutiliza `create_uid`, un campo técnico que Odoo ya conserva en los registros con control de acceso. Odoo lo define automáticamente como una relación con `res.users`; el módulo solo lo muestra. Por tanto, esta instalación no altera la estructura de la base de datos.

> Importante: esto no cambia presupuestos existentes ni recalcula datos. Instalar un módulo de vistas sí guarda en Odoo las definiciones XML de las vistas heredadas, como cualquier módulo; no crea una nueva columna de negocio.

## 5. Enunciado de la actividad

### 5.1. Antes de empezar

Necesitarás:

1. Acceso de administrador a Odoo (para instalar/actualizar aplicaciones).
2. Acceso a una terminal en el ordenador o servidor donde existe `/david/APLICACIONES/Odoo`.
3. Docker y Docker Compose funcionando. Esta guía usa el nombre moderno `docker compose`; si tu equipo solo dispone de `docker-compose`, sustituye el comando.
4. Una copia de seguridad reciente. Es una modificación pequeña y reversible, pero el respaldo es la red de seguridad correcta.

No copies el módulo dentro del contenedor: el contenedor puede recrearse. Siempre se crea en la carpeta montada `./addons` del equipo anfitrión.

### 5.2. Entender los dos usuarios: creador y comercial

Son datos diferentes y conviene no confundirlos:

| Dato | Campo de Odoo | Qué significa | ¿Puede cambiar? |
| --- | --- | --- | --- |
| **Creado por** | `create_uid` | Usuario que guardó por primera vez el registro `sale.order`. | No; es la trazabilidad original. |
| **Comercial** | `user_id` | Persona comercial responsable de la venta. | Sí; se puede reasignar. |

El PDF estándar de Odoo 17 ya muestra el **Comercial** usando `doc.user_id`. Este módulo añade el **Creador** usando `doc.create_uid`, por lo que puede que ambos nombres coincidan o que sean distintos.

Ejemplo: Ana crea el presupuesto porque da apoyo administrativo y después se asigna a Bruno como comercial. El resultado será **Creado por: Ana** y **Comercial: Bruno**.

### 5.3. Haz una copia de seguridad

Primero entra en la carpeta del proyecto:

```bash
cd /david/APLICACIONES/Odoo
```

| Método de Copia de Seguridad | Instrucciones y Pasos | Comandos y Notas |
| :--- | :--- | :--- |
| **Opción recomendada:**<br>Copia desde la interfaz de Odoo | 1. Entra como administrador.<br>2. Abre el gestor de bases de datos: normalmente `http://TU_SERVIDOR:8069/web/database/manager`.<br>3. Pulsa **Copia de seguridad** en la base de datos que vas a modificar.<br>4. Guarda el archivo descargado en un lugar seguro. | Es la alternativa más directa para no depender de la consola. |
| **Opción alternativa:**<br>Copia desde Docker | Primero identifica el nombre real del servicio de PostgreSQL y de la base de datos. En muchas instalaciones el servicio se llama `db`, pero no hay que adivinarlo.<br><br>Si el servicio se llama `db` y tu base se llama `odoo`, ejecuta el comando de respaldo SQL. | **1. Identificar servicios:**<br>`docker compose config --services`<br>`docker compose ps`<br><br>**2. Comando de respaldo:**<br>`docker compose exec -T db pg_dump -U odoo -Fc odoo > copia-antes-sale_quotation_creator.dump`<br><br>*Nota: Sustituye `db`, `odoo` y el usuario si tu archivo `compose.yaml` usa otros valores. Conserva el archivo `.dump` fuera de una carpeta temporal.* |

### 5.4. Comprobar el montaje de los complementos

En `/david/APLICACIONES/Odoo`, revisa el archivo `compose.yaml` o `docker-compose.yml`. En el servicio que ejecuta Odoo debe existir un volumen equivalente a este:

```yaml
services:
  web:
    volumes:
      - ./addons:/mnt/extra-addons
```

La imagen oficial de Odoo 17 utiliza `/mnt/extra-addons` como ruta de complementos adicionales. Puedes confirmar la configuración final de Compose con:

```bash
docker compose config
```

Busca una línea que relacione la carpeta local `addons` con `/mnt/extra-addons`. Si no existe, añádela al servicio de Odoo, guarda el archivo y reinicia el servicio antes de continuar:

```bash
docker compose up -d
```

No modifiques la ruta si tu instalación ya usa una ruta distinta y funciona: en ese caso crea el módulo en la carpeta que realmente esté montada como ruta de addons.

### 5.5. Desarrollo de la práctica

#### 5.5.1. Crear la estructura del módulo

Desde `..../APLICACIONES/Odoo`, crea estas carpetas:

```bash
mkdir -p addons/sale_quotation_creator/views
mkdir -p addons/sale_quotation_creator/report
```

La estructura final debe ser exactamente esta:

```text
/david/APLICACIONES/Odoo/
└── addons/
    └── sale_quotation_creator/
        ├── __init__.py
        ├── __manifest__.py
        ├── views/
        │   └── sale_order_views.xml
        └── report/
            └── sale_order_report.xml
```

Los nombres y las mayúsculas importan. No crees una carpeta adicional entre `addons` y `sale_quotation_creator`.

#### 5.5.2. Crear los archivos, uno a uno

Puedes usar un editor gráfico o, desde la terminal, abrir cada archivo con `nano RUTA_DEL_ARCHIVO`. Pega el contenido del bloque correspondiente, guarda con `Ctrl+O`, confirma con `Intro` y sal con `Ctrl+X`.

#### 5.5.3 `__init__.py`

Ruta: `/david/APLICACIONES/Odoo/addons/sale_quotation_creator/__init__.py`

```python
# Este módulo solo hereda vistas XML y plantillas QWeb.
# No necesita modelos ni código Python.
```

Aunque no contiene lógica Python, el archivo permite que Odoo trate la carpeta como un módulo Python válido.

#### 5.5.4. `__manifest__.py`

Ruta: `/david/APLICACIONES/Odoo/addons/sale_quotation_creator/__manifest__.py`

```python
{
    'name': 'Quotation Creator',
    'summary': 'Shows the creator of a quotation in Odoo and its PDF',
    'version': '17.0.1.0.0',
    'category': 'Sales',
    'license': 'LGPL-3',
    'author': 'Tu organización',
    'depends': [
        'sale',
    ],
    'data': [
        'views/sale_order_views.xml',
        'report/sale_order_report.xml',
    ],
    'installable': True,
    'application': False,
}
```

El único módulo requerido es `sale`, que aporta el modelo `sale.order`, su formulario y el informe estándar. `application: False` evita que el módulo se presente como una aplicación independiente.

#### 5.5.5. Vista del formulario: `sale_order_views.xml`

Ruta: `/david/APLICACIONES/Odoo/addons/sale_quotation_creator/views/sale_order_views.xml`

```xml
<?xml version="1.0" encoding="utf-8"?>
<odoo>
    <!-- Herencia de la vista Formulario -->
    <record id="view_order_form_inherit_quotation_creator" model="ir.ui.view">
        <field name="name">sale.order.form.inherit.quotation.creator</field>
        <field name="model">sale.order</field>
        <field name="inherit_id" ref="sale.view_order_form"/>
        <field name="arch" type="xml">
            <xpath expr="//group[@name='order_details']" position="inside">
                <field name="create_uid" string="Creado por" readonly="1"/>
            </xpath>
        </field>
    </record>

    <!-- Herencia de la vista Lista (Árbol) -->
    <record id="view_quotation_tree_inherit_quotation_creator" model="ir.ui.view">
        <field name="name">sale.order.tree.inherit.quotation.creator</field>
        <field name="model">sale.order</field>
        <field name="inherit_id" ref="sale.view_quotation_tree_with_onboarding"/>
        <field name="arch" type="xml">
            <xpath expr="//field[@name='user_id']" position="after">
                <field name="create_uid" string="Creado por" optional="show"/>
            </xpath>
        </field>
    </record>
</odoo>
```

Qué hace este archivo:

* Hereda, sin sustituir, la vista estándar identificada como `sale.view_order_form`.
* Inserta el campo técnico existente `create_uid` dentro del grupo de datos del pedido llamado `order_details`.
* Lo marca como solo lectura, que es coherente con la trazabilidad de Odoo.

No se añade una definición `<field>` Python: `create_uid` ya existe automáticamente en `sale.order`.

**Debes explicar con tus propias palabras qué hace el código XML de las vistas.**

#### 5.5.6. Plantilla del PDF: `sale_order_report.xml`

Ruta: `/david/APLICACIONES/Odoo/addons/sale_quotation_creator/report/sale_order_report.xml`

```xml
<?xml version="1.0" encoding="utf-8"?>
<odoo>
    <template id="report_saleorder_document_inherit_quotation_creator"
              inherit_id="sale.report_saleorder_document">
        <xpath expr="//div[@id='informations']" position="inside">
            <div t-if="doc.create_uid" class="col-auto col-3 mw-100 mb-2">
                <strong>Creado por:</strong><br/>
                <span class="m-0" t-field="doc.create_uid"/>
            </div>
        </xpath>
    </template>
</odoo>
```

Qué hace este archivo:

* Hereda la plantilla QWeb estándar `sale.report_saleorder_document`; no crea un informe ni un botón de impresión nuevo.
* Inserta un bloque en el contenedor `id="informations"`, el mismo bloque donde Odoo 17 presenta fecha, vencimiento y comercial.
* Usa `t-field="doc.create_uid"` para que QWeb formatee el nombre del usuario correctamente.
* El `t-if` evita que se muestre un bloque vacío si, excepcionalmente, el dato no está disponible.

**Debes explicar con tus propias palabras qué hace el código XML del informe.**

#### 5.5.7. Comprobar los archivos antes de instalar

Comprueba que están donde deben estar:

```bash
find addons/sale_quotation_creator -maxdepth 2 -type f | sort
```

Deberías ver exactamente cuatro archivos. Comprueba también que los XML están bien formados; este comando solo lee y valida:

```bash
xmllint --noout addons/sale_quotation_creator/views/sale_order_views.xml addons/sale_quotation_creator/report/sale_order_report.xml
```

Si `xmllint` no está instalado, puedes omitir esa comprobación; Odoo validará los XML durante la instalación.

#### 5.5.8. Reiniciar Odoo para que detecte el módulo

Desde `/david/APLICACIONES/Odoo`:

```bash
docker compose restart web
```

En esta guía se asume que el servicio de Odoo se llama `web`. Si `docker compose config --services` mostró otro nombre, usa ese nombre. Espera unos segundos y comprueba que el servicio sigue activo:

```bash
docker compose ps
docker compose logs --tail=100 web
```

No debe aparecer un error de arranque. Ver que el servicio esté `running` o `Up` es suficiente para pasar al siguiente punto.

#### 5.5.9. Instalar el módulo desde Odoo

1. Entra en Odoo con una cuenta administradora.
2. Ve a **Aplicaciones**.
3. Activa el modo desarrollador. Una forma habitual: **Ajustes > Activar el modo desarrollador**. También puedes añadir `?debug=1` a la URL de Odoo.
4. En **Aplicaciones**, abre el menú de acciones (icono de ajustes) y pulsa **Actualizar lista de aplicaciones**. Confirma.
5. Busca `Quotation Creator`.
6. Pulsa **Instalar**.

Si no aparece al buscar, borra cualquier filtro de “Aplicaciones”, busca por `sale_quotation_creator` y revisa el apartado de resolución de problemas.

#### 5.5.10. Verificar el formulario y el PDF

##### Formulario

1. Abre **Ventas > Pedidos > Presupuestos**.
2. Abre un presupuesto existente o crea uno de prueba y guárdalo.
3. En el bloque de datos del pedido, junto a fecha, tarifa y condiciones de pago, debe aparecer **Creado por**.
4. El valor debe ser el usuario que creó por primera vez ese presupuesto.

##### PDF

1. Desde el mismo presupuesto, pulsa **Imprimir**.
2. Elige **Presupuesto / Pedido** (el nombre puede estar traducido de manera ligeramente distinta).
3. Abre o descarga el PDF.
4. En la zona superior de información deben aparecer **Comercial** (si está asignado) y **Creado por**.

Para una prueba completa, crea un presupuesto con un usuario y asígnale otro comercial. Así confirmas visualmente que el módulo enseña el creador y no está duplicando el comercial.

#### 5.5.11. Actualizar el módulo después de cambiar XML

Cada vez que modifiques `__manifest__.py` o cualquiera de los XML:

1. Guarda los archivos en `/david/APLICACIONES/Odoo/addons/sale_quotation_creator`.
2. Reinicia el servicio si quieres que recargue la lista de módulos:

   ```bash
   cd /david/APLICACIONES/Odoo
   docker compose restart web
   ```

3. En Odoo: **Aplicaciones > Quotation Creator > Actualizar**.
4. Recarga el navegador con fuerza (`Ctrl+F5`) y vuelve a generar el PDF.

También puedes actualizar desde la terminal. Esta opción requiere el nombre exacto de la base de datos y del servicio:

```bash
docker compose run --rm web odoo -d NOMBRE_DE_TU_BD -u sale_quotation_creator --stop-after-init
docker compose up -d web
```

Sustituye `NOMBRE_DE_TU_BD`. No ejecutes ese comando con una base de datos desconocida. La actualización desde la interfaz es más sencilla para este caso.

#### 5.5.12. Consulta a la base de datos mediante PgAdmin (Criterio a)
Para comprobar que los datos del módulo se están registrando correctamente a nivel de base de datos, realizaremos una consulta SQL directamente en PostgreSQL utilizando PgAdmin.

1. Accede a PgAdmin: Abre tu cliente de PgAdmin y conéctate al servidor de PostgreSQL de tu contenedor Docker.

2. Localiza la base de datos: Despliega el árbol de servidores, selecciona tu base de datos de Odoo y abre la Query Tool (Herramienta de consultas).

3. Diseña la consulta SQL: Escribe una consulta que relacione la tabla de pedidos de venta (sale_order) con la tabla de usuarios (res_users) para obtener el nombre en texto plano de quién creó el presupuesto y de quién es el comercial asignado.Pista para el alumno: El campo create_uid de sale_order guarda el ID del usuario creador, y el campo user_id guarda el ID del comercial. Ambos IDs se relacionan con la columna id de la tabla res_users.

4. Ejecuta la consulta: Comprueba que los resultados coinciden con lo que ves en la interfaz de Odoo.

5. Documentación: Toma una captura de pantalla donde se vea claramente la consulta SQL escrita y los resultados obtenidos en la parte inferior para incluirla en tu PDF.

#### 5.5.13. Exportación de datos e informes a CSV (Criterio d)
Ahora vamos a exportar un informe en formato CSV directamente desde la aplicación de Odoo para tratar los datos externamente.

1. Ve a la vista de lista: En Odoo, dirígete a Ventas > Pedidos > Presupuestos. Asegúrate de estar en la vista de lista (icono de líneas horizontales arriba a la derecha).

2. Selecciona los registros: Marca la casilla superior izquierda para seleccionar todos los presupuestos de la lista, o selecciona manualmente tres presupuestos de prueba.

3. Abre la herramienta de exportación: Haz clic en el botón de Acción (icono del engranaje) que aparece en la parte superior central al seleccionar registros y pulsa en Exportar.

4. Configura la exportación:

	- Tipo de exportación: Selecciona "Exportar datos actualizados" (o "Export all data" dependiendo del idioma).
	- Formato: Elige CSV.

5. Selecciona los campos solicitados: En el panel de la izquierda, busca y añade al panel de la derecha los siguientes campos:

	- Referencia del pedido (name)
	- Comercial (user_id)
	- Creado por (create_uid - Nota: es posible que tengas que buscarlo en el desplegable de campos técnicos o revisar su nombre traducido).

6. Genera el archivo: Pulsa el botón Exportar y guarda el archivo .csv en tu equipo.

7. Documentación: Abre el archivo CSV con un editor de texto plano o una hoja de cálculo. Toma una captura de pantalla mostrando los datos bien delimitados e inclúyela en tu documento explicando brevemente el proceso que has seguido.

## 6. Material proporcionado al alumnado

- Ordenador personal
- Sistema operativo Linux
- MV Linux mint con Oddo en Docker.

## 7. Entrega
- Documento único en PDF, con encabezado, pie de página, índice de contenidos y apartados diferenciados, describiendo (no solo capturas) los pasos seguidos.
- Formato del documento: PDF.
- **Nombre del fichero: tuNombre_Apellido1_Apellido2_UT03-A1.pdf**

## 8. Rúbrica

| Criterio | Excelente | Bien | Suficiente | Insuficiente |
| :--- | :--- | :--- | :--- | :--- |
| **a) Usa herramientas / lenguajes de consulta y manipulación de datos** | Crea la consulta SQL con PgAdmin mostrando todos los campos pedidos. La consulta está correctamente probada, sin errores de sintaxis, y se evidencia su funcionamiento con capturas claras y explicadas. **(2,50 puntos)** | Crea la consulta SQL con la mayoría de los campos pedidos. Funciona correctamente, pero contiene alguna imprecisión menor en la sintaxis o la evidencia visual es mejorable. **(1,75 puntos)** | Crea una consulta parcial o con errores menores que impiden mostrar todos los datos requeridos. La justificación o prueba en PgAdmin es superficial. **(1,25 puntos)** | No crea la consulta, la sintaxis es totalmente incorrecta o no logra conectarse a la base de datos. **(0,00 puntos)** |
| **b) Genera formularios (Vistas XML)** | Crea las vistas solicitadas (formulario y árbol) con el código XML totalmente correcto, bien indentado y sin errores. Explica de forma detallada y coherente el funcionamiento del código y los componentes heredados. **(2,50 puntos)** | Crea las vistas solicitadas funcionando correctamente en Odoo, pero el código XML carece de buena estructura/indentación, o la explicación del código es incompleta en algún punto. **(1,75 puntos)** | Crea alguna de las vistas de forma parcial o con errores menores en el XML que dificultan su correcta visualización o actualización. Explicación muy superficial. **(1,25 puntos)** | No crea las vistas pedidas o el código XML aportado es inválido y no muestra comprensión del proceso de herencia. **(0,00 puntos)** |
| **d) Exporta datos e informes** | Genera el archivo CSV con todos los campos solicitados, correctamente exportado desde la aplicación. Los datos están íntegros, bien delimitados y el proceso de exportación se explica paso a paso. **(2,50 puntos)** | Genera el archivo CSV con la mayoría de los campos pedidos, pero omite alguno de menor importancia o la explicación del proceso de exportación desde Odoo es demasiado breve. **(1,75 puntos)** | Genera un CSV incompleto, con problemas de formato (ej. delimitadores incorrectos) o errores, pero demuestra haber intentado la funcionalidad de exportación. **(1,25 puntos)** | No genera el archivo CSV o entrega un formato completamente diferente e inservible. **(0,00 puntos)** |
| **g) Documenta operaciones e incidencias (y Formato de entrega)** | Documenta todas las operaciones e incidencias de forma clara y estructurada (no solo capturas). El documento PDF incluye portada, encabezado, pie de página, índice y apartados bien diferenciados según las instrucciones. **(2,50 puntos)** | Documenta la mayoría de las operaciones, pero falta algún elemento de formato menor exigido (ej. falta índice o pie de página) o alguna captura carece de texto descriptivo. **(1,75 puntos)** | Documentación escasa, poco ordenada o basada casi exclusivamente en capturas sin explicación. Faltan varios elementos estructurales exigidos en el apartado 7. **(1,25 puntos)** | No documenta las operaciones ni las incidencias, o entrega un archivo mal formateado o en un formato distinto a PDF. **(0,00 puntos)** |


### Penalización por retraso en la entrega

| | Se retrasa más de 5 días | Se retrasa entre 4 y 5 días | Se retrasa entre 2 y 3 días | Se retrasa 1 día. | Entrega en plazo |
| :--- | :---: | :---: | :---: | :---: | :---: |
| **Entrega tarea en plazo** | - 5 | - 3 | - 2 | - 1 | 0 |