**¿Cómo crear imagenes docker en Github?**
-----------------------------------

El registro de contenedores de GitHub (GitHub Container Registry, GHCR) ofrece una forma conveniente de publicar imágenes Docker directamente desde tu repositorio de GitHub. En este guía, exploraremos el proceso de publicar imágenes Docker en GHCR y automatizarlo utilizando GitHub Actions.

**¿Qué es GitHub Container Registry?**
-----------------------------------

GitHub Container Registry permite a los desarrolladores almacenar y compartir imágenes de contenedores Docker directamente dentro de sus repositorios de GitHub. Esta característica permite una integración sin problemas de aplicaciones empaquetadas en contenedores con el ecosistema de GitHub.

**Paso 1: Creando un Token de Acceso Personal**
------------------------------------------

Para interactuar con GHCR, necesitamos crear un token de acceso personal con las permisiones adecuadas. Siga estos pasos:

* Ve a tu cuenta de GitHub y navega a **Configuración** > **Configuración del desarrollador** > **Tokens de acceso personal**.
* Crea un nuevo token con las permisiones necesarias para loguear con Docker, leer/escritura imágenes y eliminar imágenes.

**Paso 2: Iniciando sesión en GHCR con Docker**
----------------------------------------------

Antes de empujar imágenes Docker a GHCR, debemos iniciarnos usando Docker con nuestro token de acceso personal. Aquí hay cómo hacerlo:

* Abre tu terminal.
* Utiliza el comando `docker login` con tu usuario de GitHub y tu token de acceso personal.
* Especifique la URL del registro de contenedores GHCR (`ghcr.io`).

**Ejemplo de Comando Docker Login**
```bash
docker login --username mert2m --password your_personal_access_token ghcr.io
```
**Paso 3: Construyendo y subiendo Imágenes Docker**
------------------------------------------------

Ahora, construya su imagen Docker y empéjalos a GHCR:

* Navega hasta tu directorio de proyecto que contiene el archivo `Dockerfile`.
* Utiliza el comando `docker build` para construir la imagen.
* Etiquete la imagen con la URL del repositorio de GHCR y la versión.
* Empuje la imagen utilizando el comando `docker push`.

**Ejemplo de Comandos Docker Build y subiendo**
```bash
docker build . -t ghcr.io/mert2m/actions-runner-controller-ghcr:latest
docker push ghcr.io/mert2m/actions-runner-controller-ghcr:latest
```
**Paso 4: Automatizando la Publicación de Imágenes con GitHub Actions**
-----------------------------------------------------------------

Para automatizar el proceso de publicar imágenes Docker cuando se produzcan cambios, configuraremos un flujo de trabajo de GitHub Actions:

* Crea un nuevo archivo de YAML en el directorio `.github/workflows`.
* Define el flujo para desencadenarse sobre eventos de push.
* Configura el flujo para iniciar sesión con GHCR usando el token de acceso personal.
* Especifique los pasos para construir y empujar la imagen.

**Ejemplo de Flujo de Trabajo de GitHub Actions**
```yml
name: Docker Image CI for GHCR

on:
  push:

jobs:
  build_and_publish:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Build and push the image
        run : |
          docker login --username mert2m --password ${{ secrets.GH_PAT }} ghcr.io
          docker build . --tag ghcr.io/mert2m/actions-runner-controller:latest
          docker push ghcr.io/mert2m/actions-runner-controller:latest
```