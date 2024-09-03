# Introducción a GKE

## Objetivo de la práctica:
Al finalizar la práctica, serás capaz de:
- Crear un Cluster de Kubernetes utilzando GKE autopilot.
- Uso basico del cluster
- Desplegar contenedores en tu cluster de Kubernetes

## Duración aproximada:
- 95 minutos.

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
gsutil cp -r gs://spls/gsp021/* .
```

Paso 2. Cambia al directorio "orchestrate-with-kubernetes/kubernetes". Utiliza el siguiente comando:

```
cd orchestrate-with-kubernetes/kubernetes
```

### Tarea 3. Crea tu cluster de Kubernetes
Paso 1. Crea tu primer cluster de Kubernetes. En Cloud Shell utiliza en comando:

```
gcloud container clusters create k1 --zone us-west4
```

Esto puede demorar hasta 15 minutos.

Paso 2. Configura tu .kube file. Utiliza el comando:

```
gcloud container clusters get-credentials k1
```

### Tarea 4. Crea tus primeros objetos de Kubernetes
Antes de desplegar nuestra app tengamos un poco de practica con Kubernetes y kubectl

Paso 1. Lanza una instancia de un contenedor de nginx. Utiliza el comando:

```
kubectl create deployment nginx --image=nginx:1.10.0
```

Paso 2. Verifica que exista la pod ejecutando tu contenedor de nginx. Utiliza el comando:

```
kubectl get pods
```

Paso 3. Una vez se encuentre ejecutandose tu POD exponla fuera de Kubernetes. Utiliza el comando:

```
kubectl expose deployment nginx --port 80 --type LoadBalancer
```

Paso 4. Ve la lista de servicios que tienes desplegados. Utiliza el comando:

```
kubectl get services
```

Paso 5. Haz un ping a tu contenedor de manera remota. Utiliza el comando:

```
curl http://<IP Externa>:80
```
Puedes encontrar la IP, en el paso anterior (Paso 4).

Apartir de aqui empezaremos a trabajar con nuestro codigo muestra

### Tarea 5. Explora el codigo de la aplicación

Date unos minutos para explorar el codigo muestra de la aplicacción, sobre todo pon atención a los archivos YAML.

### Tarea 6. Utiliza los archivos de configuracion para desplegar Pods.

Paso 1. Asegurate de estar en el directorio "*orchestrate-with-kubernetes/kubernetes*". 

```
cd ~/orchestrate-with-kubernetes/kubernetes
```

Paso 2. Visualiza el archivo monolith.yaml que se encuentra en la carpeta pods. Utiliza el comando:

```
cat pods/monolith.yaml
```

Paso 2. Crea la pod monolith. Utiliza el comando:

```
kubectl create -f pods/monolith.yaml
```

Paso 4. Verifica el estado de tus pods. Utiliza el comando:

```
kubectl get pods
```

Paso 5. Obten informacion adicional de to pod. Utiliza el comando:

```
kubectl describe pods monolith
```

### Tarea 7. Crea un servicio para acceder a la Pod.
Paso 1. Asegurate de estar en el directorio "*orchestrate-with-kubernetes/kubernetes*". 

```
cd ~/orchestrate-with-kubernetes/kubernetes
```

Paso 2. Despliega pods con el archivo de configuracion secure-monolith.yaml, asegurate de crear una pod segura con datos de configuracion. Utiliza los siguientes comandos:

```
kubectl create secret generic tls-certs --from-file tls/
```

```
kubectl create configmap nginx-proxy-conf --from-file nginx/proxy.conf
```

```
kubectl create -f pods/secure-monolith.yaml
```

Paso 3. Explora la configuracion el servicio. Utiliza el comando:

```
cat services/monolith.yaml
```

Paso 4. Crea el servicio con el archivo de configuracion "*monolith.yaml*" que se encuentra en la carpeta Services. Utiliza el comando:

```
kubectl create -f services/monolith.yaml
```

Paso 5. Configura una regla de firewall para permitir trafico hacia tu servicio. Utiliza el comando:

```
gcloud compute firewall-rules create allow-monolith-nodeport \
  --allow=tcp:31000
``` 
Generalmente esto lo hace Kubernetes por nosotros. Sin embargo nosotros estamos eligiendo un puerto en particular para configurar health checks de manera mas sencilla si asi quisieramos.

Paso 6. Obten la direccion IP externa para alguno de tus nodos. Utiliza el comando:

```
gcloud compute instances list
```

Paso 7. Intenta hacer un pin a tu *secure-monolith*. Utiliza el comando:

```
curl -k https://<IP EXTERNA>:31000
```
Podras notar que hay un timeout, esto es debido a las etiquetas, nuestras pods no tienen las etiquetas que utilizamos en nuestro servicio.

### Tarea 8. Añade etiquetas a tus pods.
Paso 1. Verifica las pods ejecutandose con la etiqueta "app=monolith". Utiliza el comando:

```
kubectl get pods -l "app=monolith"
```

Paso 2. Verifica las pods ejecutandose con la etiqueta "secured=enabled". Utiliza el comando:

```
kubectl get pods -l "secured=enabled"
```
Podras notar que no encuentras ninguna Pod.

Paso 3. Añade las etiquetas faltantes. Utiliza el comando:

```
kubectl label pods secure-monolith 'secure=enabled'
```

Paso 4. Verifica que las etiquetas se hayan actualizado. Utiliza el comando

```
kubectl get pods secure-monolith --show-labels
```

Paso 5. Vizualiza la lista de *Endpoints* de tu servicio. Utiliza el comando:

```
kubectl describe services monolith | grep Endpoints
```

Paso 6. Intenta hacer un ping a tu pod. Utiliza el comando:

```
curl -l https://<IP EXTERNA>:31000
```
Deberías poder observar que si tenemos contacto con nuestra pod.

### Tarea 9. Separa la aplicación monolitica a microservicios.
Ahora que ya tuviste practica desplegando Pods toca practicar desplegando "Deployments"

Paso 1. Examina la configuracion del archivo "auth.yaml". Utiliza el comando

```
cat deployments/auth.yaml
```

Paso 2. Crea tu objeto deployment basado en el archivo auth.yaml. Utiliza el comando

```
kubectl create -f deployments/auth.yaml
```

Paso 3. Crea un servicio para tu deployment. Utiliza el comando:

```
kubectl create -f services/auth.yaml
```

Paso 4. Repite los mismos pasos para los archivos *hello.yaml*. Los puedes encontrar en la carpeta deployments y services.

Paso 5. Repite los mismos pasos para los archivos *frontend.yaml*. Los puedes encontrar en la carpeta deployments y services. Pero primero utiliza el comando:

```
kubectl create configmap nginx-frontend-conf --from-file=nginx/frontend.conf
``` 
Este paso es un configmap que requiere el contenedor del frontend.yaml

Paso 6. Obten la IP externa de tu servicio frontend. Utiliza el comando

```
kubectl get services frontend
```

Paso 7. Has un ping a tu servicio. Utiliza el comando:

```
curl -k https://<IP EXTERNA>
```



### Resultado esperado
![imagen resultado](img/resultado.png)

¡Felicidades! Con esto haz concluido tu Tercer laboratorio. 
No olvides solicitar ayuda a tu entrenador para eliminar los recursos que recien creaste.
