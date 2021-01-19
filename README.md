# Criptografía asimétrica con OpenSSL

Desde siempre ha existido la preocupación por asegurar la privacidad en las comunicaciones, intentado garantizar que un mensaje sólo pudiera ser leído por el receptor al que iba destinado. Imaginar el reto que puede suponer mantener este nivel de privacidad en el mundo actual en el que vivimos en donde se hace un uso masivo e intensivo de las comunicaciones digitales a través de internet. La criptografía juega un papel fundamental para alcanzar este objetivo, ayudándonos a establecer comunicaciones seguras mediante la aplicación de algoritmos y protocolos de seguridad.

En este artículo aprenderemos a realizar algunas operaciones criptográficas elementales como generar un par de claves, cifrar y descifrar mensajes y verificar que los datos de un mensaje no han sido alterados. Para ello vamos a utilizar RSA, como algoritmo de cifrado asimétrico y OpenSSL.

### RSA
RSA es un algoritmo de cifrado asimétrico, por lo que trabaja a partir de dos claves, una pública y otra privada. Se trata de uno los algoritmos más utilizados en la actualidad. De hecho, la mayor parte de los sitios web hoy corren sobre SSL/TLS, y permiten la autenticación mediante cifrado asimétrico basado en RSA.

RSA sirve para cifrar y descifrar información, y por ello también provee servicios de autenticidad y de integridad, mediante lo que se conoce cono Infraestructura de clave pública (PKI)

### OpenSSL
OpenSSL es un kit de herramientas para los protocolos de Seguridad de la capa de transporte (TLS) y de Capa de sockets seguros (SSL). También es una biblioteca de criptografía de propósito general. OpenSSL tiene una licencia de estilo Apache, lo que significa que eres libre de obtenerla y usarla con fines comerciales y no comerciales. Puedes descargar el código fuente de esta librería en su página oficial [openssl](www.openssl.org).

Para comprobar la versión de OpenSSL que tienes instalada, abre la línea de comandos e introduce:


```sh
openssl version
```
Y si quieres consultar todos los comandos que puedes ejecutar con OpenSSL:
```sh
openssl help
```

## Cómo generar de un par de claves

Antes de cifrar o descifrar mensajes necesitamos generar un par de claves. Este par está compuesto por una clave pública, que podemos poner a disposición de todos, y una clave privada,que debemos mantener en secreto. Cuando alguien quiera enviarnos algún mensaje y asegurarse de que nadie más pueda verlo, puede usar nuestra clave pública para cifrarlo.

Lo primero de todo será generar una clave privada RSA:
```sh
openssl genrsa –out private_key.pem 2048 
```
Generar privada en formato PEM con contraseña
Siempre que utilicemos la clave openssl nos pedirá la contraseña.
```sh
openssl genrsa -aes128 -out private_key.pem 2048
```
A continuación, generaremos la clave pública a partir de la clave privada que acabamos de generar:
```sh
openssl rsa -in private_key.pem -outform PEM -pubout -out public_key.pem
```
## Cifrar y descifrar mensajes
Una vez hemos generado nuestro par de claves, vamos a utilizarlas para cifrar un mensaje con la clave pública y descifrarlo con la privada. Para ello, vamos a crear un archivo de texto llamado “secret.txt” con un texto que sólo podrá ser leído por un usuario autorizado.

```sh
echo 'Este es un mensaje secreto' > secret.txt
```
A continuación, introduce el siguiente comando para cifrar el archivo con la clave pública:
```sh
openssl rsautl -encrypt -pubin -inkey public_key.pem -in secret.txt -out secret.enc
```
Este comando ha generado el archivo «secret.enc«, que es una versión cifrada de «secret.txt«. Como podrás imaginar, si intentamos abrir el archivo que acabamos de generar, veremos que la información aparece distorsionada:

Para descifrar el fichero “secret.enc” y recuperar el contenido original, utilizaremos nuestra clave privada

```sh
openssl rsautl -decrypt -inkey private_key.pem -in secret.enc
```

## Hash y firma digital
Lo siguiente que haremos será crear un hash de un mensaje y luego firmarlo digitalmente. Para ello utilizaremos el algoritmo hash SHA256. A continuación, verificaremos dicha firma para asegurarnos que el mensaje no se modificado ni falsificado. Si se modificara el mensaje, el hash sería diferente al firmado y por tanto la verificación no sería correcta.

Para crear un hash del mensaje introduce el siguiente comando:
```sh
openssl dgst -sha256 -sign private_key.pem -out secret.txt.sha256 secret.txt
```
Este comando generará un archivo llamado «secret.txt.sha256» con nuestra clave privada, que incluye el hash del archivo de texto secreto. Con este archivo, cualquiera puede usar la clave pública y el hash para verificar que el archivo no se haya modificado desde que se creó y cifró. Para realizar esta verificación, utiliza el siguiente comando:
```sh
openssl dgst -sha256 -verify public_key.pem -signature secret.txt.sha256 secret.txt
```
Se mostrará el siguiente resultado indicando que la verificación se realizó correctamente y que ningún tercero malicioso modificó el archivo


Si se muestra otro resultado, quiere decir que se modificó el contenido del archivo, y es probable que ya no sea seguro.


## Referencias
- [rsa-como-funciona-este-algoritmo](https://juncotic.com/rsa-como-funciona-este-algoritmo/)
- [openssl](https://www.openssl.org/)
- [wiki.openssl](https://wiki.openssl.org/)
