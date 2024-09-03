# Introduccion a Pub/Sub

## Objetivo de la práctica:
Al finalizar la práctica, serás capaz de:
- Crear topicos de pub/sub
- Podras utilizar el topicos de pub/sub en tus aplicacciónes

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

Paso 6. Habilita las APIs requeridas. Utiliza el siguiente comando:

```
gcloud services enable run.googleapis.com
```

Paso 7. Crea una variable de entorno con el numero de proyecto. Utiliza el comando:

```
export PROJECT_NUMBER=$(gcloud projects list --filter="qwiklabs-gcp" --format='value(PROJECT_NUMBER)')
```

### Tarea 2. Crear un Topico de Pub/Sub
Paso 1. Crea un topico de Pub/Sub. En cloud Shell utiliza el siguiente comando:

```
gcloud pubsub topics create my-first-topic
```
### Tarea 3. Descarga y prepara el codigo muestra.
Paso 1. Descarga el codigo muestra necesario para el laboratorio. Utiliza el siguiente comando:

```
git clone https://github.com/rosera/pet-theory.git
```

Paso 2. Cambia al directorio *pet-theory/lab05/lab-service*. Utiliza el comando:

```
cd pet-theory/lab05/lab-service
```

Paso 3. Instala los paquetes express, body-parser y de pub/sub. Utiliza el comando:

```
npm install express
npm install body-parser
npm install @google-cloud/pubsub
```

Paso 4. Abre el aarchivo package.json. Utiliza el comando:

```
nano package.json
```
Puedes utilizar el editor de tu preferencia (VIM, Nano etc.)

Paso 5. En linea 7 en el apartado de scripts, añade *"start" : "node index.js",*. La seccion debería verse así:

```json
  "scripts": {
    "start": "node index.js",
    "test": "echo \"Error: no test specified\" && exit 1"
  },
```
Paso 6. Crea un archivo nuevo llamado index.js con el siguiente contenido:

```
const {PubSub} = require('@google-cloud/pubsub');
const pubsub = new PubSub();
const express = require('express');
const app = express();
const bodyParser = require('body-parser');
app.use(bodyParser.json());
const port = process.env.PORT || 8080;

app.listen(port, () => {
  console.log('Listening on port', port);
});

app.post('/', async (req, res) => {
  try {
    const labReport = req.body;
    await publishPubSubMessage(labReport);
    res.status(204).send();
  }
  catch (ex) {
    console.log(ex);
    res.status(500).send(ex);
  }
})

async function publishPubSubMessage(labReport) {
  const buffer = Buffer.from(JSON.stringify(labReport));
  await pubsub.topic('my-first-topic').publish(buffer);
}
```

Utiliza el comando: 

```
nano index.js
```

Paso 7. Crea un archivo Dockerfile con el siguiente contenido:

```
FROM node:18
WORKDIR /usr/src/app
COPY package.json package*.json ./
RUN npm install --only=production
COPY . .
CMD [ "npm", "start" ]
```

Utiliza el comando:

```
nano Dockerfile
```

### Tarea 4. Despliegua el codigo del servicio Lab Report
Paso 1. Crea un archivo llamado deploy.sh con el siguiente contenido:

```
gcloud builds submit \
  --tag gcr.io/$GOOGLE_CLOUD_PROJECT/lab-report-service
gcloud run deploy lab-report-service \
  --image gcr.io/$GOOGLE_CLOUD_PROJECT/lab-report-service \
  --platform managed \
  --region us-east4 \
  --allow-unauthenticated \
  --max-instances=1
```

Utiliza el comando:

```
nano deploy.sh
```

Paso 2. Vuelve el archivo ejecutable. Utiliza el comando:

```
chmod u+x deploy.sh
```

Paso 3. Ejecuta el script de despliegue. Utiliza el comando:

```
./deploy.sh
```

Paso 4. Guarda la URL del servicio en una variable de entorno. Utiliza el comando:

```
export LAB_REPORT_SERVICE_URL=$(gcloud run services describe lab-report-service --platform managed --region us-east4 --format="value(status.address.url)")
```

Paso 5. Confirma que la variable de entorno tenga el URL. Utiliza el comando:

```
echo $LAB_REPORT_SERVICE_URL
```

Paso 6. Crea un archivo llamado post-reports.sh con el siguiente contenido:

```
curl -X POST \
  -H "Content-Type: application/json" \
  -d "{\"id\": 12}" \
  $LAB_REPORT_SERVICE_URL &
curl -X POST \
  -H "Content-Type: application/json" \
  -d "{\"id\": 34}" \
  $LAB_REPORT_SERVICE_URL &
curl -X POST \
  -H "Content-Type: application/json" \
  -d "{\"id\": 56}" \
  $LAB_REPORT_SERVICE_URL &
```

Utiliza el comando:

```
nano post-reports.sh
```

Paso 7. Haz que el archivo sea ejecutable. Utiliza el comando:

```
chmod u+x post-reports.sh
```

Paso 8. Ejecuta el archivo el cual debería generar 3 reportes en nuestra aplicación. Utiliza el comando:

```
./post-reports.sh
```

Paso 9. Ve a la pagína de Cloud Run, y abre el servicio desplegado. Puedes ir a la pestaña de logs para corroborar que se generaron 3 entradas se muestras como:

![entradas](img/entradas.png)

### Tarea 5. Prepara el codigo del servicio Email
Paso 1. Cambia al directorio *lab05/email-service*. Utiliza el comando:

```
cd ~/pet-theory/lab05/email-service
```

Paso 2. Instala express y body-parser. Utiliza el comando:

```
npm install express
npm install body-parser
```

Paso 3. En linea 7 en el apartado de scripts, añade *"start" : "node index.js",*. La seccion debería verse así:

```json
  "scripts": {
    "start": "node index.js",
    "test": "echo \"Error: no test specified\" && exit 1"
  },
```

Paso 4. Crea un archivo llamado index.js con el siguiente contenido:

```
const express = require('express');
const app = express();
const bodyParser = require('body-parser');
app.use(bodyParser.json());

const port = process.env.PORT || 8080;
app.listen(port, () => {
  console.log('Listening on port', port);
});

app.post('/', async (req, res) => {
  const labReport = decodeBase64Json(req.body.message.data);
  try {
    console.log(`Email Service: Report ${labReport.id} trying...`);
    sendEmail();
    console.log(`Email Service: Report ${labReport.id} success :-)`);
    res.status(204).send();
  }
  catch (ex) {
    console.log(`Email Service: Report ${labReport.id} failure: ${ex}`);
    res.status(500).send();
  }
})

function decodeBase64Json(data) {
  return JSON.parse(Buffer.from(data, 'base64').toString());
}

function sendEmail() {
  console.log('Sending email');
}
```

Utiliza el comando:

```
nano index.js
```

Paso 5. Crea un archivo Dockerfile con el siguiente contenido:

```
FROM node:18
WORKDIR /usr/src/app
COPY package.json package*.json ./
RUN npm install --only=production
COPY . .
CMD [ "npm", "start" ]
```

Utiliza el comando:

```
nano Dockerfile
```

### Tarea 6. Despliegua el codigo del servicio Email
Paso 1. Crea un archivo llamado deploy.sh con el siguiente contenido:

```
gcloud builds submit \
  --tag gcr.io/$GOOGLE_CLOUD_PROJECT/email-service

gcloud run deploy email-service \
  --image gcr.io/$GOOGLE_CLOUD_PROJECT/email-service \
  --platform managed \
  --region us-east4 \
  --no-allow-unauthenticated \
  --max-instances=1
```

Utiliza el comando

```
nano deploy.sh
```

Paso 2. Haz al archivo ejecutable. Utiliza el comando:

```
chmod u+x deploy.sh
```

Paso 3. Ejecuta el archivo. Utiliza el comando:

```
./deploy.sh
```

### Tarea 7. Configura Pub/Sub para hacer trigger a nuestro Servicio de Email
Paso 1. Crea una cuenta de servicio para pub/sub. Utiliza el comando:

```
gcloud iam service-accounts create pubsub-cloud-run-invoker --display-name "PubSub Cloud Run Invoker"
```

Paso 2. Provee a la cuenta de servicio con los accesos adecuados. Utiliza el comando:

```
gcloud run services add-iam-policy-binding email-service --member=serviceAccount:pubsub-cloud-run-invoker@$GOOGLE_CLOUD_PROJECT.iam.gserviceaccount.com --role=roles/run.invoker --region us-east4 --platform managed
```

Paso 3. Habilita a pub/sub el crear tokens de autentificacion. Utiliza el comando: 

```
gcloud projects add-iam-policy-binding $GOOGLE_CLOUD_PROJECT --member=serviceAccount:service-$PROJECT_NUMBER@gcp-sa-pubsub.iam.gserviceaccount.com --role=roles/iam.serviceAccountTokenCreator
```

Paso 4. Genera una variable de entorno para el URL del servicio. Utiliza el comando:

```
exportEMAIL_SERVICE_URL=$(gcloud run services describe email-service --platform managed --region us-east4 --format="value(status.address.url)")
```

Paso 5. Corrobora que la variable de entorno tenga la URL. Utiliza el comando:

```
echo $EMAIL_SERVICE_URL
```

Paso 7. Crea una suscripcion para el servicio de Email. Utiliza el comando:

```
gcloud pubsub subscriptions create email-service-sub --topic my-first-topic --push-endpoint=$EMAIL_SERVICE_URL --push-auth-service-account=pubsub-cloud-run-invoker@$GOOGLE_CLOUD_PROJECT.iam.gserviceaccount.com
```

Paso 8. Prueba el servicio de Email. Utiliza el siguiente comando:

```
~/pet-theory/lab05/lab-service/post-reports.sh
```

Paso 7. Corrobora que funcionando de la misma manera que corroboraste el servicio anterior.


### Tarea 8. Servicio de SMS
Paso 1. Cambia al directorio *pet-theory/lab05/sms-service*. Utilza el comando:

```
cd ~/pet-theory/lab05/sms-service
```

Paso 2. Instala los pquetes expres y body-parser. Utiliza el comando:

```
npm install express
npm install body-parser
```

Paso 3. En linea 7 en el apartado de scripts, añade *"start" : "node index.js",*. La seccion debería verse así:

```json
  "scripts": {
    "start": "node index.js",
    "test": "echo \"Error: no test specified\" && exit 1"
  },
```

Paso 4. Crea un archivo llamado index.js con el siguiente contenido:

```
const express = require('express');
const app = express();
const bodyParser = require('body-parser');
app.use(bodyParser.json());

const port = process.env.PORT || 8080;
app.listen(port, () => {
  console.log('Listening on port', port);
});

app.post('/', async (req, res) => {
  const labReport = decodeBase64Json(req.body.message.data);
  try {
    console.log(`SMS Service: Report ${labReport.id} trying...`);
    sendSms();

    console.log(`SMS Service: Report ${labReport.id} success :-)`);    
    res.status(204).send();
  }
  catch (ex) {
    console.log(`SMS Service: Report ${labReport.id} failure: ${ex}`);
    res.status(500).send();
  }
})

function decodeBase64Json(data) {
  return JSON.parse(Buffer.from(data, 'base64').toString());
}

function sendSms() {
  console.log('Sending SMS');
}
```

Utiliza el comando:

```
nano index.js
```

Paso 5. Crea un archivo Dockerfile con el siguiente contenido:

```
FROM node:18
WORKDIR /usr/src/app
COPY package.json package*.json ./
RUN npm install --only=production
COPY . .
CMD [ "npm", "start" ]
```

Utiliza el comando:

```
nano Dockerfile
```

### Tarea 9. Despliega el servicio SMS
Paso 1. Crea un archivo llamada deploy.sh con el siguiente contenido:

```
gcloud builds submit \
  --tag gcr.io/$GOOGLE_CLOUD_PROJECT/sms-service

gcloud run deploy sms-service \
  --image gcr.io/$GOOGLE_CLOUD_PROJECT/sms-service \
  --platform managed \
  --region us-east4 \
  --no-allow-unauthenticated \
  --max-instances=1
```

Utiliza el comando:

```
nano deploy.sh
```

Paso 2. Haz el archivo ejecutable. Utiliza el comando:

```
chmod u+x deploy.sh
```

Paso 3. Ejecuta el archivo. Utiliza el comando:

```
./deploy.sh
```

### Tarea 10. Pub/sub trigger a servicio SMS
Paso 1. Añade los permisos necesarios a pub/sub. Utiliza el comando

```
gcloud run services add-iam-policy-binding sms-service --member=serviceAccount:pubsub-cloud-run-invoker@$GOOGLE_CLOUD_PROJECT.iam.gserviceaccount.com --role=roles/run.invoker --region us-east4 --platform managed
```

Paso 2. Haz que Pub/Sub invoque el servicoi de SMS cuando se publiquen nuevos reportes. Utiliza el comando:

```
SMS_SERVICE_URL=$(gcloud run services describe sms-service --platform managed --region us-east4 --format="value(status.address.url)")
```

Paso 3. Crea una variable de entorno con la URL del servicio de SMS. Utiliza el comando:

```
SMS_SERVICE_URL=$(gcloud run services describe sms-service --platform managed --region us-east4 --format="value(status.address.url)")
```

Paso 4. Corrobora que la variable tenga la url. Utiliza el comando:

```
echo $SMS_SERVICE_URL
```

Paso 5. Crea la suscripcion de pub/sub para el servicio. Utiliza el comando:

```
gcloud pubsub subscriptions create sms-service-sub --topic my-first-topic --push-endpoint=$SMS_SERVICE_URL --push-auth-service-account=pubsub-cloud-run-invoker@$GOOGLE_CLOUD_PROJECT.iam.gserviceaccount.com
```

Paso 7. Ejecuta la prueba del servicio. Utiliza el comando: 

```
~/pet-theory/lab05/lab-service/post-reports.sh
```

Paso 8. Revisa los logs del servicio SMS para corroborar su funcionamiento.
### Resultado esperado
![imagen resultado](img/resultado.png)

¡Felicidades! Con esto haz concluido tu cuarto laboratorio. 
No olvides solicitar ayuda a tu entrenador para eliminar los recursos que recien creaste.
