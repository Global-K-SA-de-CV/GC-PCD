# Introducción a App Engine 

## Objetivo de la práctica:
Al finalizar la práctica, serás capaz de:
- Crear y desplegar una aplicacción en App Engine

## Duración aproximada:
- 60 minutos.

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
### Tarea 2. Descarga tu codigo muestra.
Paso 1. En cloud Shell utiliza el siguiente comando:

```
gcloud storage cp -r gs://spls/gsp023/flex_and_vision/ .
```

Paso 2. Cambia al directorio "flex_and_vision". Utiliza el siguiente comando:

```
cd flex_and_vision
```

### Tarea 3. Habilita las APIS requeridas.

Paso 1. En el buscador dentro de la consola busca "Cloud Vision API" y haz click en habilitar.

![Enable](img/enable.png)

Paso 2. En el buscador dentro de la consola busca "Cloud Datastore API" y haz click en habilitar.

Paso 3. En el buscador dentro de la consola busca "Cloud Storage API" y haz click en habilitar.

### Tarea 4. Crea una cuenta de servicio con los permisos adecuados para generar peticiones a las APIs habilitadas.

Paso 1. Crea una variable de entorno en la cual guardes el id de tu proyecto. En cloud Shell utiliza el siguiente comando:

```
export MY_PROJECT_ID=$(gcloud config get-value project)
```

Paso 2. Crea una variable de entorno en la cual guardes la region a usar. En cloud Shell utiliza el siguiente comando:

```
export APP_REGION=us-central1
```

Paso 3. Crea una cuenta de servicio que pueda acceder a las APIs cuando hagas pruebas locales. Utiliza el siguiente comando:

```gcloud iam service-accounts create my-service-account \
  --display-name "lab-service-account"
```

Paso 4. Proporciona los permisos adecuados a la cuenta de servicio. Utiliza el siguiente comando:

```
gcloud projects add-iam-policy-binding ${MY_PROJECT_ID} \
--member serviceAccount:my-service-account@${MY_PROJECT_ID}.iam.gserviceaccount.com \
--role roles/owner
```

Paso 5. Genera una llave de cuenta de servicio. Utiliza el comando:

```
gcloud iam service-accounts keys create ~/key.json \
--iam-account my-service-account@${MY_PROJECT_ID}.iam.gserviceaccount.com
```

Paso 6. Crea una variable de entorno que guarde la ubicacion donde se genero la llave de la cuenta de servicio. Utiliza el comando:

```
export GOOGLE_APPLICATION_CREDENTIALS="/home/${USER}/key.json"
```

### Tarea 5. Prueba tu aplicacción de manera local

Paso 1. Crea un entorno aislado de Python 3 con virtualenv. En Cloud Shell utiliza el siguiente comando:

```
virtualenv -p python3 my-virtual-env
```

Paso 2. Entra a tu entorno virtual llamado my-virtual-env. Utiliza el siguiente comando:

```
source my-virtual-env/bin/activate
```

Paso 3. Instala tud dependencias usando PIP. Utiliza el comando:

```
pip install -r requirements.txt
```

Paso 4. Crea una App de App Engine. Utiliza el comando:

```
gcloud app create --region=$APP_REGION
```

Paso 5. Crea una bucket de Cloud Storage. Utiliza el comando:

```
gsutil mb gs://${MY_PROJECT_ID}
```

Paso 6. Ejecuta tu aplicación. Utiliza el comando:

```
python main.py
```

Paso 7. Una vez la aplicación se inicie haz click en el botton de Preview en Cloud Shell y elige la opcion *Preview on Port 8080*

![preview](img/preview8080.png)

Paso 8. Prueba subiendo una imagen a tu aplicación, una vez que termines de hacer pruebas regresa a cloud shell presiona *CTRL+C* para apagar el web server.

### Tarea 6. Explora el Codigo.

Paso 1. En Cloud Shell puedes encontrar todos los archivos de la aplicación date unos minutos para verlos y trata de entenderlos.

Procura revisar to main.py, y tu app.yaml que puedes encontrar dentro de la carpeta templates.

### Tarea 7. Prepara tu aplicacion para desplegarse en App engine flexible enviroment

Paso 1. Primero modifica tu archivo de configuracion app.yaml. En Cloud Shell utiliza el siguiente comando:

```
nano app.yaml
```
Si prefieres usar algun otro editor como VIM o EMACS puedes usarlos igualmente.

Paso 2. Dentro del archivo reemplaza <your-cloud-storage-bucket> con el nombre de la bucket que creaste en la tarea 5 paso 5. El nombre debe ser el Id de tu proyecto.

Paso 3. En el apartado de "env_variables" configura un escalado manual. Añade al final del archivo la siguiente linea de codigo:

```
manual_scaling:
  instances: 1
```

Paso 5. Cambia la version de python de 3 a 3.7.

Paso 6. Guarda y cierra tu archivo. En nano presiona *CTRL+X* despues *Y* y enter.

### Tarea 8. Despliega tu aplicación en App Engine Flexible enviroment

Paso 1. Actualiza el tiempo fuera de Cloud Build. En Cloud Shell utiliza el comando:

```
gcloud config set app/cloud_build_timeout 1000
```

Paso 2. Despliega tu aplicación. Utiliza el comando:

```
gcloud app deploy
```

Si aparece el mensaje *Do you want to continue (Y/n)?*, escribe *Y* y preciona Enter. Este proceso puede tardar hasta 10 minutos.

Paso 3. Una vez desplegada la app puedes abrir tu app con la siguiente url:

https://ID_DE_TU_PROYECTO.appspot.com

No olvides cambiar ID_DE_TU_PROYECTO por le ID de tu proyecto.


### Resultado esperado
![imagen resultado](img/resultado.png)

¡Felicidades! Con esto haz concluido tu segundo laboratorio. 
No olvides solicitar ayuda a tu entrenador para eliminar los recursos que recien creaste.
