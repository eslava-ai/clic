
# IDEA DEL USUARIO

Quiero automatizar el acceso a la página que te adjunto para realizar automaticamente el check en un checkbox de ciertas horas.

Estudia la tecnología empleada en la página. Primero habría que automatizar el login con mi usuario y contraseña. Luego habría que entrar en algunas secciones específicas y hacer check de forma automática según ciertas condiciones.

Analiza diferentes tipos de soluciones para llevar a cabo el proyecto.

Url de login (fichero login.html adjunto):
https://acces.edu.gva.es/sso/login.xhtml?callbackUrl=https://itaca3.edu.gva.es/escriptori/

Acceso intermedio al listado de horarios (horario.html):
https://docent.edu.gva.es/md-front/www/#centre/46018761/horari

Página destino para hacer clic (clase.html):
https://docent.edu.gva.es/md-front/www/#centre/46018761/grup/3417988457,3419608272,/tasques/diaries/perSessio/sessio/1586246674;1589822533,1589822534,;2025-09-24;13:05/desdeHorari

Elemento para hacer check:
<div class="imc-check">
<input type="checkbox" tabindex="-1"> <span></span> 
</div>

## Respuesta Google AI Studio

¡Hola! Claro que sí. He analizado la estructura y tecnología de las páginas que has proporcionado. A continuación, te presento un análisis completo y varias soluciones para automatizar el proceso que necesitas.

### 1. Análisis de la Tecnología Empleada

Tras estudiar los ficheros HTML, podemos identificar las siguientes tecnologías y patrones de diseño:

*   **Página de Login (`login.html`):**
    *   Utiliza **JavaServer Faces (JSF)**, una tecnología de backend de Java. Esto se deduce por elementos como la URL (`.xhtml`) y los IDs autogenerados con dos puntos (ej: `form1:j_username`).
    *   Es un formulario HTML estándar que envía los datos de usuario y contraseña mediante una petición POST. La gestión del estado de la sesión es crucial aquí.

*   **Páginas de la Aplicación (`horario.html`, `clase.html`):**
    *   Se trata de una **Single Page Application (SPA)**. Esto significa que, una vez dentro, la página no se recarga por completo. El contenido se carga dinámicamente usando JavaScript.
    *   La navegación se gestiona mediante **AJAX** y se apoya en fragmentos de URL (el `#`). Los enlaces con `javascript:;` y atributos como `data-href` confirman que las acciones del usuario desencadenan funciones de JavaScript en lugar de cargar páginas nuevas.
    *   Utiliza la librería **jQuery** para la manipulación del DOM y la gestión de eventos, lo cual es muy común en este tipo de aplicaciones.
    *   La presencia de `cordova.js` sugiere que la aplicación también está diseñada para funcionar como una aplicación móvil híbrida, pero para la automatización web, la trataremos como una SPA estándar.

**Conclusión del análisis:** Debido a que la aplicación es una SPA que depende intensamente de JavaScript, no se puede automatizar con herramientas simples que solo descargan HTML. Se necesita una solución que pueda **ejecutar JavaScript en un navegador real** para interactuar con los elementos dinámicos de la página.

---

### 2. Estrategia de Automatización (Paso a Paso)

El script de automatización deberá seguir estos pasos lógicos:

1.  **Iniciar un navegador:** Abrir una instancia de un navegador como Chrome o Firefox.
2.  **Navegar a la página de login:** Ir a la URL de `login.html`.
3.  **Realizar el login:**
    *   Localizar el campo de usuario (ID: `form1:j_username`) e introducir el DNI/NIE.
    *   Localizar el campo de contraseña (ID: `form1:j_password`) e introducir la contraseña.
    *   Hacer clic en el botón "Entrar" (ej: `input[name="form1:j_id78"]`).
    *   Esperar a que la autenticación se complete y se cargue la página principal.
4.  **Navegar al horario:**
    *   Una vez dentro, hacer clic en el enlace "Horario de sesiones" (selector CSS: `a.imc-centre-horari`).
    *   Esperar a que se cargue dinámicamente la vista del horario (`horario.html`).
5.  **Seleccionar la clase correcta:**
    *   El script deberá tener una lógica para identificar la sesión de clase correcta. Por ejemplo, si quieres marcar la asistencia de la clase de las 13:05 del día actual, deberá:
        *   Buscar el día correcto (ej: `h2.imc-dia` con el `data-data` correspondiente a la fecha).
        *   Dentro de ese día, buscar la sesión con la hora correcta (ej: `li` con `data-desde="13:05"`).
        *   Hacer clic en el enlace (`<a>`) de esa sesión.
6.  **Realizar el "Check" en la casilla:**
    *   Una vez en la página de la clase (`clase.html`), el script deberá localizar la casilla deseada. Basado en tu petición, el objetivo es marcar "Sin faltas" para toda la sesión.
    *   El elemento a clicar no es el `input` directamente, sino la `label` que lo contiene. El selector CSS sería: `li.imc-sessio-sense-faltes label`.
    *   Ejecutar un clic sobre ese elemento para marcar la casilla.
7.  **Cerrar el navegador:** Finalizar el proceso.

---

### 3. Soluciones Propuestas para el Proyecto

Aquí te presento tres enfoques diferentes, desde el más robusto y profesional hasta el más sencillo.

#### Solución 1: Librerías de Automatización de Navegadores (Recomendada)

Esta es la solución más potente y fiable. Consiste en escribir un script (por ejemplo, en Python o JavaScript) que controla un navegador real.

*   **Herramientas:**
    *   **Playwright:** Es la opción más moderna y potente. Permite controlar Chrome, Firefox y WebKit con una API muy intuitiva. Es muy rápido y robusto para esperar elementos dinámicos.
    *   **Selenium:** Es la herramienta más veterana y con una comunidad enorme. Es muy versátil y compatible con muchos lenguajes de programación.

*   **Lenguaje de Programación:**
    *   **Python:** Es ideal por su sintaxis sencilla y las potentes librerías que lo acompañan. La combinación de **Python + Playwright** es excelente para este proyecto.

*   **Ventajas:**
    *   **Totalmente automático:** Puede ejecutarse sin intervención humana, incluso en un servidor y de forma programada (ej. todos los días a una hora específica).
    *   **Manejo de SPAs:** Emula a la perfección la interacción de un usuario, ejecutando todo el JavaScript necesario.
    *   **Flexible:** Permite añadir lógica compleja, como "marcar solo las clases de los miércoles y viernes" o "no marcar si el día es festivo".

*   **Desventajas:**
    *   Requiere conocimientos básicos de programación.
    *   Necesita una configuración inicial (instalar Python, Playwright y los navegadores).

#### Solución 2: Scripts de Navegador (User Scripts)

Esta solución implica escribir un pequeño script de JavaScript que se ejecuta directamente en tu navegador cada vez que visitas la página.

*   **Herramientas:**
    *   **Tampermonkey / Greasemonkey:** Son extensiones de navegador que te permiten instalar y gestionar estos "user scripts".

*   **Cómo funcionaría:**
    1.  Tú inicias sesión manualmente.
    2.  Al navegar a la página de horarios, el script se activaría.
    3.  Podrías añadir un botón personalizado como "Marcar asistencia del día", que al pulsarlo, el script buscaría las clases del día, navegaría a cada una y marcaría la casilla "Sin faltas" automáticamente.

*   **Ventajas:**
    *   **Sencillo de implementar:** Si ya estás en la página, automatiza las tareas repetitivas.
    *   **No requiere un entorno de programación externo:** Solo la extensión del navegador.

*   **Desventajas:**
    *   **No es 100% automático:** Requiere que tú inicies sesión y navegues a la página. No se puede programar para que se ejecute solo.
    *   La automatización del login es más compleja y menos segura de gestionar de esta manera.

#### Solución 3: Herramientas de Automatización RPA (Low-Code/No-Code)

Estas herramientas permiten "grabar" tus acciones en una página web y luego reproducirlas. Son ideales para usuarios sin experiencia en programación.

*   **Herramientas:**
    *   **UI.Vision RPA:** Una extensión de navegador muy popular para grabar y reproducir tareas.
    *   **Axiom.ai / Browserflow:** Plataformas visuales para crear flujos de automatización en el navegador.

*   **Ventajas:**
    *   **Muy fácil de usar:** La mayoría de las acciones se configuran con clics y menús desplegables.
    *   **Rápida puesta en marcha:** Puedes tener un prototipo funcionando en minutos.

*   **Desventajas:**
    *   **Menos robusto:** Puede fallar si la página cambia su estructura o si los tiempos de carga varían.
    *   **Flexibilidad limitada:** Añadir lógica condicional compleja puede ser difícil o imposible.
    *   Muchas de estas herramientas tienen planes de pago para funcionalidades avanzadas o ejecuciones programadas.

### Recomendación Final

Para un proyecto que se busca que sea fiable, desatendido y programable, la **Solución 1 con Python y Playwright es sin duda la mejor opción**. Te dará el control total sobre el flujo, manejará sin problemas la complejidad de la aplicación y te permitirá adaptarte a futuros cambios.

Si solo buscas una ayuda para agilizar un proceso que ya haces manualmente, un **script de Tampermonkey (Solución 2)** sería una alternativa muy rápida y eficaz.



## Respuesta de ChatGPT

He revisado los tres ficheros que has adjuntado (`login.html`, `horario.html`, `clase.html`) y con ello se puede trazar una estrategia para automatizar todo el flujo (login → acceso a horarios → marcar checkbox en clase).

---

### 🔍 Análisis de la tecnología usada

1. **Login (`login.html`)**

   * Página basada en **JavaServer Faces (JSF)** con **RichFaces**.
   * Los campos relevantes son:

     * `id="j_username"` (usuario).
     * `id="input_pass"` (contraseña).
   * El formulario envía la autenticación al SSO (`https://acces.edu.gva.es/sso/login.xhtml`) y redirige con un `callbackUrl` a Itaca.
   * Usa JavaScript propio (`imc-nausica--app.js`, RichFaces framework).
     👉 Esto complica un poco un login “solo con requests”, porque es probable que haya tokens ocultos (`javax.faces.ViewState`) que cambian en cada carga.

2. **Listado de horarios (`horario.html`)**

   * Es una SPA (Single Page Application) con **JavaScript y jQuery**.
   * El contenido dinámico se carga vía AJAX.
   * La estructura está envuelta en `<section>` con clases tipo `imc-modul`, y los datos se inyectan dentro de listas o tablas vacías (`<!-- contingut dinamic -->`).

3. **Página de clase (`clase.html`)**

   * También es parte de la misma SPA.
   * Los “checkbox” que quieres marcar están en `<div class="imc-check"> <input type="checkbox"> <span></span> </div>`.
   * El marcado es **dinámico**, y el “click” seguramente desencadena un evento JS que se comunica con el backend vía AJAX.

---

### ⚙️ Posibles enfoques de automatización

#### 1. **Automatización con navegador real (recomendado)**

* Usar **Selenium** (o **Playwright**, más moderno y rápido).
* Permite:

  * Simular el login rellenando usuario y contraseña.
  * Navegar hasta el horario.
  * Abrir la sesión/clase concreta.
  * Marcar el checkbox ejecutando un `element.click()`.
* Ventaja: No tienes que preocuparte por tokens dinámicos, JSF, AJAX ni estructuras internas.
* Inconveniente: Más pesado, requiere tener un navegador (Chrome/Firefox/Edge) o ejecutarlo en modo headless.

#### 2. **Automatización con peticiones HTTP**

* Usar **requests (Python)** + análisis de cookies y tokens (`javax.faces.ViewState`).
* Se haría:

  * POST al login con `j_username`, `j_password`, `javax.faces.ViewState`.
  * Guardar cookies de sesión.
  * Investigar endpoints AJAX que cargan los horarios y que reciben los cambios de los checkbox.
* Ventaja: Más ligero, sin navegador.
* Inconveniente: Bastante complejo porque la aplicación usa JSF + AJAX dinámico. Hay que sniffear con las herramientas de red del navegador para replicar los requests.

#### 3. **Automatización híbrida**

* Usar **Playwright** para el login y navegación inicial.
* Una vez autenticado, **extraer cookies/tokens de sesión** y pasar a requests para los clicks en lote.
* Ventaja: Lo mejor de ambos mundos (menos dependencia del navegador).
* Inconveniente: Mayor complejidad inicial.

#### 4. **Usuarioscript o extensión de navegador**

* Crear un **userscript (Greasemonkey/Tampermonkey)** que se ejecute en el navegador cada vez que entres.
* El script podría:

  * Detectar la hora/grupo.
  * Auto-marcar los checkboxes si se cumple una condición.
* Ventaja: Muy rápido de usar, sin necesidad de backend.
* Inconveniente: Solo funciona mientras tengas la pestaña abierta y con usuario logueado.

---

### 📌 Recomendación práctica

Dada la tecnología (JSF + SPA con AJAX):

* Lo más **seguro y mantenible** es **Playwright** o **Selenium**.
* Puedes programar un bot en Python que:

  1. Abra la página de login.
  2. Meta usuario/contraseña.
  3. Espere a la redirección al horario.
  4. Navegue al grupo/sesión según fecha/hora.
  5. Ejecute `.click()` en el checkbox correspondiente.

Después, si quieres escalar y hacerlo sin navegador, se podría investigar el tráfico AJAX para replicarlo con requests.

---

¿Quieres que te prepare un **ejemplo inicial en Python con Playwright** que haga login y localice un checkbox de prueba, para que luego lo adaptemos a tus condiciones?
