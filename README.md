# Carga de imágenes con Cloudinary

Este ejemplo integra **Cloudinary** como servicio de almacenamiento de imágenes. La aplicación permite seleccionar una imagen desde el dispositivo, subirla a Cloudinary y obtener una URL pública para mostrarla o guardarla posteriormente en una base de datos como Cloud Firestore.

## 1. Crear una cuenta en Cloudinary

Ingresar a [Cloudinary](https://cloudinary.com/) y crear una cuenta gratuita. Para este ejemplo no es necesario asociar una tarjeta.

## 2. Obtener el Cloud name

En el panel principal de Cloudinary, localizar la sección **Product Environment** y copiar el valor de **Cloud name**.

El código sería similar a:

```javascript
cloudName: "hea9gd9f"
```

El `cloudName` identifica el espacio de almacenamiento de la cuenta. No es una contraseña ni una clave privada.

## 3. Crear un Upload Preset

El Upload Preset permite establecer las condiciones con las que se subirán los archivos.

1. Entrar en **Settings**.
2. Seleccionar **Upload**.
3. Abrir la pestaña **Upload presets**.
4. Presionar **Add upload preset**.
5. Completar los siguientes datos:

```text
Upload preset name: usuarios_web
Signing mode: Unsigned
Asset folder: usuarios
```

6. Guardar los cambios.

El modo `Unsigned` permite realizar la carga directamente desde el navegador sin utilizar una clave secreta. Es apropiado para este ejemplo educativo. Para reducir usos no deseados, conviene limitar desde el preset el tamaño y los formatos permitidos.

## 4. Incorporar el widget de Cloudinary

Agregar el script oficial antes del código JavaScript de la aplicación:

```html
<script src="https://upload-widget.cloudinary.com/latest/global/all.js"></script>
```

## 5. Crear los elementos HTML

Se necesita un botón para abrir el selector, una imagen para la vista previa y un párrafo para mostrar la URL obtenida:

```html
<h1>Cargar imagen</h1>

<button id="btnImagen">Seleccionar imagen</button>

<br><br>

<img
    id="vistaPrevia"
    width="200"
    alt="Imagen cargada"
    style="display: none;"
>

<p id="resultado"></p>

<script src="https://upload-widget.cloudinary.com/latest/global/all.js"></script>
```

## 6. Configurar la carga desde JavaScript

Se crea el widget indicando el `cloudName` y el nombre del Upload Preset:

```javascript
const btnImagen = document.getElementById("btnImagen");
const vistaPrevia = document.getElementById("vistaPrevia");
const resultado = document.getElementById("resultado");

let imagenUrl = "";

const widget = cloudinary.createUploadWidget(
    {
        cloudName: "hea9gd9f",
        uploadPreset: "usuarios_web",
        sources: ["local", "camera"],
        multiple: false,
        resourceType: "image",
        maxFileSize: 3000000,
        clientAllowedFormats: ["jpg", "jpeg", "png", "webp"]
    },
    (error, respuesta) => {
        if (error) {
            console.error("Error al subir la imagen:", error);
            resultado.textContent = "No se pudo subir la imagen.";
            return;
        }

        if (respuesta.event === "success") {
            imagenUrl = respuesta.info.secure_url;

            vistaPrevia.src = imagenUrl;
            vistaPrevia.style.display = "block";
            resultado.textContent = imagenUrl;

            console.log("URL de la imagen:", imagenUrl);
        }
    }
);

btnImagen.addEventListener("click", () => {
    widget.open();
});
```

## 7. Probar el funcionamiento

1. Abrir la página mediante **Live Server**.
2. Presionar **Seleccionar imagen**.
3. Elegir una imagen del dispositivo o utilizar la cámara.
4. Confirmar la carga.
5. Verificar que la imagen y su URL aparezcan en la página.
6. Comprobar que el archivo también figure en la sección **Assets** de Cloudinary.

La URL generada tendrá una estructura similar a esta:

```text
https://res.cloudinary.com/hea9gd9f/image/upload/...
```

## Resultado

Al finalizar, la variable `imagenUrl` contiene el enlace público de la imagen:

```javascript
console.log(imagenUrl);
```

Esta URL puede mostrarse en una etiqueta `<img>`:

```html
<img src="URL_OBTENIDA" alt="Imagen del usuario">
```

También puede guardarse posteriormente en Cloud Firestore junto con la información de un usuario o producto:

```javascript
{
    nombre: "Usuario de ejemplo",
    correo: "usuario@correo.com",
    imagenUrl: "https://res.cloudinary.com/..."
}
```
