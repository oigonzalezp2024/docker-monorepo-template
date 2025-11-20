# 🚀 **Guía de Onboarding para Nuevos Desarrolladores**



Bienvenido al proyecto.
Esta guía explica **únicamente** lo que un desarrollador debe hacer **después de clonar el repositorio**, sin necesidad de crear Laravel desde cero.

El objetivo es permitir que cualquier persona levante el entorno en minutos usando **Docker + Laravel Sail**, sin instalar PHP ni Composer localmente.

Esta guia a sido desarrollada con la ayuda de Gemini y ChatGPT pero depurada y corregida de forma manual para asegurar su usabilidad.

---

# 🟦 1. Requisitos previos

Antes de comenzar, asegúrate de tener instalado:

* **Docker Desktop** (Windows/Mac)
* **Docker Engine** (Linux)
* **WSL2** (solo para Windows)

No necesitas instalar:

* PHP
* Composer
* MySQL
* Apache/Nginx

Todo esto lo maneja Docker.

---

# 🟩 2. Clonar el repositorio

```bash
git clone https://github.com/tu-usuario/docker-monorepo-template.git
cd docker-monorepo-template
```

El proyecto Laravel se encuentra en:

```
api/
```

---

# 🟦 3. Entrar al proyecto Laravel

```bash
cd api
```

---

# 🟦 4. Crear archivo `.env`

```bash
cp .env.example .env
```

---

# 🟦 5. Hacer ejecutable Sail (solo Linux/WSL/macOS)

```bash
chmod +x vendor/bin/sail
```

---

# 🟩 6. Iniciar el entorno por primera vez

La primera ejecución construirá las imágenes:

```bash
./vendor/bin/sail up -d --build
```

Esto iniciará:

* PHP + Laravel + Nginx
* MySQL
* Servicios internos de Sail

---

# 🟧 7. Generar la clave de aplicación

```bash
./vendor/bin/sail artisan key:generate
```

---

# 🟧 8. Ejecutar migraciones

```bash
./vendor/bin/sail artisan migrate
```

---

# 🟧 9. Crear enlace de almacenamiento

```bash
./vendor/bin/sail artisan storage:link
```

---

# 🟩 10. Acceder al proyecto

Abrir en navegador:

```
http://localhost:8000
```

---

# 🟥 11. Comandos diarios para trabajar

### 🔹 Encender el entorno

```bash
./vendor/bin/sail up -d
```

### 🔹 Detener el entorno (pausa)

```bash
./vendor/bin/sail stop
```

### 🔹 Apagar completamente

```bash
./vendor/bin/sail down
```

### 🔹 Apagar + borrar volúmenes (incluye la base de datos)

```bash
./vendor/bin/sail down -v
```

### 🔹 Ejecutar comandos de Laravel

```bash
./vendor/bin/sail artisan <comando>
```

---

# 🟦 12. Estructura del repositorio

```
docker-monorepo-template/
│
├── api/                    # Proyecto Laravel completo
│   ├── app/
│   ├── bootstrap/
│   ├── config/
│   ├── database/
│   ├── routes/
│   ├── storage/
│   ├── composer.json
│   └── ...
│
├── docs/                   # Documentación opcional
├── .gitignore
├── README.md               # Guía principal del proyecto
└── LICENSE
```

---

# 🟦 13. Buenas prácticas del proyecto

* Nunca ejecutes `php artisan ...` directamente → usa Sail siempre.
* No instales PHP ni Composer globalmente; no son necesarios.
* No modifiques archivos dentro de `vendor/`.
* Los cambios siempre van en ramas nuevas.
* Antes de programar:

  ```bash
  ./vendor/bin/sail up -d
  ```

---

# 🟢 14. Resumen rápido (cheat sheet)

| Acción                 | Comando                                  |
| ---------------------- | ---------------------------------------- |
| Iniciar entorno        | `./vendor/bin/sail up -d`                |
| Iniciar reconstruyendo | `./vendor/bin/sail up -d --build`        |
| Detener                | `./vendor/bin/sail stop`                 |
| Apagar                 | `./vendor/bin/sail down`                 |
| Apagar + borrar BD     | `./vendor/bin/sail down -v`              |
| Artisan                | `./vendor/bin/sail artisan`              |
| Migraciones            | `./vendor/bin/sail artisan migrate`      |
| Generar clave          | `./vendor/bin/sail artisan key:generate` |

---
