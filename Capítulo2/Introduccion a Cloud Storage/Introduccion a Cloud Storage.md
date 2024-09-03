# Introduccion a Cloud Storage

## Objetivo de la práctica:
Al finalizar la práctica, serás capaz de:
- Crear buckets de Cloud Storage
- Gestionar objetos dentro de Cloud Storage

## Duración aproximada:
- 30 minutos.

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
### Tarea 2. Acceder a Cloud Storage.
Paso 1.  En el menú de navegación, desplázate hacia abajo y haz clic en "Storage" > "Buckets".


### Tarea 3. Crear un Bucket de Cloud Storage
Paso 1. Haz clic en el botón *Create* dentro de la. pagína de Cloud Storage.

Paso 2. Asigna un nombre único a tu bucket utilizando el Id de tu proyecto. Ejemplo:

- ID_DEL_PROYECTO-storageLab.

Paso 3. Configura la bucket con las siguientes caracterizticas:

| Configuración | Valor | 
| Location type | Region | 
| Location | us-west4 |
| Storage Class | Standard |

El resto dejalo en los predeterminados y haz click en *CREATE*


### Tarea 4. Subir Objetos a tu bucket.
Paso 1. Asgurate de estar dentro de la bucket que acabas de crear.

Paso 2. Haz click en el botón *Upload Files* para seleccionar un archivo de tu computadora. Puedes subir cualquier archivo solo asegurate que no sea algo de gran tamaño para no usar tanto tiempo esperando a la carga.

### Tarea 5. Configurar permisos  de acceso publico.
Paso 1. Asegurate el objeto este cargado en el bucket. Puedes refrescar la pagina haciendo click en el botton de *Refresh*

Paso 2. Abre la pestaña de *Permissions*.

Paso 3. Haz click en *REMOVE PUBLIC ACCESS PREVENTION* y despues en *Confirm*.

Paso 4. Haz click en *GRANT ACCESS*.

Paso 6. En "new principals" escribe allUsers.

Paso 7. Asigna el rol *Storage Object Viewer*.

Paso 8. Haz Click en *Save*

### Tarea 6. Interactua con tu objeto.
Paso 1. Regresa a la pestaña de *Objects*.

Paso 2. Haz click en el objeto que habias subido para abrir sus detalles.

Paso 3. Copia el URL Publico para acceder a tu objeto.

Paso 4. Utiliza el URL en tu navegador para acceder a tu objecto.

### Resultado esperado
![imagen resultado](img/resultado.png)

¡Felicidades! Con esto haz concluido tu cuarto laboratorio. 
No olvides solicitar ayuda a tu entrenador para eliminar los recursos que recien creaste.
