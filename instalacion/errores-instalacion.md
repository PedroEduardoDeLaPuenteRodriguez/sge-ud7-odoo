# Errores de instalación: Odoo 19.0 Community con Docker

Registro de errores típicos al instalar Odoo 19.0 Community con Docker y Docker Compose (Odoo + PostgreSQL), con su causa, la solución y la fuente donde se consulta.

| Error o mensaje | Causa | Solución aplicada | Fuente consultada |
|---|---|---|---|
| `Bind for 0.0.0.0:8069 failed: port is already allocated` | El puerto 8069 ya lo usa otro contenedor o programa (por ejemplo, otra instancia de Odoo). | Detener el proceso o contenedor que lo ocupa (`docker ps`, `docker stop <id>`), o cambiar el mapeo de puertos en `docker-compose.yml` a `8070:8069` y entrar por `http://localhost:8070`. | Documentación oficial de Docker: publicación de puertos (docs.docker.com) |
| `Cannot connect to the Docker daemon at unix:///var/run/docker.sock. Is the docker daemon running?` | El servicio de Docker no está iniciado. | Iniciar Docker Desktop, o en Linux ejecutar `sudo systemctl start docker`. | Documentación oficial de Docker: instalación y post-instalación (docs.docker.com) |
| `permission denied while trying to connect to the Docker daemon socket` | El usuario no pertenece al grupo `docker`. | Añadir el usuario al grupo con `sudo usermod -aG docker $USER` y volver a iniciar sesión. | Documentación oficial de Docker: pasos posteriores a la instalación en Linux (docs.docker.com) |
| `manifest for odoo:19 not found` o `pull access denied for odoo` | Etiqueta de imagen mal escrita o inexistente. | Usar la etiqueta indicada en la página oficial de la imagen (`odoo:19.0`) y comprobar la conexión a Internet. | Imagen oficial de Odoo en Docker Hub (hub.docker.com/_/odoo) |
| `FATAL: password authentication failed for user "odoo"` | Las credenciales de PostgreSQL del servicio `web` no coinciden con las del servicio `db`, o el volumen conserva una contraseña anterior. | Igualar `POSTGRES_USER` y `POSTGRES_PASSWORD` en ambos servicios. Si el volumen es antiguo, ejecutar `docker compose down -v` y volver a levantar (borra los datos). | Imagen oficial de Odoo en Docker Hub y documentación de PostgreSQL sobre autenticación (postgresql.org/docs) |
| `connection to server at "db" ... port 5432 failed: Connection refused` | Odoo arrancó antes de que PostgreSQL estuviera listo. | Añadir `depends_on: - db` en el servicio `web` y reiniciar con `docker compose restart web`. | Documentación oficial de Docker Compose: `depends_on` (docs.docker.com) |
| `PermissionError: [Errno 13] Permission denied: '/var/lib/odoo'` | El volumen o la carpeta montada no tiene permisos para el usuario `odoo` del contenedor. | Usar volúmenes nombrados de Docker en lugar de carpetas del equipo, o ajustar los permisos de la carpeta montada. | Imagen oficial de Odoo en Docker Hub (sección de volúmenes) |
| `localhost:8069` no carga (`ERR_CONNECTION_REFUSED`) | El contenedor aún está arrancando o se ha detenido por un error. | Esperar unos segundos, revisar con `docker compose ps` y `docker compose logs web`, y corregir el error que aparezca. | Documentación oficial de Docker Compose: comando `logs` (docs.docker.com) |
| `Database creation error: database "xxx" already exists` | Ya existe una base de datos con ese nombre. | Elegir otro nombre, o eliminar la base antigua desde el gestor de bases de datos (`/web/database/manager`). | Documentación de Odoo 19: gestión de bases de datos (odoo.com/documentation/19.0) |
| `Access Denied` o `Wrong master password` al crear o borrar una base | La contraseña maestra introducida no coincide con `admin_passwd` de `odoo.conf`. | Usar la contraseña maestra definida en la configuración, o modificarla en `odoo.conf` y reiniciar el contenedor. | Documentación de Odoo 19: seguridad del gestor de bases de datos (odoo.com/documentation/19.0) |
| `WSL 2 installation is incomplete` o `Virtualization must be enabled` (Docker Desktop en Windows) | WSL 2 no está instalado o la virtualización está desactivada en la BIOS. | Ejecutar `wsl --install`, activar la virtualización en la BIOS y reiniciar Docker Desktop. | Documentación oficial de Docker Desktop para Windows (docs.docker.com) |
| El contenedor se cierra solo (`Killed`, código de salida 137) | Memoria insuficiente asignada a Docker. | Subir la memoria de Docker Desktop a 4 GB como mínimo (Ajustes, Resources). | Documentación oficial de Docker Desktop: recursos (docs.docker.com) |

## Comandos de diagnóstico útiles

```bash
docker compose ps            # estado de los contenedores
docker compose logs -f web   # registros de Odoo en directo
docker compose logs db       # registros de PostgreSQL
docker compose down -v       # parar y borrar contenedores y volúmenes
```
