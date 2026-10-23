# UT03. Práctica 1. Instalación Odoo 17 sobre Ubuntu Server 24 LTS

**Módulo:** UT03. Instalación y Configuración de Sistemas...
**Profesor:** Mario Hermoso Calero
**Instituto:** IES Alonso de Avellaneda
**Alumno (Nombre y Apellidos):** Rubén Barrado Pastor\
 **Curso:** 1 DAM **Fecha:** 23/10/2026

## 🎯 Objetivo de la práctica

Los alumnos trabajarán en equipo e instalarán y configurarán un ERP. Mejorarán su competencia en el trabajo en equipo y en la realización de instalaciones y configuraciones complejas paso a paso siguiendo las indicaciones de esta práctica. Además, practicarán los conocimientos aprendidos en cursos pasados sobre virtualización, con la creación de 2 máquinas virtuales: una para Ubuntu Server y otra para Windows.

## 📝 Descripción de la práctica

Los alumnos trabajarán en grupos de 3 e instalarán y configurarán Odoo 17. Lo harán sobre el sistema operativo Ubuntu Server 24 LTS, el cual habrán instalado previamente en una máquina virtual usando el software de virtualización "Oracle Virtual Box" que tendrán instalado en sus equipos. Además, los alumnos instalarán el cliente gráfico de PostgreSQL "pgAdmin" sobre un Windows 10/11 que hayan instalado previamente en otra máquina virtual usando también "Oracle Virtual Box".

> **Recomendaciones:**
> Cada vez que se realice alguna instalación y/o configuración, **REALIZAD UNA INSTANTÁNEA DE VUESTRA MÁQUINA VIRTUAL** para poder ir atrás si cometéis cualquier equivocación que no sepáis resolver.

## ⚠️ Consideraciones

1. Esta instalación está pensada para la docencia, para que se pueda instalar Odoo en una máquina virtual, un vps, un contenedor o una máquina local, pudiendo instalar Odoo desde cero y desarrollar sobre ese Odoo que se ha instalado. En ningún caso, se va a entrar en detalles de ver cómo sería una instalación para producción.

2. Instalaréis la última versión de Odoo, que es la 17 y lo haréis sobre una máquina virtual de Ubuntu Server 24 LTS.

3. Para instalar la máquina virtual de Ubuntu Server 24 LTS, usaréis el software de virtualización "Oracle Virtual Box" que tenéis ya instalado en vuestros equipos.

4. Hay varias formas de instalar Odoo 17. En vuestro caso, la instalación la realizaréis **desde el código fuente de Odoo 17**. Instalar desde la fuente en lugar de usar paquetes de instaladores tiene las siguientes ventajas:

   * En la instalación desde la fuente no se trata de instalar Odoo, sino de ejecutarlo directamente desde la fuente.

   * Para desarrolladores de módulos, es más conveniente usar la fuente.

   * Hace que el iniciar y detener Odoo sea más flexible y explícito. Permite sobrescribir ajustes usando parámetros de consola sin editar el archivo de configuración.

   * Permite tener más control sobre la configuración y mantener varias versiones al mismo tiempo.

5. A lo largo de la práctica, tendréis que ejecutar comandos en Ubuntu Server. Solamente se os proporcionarán los nuevos o de mayor dificultad.

## 🛠️ Ejercicio Práctico

### 1. Instalar Ubuntu Server 24 LTS

Usando Oracle Virtual Box:

1. Descargar la ISO de la página oficial de Ubuntu.

2. Proceder a la instalación con la siguiente configuración:

   * **RAM:** 2048 MB.

   * **Disco virtual VDI:** 30 GB (reserva dinámica).

   * **RED:** Adaptador puente.

   * **Procesador:** 2 CPUs.

3. **Instalación del sistema operativo:**

   * **IP:** DHCP.

   * **Nombre:** `alumno`.

   * **Usuario y contraseña administrador:** `alumno` / `alumno`.

   * **Nombre de la máquina:** `Ubuntu Server 24 LTS`.

   * **Servidor SSH:** Marcar SÍ a "servidor OpenSSH".

   * **IMPORTANTE:** No marquéis la instalación de ningún otro servicio.

### 2. Comprobar IP

Logarse en Ubuntu Server 24 LTS, comprobar la IP y apuntarla.

### 3. Instalación de Windows

Repetir el paso 1 para Windows (si no está instalado). ISO disponible en el NAS del instituto:
`http://10.1.0.100/ISOs/SSOO/es_windows_10_enterprise_ltsc_2019_x64_vl.iso`

### 4. Conexión por SSH al servidor Ubuntu

Realizarlo desde la máquina Windows de dos maneras:

* **Vía cliente "puTTY":** Descargar puTTY, usar la IP del paso 2 y acceder.

* **Vía terminal (CMD):** Usar el símbolo del sistema de Windows y conectar por SSH.

### 5. Instalar el ERP Odoo 17 desde la fuente

Una vez logados como administrador (`alumno`) en Ubuntu Server:

#### A. Crear usuario Odoo

Cread un usuario para lanzar el servidor y descargar el código en su home:

* Usuario/Contraseña: `odoo` / `odoo`.

#### B. Extraer código fuente (Git)

Comprobad si Git está instalado (`git --version`); si no, instaladlo.

1. Logarse con el usuario `odoo`.

2. Descargar Odoo 17:

   ```
   git clone https://github.com/Odoo/odoo.git --depth 1 --branch 17.0 --single-branch
   
   ```

   > **NOTA 1:** Se descarga solo la v17.0 (`--branch 17.0 --single-branch`) y solo el último commit (`--depth 1`) para ahorrar espacio y tiempo.
   > **NOTA 2:** Usamos HTTPS en lugar de SSH, ya que es más idóneo para este entorno.

#### C. Python

Odoo 17 necesita Python 3.10+. Ubuntu Server 24 trae 3.12.3.

1. Comprobar versión: `python3 --version`.

2. Verificar e instalar `pip` (gestor de paquetes de Python):

   ```
   pip3 --version
   
   ```

   *(Si no está instalado, instaladlo con el usuario `alumno` según indique el sistema).*

#### D. PostgreSQL

1. Instalar PostgreSQL (v12 o superior) y su cliente: paquetes `postgresql` y `postgresql-client`.

   > **NOTA:** Se crea automáticamente el superusuario `postgres` (sin contraseña por ahora).

2. Odoo prohíbe usar el superusuario, así que cread el usuario `odoo`:

   ```
   sudo su postgres
   createuser --createdb --pwprompt odoo
   exit
   
   ```

   *(Estableced la contraseña `odoo`).*

   > **Solución a error de conexión (Unix domain socket):**
   > Si falla el comando `createuser`, como usuario `alumno` ejecutad:
   >
   > ```
   > pg_lsclusters
   > 
   > ```
   >
   > Luego (reemplazando versión y cluster según la salida):
   >
   > ```
   > sudo su postgres
   > pg_ctlcluster 16 main start
   > 
   > ```
   >
   > Tras esto, volved a intentar el comando `createuser`.

#### E. Dependencias

Instalar paquetes necesarios usando el script de Odoo (como usuario `alumno`):

```
sudo /home/odoo/odoo/setup/debinstall.sh

```

#### F. Ejecutar Odoo

1. Averiguad la IP del servidor Ubuntu y apuntarla.

2. Arrancar Odoo:

   ```
   su - odoo
   cd odoo
   python3 odoo-bin --addons-path=addons
   
   ```

3. Ir al navegador: `http://IP_Servidor_Ubuntu:8069`.

4. Rellenar el formulario inicial de Odoo:

   * **Master Password:** Dejar la generada automáticamente.

     > *Atención: Si se pierde, se puede borrar la línea `admin_password` del archivo de configuración `odoo.conf`.*

   * **Database Name:** `test`

   * **Email / Password:** `admin` / `admin`

   * **Phone Number:** Vacío

   * **Language / Country:** Spanish (Español) / Spain

   * **Demo data:** Marcar el check (para pruebas).

   * Pulsar **Create database**.

5. Iniciar sesión con `admin` / `admin` en la pantalla de login. Veréis el catálogo de aplicaciones.

#### G. Instalar wkhtmltopdf (Generación de PDFs)

1. Descargar versión 0.12.6 para Ubuntu (Jammy amd64) en `.deb`.

2. Como usuario `alumno`, descargad e instalad el archivo `.deb`.

3. Reiniciar Odoo:

   ```
   su - odoo
   cd odoo
   python3 odoo-bin --addons-path=addons
   
   ```

   *(Si estaba en ejecución, detenedlo con `Ctrl + C` primero).*

4. Comprobar: Activar el módulo **Ventas**, entrar a un registro y pulsar "Vista previa" para ver si genera el PDF correctamente.

#### H. Archivo de configuración de Odoo

1. Como usuario `odoo`, buscad el fichero oculto `.odoorc` en el home.

2. Cread el directorio `conf` y copiad el archivo allí renombrándolo:

   ```
   mkdir conf
   cp .odoorc conf/odoo-server.conf
   
   ```

3. Editar `conf/odoo-server.conf` (dejar el `admin_passwd` intacto) y añadir/modificar:

   ```
   addons_path = /home/odoo/odoo/odoo/addons,/home/odoo/odoo/addons
   db_host = 127.0.0.1
   db_port = 5432
   db_user = odoo
   db_password = odoo
   
   ```

4. Ejecutar Odoo usando este archivo:

   ```
   python3 odoo-bin -c conf/odoo-server.conf
   
   ```

#### I. Configurar Odoo como un servicio (systemd)

Como usuario `alumno`, cread el fichero `/etc/systemd/system/odoo.service`:

```
[Unit]
Description=Odoo Open Source ERP and CRM
After=network.target

[Service]
Type=simple
User=odoo
Group=odoo
ExecStart=python3 /home/odoo/odoo/odoo-bin -c /home/odoo/conf/odoo-server.conf --logfile /home/odoo/odoo.log --dev=all
KillMode=mixed

[Install]
WantedBy=multi-user.target

```

*Probad a arrancar, parar, reiniciar y ver el estado del servicio `odoo.service`.*

### 6. Acceso a BD Odoo vía terminal (`psql`)

1. Como `alumno`, cread una contraseña de sistema para el usuario linux `postgres`.

2. Logarse como `postgres` y entrar al cliente `psql`.

   > **Comandos útiles psql:**
   > `\du` (Roles), `\l` (Bases de datos), `\dt` (Tablas), `\connect Nombre_BD`, `\q` (Salir).

3. Usar `\du` para confirmar que existen los roles `postgres` y `odoo`.

4. Probad algunos comandos básicos para familiarizaros.

### 7. Acceso a BD Odoo vía Cliente Gráfico (`pgAdmin`)

Desde la máquina Windows:

1. Descargar e instalar pgAdmin desde su web oficial (todo por defecto).

2. Abrir pgAdmin -> **Add New Server**.

3. Antes de configurar la conexión, debemos asignar clave al RDBMS para `postgres` desde Ubuntu:

   ```
   postgres=# ALTER ROLE postgres PASSWORD 'postgres';
   
   ```

4. Completar datos en pgAdmin:

   * **General > Name:** (Nombre identificativo)

   * **Connection:**

     * **Host:** IP de Ubuntu

     * **Port:** 5432

     * **Maintenance db:** `postgres`

     * **Username/Password:** `postgres` / `postgres`

5. **Apertura de puertos (Solución al error "connection timeout"):**
   PostgreSQL por defecto bloquea conexiones externas. Como usuario `root` en Ubuntu modificad:

   * `/etc/postgresql/16/main/postgresql.conf`:
     Buscar `listen_addresses = 'localhost'`, comentar y añadir abajo:

     ```
     listen_addresses = '*'
     
     ```

   * `/etc/postgresql/16/main/pg_hba.conf`:
     Bajo `# IPv4 local connections`, añadir:

     ```
     host    all             all             all                     scram-sha-256
     
     ```

6. Reiniciar servicio PostgreSQL (`systemctl restart postgresql.service`).

7. Conectar desde pgAdmin (ahora debería funcionar). Desplegar DB `test` para ver las 366 tablas creadas por Odoo.

8. **Segunda conexión:** Cread una nueva conexión al servidor en pgAdmin pero usando las credenciales del usuario `odoo` (Username: `odoo`, Password: `odoo`) para mayor seguridad y consistencia de acceso.

## ✅ Resultados esperados

**Informe escrito:** Se entregará un informe detallando todos los pasos de las diferentes instalaciones/configuraciones, explicadas tanto en modo texto como en gráfico, pegando todas y cada una de las capturas de pantalla de cada paso que deis en esta práctica. Este informe, tendréis que subirlo, en formato PDF y con los nombres y primer apellido de los participantes, a la tarea correspondiente del aula virtual para su posterior corrección.

## 📚 Bibliografía

* [Odoo 17 Documentation - Install from source](https://www.odoo.com/documentation/17.0/es/administration/on_premise/source.html)

* [Ángel Moya - Instalar Odoo 15 en Ubuntu 20.04](https://angelmoya.es/sin-categoria/instalar-odoo-15-en-ubuntu-20-04/)