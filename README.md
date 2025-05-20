# 🔐 Proyecto: Migración webapp VProfile a AWS

![Bash](https://img.shields.io/badge/-Bash-4EAA25?logo=gnu-bash&logoColor=white)![SSH](https://img.shields.io/badge/-SSH-000000?logo=ssh)![AWS](https://img.shields.io/badge/AWS-232F3E?style=flat&logo=amazonwebservices&logoColor=white)![RabbitMQ Badge](https://img.shields.io/badge/RabbitMQ-F60?logo=rabbitmq&logoColor=fff&style=flat)![Apache Tomcat Badge](https://img.shields.io/badge/Apache%20Tomcat-F8DC75?logo=apachetomcat&logoColor=000&style=flat)![MySQL Badge](https://img.shields.io/badge/MySQL-4479A1?logo=mysql&logoColor=fff&style=flat)![GoDaddy Badge](https://img.shields.io/badge/GoDaddy-1BDBDB?logo=godaddy&logoColor=000&style=flat)![Apache Maven Badge](https://img.shields.io/badge/Apache%20Maven-C71A36?logo=apachemaven&logoColor=fff&style=flat)



## 📚 Descripción del Proyecto

VProfile es una aplicación web monolítica utilizada como caso de estudio práctico en el curso de _**"DevOps beginners to advanced with projects"**_ de _**Imran Teli**_ en udemy. Este proyecto tiene como objetivo demostrar el proceso completo de migración de una infraestructura local a la nube, siguiendo la estrategia clásica de **Lift and Shift**, que implica mover una aplicación sin rediseñarla, pero aprovechando la elasticidad y automatización de los servicios en la nube.

La fase inicial del proyecto consistió en la construcción de un entorno **On-Premise simulado utilizando Vagrant**, donde se desplegaron múltiples instancias de máquinas virtuales que representaban distintos componentes de la arquitectura (como la base de datos, servidor de aplicaciones, servidor web, caché, y mensajería).

Posteriormente, se procedió a la migración del entorno hacia **Amazon Web Services (AWS)**, implementando buenas prácticas de infraestructura como código y alta disponibilidad. Durante esta etapa se utilizaron los siguientes servicios y recursos:

-   **EC2 (Elastic Compute Cloud)** para la creación de instancias.
    
-   **S3 (Simple Storage Service)** para almacenar artefactos de la aplicación.
    
-   **Auto Scaling Groups** para garantizar la escalabilidad y la disponibilidad de las instancias de la aplicación.
    
-   **Elastic Load Balancer (ELB)** para distribuir el tráfico entrante y habilitar balanceo de carga con soporte para HTTPS mediante certificados gestionados por **ACM (AWS Certificate Manager)**.
    
-   **Route 53** para la gestión del DNS y zonas hospedadas privadas que facilitaron la resolución de nombres entre instancias.
    
-   **Cloud-init / userdata scripts** para la configuración automática de instancias durante su lanzamiento.


----------

## Índice



<!-- Tabla de contenidos -->
<details open>
  <summary> Tabla de Contenidos</summary>
  <ol>
    <li><a href="#Objetivo-del-proyecto">Objetivo del Proyecto</a>
      <ul>
        <li><a href="#escenario-actual-on-premise">Escenario Actual (On-Premise)</a></li>
        <li><a href="#solución-migración-a-aws">Solución: Migración a AWS</a></li>
        <li><a href="#servicios-de-aws-utilizados">Servicios de AWS Utilizados</a></li>
        <li><a href="#arquitectura-aws">Arquitectura AWS (Resumen Visual)</a></li>
        <li><a href="#flujo-de-ejecución-del-proyecto">Flujo de Ejecución del Proyecto</a></li>
      </ul>
    </li>
    <li><a href="#crear-grupos-de-seguridad">Crear Grupos de Seguridad</a>
      <ul>
        <li><a href="#grupos-de-seguridad-a-crear">Grupos de Seguridad a Crear</a></li>
        <li><a href="#grupo-de-seguridad-para-el-balanceador-de-carga">Grupo de Seguridad para el Balanceador de Carga ELB</a></li>
        <li><a href="#grupo-de-seguridad-para-la-aplicación-tomcat">Grupo de Seguridad para la Aplicación (Tomcat)</a></li>
        <li><a href="#grupo-de-seguridad-para-el-backend">Grupo de Seguridad para el BackEnd</a></li>
        <li><a href="#par-de-claves-key-pair">Par de Claves (Key Pair)</a></li>
        <li><a href="#consejos-de-seguridad-y-buenas-prácticas">Consejos de Seguridad y Buenas Prácticas</a></li>
      </ul>
    </li>
    <li>
      <a href="#aprovisionamiento-de-4-instancias-ec2-en-aws-con-userdata">Aprovisionamiento de 4 Instancias EC2 en AWS con UserData</a>
      <ul>
        <li><a href="#objetivo">Objetivo</a></li>
        <li><a href="#estructura-de-grupos-de-seguridad">Estructura de Grupos de Seguridad</a></li>
        <li><a href="#paso-1-clonar-el-repositorio">Paso 1: Clonar el Repositorio</a></li>
        <li><a href="#paso-2-crear-instancia-mysql">Paso 2: Crear Instancia MySQL</a></li>
        <li><a href="#paso-3-crear-instancia-memcache">Paso 3: Crear Instancia Memcache</a></li>
        <li><a href="#paso-4-crear-instancia-rabbitmq">Paso 4: Crear Instancia RabbitMQ</a></li>
        <li><a href="#paso-5-crear-instancia-tomcat">Paso 5: Crear Instancia Tomcat</a></li>
        <li><a href="#buenas-prácticas">Buenas Prácticas</a></li>
      </ul>
    </li>
    <li><a href="#dns-y-configuración-de-route53">DNS y Configuración de Route53</a>
      <ul>
        <li><a href="#qué-se-está-configurando">¿Qué se está configurando?</a></li>
        <li><a href="#por-qué-no-usar-ips-en-los-archivos-de-configuración">¿Por qué no usar IPs en los archivos de configuración?</a></li>
        <li><a href="#solución-propuesta-usar-route-53-como-dns-privado">Solución propuesta: Usar Route 53 como DNS privado</a></li>
        <li><a href="#paso-a-paso">Paso a Paso</a></li>
        <li><a href="#verificación-de-resolución-dns">Verificación de Resolución DNS</a></li>
        <li><a href="#validaciones-finales">Validaciones Finales</a></li>
        <li><a href="#archivo-de-configuración">Archivo de Configuración</a></li>
        <li><a href="#conclusión">Conclusión</a></li>
      </ul>
    </li>
    <li><a href="#build-y-deploy-de-los-artifacts">Build y Deploy de los Artifacts</a>
      <ul>
        <li><a href="#despliegue-de-artefacto-con-maven-aws-s3-y-tomcat">Despliegue de artefacto con Maven, AWS S3 y Tomcat</a></li>
        <li><a href="#resumen-del-flujo">Resumen del Flujo</a></li>
        <li><a href="#requisitos-previos-en-el-equipo-local">Requisitos Previos en el Equipo Local</a></li>
        <li><a href="#configuración-en-aws">Configuración en AWS</a></li>
        <li><a href="#configuración-en-nuestra-máquina-local">Configuración en Nuestra Máquina Local</a></li>
        <li><a href="#construcción-del-artefacto">Construcción del Artefacto</a></li>
        <li><a href="#subir-artefacto-al-bucket-s3">Subir Artefacto al Bucket S3</a></li>
        <li><a href="#precaución-sobre-claves-iam">Precaución sobre Claves IAM</a></li>
        <li><a href="#descarga-y-despliegue-del-artefacto-en-la-instancia-tomcat">Descarga y Despliegue del Artefacto en la Instancia Tomcat</a></li>
        <li><a href="#instancia-tomcat">Instancia Tomcat</a></li>
        <li><a href="#descargar-el-artefacto-desde-s3">Descargar el Artefacto desde S3</a></li>
        <li><a href="#mover-el-archivo-al-directorio-de-despliegue-de-tomcat">Mover el Archivo al Directorio de Despliegue de Tomcat</a></li>
        <li><a href="#reiniciar-tomcat">Reiniciar Tomcat</a></li>
        <li><a href="#acceder-a-la-aplicación-desde-el-navegador">Acceder a la Aplicación desde el Navegador</a></li>
      </ul>
    </li>
    <li><a href="#load-balancer-y-dns">Load Balancer y DNS</a>
      <ul>
        <li><a href="#verificación-de-instancia-tomcat-antes-del-elb">Verificación de Instancia Tomcat antes del ELB</a></li>
        <li><a href="#creación-del-load-balancer-elb">Creación del Load Balancer (ELB)</a></li>
        <li><a href="#crear-el-target-group-grupo-de-destino">Crear el Target Group (Grupo de Destino)</a></li>
        <li><a href="#crear-el-application-load-balancer">Crear el Application Load Balancer</a></li>
        <li><a href="#configurar-oyentes">Configurar Oyentes</a></li>
        <li><a href="#configurar-dns-opcional">Configurar DNS (Opcional)</a></li>
        <li><a href="#verificar-funcionamiento-del-elb">Verificar Funcionamiento del ELB</a></li>
        <li><a href="#verificación-de-servicios-internos">Verificación de Servicios Internos</a></li>
      </ul>
    </li>
    <li><a href="#creación-de-un-autoscaling-group-asg-en-aws">Creación de un AutoScaling Group (ASG) en AWS</a>
      <ul>
        <li><a href="#componentes-necesarios">Componentes Necesarios</a></li>
        <li><a href="#paso-1-creamos-una-ami-desde-la-instancia">Paso 1: Creamos una AMI desde la Instancia</a></li>
        <li><a href="#paso-2-crear-launch-template">Paso 2: Crear Launch Template</a></li>
        <li><a href="#paso-3-crear-el-auto-scaling-group">Paso 3: Crear el Auto Scaling Group</a></li>
        <li><a href="#políticas-de-escalado-automático">Políticas de Escalado Automático</a></li>
        <li><a href="#protección-y-comportamiento-de-instancias">Protección y Comportamiento de Instancias</a></li>
        <li><a href="#notificaciones-sns">Notificaciones (SNS)</a></li>
        <li><a href="#opcional-activar-stickiness-en-target-group">Activar Stickiness en Target Group (Opcional)</a></li>
        <li><a href="#resumen-final">Resumen Final</a></li>
      </ul>
    </li>
    <li><a href="#validaciones-y-resumen-final">Validaciones y Resumen Final</a>
      <ul>
        <li><a href="#final-del-proyecto-lift-and-shift-con-auto-scaling-y-elb">Final del Proyecto – Lift and Shift con Auto Scaling y ELB</a></li>
        <li><a href="#estado-final-de-la-infraestructura">Estado Final de la Infraestructura</a></li>
        <li><a href="#ingreso-a-la-app">Ingreso a la App</a></li>
        <li><a href="#limpieza-de-recursos">Limpieza de Recursos</a></li>
        <li><a href="#resumen-del-flujo-arquitectónico">Resumen del Flujo Arquitectónico</a></li>
        <li><a href="#resumen-final-del-proyecto">Resumen Final del Proyecto</a></li>
      </ul>
    </li>
    <li><a href="#consideraciones-finales">Consideraciones Finales</a>
      <ul>
        <li><a href="#desafíos-enfrentados">Desafíos Enfrentados</a></li>
        <li><a href="#lecciones-aprendidas">Lecciones Aprendidas</a></li>
        <li><a href="#pros-y-contras-del-enfoque-lift-y-shift">Pros y Contras del Enfoque Lift & Shift</a></li>
        <li><a href="#próximos-pasos">Próximos Pasos</a></li>
      </ul>
    </li>
    <li><a href="#contacto">Contacto</a></li>
  </ol>
</details>
<br>




---

## 1.- Objetivo del proyecto

Migrar la aplicación vProfile desde un entorno local On-Premise con Vagrant a la nube de AWS usando la estrategia de Lift and Shift.
<p align="right">(<a href="#índice">Volver al inicio</a>)</p>

---

### Escenario Actual (On-Premise)

-   Aplicación con múltiples servicios:
    -   **Frontend**: Apache/Nginx
    -   **Aplicación**: Tomcat
    -   **Backend**: MySQL, RabbitMQ, Memcached
-   Todo en VMs locales con Vagrant
-   Infraestructura compleja y costosa de mantener
-   Escalado y automatización limitados

### Solución: Migración a AWS

#### ✅ Beneficios del cambio:

-   No hay inversión inicial
-   Modelo _Pay-as-you-go_
-   Infraestructura elástica
-   Automatización completa
-   Mayor agilidad y fiabilidad
<p align="right">(<a href="#índice">Volver al inicio</a>)</p>

---


### Servicios de AWS Utilizados


| 🧩 Servicio             | 📝 Uso                                                                 |
|------------------------|------------------------------------------------------------------------|
| **EC2**                | VMs para Tomcat, MySQL, Memcache y RabbitMQ                            |
| **ELB (ALB)**          | Balanceador de carga HTTPS (Reemplazo de NGINX)                        |
| **Auto Scaling Group** | Escalado dinámico de instancias Tomcat (Control de recursos y costos) |
| **S3**                 | Almacenamiento de artefactos de aplicación (Almacenamiento Compartido)|
| **Route 53**           | DNS privado para backends y público para usuarios                      |
| **ACM**                | Certificados SSL para HTTPS                                             |
| **IAM**                | Gestión de identidades y roles                                          |
| **EBS**                | Discos para las instancias EC2                                          |

---

### Arquitectura AWS

```yaml
Usuarios -> GoDaddy DNS -> ALB (HTTPS con ACM)
                              |
                          [Grupo Auto Scaling]
                              |
                        EC2 con Apache Tomcat
                              |
          -----------------------------------------
	         |             |                 |
	     Memcached     RabbitMQ           MySQL
	         \             |                /
      EC2 en zona privada con DNS interno (Route 53)


```
<p align="right">(<a href="#índice">Volver al inicio</a>)</p>

----------


### Flujo de Ejecución del Proyecto

1.  🔐 **Login AWS** + crear pares de claves SSH
2.  🔒 **Crear grupos de seguridad** para:
    -   ALB (HTTPS)
    -   Tomcat (8080 solo desde ALB)
    -   Backends (acceso desde Tomcat)
3.  💻 **Lanzar EC2s** con `user data` (Bash Scripts) para configurar servicios
4.  🧠 **Configurar Route 53**
    -   DNS privado para backends
    -   DNS público (GoDaddy) para el frontend
5.  🛠 **Compilar la aplicación localmente**
    -   Subir artefacto `.war` a **S3**
6.  ⬇️ **Descargar artefacto en EC2 Tomcat**
7.  🌐 **Configurar ELB (ALB) con HTTPS**
8.  🔁 **Crear Auto Scaling Group** para Tomcat
9.  ✅ Verificación

<p align="right">(<a href="#índice">Volver al inicio</a>)</p>


----------

## 2.- Crear Grupos de Seguridad 


### Grupos de Seguridad a Crear

#### Grupo de seguridad para el Balanceador de Carga

-   **Nombre:** `vprofile-elb-sg`
-   **Descripción:** Grupo de seguridad para el balanceador de carga del proyecto VProfile.
-   **Reglas de entrada:**
    -   `HTTP (puerto 80)` desde **cualquier IPv4** y **cualquier IPv6**
    -   `HTTPS (puerto 443)` desde **cualquier IPv4** y **cualquier IPv6**
-   **Reglas de salida:** _No modificar_ (permite todo por defecto)
  ![1 - Creando Grupo de Seguridad para el Balanceador de Carga (ELB)](https://github.com/user-attachments/assets/57ea2b16-43f1-49a1-9ae9-a257337cf70b)


📝 _Más adelante se eliminará la regla HTTP para dejar solo HTTPS._

#### Grupo de Seguridad para la Aplicación Tomcat

-   **Nombre:** `vprofile-app-sg`
-   **Descripción:** Grupo de seguridad para el servidor de aplicaciones Tomcat.
-   **Reglas de entrada:**
    -   `TCP 8080` desde **el grupo de seguridad del ELB**
    -   `SSH 22` desde **mi IP** (⚠️ Actualizar si cambia la IP)
-   **Reglas de salida:** _No modificar_

![2 - Creando Grupo de Seguridad para la Aplicación (Tomcat) y Acceso SSH](https://github.com/user-attachments/assets/7dac0de7-93bc-4884-a366-a024a937190f)


<p align="right">(<a href="#índice">Volver al inicio</a>)</p>


----------

### Grupo de Seguridad para el BackEnd

-   **Nombre:** `vprofile-backend-sg`
-   **Descripción:** Grupo de seguridad para MySQL, Memcache y RabbitMQ. Permite conexiones desde el servidor Tomcat.
-   **Reglas de entrada:**
    -   `MySQL 3306` desde **el SG de la aplicación**
    -   `Memcache 11211` desde **el SG de la aplicación**
    -   `RabbitMQ 5672` desde **el SG de la aplicación**
    -   `SSH 22` desde **mi IP** (Como es a modo de prueba, aqui debiesemos entrar con Session Manager de AWS System Manager (SSM)
-   **Regla adicional recomendada:**
    -   **Todo el tráfico desde sí mismo** (para permitir que las instancias backend interactúen entre sí)

![3 - Creando Grupo de Seguridad para el Back-End](https://github.com/user-attachments/assets/d0aec477-528a-4e3f-aab9-00f3aef04df4)


💡 _Esta última regla permite que memcache, RabbitMQ y MySQL se comuniquen internamente._

🧱 _Ejemplo de revisión en `application.properties` para confirmar los puertos:_

```
db.port=3306
memcached.port=11211
rabbitmq.port=5672
```

<p align="right">(<a href="#índice">Volver al inicio</a>)</p>


----------

### Par de Claves Key Pair

-   **Nombre:** `.key`
-   **Formato:** `.pem` 
-   **Uso:** Para acceder vía SSH a las instancias EC2

![KeyPair creado](https://github.com/user-attachments/assets/ed64cc4e-04da-4064-84fa-a97cc5e1a4d5)


----------

### Consejos de Seguridad y Buenas Prácticas

-   Nunca permitir **todo el tráfico** desde **cualquier lugar** a menos que sea estrictamente necesario (como el ELB).
-   **Evitar** usar "All traffic" entre grupos de seguridad si no se entienden bien las implicancias.
-   Asegúrate de **revisar y actualizar tu IP** para conexiones SSH cuando cambies de red.
-   Comprender **cómo fluye el tráfico** entre servicios te convierte en un mejor ingeniero DevOps.
-   Las capturas de pantalla aquí expuestas no contienen informnación sensible, además se realizó una limpieza total al finalizar el proyecto
  
<p align="right">(<a href="#índice">Volver al inicio</a>)</p>

![SG CREADOS](https://github.com/user-attachments/assets/84547aff-661f-4a24-9aa8-632dbc91d6a1)


----------

## 3.- Aprovisionamiento de 4 Instancias EC2 en AWS con UserData

### Objetivo

Configurar 4 instancias EC2 en AWS:

1.  **MySQL**
2.  **Memcached**
3.  **RabbitMQ**
4.  **Tomcat**

![2 - MySQL EC2 Instance Sumary](https://github.com/user-attachments/assets/ba28ee6a-2e94-48d8-9a1b-8ea59d4f41b4)


Cada una aprovisionada automáticamente con un script `userdata`.



<p align="right">(<a href="#índice">Volver al inicio</a>)</p>

----------

### Estructura de Grupos de Seguridad

-   **Backend Security Group**: Para las instancias de MySQL, Memcache y RabbitMQ.
-   **Application Security Group**: Para Tomcat.
-   **Load Balancer Security Group**: Se usará más adelante.

----------

## Paso 1: Clonar el Repositorio

Repositorio:

🔗 `Este mismo repositorio`

Pasos:

1.  Abrir VSCode.
2.  Ir a "Control de Código Fuente" → Clonar Repositorio.
3.  Pegar URL del repositorio.
4.  Elegir ubicación (por ejemplo, `D:\Downloads\vprofile-project`).
5.  Abrir carpeta `UserData`.

<p align="right">(<a href="#índice">Volver al inicio</a>)</p>

----------

## Paso 2: Crear Instancia MySQL

📁 Script: `mysql.sh`

📌 AMI: Amazon Linux 2023

📌 Grupo de seguridad: **backend**

📌 Tipo de instancia: T2 Micro o T3 Micro (free tier)

📌 Key Pair: `nombredelkey.key`

### 🔧 Script hace lo siguiente:

-   Instala `mariadb105-server`
-   Habilita e inicia el servicio MariaDB
-   Clona el código fuente
-   Ejecuta SQLs para:
    -   Borrar usuarios de prueba
    -   Crear la base de datos
    -   Crear el usuario `admin` y darle privilegios
    -   Importar el esquema

<p align="right">(<a href="#índice">Volver al inicio</a>)</p>

----------

## Paso 3: Crear Instancia Memcache

📁 Script: `memcache.sh`

📌 AMI: Amazon Linux 2023

📌 Grupo de seguridad: **backend**

📌 Tipo de instancia: T2/T3 Micro

📌 Key Pair: `key.key`

### 🔧 Script hace lo siguiente:

-   Instala `memcached`
-   Configura para escuchar en `xx.xx.xx.xx` (conexión remota)
-   Reinicia el servicio
-   Verifica con `netstat` si escucha en el puerto `11211`

<p align="right">(<a href="#índice">Volver al inicio</a>)</p>

----------

## Paso 4: Crear Instancia RabbitMQ

📁 Script: `rabbitmq.sh`

📌 AMI: Amazon Linux 2023

📌 Grupo de seguridad: **backend**

📌 Tipo de instancia: T2/T3 Micro

📌 Key Pair: `key.key`

### 🔧 Particularidades

-   Amazon Linux 2023 **no usa los mismos repositorios** que CentOS, así que se requieren pasos adicionales.
-   Se descarga un archivo `.repo` desde tu repositorio GitHub que contiene la configuración del repositorio Erlang y RabbitMQ.

### 🔧 Script hace lo siguiente:

1.  Agrega claves de firma de RabbitMQ.
2.  Descarga archivo `.repo` desde el repositorio GitHub y lo guarda en `/etc/yum.repos.d`.
3.  Instala dependencias (`socat`, `logrotate`, `erlang`, `rabbitmq-server`).
4.  Inicia y habilita `rabbitmq-server`.
5.  Crea usuario de prueba con permisos de administrador.

💡 Alternativa: Se podría escribir el contenido del archivo `.repo` directamente en el script usando `cat <<EOF`, pero éste se volvería más desordenado.

<p align="right">(<a href="#índice">Volver al inicio</a>)</p>


----------

## Paso 5: Crear Instancia Tomcat

📁 Script: `tomcat-ubuntu.sh`

📌 AMI: **Ubuntu 24.04**

📌 Grupo de seguridad: **application**

📌 Tipo de instancia: T2/T3 Micro

📌 Key Pair: `key.key`

### 🔧 Script hace lo siguiente:

-   Instala Tomcat 10 directamente desde los repositorios oficiales (simple con `apt install`).
-   No requiere descarga de binarios ni configuración compleja como en CentOS.

💡 Se eligió Ubuntu para demostrar lo sencillo que puede ser el aprovisionamiento en comparación con CentOS.

<p align="right">(<a href="#índice">Volver al inicio</a>)</p>

----------

## Buenas prácticas

✅ Se usaron **nombres coherentes** para las instancias, por ejemplo:

-   `vprofile-db-01`
-   `vprofile-mc-01`
-   `vprofile-mq-01`
-   `vprofile-app-01`

✅ Se agregaron **etiquetas (tags)**:

-   `Name`, `Project`, `Owner`, `Environment`

✅ Se etiquetaron también los **volúmenes** (no solo la instancia) para evitar eliminación accidental o consumo innecesario de espacio.

<p align="right">(<a href="#índice">Volver al inicio</a>)</p>


![7 - Instanacias EC2 Corriendo](https://github.com/user-attachments/assets/5f08420f-3847-41cc-88e6-b1b0f6ad0eef)


---
## 4.- DNS y configuración de Route53

Aprendí a configurar un DNS privado en AWS Route 53 para que las aplicaciones puedan resolver los nombres de servicios internos (como DB, Memcache, RabbitMQ) mediante nombres DNS en lugar de IPs estáticas, lo que es más escalable y profesional.

----------

### Qué se está configurando

Nuestra app `app01` necesita comunicarse con:

-   🗄️ `db01` (base de datos)
-   🧠 `mc01` (memcached)
-   🐰 `rmq01` (RabbitMQ)

En vez de usar direcciones IP directamente (que pueden cambiar), usaremos nombres de host para cada servicio.

----------

### Por qué no usar IPs en los archivos de configuración

Porque:

-   Las IPs privadas pueden cambiar si eliminas y recreas la instancia.
-   Modificar manualmente el código o los archivos `.properties` cada vez que cambia una IP no es muy práctico.
-   Usar nombres es más limpio y mantiene el código desacoplado de la infraestructura.

<p align="right">(<a href="#índice">Volver al inicio</a>)</p>

----------

### Solución propuesta: Usar Route 53 como DNS privado

### Paso a paso:

#### 1. Crear una zona privada alojada en Route 53

-   🧩 Nombre del dominio: `nombredominio.net`
-   Tipo: `Zona alojada privada`
-   Región: **N. Virginia (us-east-1)**
-   VPC: seleccionar la **VPC por defecto** 

![1 - Hosted Zone Route53](https://github.com/user-attachments/assets/f1057d56-b7f6-401a-b858-28a94879c007)
![2 - Hosted Zones for Route53](https://github.com/user-attachments/assets/330c125f-4fd9-41e5-8ec3-a329bdb4729f)



----------

#### 2. Crear registros de tipo `A` (nombre ➡ IP privada)

```md
| 🖥️ Nombre del host     | 📍 Dirección IP privada        | 📝 Notas                                                            |
|------------------------|-------------------------------|---------------------------------------------------------------------|
| `db01.vprofile.net`    | IP privada de instancia DB01   | Base de datos                                                       |
| `mc01.vprofile.net`    | IP privada de MC01             | Memcached                                                           |
| `rmq01.vprofile.net`   | IP privada de RMQ01            | RabbitMQ                                                            |
| `app01.vprofile.net`   | (opcional) IP privada de App01 | No es estrictamente necesario, pero puede ayudar                    |

```

🛑 **Importante**: Solo usar **IP privadas**, _no públicas_.
<p align="right">(<a href="#índice">Volver al inicio</a>)</p>

----------

### Verificación de resolución DNS

Desde la instancia `app01`:

```bash
ping -c 4 db01.vprofile.net
```

✔️ Si el nombre se resuelve a la IP privada correcta de `db01`, está bien configurado.

![3 - MariaDB Status](https://github.com/user-attachments/assets/a9473007-cc7a-477f-b175-29c26e57c8b2)


Repetir para `mc01` y `rmq01`.

----------

### Validaciones finales

-   Se verificaron que todas las IPs mostradas en los pings sean **privadas** (tipo `172.x.x.x` o `10.x.x.x`)
-   Me aseguré de que los nombres usados en los archivos de configuración coincidan con los que definidos en Route 53.
<p align="right">(<a href="#índice">Volver al inicio</a>)</p>

----------

### Archivo de configuración

```
db01.vprofile.in
mc01.vprofile.in
rmq01.vprofile.in
```

----------

### Conclusión

Se han reemplazado IPs duras por nombres DNS gestionados por Route 53. Esto:

-   Mejora la mantenibilidad
-   Facilita la escalabilidad
-   Es una **mejor práctica DevOps**

<p align="right">(<a href="#índice">Volver al inicio</a>)</p>

----------

## 5.- Build y deploy de los Artifacts

Este fragmento describe todo el flujo de trabajo para construir y desplegar un artefacto en una instancia EC2 con Tomcat, utilizando AWS S3 como repositorio temporal.

----------

### Despliegue de artefacto con Maven, AWS S3 y Tomcat

### Resumen del flujo

1.  Construcción del artefacto localmente usando Maven.
2.  Subida del artefacto a un bucket S3.
3.  Descarga y despliegue desde S3 en la instancia EC2 que ejecuta Tomcat.

----------

### Requisitos previos en el equipo local

-   ☕ **JDK** (Java 17)
-   🔨 **Maven** (versión 3.9.9)
-   ☁️ **AWS CLI**
-   💻 **VS Code** (o terminal equivalente)
-   🧑‍💻 **Credenciales IAM con privilegios de administrador (No ROOT)**

<p align="right">(<a href="#índice">Volver al inicio</a>)</p>

----------

### Configuración en AWS

#### 1️⃣ Crear un **bucket S3**

-   Ir al servicio **S3**.
-   Crear un bucket con un nombre único (por ejemplo: `v-profile-artifacts-1234`).
-   ✅ **Anotar el nombre** del bucket para uso posterior.
  
![1 - S3 Bucket](https://github.com/user-attachments/assets/0acd32f8-e8a3-4de2-8d66-f817973b19ac)


#### 2️⃣ Crear un **usuario IAM**

-   Ir al servicio **IAM** > Usuarios > Crear usuario.
-   Nombre sugerido: `s3-admin-profile`.
-   Adjuntar permisos directamente: `AmazonS3FullAccess`.
-   Generar claves de acceso.
-   Descargar el archivo `.csv` con la `access key` y `secret key`.
    
    ⚠️ **No perder este archivo**, no se puede volver a recuperar. En tal caso, se deberá crear un nuevo usuario con los permisos correspondientes.
    

#### 3️⃣ Crear un rol IAM para EC2

-   Ir a IAM > Roles > Crear rol.
-   Tipo de entidad: **AWS Service > EC2**.
-   Adjuntar permiso: `AmazonS3FullAccess`.
-   Nombre sugerido: `nombreservicio-admin-role`.
-   Aplicar el rol a la instancia EC2:
    -   Ir a **Instancias > Acciones > Seguridad > Modificar rol IAM**
    -   Seleccionar `nombreservicio-admin-role`.
   <p align="right">(<a href="#índice">Volver al inicio</a>)</p>

----------

### Configuración en nuestra máquina local

#### Verificar versiones

```bash
mvn -version    # Debería mostrar 3.9.9
java -version   # Debería mostrar Java 17
aws --version   # Debería estar instalado
```

#### Configurar credenciales AWS

```bash
aws configure
```

-   Ingresar `access key` y `secret key` del archivo CSV descargado.
-   Región: `us-east-1`
-   Formato: `json`

📝 Las credenciales se almacenan en:

-   `~/.aws/credentials`
-   `~/.aws/config`

> 💡 Se pueden editar con cualquier editor si se llegasen a cometer
> errores.

<p align="right">(<a href="#índice">Volver al inicio</a>)</p>

----------

### Construcción del artefacto

#### Verificar archivo `application.properties`

Ruta:

```
src/main/resources/application.properties


```

Asegurarse de tener valores correctos como:

```
db01.v-profile.in
mc01.v-profile.in
rmk01.v-profile.in


```

📌 Estos deben coincidir con los registros de Route 53 apuntando a las IP privadas correspondientes.

#### Construir con Maven

Estando en la raíz del proyecto:

```bash
mvn install


```

![3 - Build Artifact with maven](https://github.com/user-attachments/assets/cf9215f7-d30e-486e-8359-02890a75e7c6)

✔️ Se generará una carpeta `target/` que contendrá el artefacto `.war`, por ejemplo:

`v-profile-v2:var.war`

----------

### Subir artefacto al bucket S3

Una vez construido el `.war`, usar el siguiente comando:

```bash
aws s3 cp target/v-profile-v2:var.war s3://NOMBRE_DEL_BUCKET/


```

![5 - Subiendo artifact a S3 bucket](https://github.com/user-attachments/assets/1e478a6d-ab88-4fe3-9b3b-32da0fc6c7eb)


<p align="right">(<a href="#índice">Volver al inicio</a>)</p>


----------

### Precaución sobre claves IAM

> ⚠️ No subir las claves a repositorios públicos.
> 
> Bots en Internet escanean automáticamente repos públicos buscando claves para luego:
> 
> -   Minar criptomonedas con tu cuenta AWS.
> -   Generar grandes cargos en tu factura.
> -   Pedir rescates o comprometer tu infraestructura.

**Solución si accidentalmente expones tus claves:**

-   Borra las claves desde IAM.
-   Desactiva o elimina el usuario.
-   Eliminar las credenciales inmediatamente.

----------

### Descarga y despliegue del artefacto en la instancia Tomcat

✅ Ya construimos el artefacto con Maven, lo subimos al bucket S3, y configuramos correctamente `aws configure` en nuestro computador.

----------

### Instancia Tomcat

1.  **Nos conectamos a la instancia Tomcat (`app01`)**:
    
    Usa SSH para entrar:
    
    ```bash
    ssh -i "tu-clave.pem" nombre_de_usuario@<IP_PRIVADA_O_PÚBLICA>
    
    
    ```
    <p align="right">(<a href="#índice">Volver al inicio</a>)</p>

----------

### Descargar el artefacto desde S3

Una vez dentro de la instancia, vamos a descargar el `.war`:

```bash
aws s3 cp s3://<NOMBRE_DEL_BUCKET>/<nombre-del-archivo>.war .


```

🔸 Este comando descargará el archivo `.war` en el directorio actual.

Ejemplo:

```bash
aws s3 cp s3://v-profile-artifacts-bucket/v-profile-v2.war .


```

![7 - Copiando artifact desde S3 a carpeta raiz de Tomcat](https://github.com/user-attachments/assets/19d5e0ed-c48e-4045-9f8b-47fb3985761c)


----------

### Mover el archivo al directorio de despliegue de Tomcat

Después de descargarlo, necesitaremos moverlo al directorio `webapps/` de Tomcat:

```bash
sudo mv v-profile-v2.war /opt/tomcat/webapps/


```

![7 - Copiando artifact desde S3 a carpeta raiz de Tomcat](https://github.com/user-attachments/assets/d90e00cb-c0d2-47f9-849d-2da44aa660fa)


🔧 Nos aseguramos de que Tomcat esté instalado en `/opt/tomcat/`. Si no, ajustamos la ruta.


<p align="right">(<a href="#índice">Volver al inicio</a>)</p>

----------

### Reiniciar Tomcat

Reiniciamos el servicio de Tomcat para desplegar el `.war`:

```bash
sudo systemctl restart tomcat


```

🧪 Verificamos que el servicio esté corriendo:

```bash
sudo systemctl status tomcat


```

----------

### Acceder a la aplicación desde el navegador

Navegamos a:

```
http://<dominio-o-ip>:8080/v-profile-v2


```

🟢 Si todo está bien, deberíamos ver la aplicación desplegada y funcionando correctamente.
<p align="right">(<a href="#índice">Volver al inicio</a>)</p>

----------

## 6.- Load Balancer y DNS

Esta sección trata sobre la **verificación de servicios, configuración del Load Balancer (ELB), HTTPS, DNS y pruebas de conectividad**.

----------

### Verificación de instancia Tomcat antes del ELB

1.  ✅ **Acceso a la instancia `app01` vía navegador**:
    
    -   Verificamos que Tomcat responde en el puerto `8080`.
    -   Revisamos que el **grupo de seguridad de la app** permite tráfico desde el grupo de seguridad del Load Balancer.
    -   Alternativamente, añadimos una regla temporal `8080` desde "mi IP" para test.
2.  🌐 **Abrir IP pública de la instancia en el navegador**:
    
    ```
    http://<IP_PUBLICA_APP01>:8080
    
    
    ```
    
    -   Deberíamos ver la pantalla de inicio de sesión de Tomcat.
    -   Solo es una prueba. La conexión real será a través del ELB.
   <p align="right">(<a href="#índice">Volver al inicio</a>)</p>

----------

## Creación del Load Balancer (ELB)

### Crear el Target Group (Grupo de destino)

-   Tipo: **Instancias**
-   Nombre sugerido: `profile-app-TG`
-   Protocolo: `HTTP`
-   Puerto: `8080` (donde corre Tomcat)

  ![2 - Crear el Target Group (Grupo de destino)](https://github.com/user-attachments/assets/084c3429-f75f-459f-8a73-4b972c706a47)


🔍 **Configuración de chequeo de salud**:

-   Ruta: `/` (por defecto)
-   Anular puerto de salud: usa `8080` (no el 80 por defecto)

➡️ **Selecciona la instancia app01**

-   Asegúrate de usar el puerto `8080`
-   Añadir como objetivo ➝ Crear el grupo


<p align="right">(<a href="#índice">Volver al inicio</a>)</p>

----------

### Crear el Application Load Balancer

-   Tipo: HTTP y HTTPS (Application Load Balancer)
-   Nombre sugerido: `profile-last-ELB`
-   Selecciona todas las zonas de disponibilidad en la VPC
-   Asigna el grupo de seguridad del ELB (no uses el por defecto)

![4 - Creando AppLoadBalancer (ALB)](https://github.com/user-attachments/assets/101c4cae-2e75-49aa-b23b-1a4d35cf3414)
![4 1 - LoadBalancer Creado](https://github.com/user-attachments/assets/9ac08a9b-1279-465d-b95e-bc10cfca4ef5)


### Configurar oyentes:

-   **Puerto 80** ➝ Redirige al Target Group creado
-   (Opcional) **Puerto 443** ➝ HTTPS (requiere certificado SSL)

🔐 Si deseas HTTPS:

-   Debes tener un **certificado en AWS ACM**
-   Este debe coincidir con un dominio (por ejemplo, `midominio.com`)
-   Si no lo tienes, puedes omitir el listener 443

➡️ Finaliza y crea el ELB

----------

### Configurar DNS (opcional)

👉 Como se compró un dominio en GoDaddy para éste laboratorio, se configuró esa plataforma de la siguiente manera:

1.  Vamos a nuestra cuenta de GoDaddy
2.  Seleccionamos nuestro dominio y vamos a la sección DNS
3.  Añadimos un **registro CNAME** con los siguientes datos:
    -   **Nombre**: ej. `vprofile-app`
    -   **Valor**: nombre DNS del Load Balancer (`.elb.amazonaws.com`)
4.  Guardamos el registro

🎯 Si no se cuenta con un dominio, de todas formas se puede acceder con el **DNS del ELB directamente**, pero sin conexión HTTPS.
<p align="right">(<a href="#índice">Volver al inicio</a>)</p>

----------

### Verificar funcionamiento del ELB

1.  Vamos al **Target Group** y revisamos estado:
    
    -   Si aparece **no saludable**, revisamos:
        -   Que el servicio Tomcat esté corriendo en la instancia
        -   Que el **grupo de seguridad de Tomcat** permita tráfico 8080 desde el ELB
        -   Que el **chequeo de salud esté configurado en el puerto 8080**
        -   Esperamos unos minutos y actualizamos
2.  Accedemos vía ELB:
    
    -   HTTP (sin dominio):
        
        ```
        http://<DNS_ELB>
      
        
        ```

![4 5 - Acceso web desde http puerto 80](https://github.com/user-attachments/assets/4d3cfcde-b55e-4f4e-aaee-2e22e1787619)


    -   HTTPS (si tenemos dominio y certificado):
        
        ```
        <https://vprofile-app.nombredominio.com>
        
        
        ```

 ![5 - Acceso web https](https://github.com/user-attachments/assets/434d5417-2c90-45b7-8183-8174ef6c2bb7)

    
    ✅ El certificado debe decir “válido” y mostrar que viene de **Amazon** (emitido por ACM).
    <p align="right">(<a href="#índice">Volver al inicio</a>)</p>
    

----------

### Verificación de servicios internos

🔑 **Inicio de sesión:**

-   Usuario: `nombredeusuario`
-   Contraseña: la configurada previamente

📊 Verificaciones:

-   ✅ **Base de datos**: si podemos iniciar sesión, la conexión a DB funciona
-   ✅ **RabbitMQ**: comprobamos si se genera la cola correctamente
-   ✅ **Memcached**:
    -   Hacemos clic en un usuario
    -   Primera vez: “datos desde la base de datos”
    -   Segunda vez: “datos desde la caché”
    <p align="right">(<a href="#índice">Volver al inicio</a>)</p>

----------

## 7.- Creación de un AutoScaling Group (ASG) en AWS


### Componentes Necesarios

Para configurar un Auto Scaling Group (ASG), se requieren tres elementos principales:

1.  **AMI (Amazon Machine Image)**
    
    Imagen personalizada basada en nuestra instancia existente.
    
2.  **Launch Template (Plantilla de lanzamiento)**
    
    Define cómo debe lanzarse una nueva instancia (tipo de instancia, grupo de seguridad, clave SSH, etc.).
    
3.  **Auto Scaling Group (ASG)**
    
    El grupo que administra el escalado hacia arriba o hacia abajo según las métricas definidas.
    

----------

### Paso 1: Creamos una AMI desde la Instancia

1.  Seleccionamos nuestra instancia (por ejemplo, `App-01`).
2.  Hacemos clic en **Actions > Image and templates > Create image**.
3.  Asignamos un nombre a la imagen (por ejemplo, `lets-app-ami`) y una descripción.
4.  Hacemos clic en **Create Image**.
5.  Podemos revisar el progreso en la sección **AMIs**, donde el estado estará en "pending" hasta que se complete.
<p align="right">(<a href="#índice">Volver al inicio</a>)</p>

![1 - Creando una AMI desde la instancia app01](https://github.com/user-attachments/assets/a301da72-c147-4569-9dbb-69d4bc96f596)


----------

### Paso 2: Crear Launch Template

Una vez que la AMI esté lista:

1.  Vamos a **Launch Templates** y seleccionamos **Create launch template**.
2.  Asignamos un nombre, por ejemplo: `lets-app-template`.
3.  En el campo AMI, seleccionamos nuestra imagen personalizada desde **My AMIs**.
4.  Seleccionamos el tipo de instancia (por ejemplo, `t2.micro` o `t3.micro` si es elegible para capa gratuita).
5.  Seleccionamos la **key pair**.
6.  En **Security group**, seleccionamos el grupo existente asociado a nuestra aplicación (ej. `App-sg`).
7.  Agregamos **etiquetas (tags)**:
    -   `Name`: `v-profile-app-01`
    -   `Project`: `v-profile` (Nos aseguramos de marcar que aplique también a volúmenes).
8.  En **Advanced details** podemos asignar un **IAM role** (opcional en esta etapa, pero importante en entornos reales o CI/CD).
9.  Hacemos clic en **Create launch template**.
<p align="right">(<a href="#índice">Volver al inicio</a>)</p> 

----------

### Paso 3: Crear el Auto Scaling Group

1.  Vamos a **Auto Scaling Groups > Create Auto Scaling Group**.
2.  Nombramos nuestra ASG (ej. `lets-app-asg`).
3.  Seleccionamos la **launch template** creada anteriormente.
4.  Elegimos la **VPC** y **zonas de disponibilidad** (podemos seleccionar todas si deseamos alta disponibilidad).
5.  Adjuntamos el grupo al **load balancer existente**:
    -   Como necesitamos un **Application Load Balancer (ALB)**, seleccionamos el **target group** ya creado.
6.  En **Health checks**:
    -   Activamos **ELB health check** para que el balanceador revise el estado real del servicio (no solo si la instancia está "up").
7.  Configuramos el tamaño del grupo:
    -   **Desired capacity**: 1 (o 2 si queremos empezar con alta disponibilidad).
    -   **Min capacity**: 1
    -   **Max capacity**: 4

![2 - ASG creado](https://github.com/user-attachments/assets/9f5dcb33-5918-4a3f-806b-4bbf186f058e)


----------

### Políticas de Escalado Automático

1.  Utilizamos políticas **target tracking** (seguimiento de objetivos).
2.  Usaremos **CPU Utilization** como métrica:
    -   Por ejemplo, escala hacia fuera si el promedio de CPU supera el **50%**.
    -   Escala hacia dentro si baja del 50%.
3.  Podemos optar también por solo escalar hacia fuera, desactivando el escalado hacia dentro si preferimos manejarlo manualmente.
<p align="right">(<a href="#índice">Volver al inicio</a>)</p>

----------

### Protección y Comportamiento de Instancias

-   **Instance scale-in protection**:
    -   Si está activada, la instancia **no será terminada automáticamente** incluso si está marcada como no saludable.
    -   Es útil para diagnósticos y troubleshooting.
    -   Sin embargo, el ASG no enviará tráfico a esa instancia.

----------

### Notificaciones (SNS)

-   Si configuramos previamente un **SNS Topic** Habilitaremos notificaciones para eventos, tales como:

        -   `Instance launch`
        -   `Instance termination`
        -   `Launch failure`
        -   `Termination failure`
   -   Esto hará que AWS nos envíe correos electrónicos según lo definido arriba.
   <p align="right">(<a href="#índice">Volver al inicio</a>)</p>

----------

### (Opcional) Activar Stickiness en Target Group

1.  Vamos a **Target Groups > Tu Target Group > Attributes > Edit**.
2.  Activamos la opción **Stickiness**.
3.  Esto asegura que un usuario sea dirigido siempre a la misma instancia durante una sesión (útil para sesiones de usuario o caché).
4.  Podemos definir la duración de la stickiness (ej. 1 día).

----------

### Resumen Final

Con esto:

-   Tenemos una AMI personalizada.
-   Tenemos una plantilla de lanzamiento reutilizable.
-   El ASG manejará el escalado según la carga real del sistema.
-   El balanceador de carga distribuirá y verificará el estado de las instancias activas.
-   Podremos recibir notificaciones y proteger instancias según nuestras necesidades.
<p align="right">(<a href="#índice">Volver al inicio</a>)</p>

----------

## 8.- Validaciones y Resumen final

### Final del Proyecto Lift and Shift con Auto Scaling y ELB

----------

### Estado final de la infraestructura

-   🔁 Solo nos debe quedar **una instancia activa** gestionada por el **Auto Scaling Group (ASG)**.
-   🩺 La instancia debe estar en estado **saludable**.
-   🛑 La instancia original `app-01` debe estar **terminada**.
-   🧩 Toda instancia debe estar **gestionada por el ASG**.
-   🍯 **Stickiness** debe estar activado **solo si tenemos múltiples instancias**.
-   🌐 Si no contamos con un dominio personalizado:
    -   Podemos acceder usando el **DNS del Load Balancer**.
-   🔗 Si contamos con un dominio (ej. en GoDaddy):
    -   Nos aseguramos de haber creado el **registro CNAME** apuntando al DNS del ELB.
 <p align="right">(<a href="#índice">Volver al inicio</a>)</p>

----------

### Ingreso a la app

-   Usamos:
    -   **Usuario:** `nombre_de_usuario`
    -   **Contraseña:** `contraseña`
-   Verificamos que servicios como `RabbitMQ`, `Memcached` funcionen.
-   Si al actualizar no pasa nada ➡️ probablemente el _Stickiness esté desactivado_.
<p align="right">(<a href="#índice">Volver al inicio</a>)</p>

----------

### Limpieza de recursos

> ⚠️ Muy importante para evitar cargos innecesarios ya que la idea principal es aprovechar al máximo el free tier que nos proporciona AWS

**La eliminación de recursos creados debe ser en el siguiente orden:**

1.  **Eliminar Auto Scaling Group**
    -   🗑️ Si no lo hacemos, la instancia será recreada una y otra vez.
    -   Eliminará automáticamente la instancia `app01`.
2.  **Eliminar Load Balancer**
    -   Nos aseguramos de confirmar la eliminación.
3.  **Eliminar Target Group (opcional)**
    -   No genera cargos en este caso, pero procedemos a limpiar.
4.  **Verificar y eliminar volúmenes (EBS)**
    -   Solo si son volúmenes _no asociados_ a instancias.
5.  **Eliminar zona hospedada en Route 53**
    -   Primero eliminamos todos los **registros DNS** creados.
    -   Luego eliminamos la **zona hospedada privada**.
6.  **Eliminar Security Groups y pares de claves**
    -   Si ya no los necesitamos, podemos eliminarlos.
7.  **Eliminar AMI e instantáneas**
    -   No se puede "eliminar" una AMI directamente, solo se puede:
        -   🔧 **Deregister** (desregistrar) la AMI.
        -   🧹 Luego borrar la **snapshot asociada**.
8.  **Eliminar bucket S3**
    -   Primero vaciamos el bucket (`permanently delete`).
    -   Luego eliminamos el bucket.

----------

### Resumen del flujo arquitectónico

1.  🔐 Creamos los Security Groups + pares de claves.
2.  🚀 Lanzamos 4 instancias EC2 con _user-data scripts_.
3.  🛠️ Construimos y desplegamos el artefacto desde un bucket S3.
4.  ⚖️ Configuramos un Load Balancer con certificado HTTPS (ACM).
5.  🔁 Movimos la instancia a un Auto Scaling Group.
6.  🌐 Implementamos entradas DNS privadas en Route 53 para comunicación interna.
<p align="right">(<a href="#índice">Volver al inicio</a>)</p>

----
### Resumen final del proyecto


Durante el proceso, se aplicaron conceptos clave como la configuración de grupos de seguridad, claves SSH, registros DNS personalizados, y la implementación de mecanismos de **"stickiness"** (afinidad de sesión) en el balanceador de carga.

Este proyecto no sólo representa una migración técnica, sino también una oportunidad para afianzar competencias clave en DevOps como: aprovisionamiento de infraestructura, automatización, despliegue continuo, monitoreo básico y gestión de servicios en la nube.

Finalmente, se realizó un proceso de **limpieza controlada** del entorno, eliminando recursos no persistentes como grupos de Auto Scaling, instancias, snapshots, AMIs, registros DNS, buckets de S3 y otros componentes temporales, siguiendo buenas prácticas para evitar costos innecesarios.

<p align="right">(<a href="#índice">Volver al inicio</a>)</p>


----------

## 9.- Consideraciones Finales

### Desafíos Enfrentados

-   📌 **Sincronización entre componentes**: Al trabajar con múltiples instancias y servicios distribuidos, asegurar la correcta comunicación entre backend y frontend implicó una configuración precisa de DNS privados.
    
-   🛠️ **Pegajosidad en el Load Balancer**: Hubo que habilitar la “stickiness” para mantener la sesión del usuario en aplicaciones con estados, como RabbitMQ y Memcached.
    
-   🔐 **Configuración HTTPS**: Integrar certificados de ACM y habilitar HTTPS en el ALB presentó retos de compatibilidad inicial.
    
-   🔄 **Persistencia de datos**: Dado que el modelo Lift & Shift no implica rediseñar la aplicación, fue necesario asegurar almacenamiento persistente con EBS sin modificar la lógica del backend.
<p align="right">(<a href="#índice">Volver al inicio</a>)</p>

---

### Lecciones Aprendidas

-   💡 La nube permite abstraer y automatizar procesos que en entornos on-premise requieren gran esfuerzo manual.
    
-   🧠 Comprender el modelo de responsabilidad compartida y los servicios de AWS es fundamental para mantener entornos seguros y escalables.
    
-   📝 Documentar cada paso y entender el flujo general ayuda a replicar y explicar la arquitectura en contextos profesionales.
 <p align="right">(<a href="#índice">Volver al inicio</a>)</p>

---

### Pros y Contras del Enfoque Lift y Shift

| ✅ Pros | ⚠️ Contras |
|--------|------------|
| Rápida migración sin refactorizar el código | No aprovecha completamente los servicios cloud-native |
| Requiere menor esfuerzo de desarrollo | Puede heredar ineficiencias del entorno legacy |
| Fácil de entender y mantener en fases tempranas | Menor automatización en comparación con soluciones modernas (containers, serverless) |
| Ideal para validar conceptos de arquitectura cloud | Costos potencialmente más altos si no se optimiza el uso de recursos |


___


### Próximos Pasos

En la siguiente fase de mi roadmap, la aplicación será **rediseñada**  para adoptar una arquitectura más moderna, utilizando **contenedores Docker, orquestación con Kubernetes**, e integración con **CI/CD pipelines**  para despliegues automatizados. Lo que me preparará y me dará las bases para avanzar hacia herramientas más avanzadas y actuales.

<p align="right">(<a href="#índice">Volver al inicio</a>)</p>

----------

📌 _Repositorio creado como parte de mi formación DevOps. Listo para escalar hacia soluciones más robustas con herramientas como Ansible, Terraform y CI/CD._

----------

🚀 **Si, has llegado hasta aquí, ¡Gracias por leer!. Si te interesa ver el código o probarlo, clona el repo y comienza tu propia aventura y si tienes alguna consulta o duda, enviame un mensaje privado por linkedin**
<p align="right">(<a href="#índice">Volver al inicio</a>)</p>

---

## 10.- Contacto

Enlace a Linkedin [![LinkedIn](https://img.shields.io/badge/-LinkedIn-0077B5?logo=linkedin)](https://www.linkedin.com/in/diegorojasv/)<br>
💬 💡**TIP**:  Haz clic con el botón derecho o Ctrl+clic para abrir en una pestaña nueva 😉
