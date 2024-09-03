# Introducción a Cloud Shell

## Objetivo de la práctica:
Al finalizar la práctica, serás capaz de:
- Tener un primer acercamiento a Cloud Shell
- Conocer commandos basicos para configurar Cloud Shell
- Crea tu primer recurso de Compute Engine por medio de Cloud Shell

## Duración aproximada:
- 15 minutos.

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


### Tarea 2. Configurar tu entorno.
Una vez que Cloud Shell esta listo para utilizarse o cloud SDK, podemos empezar a configurar nuestro entorno antes de empezar a crear recursos.

Paso 1. Configura tu region predefinida sobre la cual crearas recursos. Utiliza el comando:

```
gcloud config set compute/region REGION
```
No olvides cambiar `REGION` por la region de tu preferencia o que te indique tu instructor.

Paso 2. Verifica que la configuracion se guardo exitosamente con el comando:

```
gcloud config get-value compute/region
```

Paso 3. Configura la zona predefinida para la creacion de recursos en tu proyecto. Utiliza el comando:

```
gcloud config set compute/zone ZONE
```
No olvides cambiar `ZONE` por la zona adecuada a tu region o la que indique tu instructor.

Paso 4. Verifica que la configuracion se guardo exitosamente con el comando:

```
gcloud config get-value compute/zone
```

### Tarea 3. Configurar variables de entorno.
Muchas veces existen ciertos valores que usamos con frecuensia mientras trabajamos con la linea de comandos, para facilitarnos el uso de estos valores podemos crear variables de entorno.

Paso 1. Crea la variable de entorno que guarda el id de tu proyecto. Utiliza el siguiente comando:

```
export PROJECT_ID=$(gcloud config get-value project)
```

Paso 2. Verifica que la variable se guardo de forma exitosa:

```
echo $PROJECT_ID
```

Paso 3. Crea una variable de entorno para guardar una zona. Utiliza el siguiente comando:

```
export ZONE=`ZONE`
```
No olvides cambiar ZONE por la zona de tu preferencia o la que indique tu instructor.

Paso 4. Verifica que la variable se aguardo de forma exitosa:

```
echo $ZONE
```

### Tarea 4. Crea y maneja una maquina virtual.
Cloud Shell y Cloud SDK son herramientas importantes para el manejo de recursos en Google Cloud, vamos a crear nuestro primer recurso en la nube.

Paso 1. Crea una maquina virtual. Utiliza el siguiente comando:

```
glcoud compute instances create my-first-vm --machine-type e2-medium --zone $ZONE
```
La creacion de la maquina virtual no debe tardar mas de un minuto.

Paso 2. Verifica que tu maquina virtual se haya creado. Utiliza el siguiente comando:

```
gcloud compute instances list
```

Paso 3. Añade un filtro a tu listado. Utiliza el siguiente comando:

```
gcloud compute instances list --filter="name=('my-first-vm')"
```
Este filtro, nos muestra solo las maquinas virtuales que tengan ese nombre en particular, hay varios filtros que podemos usar como zona, region y más, intenta crear tus propios filtros.

### Tarea 4. Conectate a tu maquina virtual
Es posible hacer ssh o rdo a nuestras maquinas virtuales en google cloud.

Paso 1. Verifica las reglas de firewall en nuestro proyecto. Utiliza el comando:

```
gcloud compute firewall-rules list
```

Dentro de la lista debemos tener un resultado llamado default-allow-ssh, si no se encuentra pide ayuda a tu instructor.

Paso 2. Has una conexion SSH a tu maquina virtual. Utiliza el comando:

```
gcloud compute ssh my-first-vm --zone $ZONE
```

Cuando te pregunte escribe *y* y preciona enter. Igualmente deja el *passphrase* en blanco.

Paso 3. Una vez conectado a tu maquina virtual instala nginx. Utiliza el comando:

```
sudo apt-install -y nginx
```

Paso 4. Termina la conexion a tu maquina virtual. Utiliza el comando:

```
exit
```

### Resultado esperado
![result](img/resultado.png)


¡Felicidades! Con esto haz concluido tu primer laboratorio. 
No olvides solicitar ayuda a tu entrenador para eliminar los recursos que recien creaste.

