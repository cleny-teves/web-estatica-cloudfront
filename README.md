# Web Estática Segura con S3 y CloudFront (Desplegada con IaC)

Este repositorio contiene la plantilla de Infraestructura como Código (IaC) y el contenido HTML para desplegar una página web estática segura y de alto rendimiento en AWS utilizando **CloudFormation puro**.

## 🏛️ Diagrama de la Arquitectura

La arquitectura implementada sigue las mejores prácticas de seguridad, utilizando un bucket S3 privado y Amazon CloudFront con Origin Access Control (OAC) para la entrega de contenido.

![Arquitectura Web Estática](arquitectura-web-estatica)

1. El lector solicita el sitio web en www.example.com.
2. Si el objeto solicitado está en caché, CloudFront devuelve el objeto de su caché al lector.
3. Si el objeto no está en la caché de CloudFront, CloudFront solicita el objeto desde el origen (un bucket de S3).
4. S3 devuelve el objeto a CloudFront.
5. CloudFront almacena el objeto en caché.
6. Los objetos se devuelven al espectador. Las solicitudes posteriores del objeto que llegan a la misma ubicación de borde de CloudFront se sirven desde la memoria caché de CloudFront.

## 🛠️ Tecnologías Utilizadas

* **AWS CloudFormation:** Para definir y automatizar el despliegue de toda la infraestructura.
* **Amazon S3:** Como almacenamiento seguro y privado para los archivos del sitio web (`index.html`, `error.html`).
* **Amazon CloudFront:** Para distribuir el contenido globalmente, proporcionar caché y asegurar el acceso vía HTTPS.
* **AWS S3 Bucket Policy & CloudFront OAC:** Para garantizar que solo CloudFront pueda acceder al contenido del bucket S3.

## 🚀 Cómo Desplegar este Proyecto

Para desplegar esta infraestructura en tu propia cuenta de AWS, necesitas tener la **AWS CLI** instalada y configurada.

1.  **Clona este repositorio** o descarga los archivos (`template.yaml`, `index.html`, `error.html`) en una carpeta en tu computadora.
2.  Abre tu terminal y navega hasta la carpeta del proyecto.
3.  Ejecuta el comando de despliegue de CloudFormation:
    ```bash
    aws cloudformation deploy --template-file template.yaml --stack-name mi-web-estatica --capabilities CAPABILITY_IAM
    ```
4.  Una vez finalizado el despliegue, ejecuta el siguiente comando para subir el contenido HTML al bucket S3 recién creado:
    ```bash
    BUCKET_NAME=$(aws cloudformation describe-stacks --stack-name mi-web-estatica --query "Stacks[0].Outputs[?OutputKey=='S3BucketName'].OutputValue" --output text) && aws s3 cp . s3://$BUCKET_NAME/ --recursive --exclude "*" --include "*.html"
    ```
5.  Finalmente, obtén la URL de tu sitio web ejecutando:
    ```bash
    aws cloudformation describe-stacks --stack-name mi-web-estatica --query "Stacks[0].Outputs[?OutputKey=='WebsiteURL'].OutputValue" --output text
    ```
    ¡Abre esa URL en tu navegador para ver el resultado!

## 🗑️ Limpieza

Para eliminar todos los recursos creados por este proyecto y evitar costos, ejecuta el siguiente comando:
```bash
aws cloudformation delete-stack --stack-name mi-web-estatica
