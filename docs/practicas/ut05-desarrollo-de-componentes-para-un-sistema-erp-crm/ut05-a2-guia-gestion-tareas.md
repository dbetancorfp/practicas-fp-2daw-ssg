# Guía paso a paso: módulo "Gestión de Tareas" en Odoo 17

Entorno de referencia usado en esta guía:
- Odoo 17.0 en contenedor Docker (`odoo:17.0`)
- Carpeta de módulos personalizados montada como volumen: `./addons:/mnt/extra-addons`
- `addons_path` en `odoo.conf` incluye `/mnt/extra-addons`

---

Este documento es más que una guía paso a paso. A través del desarrollo de este módulo aprenderemos cómo está estructurado (Carpetas y nombres de archivos) Odoo y cómo se son las estructuras de código para desrrollar nuevos elementos. ¡Adelante! Creemos nuestro primer módulo, o App, como lo llama Odoo.

## Paso 1 — Esqueleto del módulo

### ¿Qué es un "módulo" en Odoo?

Un módulo de Odoo no es más que una **carpeta con una estructura concreta** dentro de uno de los directorios listados en `addons_path`. Odoo escanea esas carpetas, lee un fichero especial llamado `__manifest__.py` en cada una, y si lo encuentra válido, lo ofrece como una "app" instalable. No hay magia: es un paquete de Python con convenciones.

### Estructura creada

```
addons/gestion_tareas/
├── __init__.py
└── __manifest__.py
```

### `__init__.py` — ¿para qué sirve?

```python
# De momento vacío. Aquí importaremos los modelos en el siguiente paso.
```

Odoo trata cada módulo como un **paquete de Python** (como si fuera una librería que se importa). Python exige que una carpeta tenga un fichero `__init__.py` para considerarla un paquete importable. Cuando el módulo crezca (añadamos modelos, por ejemplo), este fichero será el punto de entrada que dirá "importa también lo que hay en la carpeta `models/`". Por ahora, como no hay nada que importar, se deja vacío (el comentario no ejecuta nada, es solo documentación).

### `__manifest__.py` — explicación línea a línea

```python
{
    'name': 'Gestión de Tareas',
    'version': '17.0.1.0.0',
    'category': 'Productivity',
    'summary': 'Módulo didáctico de gestión de tareas (DAW/DAM)',
    'description': """
        Módulo de ejemplo paso a paso para practicar el desarrollo
        de módulos en Odoo 17: modelos, vistas, seguridad y menús.
    """,
    'author': 'David Betancor - IES Telesforo Bravo',
    'license': 'LGPL-3',
    'depends': ['base'],
    'data': [],
    'installable': True,
    'application': True,
}
```

Este fichero **no es código ejecutable de lógica**: es literalmente un diccionario de Python que Odoo lee como metadatos del módulo. Campo por campo:

- `'name'`: el nombre que verá el usuario en el listado de Apps.
- `'version'`: convención `X.Y.a.b.c.d`, donde `X.Y` suele ser la versión de Odoo compatible (`17.0`) y el resto es la versión propia del módulo. Sirve para que Odoo sepa si al actualizar el módulo hay cambios de versión.
- `'category'`: agrupa el módulo en el buscador de Apps (ej. "Productivity", "Sales", "Accounting"...). No afecta a la funcionalidad, solo a la organización visual.
- `'summary'`: subtítulo corto que aparece bajo el nombre en la tarjeta de la App.
- `'description'`: texto largo (admite HTML/RST) que se muestra en la ficha detallada del módulo.
- `'author'`: autoría. Aparece en la ficha del módulo.
- `'license'`: obligatorio desde hace varias versiones; Odoo lo valida y si no es una licencia reconocida (`LGPL-3`, `GPL-3`, `OEEL-1`, etc.) el módulo puede no instalar o dar aviso.
- `'depends'`: **lista de módulos de los que depende el nuestro**. `['base']` significa "antes de instalar este módulo, asegúrate de que el módulo `base` (el núcleo de Odoo, con usuarios, empresas, etc.) está instalado". Cuando en el Paso 2 queramos heredar de algún modelo existente o usar vistas de otro módulo, añadiremos esos módulos aquí.
- `'data'`: lista de **ficheros XML/CSV que Odoo debe cargar** al instalar o actualizar el módulo (vistas, menús, reglas de seguridad, datos de ejemplo...). Ahora está vacía porque todavía no hemos creado ninguno; en el Paso 3 y 4 la iremos rellenando.
- `'installable'`: si es `False`, Odoo ni siquiera muestra el módulo como instalable (útil para desactivar un módulo sin borrarlo). Lo dejamos en `True`.
- `'application'`: si es `True`, el módulo se muestra directamente como una "aplicación" en el listado de Apps (icono grande, aparece sin tener que quitar filtros). Si fuera `False`, se consideraría un módulo "técnico" y quedaría oculto por defecto en el buscador de Apps.

### Comandos y acciones ejecutadas

```bash
docker compose restart web
```

Esto reinicia **solo** el contenedor del servicio `web` (el proceso de Odoo), no la base de datos. ¿Por qué hace falta? Porque Odoo construye en memoria, al arrancar, un **registro de módulos disponibles** leyendo el sistema de ficheros de `addons_path`. Si añades una carpeta nueva mientras el proceso ya está corriendo, Odoo no se entera hasta que vuelve a arrancar (o hasta que fuerzas la actualización, que es justo el siguiente paso).

**Modo desarrollador** (Ajustes → Activar modo desarrollador): habilita opciones avanzadas en la interfaz (ver IDs técnicos, acceder a vistas de depuración, menús técnicos...). No es imprescindible para "Actualizar lista de aplicaciones", pero lo activamos porque lo necesitaremos constantemente en los siguientes pasos (para inspeccionar modelos, campos técnicos, etc.).

**Apps → ⋮ → Actualizar lista de aplicaciones**: esta acción le dice a Odoo "vuelve a escanear `addons_path`, lee el `__manifest__.py` de cada carpeta nueva y da de alta esos módulos en la tabla `ir.module.module`". Sin este paso, aunque la carpeta exista en disco, Odoo no sabe que existe a nivel de base de datos.

### ✅ Resultado comprobado

El módulo "Gestión de Tareas" aparece en el listado de Apps (sin instalar todavía).

---

## Paso 2 — Modelo de datos

### Concepto: ¿qué es un "modelo" en Odoo?

Un modelo es una clase Python que hereda de `models.Model`. Al arrancar, Odoo lee esa clase y **genera automáticamente la tabla SQL** correspondiente en PostgreSQL (una fila por registro, una columna por campo). No se escribe SQL a mano: el ORM traduce la clase a esquema de base de datos.

### Ficheros creados

```
addons/gestion_tareas/
├── __init__.py          ← modificado
├── __manifest__.py
└── models/
    ├── __init__.py       ← nuevo
    └── gestion_tareas.py ← nuevo
```

**`models/__init__.py`:**

```python
from . import gestion_tareas
```

Hace que, al importarse el paquete `models`, Python ejecute también `gestion_tareas.py`. Cadena de importación completa: `__init__.py` del módulo → importa `models` → `models/__init__.py` → importa `gestion_tareas`.

**`models/gestion_tareas.py`:**

```python
from odoo import fields, models


class GestionTareasTarea(models.Model):
    _name = 'gestion.tareas.tarea'
    _description = 'Tarea de Gestión de Tareas'

    name = fields.Char(string='Título', required=True)
    description = fields.Text(string='Descripción')
    date_deadline = fields.Date(string='Fecha límite')
    user_id = fields.Many2one('res.users', string='Responsable')
```

Explicación línea a línea:

- `from odoo import fields, models`: importa `models.Model` (clase base de todo modelo) y el módulo `fields` (tipos de campo: `Char`, `Text`, `Date`, `Many2one`...).
- `class GestionTareasTarea(models.Model):`: el nombre de la clase es libre (convención CamelCase); lo que identifica al modelo ante Odoo es `_name`, no el nombre de la clase.
- `_name = 'gestion.tareas.tarea'`: identificador técnico interno, usado desde vistas, seguridad y otros módulos. Convención: minúsculas y puntos. Odoo deriva de aquí el nombre de la tabla SQL sustituyendo los puntos por guiones bajos: `gestion_tareas_tarea`.
- `_description`: etiqueta legible que se ve en pantallas técnicas (como la de "Modelos" que acabamos de consultar). No es obligatoria, pero sin ella Odoo lanza un aviso en el log.
- `name = fields.Char(...)`: `name` es un campo especial por convención — Odoo lo usa como "campo de referencia" del registro (aparece en los selectores Many2one de otros modelos, en el `display_name`, etc.). `Char` = texto corto (SQL `varchar`). `required=True` añade `NOT NULL` en la base de datos.
- `description = fields.Text(...)`: texto largo multilínea (SQL `text`).
- `date_deadline = fields.Date(...)`: tipo fecha sin hora. (`Datetime` sería fecha + hora.)
- `user_id = fields.Many2one('res.users', ...)`: campo relacional — clave foránea hacia la tabla `res_users`. Odoo añade una columna entera `user_id` con un `FOREIGN KEY`. El sufijo `_id` en los Many2one es convención universal en el código de Odoo.

**`addons/gestion_tareas/__init__.py`** (antes vacío):

```python
from . import models
```

### Acción realizada

```bash
docker compose restart web
```

Y en Odoo: Apps → buscar "Gestión de Tareas" → botón **Activar** (ver nota sobre "Activar" vs "Instalar" más abajo).

> **Nota: "Activar" vs "Instalar".** En la vista de Apps de Odoo, el botón de instalación tiene dos etiquetas posibles según el atributo `'application'` del manifest: si es `True` el botón dice **"Activar"**; si es `False` dice **"Instalar"**. Por debajo ejecutan el mismo método (`button_immediate_install`): compilan el módulo, crean sus tablas en PostgreSQL y cargan los ficheros de `data`. Como pusimos `'application': True`, a nosotros nos aparece "Activar".

### Verificación

Ajustes → Técnico → Modelos → buscar `gestion.tareas.tarea`.

### ✅ Resultado comprobado

El modelo aparece registrado:

| Modelo | Descripción del modelo | Tipo | Modelo transitorio |
|---|---|---|---|
| `gestion.tareas.tarea` | Tarea de Gestión de Tareas | Objeto base | ☐ |

"Objeto base" confirma que es un modelo persistente normal (no un modelo transitorio/`TransientModel`, que es el tipo que se usa para wizards temporales cuyos registros se purgan periódicamente).

---

## Paso 3 — Seguridad (`ir.model.access.csv`)

### Concepto: ¿por qué hace falta seguridad explícita?

Odoo controla el acceso a cada modelo mediante la tabla `ir.model.access`. Si un modelo no tiene ninguna regla asociada, cualquier usuario normal (no superusuario) recibe un error de permisos al intentar abrir una vista sobre él. Como `admin` es superusuario, Odoo le deja pasar aunque no exista la regla — por eso no vimos ningún error antes, pero el módulo estaba incompleto para cualquier otro usuario.

### Fichero creado

```
addons/gestion_tareas/
└── security/
    └── ir.model.access.csv
```

```csv
id,name,model_id:id,group_id:id,perm_read,perm_write,perm_create,perm_unlink
access_gestion_tareas_tarea,gestion.tareas.tarea.user,model_gestion_tareas_tarea,base.group_user,1,1,1,1
```

Columna a columna:

- `id`: identificador externo (XML ID) único de este registro de acceso. Convención: `access_<modelo_con_guiones_bajos>`.
- `name`: nombre descriptivo libre. Convención: `<modelo>.<grupo>`.
- `model_id:id`: XML ID que Odoo genera automáticamente para cada modelo registrado: `model_` + `_name` con los puntos sustituidos por guiones bajos → `model_gestion_tareas_tarea`. El sufijo `:id` en la cabecera indica al importador CSV que es una referencia a un XML ID, no un valor literal.
- `group_id:id`: grupo de seguridad al que se aplica la regla. `base.group_user` es el grupo "Usuario interno / Empleado" (prefijo `base.` porque ese XML ID se define en el módulo `base`).
- `perm_read/write/create/unlink`: booleanos (`1`/`0`) para lectura, escritura, creación y borrado.

### Manifest actualizado

```python
    'data': [
        'security/ir.model.access.csv',
    ],
```

Sin esta línea, aunque el CSV exista en disco, Odoo no lo carga en base de datos.

### Acción realizada

`docker compose restart web` + Apps → "Gestión de Tareas" → ⋮ → **Actualizar** (no hizo falta reinstalar: solo cambiamos un fichero de `data`, no el modelo Python).

### Verificación

Ajustes → Técnico → Seguridad → Permisos de acceso, filtrado por modelo `gestion.tareas.tarea`.

### ✅ Resultado comprobado

| Nombre | Modelo | Grupo | Lectura | Escritura | Creación | Eliminación |
|---|---|---|---|---|---|---|
| `gestion.tareas.tarea.user` | Tarea de Gestión de Tareas | Tipos de usuario / Usuario interno | ✔ | ✔ | ✔ | ✔ |

---

## Paso 4 — Vistas de lista y formulario

### Concepto

El modelo (Paso 2) define qué datos existen; una vista define cómo se muestran. Son registros del modelo `ir.ui.view`, cargados desde XML. El campo clave de cada vista es `arch` (architecture): un árbol XML que describe la disposición de los campos.

### Fichero creado

```
addons/gestion_tareas/
└── views/
    └── gestion_tareas_views.xml
```

```xml
<?xml version="1.0" encoding="utf-8"?>
<odoo>

    <record id="view_gestion_tareas_tarea_tree" model="ir.ui.view">
        <field name="name">gestion.tareas.tarea.tree</field>
        <field name="model">gestion.tareas.tarea</field>
        <field name="arch" type="xml">
            <tree>
                <field name="name"/>
                <field name="user_id"/>
                <field name="date_deadline"/>
            </tree>
        </field>
    </record>

    <record id="view_gestion_tareas_tarea_form" model="ir.ui.view">
        <field name="name">gestion.tareas.tarea.form</field>
        <field name="model">gestion.tareas.tarea</field>
        <field name="arch" type="xml">
            <form>
                <sheet>
                    <group>
                        <field name="name"/>
                        <field name="user_id"/>
                        <field name="date_deadline"/>
                        <field name="description"/>
                    </group>
                </sheet>
            </form>
        </field>
    </record>

</odoo>
```

Explicación:

- `<record id="..." model="ir.ui.view">`: declara un registro en la tabla `ir.ui.view`. El `id` es el XML ID de esta vista (Odoo le antepone automáticamente el nombre técnico del módulo: `gestion_tareas.view_gestion_tareas_tarea_tree`). Convención: `view_<modelo>_<tipo>`.
- `<field name="name">`: nombre técnico/descriptivo, solo visible en pantallas técnicas.
- `<field name="model">`: a qué modelo se aplica (aquí sí el `_name` literal).
- `<field name="arch" type="xml">`: contenido real de la vista; `type="xml"` indica que el valor es un sub-árbol XML a interpretar, no texto plano.
- `<tree>`: vista de lista (en Odoo 17 el tag sigue siendo `tree`; el cambio de tag a `<list>` llega en Odoo 18). Cada `<field name="..."/>` es una columna.
- `<form><sheet><group>`: formulario de ficha individual. `sheet` da el aspecto de "hoja" con márgenes; `group` organiza los campos en dos columnas (etiqueta + valor) automáticamente.
- Se muestra `description` en el formulario pero no en la lista: la lista enseña solo lo esencial, el formulario el detalle completo.

### Manifest actualizado

```python
    'data': [
        'security/ir.model.access.csv',
        'views/gestion_tareas_views.xml',
    ],
```

Convención: la seguridad se declara antes que las vistas.

### Acción realizada

`docker compose restart web` → Apps → "Gestión de Tareas" → ⋮ → **Actualizar**.

### Verificación

Ajustes → Técnico → Interfaz de usuario → Vistas, búsqueda `gestion_tareas_tarea_tree` o `gestion_tareas_tarea_form`.

### ✅ Resultado comprobado

| Nombre de la vista | Tipo de vista | Modelo | ID externo |
|---|---|---|---|
| `gestion.tareas.tarea.tree` | Árbol | gestion.tareas.tarea | `gestion_tareas.view_gestion_tareas_tarea_tree` |
| `gestion.tareas.tarea.form` | Formulario | gestion.tareas.tarea | `gestion_tareas.view_gestion_tareas_tarea_form` |

---

## Paso 5 — Acción de ventana y menú

### Concepto: dos piezas distintas

- **Acción de ventana (`ir.actions.act_window`):** le dice a Odoo "abre estas vistas, sobre este modelo, con estas condiciones". Es independiente de desde dónde se lance.
- **Menú (`ir.ui.menu`, tag `<menuitem>`):** la entrada visual en la navegación que, al pulsarla, ejecuta una acción. Un menú sin acción solo despliega submenús.

### Fichero creado

```
addons/gestion_tareas/
└── views/
    └── gestion_tareas_menus.xml
```

```xml
<?xml version="1.0" encoding="utf-8"?>
<odoo>

    <record id="action_gestion_tareas_tarea" model="ir.actions.act_window">
        <field name="name">Tareas</field>
        <field name="res_model">gestion.tareas.tarea</field>
        <field name="view_mode">tree,form</field>
    </record>

    <menuitem id="menu_gestion_tareas_root"
              name="Gestión de Tareas"/>

    <menuitem id="menu_gestion_tareas_tarea"
              name="Tareas"
              parent="menu_gestion_tareas_root"
              action="action_gestion_tareas_tarea"
              sequence="10"/>

</odoo>
```

Explicación:

- `ir.actions.act_window`: `name` es el título mostrado; `res_model` el modelo sobre el que actúa (literal); `view_mode` es una lista separada por comas (sin espacios) de los tipos de vista y su orden de prioridad — `tree,form` muestra primero la lista y usa el formulario al abrir un registro. Odoo busca automáticamente las vistas de cada tipo definidas para ese modelo (las del Paso 4); no hace falta indicar su ID salvo que se quiera forzar una vista concreta.
- `<menuitem>` **sin `parent`**: se convierte en entrada de **nivel raíz**. Al ser `'application': True`, esto hace que "Gestión de Tareas" aparezca como app propia en el icono de cuadrícula, igual que "Ventas" o "Contactos". Sin `action`: un menú raíz sin acción no hace nada al clicar, solo organiza submenús.
- Submenú real: `parent` lo cuelga del menú raíz (referenciado por su `id` sin prefijo porque está en el mismo módulo); `action` lo enlaza a la acción de arriba; `sequence` ordena submenús hermanos (número más bajo primero).

### Manifest actualizado

```python
    'data': [
        'security/ir.model.access.csv',
        'views/gestion_tareas_views.xml',
        'views/gestion_tareas_menus.xml',
    ],
```

El fichero de menús va después del de vistas por convención (la acción necesita que las vistas que usará ya estén registradas).

### Acción realizada

`docker compose restart web` → Apps → "Gestión de Tareas" → ⋮ → **Actualizar**.

### ✅ Resultado comprobado

Al recargar, "Gestión de Tareas" aparece en el icono de cuadrícula como app independiente, con el submenú "Tareas". La lista muestra Título / Responsable / Fecha límite, y se pudo crear y guardar un registro de prueba ("Mi primera tarea", responsable David, fecha límite 15/11/2026) sin errores.

---

## Paso 6 — Refinamientos: estado y botones de acción

### Concepto: el patrón "statusbar + botones" de Odoo

Un pedido de venta o una factura muestran arriba una barra de pasos ("Presupuesto → Pedido → Facturado") y botones que avanzan ese estado. Es el mismo patrón, a pequeña escala.

### `models/gestion_tareas.py` (fichero completo)

```python
from odoo import fields, models


class GestionTareasTarea(models.Model):
    _name = 'gestion.tareas.tarea'
    _description = 'Tarea de Gestión de Tareas'

    name = fields.Char(string='Título', required=True)
    description = fields.Text(string='Descripción')
    date_deadline = fields.Date(string='Fecha límite')
    user_id = fields.Many2one('res.users', string='Responsable')
    state = fields.Selection(
        [
            ('pendiente', 'Pendiente'),
            ('en_progreso', 'En progreso'),
            ('hecha', 'Hecha'),
        ],
        string='Estado',
        default='pendiente',
        required=True,
    )

    def action_iniciar(self):
        self.state = 'en_progreso'

    def action_finalizar(self):
        self.state = 'hecha'

    def action_reiniciar(self):
        self.state = 'pendiente'
```

Novedades:

- `fields.Selection([(clave, etiqueta), ...])`: lista de tuplas `(valor_guardado, texto_mostrado)`. Se guarda como texto en SQL, no como número.
- `default='pendiente'`: valor si no se especifica al crear. Al actualizar el módulo, los registros ya existentes (como "Mi primera tarea") reciben también ese valor por defecto al añadirse la columna.
- `required=True`: evita que el campo quede sin valor, lo cual rompería la lógica de los botones.
- `def action_iniciar(self): self.state = 'en_progreso'`: método de modelo normal; `self` es un recordset (uno o varios registros). Asignar `self.state = ...` actualiza **todos** los registros del recordset en una sola instrucción — el ORM genera el `UPDATE` SQL. No hace falta `return`: el framework refresca la vista tras ejecutar el método.

### `views/gestion_tareas_views.xml` (fichero completo)

```xml
<?xml version="1.0" encoding="utf-8"?>
<odoo>

    <record id="view_gestion_tareas_tarea_tree" model="ir.ui.view">
        <field name="name">gestion.tareas.tarea.tree</field>
        <field name="model">gestion.tareas.tarea</field>
        <field name="arch" type="xml">
            <tree decoration-info="state == 'en_progreso'" decoration-success="state == 'hecha'">
                <field name="name"/>
                <field name="user_id"/>
                <field name="date_deadline"/>
                <field name="state"/>
            </tree>
        </field>
    </record>

    <record id="view_gestion_tareas_tarea_form" model="ir.ui.view">
        <field name="name">gestion.tareas.tarea.form</field>
        <field name="model">gestion.tareas.tarea</field>
        <field name="arch" type="xml">
            <form>
                <header>
                    <button name="action_iniciar" type="object" string="Iniciar"
                            class="oe_highlight" invisible="state != 'pendiente'"/>
                    <button name="action_finalizar" type="object" string="Finalizar"
                            class="oe_highlight" invisible="state != 'en_progreso'"/>
                    <button name="action_reiniciar" type="object" string="Reiniciar"
                            invisible="state != 'hecha'"/>
                    <field name="state" widget="statusbar" statusbar_visible="pendiente,en_progreso,hecha"/>
                </header>
                <sheet>
                    <group>
                        <field name="name"/>
                        <field name="user_id"/>
                        <field name="date_deadline"/>
                        <field name="description"/>
                    </group>
                </sheet>
            </form>
        </field>
    </record>

</odoo>
```

Novedades:

- `decoration-info` / `decoration-success` en `<tree>`: colorean la fila completa según una condición Python evaluada contra los campos del registro (nombres tomados de Bootstrap). Puramente visual.
- `<header>`: sección fija por encima de `<sheet>`, reservada para la barra de estado y los botones principales. Convención universal en Odoo.
- `<button name="..." type="object" string="..."/>`: `name` es el método Python a invocar; `type="object"` indica "llama a un método del propio modelo" (frente a `type="action"`, que lanzaría una acción de Odoo); `string` es el texto visible.
- `invisible="state != 'pendiente'"`: sintaxis moderna de Odoo 17 para visibilidad condicional (sustituye al antiguo `attrs="{'invisible': [...]}"`, que aún funciona en la 17 pero está en desuso y desaparece en 18/19). El valor es una expresión Python evaluada contra los campos del registro actual.
- `class="oe_highlight"`: resalta visualmente el botón de acción principal.
- `widget="statusbar"` sobre `state`: renderiza el `Selection` como la barra horizontal de pasos. `statusbar_visible` fija qué valores se muestran siempre en la barra.

### Acción realizada

No se añadió ningún fichero nuevo al manifest (se reutilizaron los mismos dos ficheros). `docker compose restart web` → Apps → ⋮ → **Actualizar** (Odoo detecta el campo `state` nuevo y añade la columna a la tabla automáticamente).

### ✅ Resultado comprobado

Ciclo completo probado sobre "Mi primera tarea": Pendiente (botón "Iniciar") → al pulsarlo, En progreso (barra marca el segundo paso, botón "Finalizar" resaltado) → al pulsarlo, Hecha (barra marca el tercer paso, botón "Reiniciar" visible y sin resaltar). Los botones aparecen y desaparecen exactamente según el estado, tal y como definían las condiciones `invisible`.

---

## Resumen final

Con estos 6 pasos el módulo "Gestión de Tareas" queda funcional de principio a fin:

1. Esqueleto de módulo (`__manifest__.py`, `__init__.py`) y cómo Odoo descubre e instala apps.
2. Modelo de datos (ORM, tipos de campo, relación `Many2one`).
3. Seguridad (`ir.model.access.csv`, grupos de usuarios).
4. Vistas de lista y formulario (`ir.ui.view`, `arch` XML).
5. Acción de ventana y menú (`ir.actions.act_window`, `<menuitem>`).
6. Estado con `Selection`, métodos de modelo y botones condicionales (`<header>`, `widget="statusbar"`, sintaxis `invisible`).

### Posibles ampliaciones para seguir practicando (no cubiertas en esta guía)

- Vista Kanban agrupada por `state`.
- Campo `priority` con widget de prioridad (estrellas).
- Relación `One2many`/`Many2many` (por ejemplo, etiquetas o subtareas).
- Datos de demostración (`demo/` + `'demo'` en el manifest) para que el módulo arranque con tareas de ejemplo.
- Tests automáticos con `odoo.tests.TransactionCase`.
