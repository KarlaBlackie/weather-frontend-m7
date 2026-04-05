# App de clima y salud emocional - Módulo 7
App de clima y bienestar

Esta aplicación es una SPA(Single page application) desarrollada con Vue 3, enfocada en  proporcionar información climática en tiempo real combinada con mensajes de bienestar emocional.En esta versión se ha implementado un sistema de autenticación, estado global con Vuex y rutas protegidas.

# Características principales:

** Sistema de usuarios:** inicio de sesión simulado que personaliza la experiencia.
** Estado global(vuex):** Manejo centralizado de la sesión de usuario y su lista de ciudades favoritas.
** Rutas protegidas:** Uso de navigation guards, para asegurar que solo los usuarios logueados accedan a sus favoritos.
** Consumo de API:** datos climáticos reales obtenidos de Open-Meteo.
** UI responsiva:** diseño moderno utilizando Bootstrap 5.

# Tecnologías utilizadas

* vue.js 3(composition API)
* vuex 4 (gestión de estado)
* vue router 4 (navegación)
* bootstrap 5 (estilos y layout)
* axios (peticiones HTTP)

# Rutas de la aplicación

| Ruta | Descripción | Acceso |

| `/` | ** Home **: Buscador de ciudades y consejos. | Público |
| `/login` | ** Login **: Formulario de acceso. | Público |
| `/acerca` | ** Acerca **: Información del proyecto. | Público |
| `/favoritos` | ** Favoritos **: bCiudades guardadas por el usuario. | Privado ( Requiere login) |


# Autenticación y personalización

1. **Login:** el usuario ingresa su nombre. Al validar, vuex guarda el objeto user con un array de favoritos vacío y cambia el estado isAuthenticated a true.
2. **Persistencia:** mientras la sesión esté activa, el nombre del usuario aparece en la interfaz.
3. ** Favoritos:** el usuario puede marcafr ciudades con un corazón. Esta información se guarda en el store de vuex y se refleja instantáneamente en la vista favoritos.
4. **Seguridad:** si un usuario no autenticado intenta escribir favoritos en la URL, el router.beforeEach lo redirige automáticamente al login.



# Instalaci+pn y ejecución
1.Clonar el repositorio
bash
git clone https://github.com/KarlaBlackie/weather-frontend-m7

2.Entrar a la carpeta del proyecto


3.Instalar dependencias
npm install

4.Ejecutar el servidor
npm run dev


# Repositorio
https://github.com/KarlaBlackie/weather-frontend-m7


# Autor
Karla Irene Jara Mena.
Desarrollado como parte del bootcamp de Frontend.-

  
