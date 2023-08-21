**Objetivo:** Contar con una guía para decidir entre la creación de una directiva, componente, servicio o pipe para una solución.

### Componente

- Crea una parte visual reutilizable y dinámica de tu interfaz de usuario.
- Contiene plantillas HTML, hojas de estilos y lógica.
- Puede incluir directivas, pipes, otros componentes y servicios para proporcionar funcionalidad y datos dinámicos.

### Directiva

- Manipula el DOM, aplicar estilos, escuchar eventos o crear comportamientos personalizados en un elemento HTML.
- Puede implementarse por atributos y recibir datos se utilizan para extender el comportamiento de los elementos HTML.
- Encapsular comportamientos y reutilizarlos en diferentes partes de la aplicación.

### Pipe

- Transforma la visualización de datos en una plantilla HTML.
- Son útiles para filtrar y manipular datos antes de mostrarlos en la interfaz de usuario.

### Servicio

- Separa la lógica de negocio, la comunicación con API, la gestión de datos y otras tareas no relacionadas con la interfaz de usuario.
- Se utilizan para compartir datos y lógica entre componentes, lo que promueve la reutilización.
- Pueden tener dependencias para acceder a otras funcionalidades, como HttpClient para llamadas a API.
