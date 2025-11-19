## 🚀 Guía de Despliegue Profesional: Proyecto Base Laravel con Sail (Docker)

Esta guía establece el estándar de desarrollo moderno utilizando **Laravel Sail** sobre Docker (PHP-FPM, MySQL) para garantizar la **máxima portabilidad** y **cero dependencia** del entorno PHP local.

### ⚙️ Fase 1: Preparación del Entorno Aislado

#### Paso 1: Verificación de Docker

Asegúrese de que **Docker Desktop** o el demonio de Docker (si está en Linux) esté **en línea y activo** en segundo plano.

```bash
docker info
```

>   * **Verificación:** El comando debe mostrar la información de **Client** y **Server**. Si falla, inicie o reinicie Docker Desktop/Servicio.

#### Paso 2: Creación y Aislamiento del Directorio de Proyecto

Mueva la terminal al directorio de trabajo deseado (ej: `/ruta/a/proyectos/`). Luego, cree y acceda a la carpeta donde residirá su aplicación.

```bash
# Crea el directorio 'api' si no existe y entra en él.
mkdir -p api
cd api
```

-----

### 📦 Fase 2: Instalación del Core y Dependencias (Solo Docker)

Ejecutaremos todos los comandos de inicialización dentro de contenedores efímeros (`--rm`) para asegurar que **ninguna dependencia local sea requerida**.

#### Paso 3: Crear el Proyecto Base de Laravel

Usamos la imagen oficial de **Composer** para inicializar la estructura de Laravel en el directorio actual (`.`).

```bash
# Usa comillas dobles para la robustez en rutas con espacios.
docker run --rm \
    -v "$(pwd):/app" \
    composer create-project laravel/laravel .
```

>   * **Verificación:** El directorio actual debe contener archivos clave como `artisan`, `composer.json`, y las carpetas `app/` y `config/`.

#### Paso 4: Instalar y Publicar Laravel Sail

Instalamos la herramienta oficial de orquestación de Docker para Laravel y generamos los archivos de configuración del entorno.

1.  **Instalar el paquete `laravel/sail`:**

    ```bash
    docker run --rm \
        -v "$(pwd):/app" \
        composer:2.7.7 require laravel/sail --dev
    ```

2.  **Publicar la configuración de Docker Compose:**
    Usamos el binario `artisan` instalado con PHP-CLI (v8.3 como referencia) para generar la configuración, solicitando el servicio de **MySQL**.

    ```bash
    # Genera el 'compose.yaml' y el 'Dockerfile' de la aplicación.
    docker run --rm \
        -v "$(pwd):/app" \
        -w /app \
        php:8.3-cli php artisan sail:install --with=mysql
    ```

>   * **Verificación:** Un archivo llamado **`compose.yaml`** (confirmado como el estándar generado por Sail) debe aparecer en el directorio raíz.

-----

### 🐳 Fase 3: Configuración y Arranque de Servicios

#### Paso 5: Configuración de Variables de Entorno (`.env`)

Abra el archivo **`.env`** y **confirme** los siguientes parámetros de conexión (estándares de Sail):

```ini
# --- Sección de Aplicación ---
APP_URL=http://localhost:8000
APP_PORT=8000 # Puerto del host para acceder al proyecto.

# --- Sección de Base de Datos ---
DB_CONNECTION=mysql
DB_HOST=mysql         # ¡VITAL! Nombre del servicio en compose.yaml (el contenedor).
DB_PORT=3306
DB_DATABASE=laravel
DB_USERNAME=sail
DB_PASSWORD=password
```

#### Paso 6: Ajuste de Permisos de Ejecución (Sistemas Unix)

Este paso es **CRÍTICO** si está en WSL, Linux o macOS. Asegure que el script de arranque de **Sail** sea ejecutable.

```bash
chmod +x vendor/bin/sail
```

#### Paso 7: Construcción y Arranque del Entorno

Utilice el script `sail` para construir las imágenes (solo la primera vez) y levantar los servicios en segundo plano (`-d`).

```bash
# Construye la imagen de la aplicación y levanta todos los contenedores.
./vendor/bin/sail up -d --build
```

>   * **Tiempo estimado:** Esto tardará varios minutos la primera vez (`--build`).
>   * **Verificación:** El comando debe confirmar la creación o el inicio de los contenedores.

#### Paso 8: Verificación del Estado de Contenedores

Espere un minuto y verifique que los servicios estén activos.

```bash
docker ps
```

>   * **Verificación:** Debe ver al menos dos contenedores en la lista con el estado **`Up`** (el servicio de `mysql` y el servicio de la aplicación, `laravel.test-1` o similar).

-----

### ✅ Fase 4: Finalización y Primer Acceso Profesional

#### Paso 9: Ejecución de Comandos Post-Instalación

Ejecute los comandos de Artisan esenciales. **Todos deben usar el prefijo `./vendor/bin/sail`**.

1.  **Generar la Clave de la Aplicación (Obligatorio):**
    ```bash
    ./vendor/bin/sail artisan key:generate
    ```
2.  **Ejecutar las Migraciones de la Base de Datos:**
    ```bash
    ./vendor/bin/sail artisan migrate
    ```

#### Paso 10: Solución de Fricción de Permisos (Profesional)

Si encuentra problemas de permisos (ej: al intentar guardar archivos de logs, caché o al crear modelos) debido a que Docker creó archivos como `root`:

```bash
# Fija los permisos y crea el enlace simbólico de la carpeta 'storage'.
./vendor/bin/sail artisan storage:link
./vendor/bin/sail artisan sail:publish # Opcional: Publica los archivos de configuración de Sail.
```

#### Paso 11: Acceso y Control

1.  **Acceda a su proyecto BASE:**
    Abra su navegador y navegue a la URL configurada:
    **`http://localhost:8000`**

2.  **Control del Entorno (Apagado / Reanudación):**

      * **Detener los contenedores (pausar):**
        ```bash
        ./vendor/bin/sail stop
        ```
      * **Reanudar los contenedores:**
        ```bash
        ./vendor/bin/sail up -d
        ```
      * **Apagar y eliminar los contenedores y redes (LIMPIEZA TOTAL):**
        ```bash
        ./vendor/bin/sail down
        ```

-----

## 🛡️ Anexo Crítico: Seguridad para Despliegue y Producción

**ADVERTENCIA:** La configuración anterior de Sail está optimizada para la **comodidad del desarrollo local**, NO para la seguridad en Internet. Si planea desplegar este proyecto en un servidor accesible públicamente (VPS, Cloud), **DEBE** realizar los siguientes ajustes obligatorios.

### 1\. Bloqueo del Modo Debug (¡Vital\!)

Jamás deje el modo debug activado en producción. Si ocurre un error, Laravel mostrará sus contraseñas y claves API al usuario en el navegador.

  * **En su archivo `.env` de producción:**
    ```ini
    APP_ENV=production
    APP_DEBUG=false
    ```

### 2\. Rotación de Credenciales de Base de Datos

Las credenciales por defecto (`sail`/`password`) son de conocimiento público y vulnerables a bots.

1.  **Modifique su archivo `.env` con claves robustas:**
    ```ini
    DB_USERNAME=mi_usuario_seguro_pro
    DB_PASSWORD=GenerarUnaClaveLargaYCompleja_#99!
    ```
2.  **Importante:** Si ya creó los volúmenes de Docker con las claves antiguas, deberá destruir el volumen para que MySQL acepte la nueva contraseña:
    ```bash
    ./vendor/bin/sail down -v  # -v elimina los volúmenes (¡CUIDADO: BORRA DATOS!)
    ./vendor/bin/sail up -d
    ```

### 3\. Restricción de Puertos (Firewall de Docker)

Por defecto, la configuración `ports: - '3306:3306'` en `compose.yaml` expone su base de datos a **todo internet** (0.0.0.0).

  * **Solución:** Modifique el archivo `compose.yaml` para que la base de datos solo escuche peticiones internas o del localhost.

    **Cambie esto:**

    ```yaml
    ports:
        - '3306:3306'
    ```

    **Por esto (Binding a localhost):**

    ```yaml
    ports:
        - '127.0.0.1:3306:3306'
    ```

    *Esto asegura que solo usted (vía túnel SSH) o la aplicación puedan conectarse a la BD, bloqueando ataques externos.*

### 4\. Generación de Clave Única

Asegúrese de que la `APP_KEY` en producción sea diferente a la de su entorno local. Esta llave encripta las sesiones y cookies de sus usuarios.

```bash
./vendor/bin/sail artisan key:generate
```

```