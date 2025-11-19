# 🟦 Fase 5: Control del Entorno (Encender, Detener, Apagar, Retomar)

Laravel Sail permite administrar *todo* a través de su script. Aquí están los comandos esenciales:

## ▶ Encender el entorno

```bash
./vendor/bin/sail up -d
```

Se usa cada vez que deseas **retomar** el proyecto.

## 🔄 Encender reconstruyendo

```bash
./vendor/bin/sail up -d --build
```

Útil si cambiaste dependencias, Dockerfile o PHP.

## 🚫 Pausar / Detener el entorno

```bash
./vendor/bin/sail stop
```

Detiene los contenedores **sin eliminarlos**. Ideal para pausar.

## 🔌 Apagar completamente

```bash
./vendor/bin/sail down
```

Detiene y elimina contenedores, pero **conserva la base de datos**.

## 💣 Apagado total + eliminar base de datos

```bash
./vendor/bin/sail down -v
```

⚠️ Elimina volúmenes → **borra la BD y todos los datos persistentes**.

## 🔍 Ver contenedores activos

```bash
docker ps
```

---

# 🛡️ Fase 6: Seguridad para producción

**⚠ Esta sección es crítica si piensas subir el proyecto a un servidor real.**

## 1. Desactivar el modo debug

```ini
APP_DEBUG=false
APP_ENV=production
```

## 2. Cambiar credenciales inseguras

Nunca uses:

* usuario: `sail`
* contraseña: `password`

## 3. Evitar exponer MySQL al exterior

Si tu `compose.yaml` tiene:

```yaml
ports:
  - "3306:3306"
```

Cámbialo por:

```yaml
ports:
  - "127.0.0.1:3306:3306"
```

## 4. Regenerar APP_KEY en producción

```bash
./vendor/bin/sail artisan key:generate
```

---

# 🎉 Proyecto funcionando

Al abrir:

```
http://localhost:8000
```

Deberías ver la pantalla inicial de Laravel.

<a href="../README.md">atrás</a>