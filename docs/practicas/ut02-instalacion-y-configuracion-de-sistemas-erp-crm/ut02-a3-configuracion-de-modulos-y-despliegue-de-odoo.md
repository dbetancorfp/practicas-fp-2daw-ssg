# UT02-A3 - Configuración de módulos y despliegue de Odoo adaptado a distintos supuestos empresariales

## 1. Resultado de aprendizaje, contenido y criterios de evaluación

### Resultado de aprendizaje
RA2. Implanta sistemas ERP-CRM interpretando la documentación técnica e identificando las diferentes opciones y módulos.

### Contenido
Parámetros de configuración del ERP-CRM; actualización del sistema; servicios de acceso; entornos de desarrollo, pruebas y explotación.                                                                                                                                

### Criterios de evaluación
c) Se han configurado los módulos instalados. </br>
d) Se han realizado instalaciones adaptadas a las necesidades planteadas en diferentes supuestos.</br>
e) Se ha verificado el funcionamiento del ERP-CRM. f) Se han documentado las operaciones realizadas y las incidencias.

## 2. Agrupamiento

Individual

## 3. Contexto

La empresa ficticia “PixelManga Studio S.L.” es una firma emergente especializada en el entretenimiento digital y la cultura pop. Su modelo de negocio es híbrido y combina tres líneas de productos principales:

 1. Libros Manga Digitales (E-books): Publicaciones digitales en formato PDF/ePub descargables directamente tras la compra online. Aplican un tipo de IVA Superreducido (4%) según la normativa para libros digitales.

2. Merchandising Físico: Figuras coleccionables, camisetas, pósteres y accesorios físicos. Requieren un seguimiento riguroso de inventario (existencias en almacén, recepción, empaquetado y logística de envío). Aplican un tipo de IVA General (21%).

Para centralizar sus operaciones comerciales, contables y de almacenamiento, la empresa necesita desplegar y configurar una instancia aislada de Odoo ERP/CRM perfectamente adaptada a su flujo de trabajo de venta multicanal (tienda física y eCommerce).

## 4. Objetivo de la actividad

El alumnado deberá desplegar una instancia independiente de Odoo, seleccionar e instalar exclusivamente los módulos esenciales, realizar la configuración paramétrica paso a paso adaptada al modelo de negocio de PixelManga Studio S.L., verificar las operaciones comerciales mediante pruebas funcionales integrales y documentar detalladamente las intervenciones técnicas y resolución de incidencias.

## 5. Enunciado de la actividad

### 5.1. Paso 1: Despliegue del docker

Se adjunta el fichero odoo-docker-alumnos.zip con todo lo necesario para levantar el docker de Odoo con todo instalado.

Descomprime el fichero y sigue las indicaciones del documento INSTRUCCIONES.md

Cuando ya puedas acceder a la parte de configuración continuamos en el siguiente paso.

### 5.2. Paso 2: Selección e Instalación de Módulos Esenciales

Accede con permisos de Administrador (*Ajustes \> Aplicaciones*) e instala los siguientes módulos esenciales justificando su requerimiento:

|           |          |           |
|-------|----------|---------|
| *website* & *website_sale* | Sitio Web & Comercio Electrónico | Portal de venta online para el catálogo de mangas y merchandising.                                     |
| *sale* / *sale_management* | Ventas                           | Gestión del flujo comercial, presupuestos, pedidos de venta y tarifas.                                 |
| *stock*                    | Inventario                       | Control de stock físico, ubicaciones, movimientos de almacén y logística de envío para merchandising.  |
| *account*                  | Facturación                      | Generación de facturas, gestión de cobros y aplicación de IVA diferenciado (4% y 21%).                 |
| *website_sale_digital*     | Productos Digitales              | Habilita la descarga automática de ficheros adjuntos (mangas y e-books) tras la confirmación del pago. |

### 5.3. Paso 3: Configuración General de la Empresa, Impuestos y Usuarios

1. Datos de la Empresa:
	- NAVEGACIÓN: *Ajustes \> Compañías \> Datos de la Compañía*.
	- Configurar: Razon Social (*PixelManga Studio S.L.*), NIF (*B-12345678*), Dirección, Correo Electrónico y Sitio Web oficial.

2. Moneda e Idioma:
	- NAVEGACIÓN: *Ajustes \> Idiomas*. Instalar y activar Español (ES).
	- NAVEGACIÓN: *Ajustes \> “Facturación / Contabilidad” / Monedas*. Establecer **Euro (€)** como moneda principal de la compañía.

3.	Configuración Fiscal e Impuestos:
	- NAVEGACIÓN: *Ajustes \> “Facturación / Contabilidad” \> Impuestos*.
	- Verificar la presencia del **IVA 21% Ventas** (para merchandising) y del **IVA 4% Ventas** (para manga digital/físico).

4.	Usuarios y Permisos:
	- NAVEGACIÓN: *Ajustes \> Usuarios \> Gestionar usuarios*.
	- Crear dos usuarios con roles diferenciados:

	- **Usuario 1 (Laura Gómez - Gestora Comercial):** Permisos de Administrador en Ventas y Sitio Web, Usuario en Facturación.
	- **Usuario 2 (Carlos Ruiz - Operador de Almacén):** Permisos de Administrador en Inventario, sin acceso a datos contables sensibles.

### 5.4. Paso 4: Configuración del Catálogo de Productos (Digitales y Físicos)

Accede a Cuadradillo \> *Ventas \> Productos \> Productos \> Nuevo* y crea los siguientes productos configurando sus parámetros específicos:

#### Producto A: Libro Manga Digital - “Manga CyberKnight Vol. 1 (PDF/ePub)”

- **Tipo de Producto:** Servicio / Producto Digital.
- **Precio de Venta:** 8,00 € (IVA excluido).
- **Impuesto de Cliente:** IVA 4% Ventas (IVA superreducido).
- **Política de Facturación:** Cantidades pedidas.
- **Adjunto Digital:** Archivo PDF interactivo alojado en el sistema para descarga en el portal del cliente.

#### Producto B: Merchandising Físico - “Figura Coleccionable CyberKnight 25cm”

- **Tipo de Producto:** Producto Almacenable (Storable Product).
- **Precio de Venta:** 45,00 € (IVA excluido).
- **Impuesto de Cliente:** IVA 21% Ventas.
- **Ruta de Logística:** Comprar / Venta directa desde Almacén Principal (*WH/Stock*).
- **Control de Existencias:** Realizar una actualización de inventario inicial estableciendo un stock de **50 unidades**.

### 5.5. Paso 5: Actualización del Sistema y Registro de Versiones

1.  Ejecuta la verificación de actualizaciones disponibles del sistema Odoo y sus componentes instalados.
2.  Registra en el informe las versiones exactas antes y después de la operación.

Abre una terminal desde la carpeta don está el proyecto Odoo en docker:

\# 1. Comprobar versión inicial

```bash
docker compose exec web odoo --version
```

\# 2. Descargar actualizaciones de la imagen de Odoo

```bash
docker compose pull web
```

\# 3. Actualizar la base de datos

```bash
docker compose exec web odoo -c /etc/odoo/odoo.conf -u all -d db_pixelmanga_prod --stop-after-init
```

\# 4. Recrear contenedor con la nueva versión e iniciar

```bash
docker compose up -d
```

\# 5. Comprobar versión final

```bash
docker compose exec web odoo --version
```

### 5.6. Paso 6: Verificación y Prueba Funcional Integral

Ejecuta la siguiente prueba de compra combinada en el portal e-Commerce para validar la integración de los módulos:

1.  **Navegación en Tienda Online:** Accede como cliente anónimo/registrado a la tienda

    e-Commerce de *PixelManga Studio S.L.*.

2.  **Cesta de la Compra Multi-producto:** Añade al carrito:

	- 1 x “Manga CyberKnight Vol. 1 (PDF/ePub)” (8,00 €)
	- 1 x “Figura Coleccionable CyberKnight 25cm” (45,00 €)

1.  **Procesamiento de Pago Simulado:** Procesa el pedido mediante la pasarela de pago en modo prueba (Wire Transfer / Demo Payment).
2.  **Comprobación de Entrega Digital:** Verifica que tras confirmarse el pedido, el cliente dispone de los enlaces de descarga directa para el manga en su panel de usuario.
3.  Comprobación de Logística de Inventario:

	- Navega a *Inventario \> Operaciones \> Envíos*.
	- Verifica que se ha generado automáticamente una albarán de salida (Picking) **únicamente para la Figura Coleccionable** (1 unidad).
  - Modera el envío, valida la salida y comprueba que las existencias de la figura han disminuido de 50 a **49 unidades**.

1.  Comprobación de Facturación e Impuestos:
	- Genera la factura desde el pedido de venta.
	- Revisa el desglose de IVA:

	- IVA 21% aplicado sobre (25,00 € + 45,00 €) = 70,00 € → Impuesto: 14,70 €
    - IVA 4% aplicado sobre 8,00 € → Impuesto: 0,32 €
    - Total Factura = 78,00 € + 15,02 € = **93,02 €**.

	- Publica la factura y registra el cobro simulado.

### 5.7. Paso 7: Registro de Operaciones e Incidencias

Documenta en una bitácora técnica las operaciones realizadas y las incidencias surgidas durante el proceso.

*Ejemplo de Incidencia Registrada:*

\* **Incidencia \#01:** Error al intentar descargar el manga digital tras la compra online (código HTTP 403 Forbidden).

\* **Causa:** El adjunto digital no tenía activada la opción de acceso público desde el portal de e-Commerce en el módulo *website_sale_digital*.

\* **Solución Aplicada:** Acceso a *Ajustes \> Sitio Web \> Productos Digitales*, habilitando la casilla “*Permitir descarga de recursos asociados a pedidos pagados*”, reanudando el correcto flujo de descarga.

## 6. Material proporcionado al alumnado

- Entornos de prueba aislado con instalación independiente de Odoo.
- Ficha del supuesto empresarial “PixelManga Studio S.L.” con listado detallado de precios e IVA.
- Documentación oficial de Odoo v17/v18 (Módulos Website Sale, Inventory, Invoicing).
- Ficheros digitales de prueba (manga_vol1_sample.pdf).

## 7. Entrega

• Informe PDF con la configuración de cada supuesto, módulos elegidos, actualizaciones y pruebas funcionales.

• Incluir evidencias de configuración de entornos y un registro de operaciones e incidencias.

• Nombre del fichero: tuNombre_Apellido1_Apellido2_UT02-A3.pdf.

## 8. Rúbrica
| Criterio | Excelente | Bien | Regular | Mal |
| :--- | :--- | :--- | :--- | :--- |
| **c) Configura los módulos instalados.**<br>Pasos 2, 3 y 4. | Configura los módulos de ambos entornos con parámetros coherentes y justificados para cada supuesto.<br>*(2,5 puntos)* | Configura ambos entornos correctamente con alguna imprecisión menor.<br>*(1,75 puntos)* | La configuración es parcial o poco diferenciada entre supuestos.<br>*(1,25 puntos)* | No configura adecuadamente los módulos.<br>*(0 puntos)* |
| **d) Adapta instalaciones a distintos supuestos.**<br>Paso 4 y Contexto | Presenta dos despliegues aislados, adaptados y diferenciados según las necesidades de cada empresa.<br>*(2,5 puntos)* | Adapta ambos despliegues con alguna carencia menor.<br>*(1,75 puntos)* | Solo adapta un supuesto o reutiliza una única configuración sin diferenciación suficiente.<br>*(1,25 puntos)* | No adapta los despliegues a los supuestos.<br>*(0 puntos)* |
| **e) Verifica el funcionamiento del ERP-CRM.**<br>Paso 6. | Realiza y documenta pruebas funcionales pertinentes en ambos despliegues y resuelve los fallos detectados.<br>*(2,5 puntos)* | Comprueba ambos entornos con pruebas básicas y alguna evidencia incompleta.<br>*(1,75 puntos)* | Las comprobaciones son superficiales o solo se aplican a un entorno.<br>*(1,25 puntos)* | No verifica el funcionamiento.<br>*(0 puntos)* |
| **f) Documenta operaciones e incidencias.**<br>Paso 1, 5 y 7. | Documenta de forma clara y completa las operaciones, versiones, incidencias y soluciones de los dos entornos.<br>*(2,5 puntos)* | Documenta la mayoría de las operaciones con alguna omisión menor.<br>*(1,75 puntos)* | La documentación es escasa o desordenada.<br>*(1,25 puntos)* | No documenta las operaciones ni las incidencias.<br>*(0 puntos)* |

Penalización por retraso:

|                  |                          |                             |                             |                   |                  |
|--------------|-------------------|----------------------|---------------------|---------------|-------------|
|                        | Se retrasa más de 5 días | Se retrasa entre 4 y 5 días | Se retrasa entre 2 y 3 días | Se retrasa 1 día. | Entrega en plazo |
| Entrega tarea en plazo | \- 5                     | \- 3                        | -2                          | -1                | 0                |
