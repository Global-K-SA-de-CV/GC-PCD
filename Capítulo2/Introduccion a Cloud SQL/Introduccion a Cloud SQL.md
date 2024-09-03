# Introduccion a Cloud Storage

## Objetivo de la práctica:
Al finalizar la práctica, serás capaz de:
- Crear instancias de Cloud SQL
- Configurar réplicas de lectura.

## Duración aproximada:
- 40 minutos.

## Instrucciones 

### Tarea 1. Ingresar a nuestra Consola en Google Cloud y configurar el proyecto en Cloud Shell.
Paso 1. Abre la Consola de Google, desde <a href="https://console.cloud.google.com/">aquí</a>

Paso 2. Inicia sesión

Paso 3. Abre Cloud Shell

![open-shell](img/activate-shell.png)

Paso 4. Configura verica que tu cuenta esta siendo usada en cloud Shell con el comando:

```
    gcloud auth list
```

Deberia aparecerte tu cuenta activa. En caso de que no aparezca o salga un cuenta diferente utiliza el comando:

```
gcloud config set acocunt `Tu cuenta`
```
Paso 5. Verifica que estes trabajando en el proyecto correcto con el comando:

```
gcloud config list project
```

Puedes encontrar el ID de tu proyecto en la parte superior izquierda al hacer click en el nombre del projecto. Si el ID es distinto utiliza el comando:

```
gcloud config set project `ID-del-proyecto`
```
### Tarea 2. Acceder a Cloud SQL.
Paso 1.  En el menú de navegación, desplázate hacia abajo y haz clic en "SQL".


### Tarea 3. Crear una instancia de Cloud SQL
Paso 1. Haz click en *Crear instancia* 

Paso 2. Selecciona MySQL como motor para tu base de datos

- ID_DEL_PROYECTO-storageLab.

Paso 3. Configura tu instancia con las siguientes caracterizticas:

| Configuración | Valor | 
| Edición de SQL | Enterprise | 
| Preset | Sandbox |
| Version de la base de datos | MySQL 8.0 |
| Id de la instancia | my-first-sql-instance |
| Constraseña | *De tu preferencia* |


El resto dejalo en los predeterminados y haz click en *Crear instancia*. Este proceso puede tardar de 10 a 15 minutos.


### Tarea 4. Conectate a tu instancia.
Paso 1. Dentro de la pagína de detalles de tu instancia puedes encontrar su IP publica, guardala para usarla más delante.

![Ip_Publica](img/ip-publica.png)

Paso 2. Ve al apartado de usuarios.

Paso 3. Haz click en añadir cuenta de usuario y agrega un usuario con las siguientes caracterizticas:

| Configuración | Valor | 
| Nombre de usuario | userdb1 | 
| Contraseña | dbpassword1 |

Haz click en añadir.

Paso 4. Conectate a tu instancia por medio de Cloud Shell. Utiliza el comando:

```
gcloud sql connect my-first-sql-instance --user=userdb1
```
Utiliza la contraseña cuando se te solicite

### Tarea 5. Interactua con tu instancia.
Paso 1. Crea una base de datos. Utiliza el siguiente comando

```sql
CREATE DATABASE mi_base_datos;
```

Paso 2. Crea una tabla en tu base de datos. Utiliza el siguiente codigo SQL:

```sql
CREATE TABLE usuarios (
      id INT AUTO_INCREMENT,
      nombre VARCHAR(100),
      email VARCHAR(100),
      PRIMARY KEY(id)
    );
```

Paso 3. Inserta datos en tu base de datos. Utiliza el siguiente codigo:

```sql
INSERT INTO usuarios (nombre, email) VALUES ('Juan Pérez', 'juan@example.com');
```

Paso 4. Haz una consulta de la base de datos. Utiliza el siguiente codigo:

```sql
SELECT * FROM usuarios;
```

Paso 5. Cierra la conexion. Utiliza el comando:

```
exit
```

### Tarea 6. Configura replicas de lectura.
Paso 1. Ve a la pagína de Cloud SQL y abre los detalles de tu instancia.

Paso 2. Ve al apartado de Replicas.

Paso 3. Haz click en *crear replica de lectura*. Deja todo por defecto y haz click en *Crear replica*

Paso 4. Conectate a tu instancia replica. Utiliza el comando:

```
gcloud sql connect my-first-sql-instance-replica --user=userdb1
```

Utiliza la contraseña cuando se te solicite

Paso 5. Realiza una consulta de los datos. Utiliza el codigo:

```sql
SELECT * FROM usuarios;
```

Paso 6. Cierra la conexion. Utiliza el comando:

```
exit
```

### Tarea 7 (Opcional) (Challenge). Conectate a la base de datos desde Workbench o un Cliente SQL similar.
Paso 1. Utiliza tu direccion IP publica para conectarte a tu instancia replica desde Workbench o un Cliente SQL similar

### Resultado esperado
![imagen resultado](img/resultado.png)

¡Felicidades! Con esto haz concluido tu quinto laboratorio. 
No olvides solicitar ayuda a tu entrenador para eliminar los recursos que recien creaste.
