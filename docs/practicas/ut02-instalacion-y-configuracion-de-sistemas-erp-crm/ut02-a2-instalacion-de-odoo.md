# 1. Resultado de aprendizaje, contenido y criterios de evaluación

<a class="md-button md-button--primary" href="ut02-a2-instalacion-de-odoo.pdf">&#x2B07; Descargar pr&aacute;ctica en PDF</a>


## Resultado de aprendizaje

|                                                                                                                          |
|--------------------------------------------------------------------------------------------------------------------------|
| Resultado de aprendizaje                                                                                                 |
| RA2. Implanta sistemas ERP-CRM interpretando la documentación técnica e identificando las diferentes opciones y módulos. |
| Contenido                                                                                                                |
| Tipos de instalación: monopuesto y cliente/servidor. Procesos de instalación del sistema ERP-CRM.                        |
| Criterios de evaluación                                                                                                  |
| b\) Se han realizado diferentes tipos de instalaciones.                                                                  |

## Contenido

## Criterios de evaluación

# 2. Agrupamiento

Individual



1.  Dentro de la máquina virtual Linux Mint, abre el navegador web **Firefox**.
2.  Accede a la URL: **http://localhost:8069**
3.  Comprueba que carga el **Gestor de Bases de Datos de Odoo**.

## Comprobación 2: Arquitectura Cliente/Servidor

1.  En tu **ordenador real (anfitrión)**, abre tu navegador habitual (Chrome, Firefox, Edge).
2.  Accede a la URL usando la IP anotada en la Fase 5: **http://TU_DIRECCION_IP:8069** (ejemplo: **\[http://192.168.1.45:8069\](http://192.168.1.45:8069)**).
3.  Comprueba que el ERP responde correctamente desde la red externa al servidor.

# 3. Contexto

Se va a implantar Odoo en un entorno de pruebas virtualizado. Para valorar la modalidad adecuada, se comprobará el funcionamiento del ERP con una configuración monopuesto y con una arquitectura cliente/servidor.

# 4. Objetivo de la actividad

El objetivo es instalar y verificar el ERP comprobando su funcionamiento en dos configuraciones:

1.  **Configuración Monopuesto:** Acceso local desde el propio servidor.
2.  **Arquitectura Cliente/Servidor:** Acceso remoto desde el equipo anfitrión hacia la máquina virtual.

****NOTA: Si ya tienes la máquina virtual con Linux Mint solo tienes que comprobar que está instalado Guest Additions. Si no lo está instalalo ****(Paso 4)****. Una vez hecho ve a la Fase 2. En caso de no tener la máquina sigue los pasos siguientes.****

****

****

****

****

****

# 5. Enunciado de la actividad

UT02-A2

Instalación de Odoo en máquina virtual

## **Enunciado de la actividad**

****Deja evidencias gráficas de cada punto conseguido. Imagen gráfica (captura) que sea legible.****

## FASE 1: Entorno de Virtualización (VirtualBox + Linux Mint)

## Descargas previas en el equipo anfitrión (tu ordenador)

- Descarga e instala **Oracle VM VirtualBox** y su **Extension Pack** (**virtualbox.org**).

<!-- -->

- Descarga la ISO de **Linux Mint (Edición Cinnamon 64-bit)** (**linuxmint.com**) o utiliza la proporcionada por el profesorado.

## Paso 1: Crear la Máquina Virtual en VirtualBox

1.  Abre VirtualBox y haz clic en el botón **Nueva** .

2.  Configura la primera ventana:

    - **Nombre:** **Servidor-Odoo** (o el nombre que prefieras).
    - **Imagen ISO:** Despliega el menú, selecciona **Otro...** y busca el archivo **.iso** de Linux Mint que descargaste.
    - **Tipo:** Linux \| **Versión:** Ubuntu (64-bit) (se autoselecciona).
    - **IMPORTANTE:** Marca la casilla **Omitir instalación desatendida** (*Skip Unattended Installation*). Esto garantiza que controlemos todo el proceso de instalación manualmente.
    - Haz clic en **Siguiente**.

3.  Configuración de Hardware:

    - **Memoria RAM:** Asigna un mínimo de **4096 MB** (4 GB). Si tu ordenador tiene 16 GB o más, asigna **8192 MB** (8 GB).
    - **Procesadores (CPU):** Asigna un mínimo de **2 núcleos** (mantén el indicador en la zona verde).
    - Haz clic en **Siguiente**.

4.  Disco Duro Virtual:

    - Selecciona **Crear un disco duro virtual ahora**.
    - Asigna un tamaño de al menos **30 GB** (al ser dinámico, solo ocupará en tu PC real lo que realmente use la VM).
    - Haz clic en **Siguiente** y luego en **Terminar**.

## Paso 2: Ajustes finales de la Máquina Virtual antes de arrancar

1.  Haz clic derecho sobre tu VM recién creada (**Servidor-Odoo**) y selecciona **Configuración**.

2.  **Pantalla:** En la pestaña *Pantalla*, sube la **Memoria de vídeo** al máximo (**128 MB**).

3.  Red:

    - Por defecto viene en **NAT** (funciona bien para salir a internet).
    - *Opcional pero muy recomendado:* Cambia la opción a **Adaptador puente** (*Bridged Adapter*) y elige tu tarjeta de red (WiFi o Ethernet). Esto asignará a la VM una IP propia en tu red local, permitiendo acceder a Odoo desde el navegador de tu ordenador real.

4.  Haz clic en **Aceptar**.

## Paso 3: Instalación del Sistema Operativo Linux Mint

1.  Haz doble clic en la máquina virtual para **Iniciar**.

2.  Al arrancar, verás un menú negro (GRUB). Presiona **Enter** en la primera opción: **Start Linux Mint 21.x Cinnamon**.

3.  El sistema cargará un escritorio "en vivo" (Live CD). Haz doble clic en el icono del escritorio **Install Linux Mint**.

4.  Sigue el asistente de instalación paso a paso:

    - **Idioma:** Selecciona *Español* y pulsa *Continuar*.

    - **Disposición del teclado:** Selecciona *Español* y prueba escribir símbolos como **@** o **\#** en la casilla de comprobación.

    - **Códecs multimedia:** Marca la casilla *Instalar códecs multimedia* y pulsa *Continuar*.

    - **Tipo de instalación:** Selecciona **Borrar disco e instalar Linux Mint** (no hay riesgo, solo formateará el disco duro virtual asignado de 30 GB). Haz clic en *Instalar ahora* y confirma el aviso pulsando *Continuar*.

    - **Zona horaria:** Selecciona tu zona (ej. *Madrid* o *Canarias*) y pulsa *Continuar*.

    - Datos de usuario:

      - *Su nombre:* **Administrador Odoo**
      - *Nombre de su equipo:* **odoo-server**
      - *Nombre de usuario:* **odoo** (o tu usuario preferido en minúsculas).
      - *Contraseña:* Elige una contraseña clara y fácil de recordar (la pedirán constantemente con el comando **sudo**).
      - Selecciona **Solicitar mi contraseña para iniciar sesión**.

5.  Haz clic en *Continuar* y espera a que finalice la copia de archivos (5-10 minutos).

6.  Cuando termine, haz clic en **Reiniciar ahora**.

7.  La pantalla te pedirá: *"Please remove the installation medium, then press ENTER"*. Presiona la tecla **Enter** en tu teclado. La máquina se reiniciará y mostrará la pantalla de inicio de sesión de Linux Mint.

## Paso 4: Instalación de las VirtualBox Guest Additions

(Necesario para tener pantalla completa, cortar/pegar entre tu PC y la VM, e integrar el ratón).

1.  Inicia sesión en Linux Mint con tu usuario y contraseña.
2.  En el menú superior de la ventana de VirtualBox (no del sistema Mint), ve a **Dispositivos** -\> **Insertar imagen de CD de las "Guest Additions"...**
3.  En la pantalla de Linux Mint aparecerá una ventana preguntando si deseas ejecutar el medio. Haz clic en **Ejecutar**.
4.  Introduce tu contraseña de Linux Mint cuando te la pida.
5.  Se abrirá una terminal negra procesando la instalación. Cuando ponga **Press Return to close this window...**, pulsa **Enter**.
6.  Ve al menú superior de VirtualBox: **Dispositivos** -\> **Portapapeles compartido** -\> Selecciona **Bidireccional**.
7.  Reinicia la máquina virtual (desde el menú de inicio de Linux Mint -\> *Apagar/Reiniciar*).

## FASE 2: Preparación del Sistema Operativo

A partir de este punto, todas las acciones se realizan dentro de la máquina virtual Linux Mint utilizando la **Terminal**.

## Paso 1: Abrir la Terminal y actualizar el sistema

1.  Abre una terminal.
2.  Actualiza la lista de software del repositorio ejecutando: **sudo apt update**
3.  Actualiza todos los paquetes del sistema a su última versión: **sudo apt upgrade -y**

## Paso 2: Instalación de herramientas base y dependencias de red

Para descargar archivos, firmar claves de seguridad y clonar repositorios durante la instalación de PostgreSQL y Odoo, se requieren utilidades básicas en el sistema.

1.  Para instalar todo el bloque de herramientas necesario de una sola vez:

    **sudo apt install -y curl wget git gnupg2 software-properties-common build-essential**

**

**

## Paso 3: Comprobación previa antes de pasar a la Fase 3

Asegúrate de que el entorno responde correctamente comprobando las versiones instaladas:

1.  Comprueba que **curl** y **git** están listos:

    1.  **git --version**

        **curl --version**

    *Resultado esperado:* La terminal debe devolver el número de versión instalado de cada herramienta sin mostrar mensajes de error.

## FASE 3: Instalación y Configuración del SGBD (PostgreSQL)

Siguiendo las recomendaciones de la [Documentación Oficial de Odoo sobre paquetes](https://www.odoo.com/documentation/19.0/es/administration/on_premise/packages.html), instalaremos el SGBD PostgreSQL.

## Paso 1: Instalación del motor de base de datos

* *sudo apt install -y postgresql**

## Paso 2: Verificación del servicio

* *sudo systemctl status postgresql**

* Asegúrate de que aparece en estado *active (running)*. Presiona *q* para salir.*

## FASE 4: Instalación de Odoo

Aplicaremos la **instalación por paquete (.deb)** utilizando el repositorio oficial APT de Odoo.

## Paso 1: Instalación de dependencias de renderizado (wkhtmltopdf)

Odoo requiere el motor *wkhtmltopdf* para exportar informes e impresiones a formato PDF.

* *sudo apt install -y wkhtmltopdf**

## Paso 2: Configuración del repositorio oficial e instalación de Odoo

1.  Importa la clave de firma GPG oficial de Odoo:

    **wget -qO - https://nightly.odoo.com/odoo.key \| gpg --dearmor \| sudo tee /usr/share/keyrings/odoo-archive-keyring.gpg \> /dev/null**

2.  Añade el repositorio de la versión estable a las fuentes del sistema:

    **echo "deb \[signed-by=/usr/share/keyrings/odoo-archive-keyring.gpg\] http://nightly.odoo.com/18.0/nightly/deb/ ./" \| sudo tee /etc/apt/sources.list.d/odoo.list**

3.  Actualiza el índice de paquetes e instala Odoo:

    **sudo apt update**

    **sudo apt install -y odoo**

## FASE 5: Configuración y Puesta en Marcha

Una vez completada la instalación del paquete de Odoo y PostgreSQL, Odoo queda integrado en el sistema como un servicio. En esta fase vas a configurar la clave maestra del sistema, verificar el servicio e iniciar sesión desde el navegador.

## Paso 1: Averiguar la dirección IP de la máquina virtual

Necesitamos saber la dirección IP de Linux Mint para poder entrar a Odoo desde el navegador (ya sea desde dentro de la máquina virtual o desde el navegador de tu ordenador real).

1.  Abre la terminal y ejecuta: **hostname -I**
2.  Verás una serie de números separados por puntos. **Apunta esa dirección IP**, la usarás más adelante. *(Si estás dentro de la máquina virtual, también puedes usar simplemente *localhost*).*

## Paso 2: Editar el archivo de configuración (**odoo.conf**)

En las instalaciones por paquete **.deb**, el archivo de configuración se crea automáticamente en **/etc/odoo/odoo.conf**. Aquí se define la **Clave Maestra** (*Master Password*), que sirve para crear, duplicar, restaurar o borrar bases de datos.

1.  En la terminal, abre el archivo con el editor de texto **nano** ejecutando:

** sudo nano /etc/odoo/odoo.conf**

1.  *(Escribe tu contraseña de Linux Mint y pulsa *Enter*).*

2.  Verás un texto similar a este:

    Ini, TOML

    **\[options\]**

    **; This is the password that allows database operations:**

    **admin_passwd = my_admin_password**

    **db_host = False**

    **db_port = False**

    **db_user = odoo**

    **db_password = False**

3.  Usa las flechas del teclado para moverte hasta la línea **admin_passwd = ...**.

4.  Cambia **my_admin_password** por una contraseña que vayas a recordar (por ejemplo: **ClaveMaestraOdoo2026**).

    - *Ejemplo final de la línea:* **admin_passwd = ClaveMaestraOdoo2026**

**

**

1.  **Guardar y salir del editor ******nano******:**

    - Pulsa la combinación de teclas **Ctrl + O** y luego la tecla **Enter** (esto guarda los cambios).
    - Pulsa la combinación de teclas **Ctrl + X** (esto cierra el editor y te devuelve a la terminal).

## Paso 3: Reiniciar y verificar el servicio de Odoo

Cada vez que modificas el archivo **odoo.conf**, debes reiniciar el servicio para que aplique los cambios.

1.  Reinicia el servicio de Odoo: **sudo systemctl restart odoo**

2.  Comprueba que Odoo se está ejecutando correctamente: **sudo systemctl status odoo**

3.  ¿Qué debes ver en pantalla?

    - Busca una línea que ponga ****Active: active (running)**** (suele aparecer con un punto verde).
    - Si aparece **active (running)**, ¡todo va perfecto!
    - Para salir de la pantalla de estado y volver a la terminal, pulsa la tecla ****q****.

4.  *(Opcional)* Asegúrate de que Odoo se inicie automáticamente cada vez que enciendas la máquina virtual: **sudo systemctl enable odoo**

## Paso 4: Abrir Odoo en el navegador web

Odoo utiliza por defecto el puerto **8069**.

1.  Abre el navegador web **Firefox** dentro de tu máquina virtual Linux Mint (o en tu ordenador real).

2.  En la barra de direcciones de arriba, escribe una de estas dos opciones y pulsa **Enter**:

    - Si estás en el navegador **de la máquina virtual**: **http://localhost:8069**
    - Si estás en el navegador **de tu PC real**: **http://TU_DIRECCION_IP:8069** *(reemplaza *TU_DIRECCION_IP* por la que anotaste en el Paso 1, ej: *\[http://192.168.1.45:8069\](**[**http://192.168.1.45:8069**](http://192.168.1.45:8069/)**)*)*.

## Paso Final: Creación de la Base de Datos Inicial

Al acceder por primera vez, verás la pantalla del **Gestor de Bases de Datos de Odoo**. Rellena el formulario con los siguientes datos:

1.  **Master Password:** Escribe la contraseña que pusiste en el archivo **odoo.conf** (en nuestro ejemplo: **ClaveMaestraOdoo2026**).
2.  **Database Name:** Escribe un nombre para tu base de datos en minúsculas y sin espacios (ejemplo: **mi_empresa_db**).
3.  **Email:** Tu correo electrónico o simplemente **admin** (será tu usuario para iniciar sesión en Odoo).
4.  **Password:** La contraseña que quieras usar para entrar a Odoo como usuario administrador (ejemplo: **admin123**).
5.  **Phone number:** Puedes dejarlo en blanco o poner un número ficticio.
6.  **Language:** Selecciona **Spanish / Español**.
7.  **Country:** Selecciona **Spain / España** (o tu país correspondiente).
8.  **Demo data:** Marca la casilla **Demo data** *(muy recomendado para entornos de aprendizaje, ya que creará datos de prueba como clientes, productos y facturas ficticias para practicar).*
9.  Haz clic en el botón verde **Create database**.

> **Nota:** La creación de la base de datos puede tardar entre 1 y 3 minutos mientras Odoo instala los módulos base y carga los datos de prueba. No cierres el navegador.

Cuando termine el proceso, el sistema te redirigirá automáticamente al **Panel de Aplicaciones de Odoo**, listo para instalar módulos como *Ventas*, *Facturación* o *Inventario*.

# 6. Material proporcionado al alumnado

• Ordenador con software de virtualización y recursos suficientes para las máquinas virtuales de prueba.

• Imagen del sistema operativo indicada para la práctica y documentación oficial de instalación de Odoo.

• Material de la unidad y acceso de red entre el equipo anfitrión y la máquina virtual.

# 7. Entrega

• Informe PDF que documente por separado las dos instalaciones, sus esquemas y las comprobaciones funcionales.

• Adjuntar evidencias de acceso en ambas modalidades e incidencias relevantes; describir los pasos, no limitarse a capturas.

• Nombre del fichero: tuNombre_Apellido1_Apellido2_UT02-A2.pdf.

# 8. Rúbrica

<div>

|                                                             |                                                                                                                                                                                                                                                                                     |                                                                                                                                                                                                            |                                                                                                                                                                                 |                                                                                                                |
|-------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------|
| Fase 1: Entorno de Virtualización (VirtualBox + Linux Mint) | Configura la VM omitiendo la instalación desatendida, asigna hardware adecuado (RAM ≥ 4GB, CPU ≥ 2 núcleos, Disco ≥ 30GB), establece la red en Adaptador Puente, instala Linux Mint con códecs multimedia y activa las Guest Additions con portapapeles bidireccional. (1,5 puntos) | Instala Linux Mint y la VM de forma funcional, pero omite o configura de forma incompleta algún ajuste secundario (Guest Additions, memoria de vídeo al máximo o portapapeles bidireccional). (1,05puntos) | La VM e instalación de Linux Mint son operativas, pero mantiene el adaptador de red en NAT sin permitir acceso externo o la asignación de recursos es deficiente. (0,75 puntos) | No logra crear la máquina virtual o el sistema operativo Linux Mint no queda instalado y operativo. (0 puntos) |
| Fase 2: Preparación del Sistema Operativo                   | Actualiza repositorios y paquetes (**apt update/upgrade**), instala el bloque completo de herramientas base (**curl**, **wget**, **git**, **gnupg2**, **build-essential**, etc.) y verifica las versiones correctamente en la terminal. (0,5 puntos)                                | Actualiza el sistema e instala el paquete de herramientas base, pero omite el paso de comprobación explícita de versiones en la terminal. (0,35 puntos)                                                    | Instala las herramientas básicas de red y desarrollo pero omite la actualización previa del sistema operativo. (0,25 puntos)                                                    | No realiza la actualización del sistema ni instala las dependencias base requeridas. (0 puntos)                |
| Fase 3: Instalación y Configuración del SGBD (PostgreSQL)   | Instala PostgreSQL desde repositorios y verifica mediante **systemctl** que el servicio se encuentra activo y en ejecución (*active/running*). (1 puntos)                                                                                                                           | Instala PostgreSQL correctamente pero no realiza la verificación del estado del servicio en la terminal. (0,7 puntos)                                                                                      | Requiere asistencia para completar la instalación del motor de base de datos o solucionar bloqueos en el gestor de paquetes. (0,5 puntos)                                       | No instala el SGBD PostgreSQL o el servicio no queda en estado funcional. (0 puntos)                           |
| Fase 4: Instalación de Odoo                                 | Instala la dependencia **wkhtmltopdf**, importa la clave GPG oficial, añade el repositorio oficial de Odoo 18.0 e instala el paquete **.deb** de forma limpia. (2,5 puntos)                                                                                                         | Instala Odoo desde el repositorio oficial de forma correcta, pero omite la instalación previa de **wkhtmltopdf** para la generación de PDFs. (1,75 puntos)                                                 | Logra instalar Odoo pero comete errores en el proceso de adición de claves GPG o fuentes que debe corregir manualmente. (1,25 puntos)                                           | No completa la instalación del paquete Odoo o falla la adición del repositorio. (0 puntos)                     |
| Fase 5: Configuración y Puesta en Marcha                    | Identifica la IP del servidor, modifica la clave maestra en **odoo.conf**, reinicia el servicio, habilita el autoarranque (**enable**) y verifica el estado *active (running)*. (2 puntos)                                                                                          | Modifica la clave maestra y reinicia el servicio correctamente, pero no configura el autoarranque o no identifica la IP de la VM. (1,4 puntos)                                                             | Modifica la configuración pero no logra reiniciar o verificar el servicio Odoo adecuadamente desde la terminal. (1 puntos)                                                      | No modifica la contraseña maestra en **odoo.conf** o el servicio Odoo no llega a ejecutarse. (0 puntos)        |
| Fase 6: Verificación de Modalidades y Creación de BD        | Comprueba el acceso monopuesto (**localhost:8069**) y cliente/servidor (**IP:8069**), y crea la BD inicial con datos demo, idioma español y clave maestra validada. (1,25 puntos)                                                                                                   | Accede a Odoo y crea la base de datos correctamente, pero solo verifica una de las dos modalidades de acceso (monopuesto o red). (0,88 puntos)                                                             | Accede a la interfaz web de Odoo pero no completa la creación de la base de datos inicial o no incluye datos de prueba. (0,63 puntos)                                           | No logra acceder a la interfaz web de Odoo por el puerto 8069 ni desplegar la base de datos. (0 puntos)        |
| b\) Realiza diferentes tipos de instalaciones               | Completa y verifica las modalidades monopuesto y cliente/servidor, distinguiendo sus componentes y aportando evidencias funcionales claras de ambas. (1,25 puntos)                                                                                                                  | Completa ambas modalidades con funcionamiento correcto, pero la documentación o alguna comprobación es parcial. (0,88 puntos)                                                                              | Solo una modalidad queda operativa o la segunda no se diferencia/verifica suficientemente. (0,63 puntos)                                                                        | No completa una instalación funcional ni evidencia dos modalidades de instalación. (0 puntos)                  |

</div>

****

****

|                        |                          |                             |                             |                   |                  |
|------------------------|--------------------------|-----------------------------|-----------------------------|-------------------|------------------|
|                        | Se retrasa más de 5 días | Se retrasa entre 4 y 5 días | Se retrasa entre 2 y 3 días | Se retrasa 1 día. | Entrega en plazo |
| Entrega tarea en plazo | \- 5                     | \- 3                        | -2                          | -1                | 0                |
