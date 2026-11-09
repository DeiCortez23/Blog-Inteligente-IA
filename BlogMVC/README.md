# BlogMVC – Plataforma de gestión de contenido con Inteligencia Artificial

Aplicación web desarrollada con **ASP.NET Core MVC y C#** que permite crear y administrar publicaciones, gestionar usuarios y comentarios, y utilizar servicios de inteligencia artificial para generar contenido, imágenes y analizar opiniones de los lectores.

El proyecto integra autenticación, autorización por roles, persistencia de información con Entity Framework Core y conexión con la API de OpenAI. También incorpora un proceso en segundo plano para analizar los comentarios publicados y clasificarlos según su sentimiento.

## Características principales

### Gestión de publicaciones
- Crear, consultar y editar publicaciones.
- Agregar imágenes de portada desde el equipo.
- Generar imágenes de portada mediante inteligencia artificial.
- Generar el contenido de los artículos a partir de un título.
- Editar el contenido utilizando un editor de texto enriquecido.
- Eliminar y restaurar publicaciones mediante eliminación lógica.
- Consultar publicaciones y visualizar su contenido.

### Usuarios y control de acceso
- Registro de nuevos usuarios.
- Inicio y cierre de sesión.
- Autenticación mediante ASP.NET Core Identity.
- Administración de usuarios.
- Asignación y modificación de roles.
- Control de acceso a las operaciones según los permisos del usuario.

### Comentarios
- Publicar comentarios en las entradas.
- Eliminar comentarios propios.
- Permitir que usuarios autorizados administren comentarios de otros usuarios.
- Aplicar eliminación lógica para conservar los registros.
- Relacionar cada comentario con su autor y publicación.

### Integración con Inteligencia Artificial

**Generación de artículos**

Se utiliza la API de OpenAI para generar contenido en formato HTML a partir del título de una publicación. La respuesta se recibe mediante streaming, permitiendo visualizar el texto conforme se genera y decidir si se incorpora al editor.

**Generación de imágenes**

La aplicación permite crear imágenes de portada relacionadas con el tema de cada publicación. Las imágenes generadas se pueden previsualizar antes de guardar la entrada.

**Análisis de sentimientos**

Los comentarios se procesan mediante la API de OpenAI para asignarles una puntuación del 1 al 5 según el sentimiento expresado.

El procesamiento utiliza Batch API de OpenAI para enviar grupos de solicitudes y consultar posteriormente sus resultados.

### Procesamiento en segundo plano
- Servicio alojado mediante `BackgroundService`.
- Detección de comentarios pendientes de análisis.
- Envío de solicitudes agrupadas a OpenAI.
- Seguimiento del estado de los lotes.
- Actualización de las puntuaciones en SQL Server.
- Consulta de publicaciones que contienen comentarios negativos.

## Tecnologías utilizadas

| Tecnología | Uso |
|---|---|
| C# | Lenguaje principal |
| ASP.NET Core MVC | Desarrollo de la aplicación web |
| Entity Framework Core | Acceso y persistencia de datos |
| SQL Server | Base de datos |
| ASP.NET Core Identity | Autenticación y usuarios |
| OpenAI API | Generación de texto, imágenes y análisis de sentimientos |
| JavaScript | Interacciones del lado del cliente |
| HTML5 y CSS3 | Estructura y presentación |
| Bootstrap | Diseño de interfaces |
| Quill.js | Editor de texto enriquecido |
| Blazor Server | Componente de listado de publicaciones |
| Azure Blob Storage | Alternativa para almacenamiento de imágenes |
| Git y GitHub | Control de versiones |

## Organización del proyecto

El código está organizado por responsabilidades para separar la lógica de negocio, el acceso a datos, las entidades y los servicios externos.

```text
BlogMVC/
│
├── Configuraciones/
│   └── ConfiguracionesIA.cs
│
├── Controllers/
│   ├── ComentariosController.cs
│   ├── EntradasController.cs
│   ├── HomeController.cs
│   └── UsuariosController.cs
│
├── Datos/
│   └── ApplicationDbContext.cs
│
├── Entidades/
│   ├── Comentario.cs
│   ├── Entrada.cs
│   ├── Lote.cs
│   └── Usuario.cs
│
├── Jobs/
│   └── AnalisisSentimientosRecurrente.cs
│
├── Models/
│   ├── ComentarioViewModel.cs
│   ├── EntradaCrearViewModel.cs
│   ├── EntradaDetalleViewModel.cs
│   ├── EntradaEditarViewModel.cs
│   ├── LoginViewModel.cs
│   ├── RegistroViewModel.cs
│   └── ...
│
├── Servicios/
│   ├── AlmacenadorArchivosAzure.cs
│   ├── AlmacenadorArchivosLocal.cs
│   ├── AnalisisSentimientosOpenAI.cs
│   ├── ServicioChatOpenAI.cs
│   ├── ServicioImagenesOpenAI.cs
│   └── Interfaces/
│
├── Utilidades/
│   ├── HttpContextExtensions.cs
│   └── Seeding.cs
│
├── Views/
│   ├── Comentarios/
│   ├── Entradas/
│   ├── Home/
│   ├── Usuarios/
│   └── Shared/
│
├── wwwroot/
│   ├── css/
│   ├── js/
│   └── img/
│
├── appsettings.json
├── Program.cs
└── BlogMVC.csproj
```

## Base de datos

El proyecto utiliza SQL Server y Entity Framework Core para administrar la información.

Las principales entidades son:

| Entidad | Descripción |
|---|---|
| Usuario | Información de los usuarios registrados. |
| Entrada | Publicaciones creadas dentro del blog. |
| Comentario | Comentarios asociados a las publicaciones. |
| Lote | Información de los procesos Batch enviados a OpenAI. |

Las relaciones permiten identificar al autor de cada publicación, relacionar los comentarios con sus respectivas entradas y mantener el historial de los procesos de análisis.

También se implementan filtros globales de Entity Framework Core para gestionar los registros eliminados lógicamente.

## Configuración e instalación

### Requisitos

- Visual Studio 2022 o superior.
- .NET SDK compatible con el proyecto.
- SQL Server.
- SQL Server Management Studio (SSMS).
- Una API Key de OpenAI para utilizar las funcionalidades de inteligencia artificial.

### 1. Clonar el repositorio

```bash
git clone https://github.com/DeiCortez23/BlogMVC.git
```

### 2. Abrir el proyecto

Abre la solución desde Visual Studio y restaura los paquetes NuGet.

### 3. Configurar la conexión a SQL Server

En `appsettings.json`, configura la cadena de conexión correspondiente a tu instancia de SQL Server mediante la clave `DefaultConnection`.

No utilices credenciales personales ni publiques cadenas de conexión reales.

### 4. Configurar OpenAI

El proyecto utiliza una sección de configuración llamada `ConfiguracionesIA`, que contiene los siguientes valores:

- `ModeloTexto`: modelo utilizado para generar artículos.
- `ModeloImagenes`: modelo utilizado para generar imágenes.
- `ModeloSentimientos`: modelo utilizado para analizar comentarios.
- `LlaveOpenAI`: clave de acceso a la API.

Configura estos valores mediante User Secrets o variables de entorno. No subas tu API Key a GitHub.

### 5. Configurar almacenamiento de imágenes

La aplicación incluye dos implementaciones del servicio de almacenamiento:

- `AlmacenadorArchivosLocal`: guarda las imágenes en el directorio de archivos estáticos de la aplicación.
- `AlmacenadorArchivosAzure`: permite almacenar imágenes utilizando Azure Blob Storage.

La implementación registrada actualmente en el contenedor de dependencias es el almacenamiento local.

### 6. Crear la base de datos

Desde la consola del Administrador de paquetes de Visual Studio, ejecuta:

```powershell
Update-Database
```

Esto aplica las migraciones existentes para crear o actualizar la base de datos.

### 7. Ejecutar la aplicación

Inicia el proyecto desde Visual Studio o mediante:

```bash
dotnet run
```

## Funcionamiento de la integración con OpenAI

La integración con OpenAI está separada en servicios para mantener independientes las distintas funcionalidades.

**Generación de contenido**

1. El usuario escribe el título de una publicación.
2. La aplicación envía el título al servicio de OpenAI.
3. El modelo genera el contenido del artículo en HTML.
4. La respuesta se transmite progresivamente al navegador.
5. El usuario puede revisar el resultado e incorporarlo al editor Quill.

**Generación de imágenes**

1. El usuario proporciona el título de la publicación.
2. Se construye una descripción a partir del tema.
3. OpenAI genera la imagen.
4. La aplicación recibe los bytes de la imagen.
5. El usuario puede visualizarla antes de guardar la publicación.

**Análisis de sentimientos**

1. El servicio identifica comentarios que todavía no tienen una puntuación.
2. Se construyen solicitudes en formato JSONL.
3. Se carga el archivo mediante la API de OpenAI.
4. Se crea un Batch para procesar las solicitudes.
5. El servicio consulta periódicamente el estado de los lotes.
6. Cuando finaliza el procesamiento, recupera los resultados.
7. Las puntuaciones se actualizan en la base de datos.

## Seguridad y permisos

Se utiliza ASP.NET Core Identity para administrar las cuentas y controlar el acceso a las funcionalidades.

La aplicación contempla tres roles principales:

- **Administrador:** acceso a la administración de usuarios y funcionalidades administrativas.
- **CRUD de entradas:** permisos para crear, editar y administrar publicaciones.
- **Borrar comentarios:** permiso para eliminar comentarios de otros usuarios.

Además, se aplican restricciones mediante atributos de autorización en los controladores y validaciones de los permisos antes de ejecutar determinadas operaciones.

## Consideraciones

- Las funcionalidades de inteligencia artificial requieren una API Key válida de OpenAI.
- El uso de OpenAI puede generar costos dependiendo de los modelos y operaciones utilizados.
- La generación de imágenes y el análisis por lotes dependen de la disponibilidad y los límites de la API.
- La aplicación necesita una instancia de SQL Server correctamente configurada.
- Las credenciales y cadenas de conexión deben mantenerse fuera del repositorio.

## Autor

**David Sánchez Cortez**

Ingeniería en Tecnologías de la Información y Comunicaciones.

- GitHub: [DeiCortez23](https://github.com/DeiCortez23)
- LinkedIn: [David Cortez](https://linkedin.com/in/david-cortez-2650b63aa)

---

Proyecto desarrollado como parte de mi práctica y aprendizaje en el desarrollo de aplicaciones web con C#, ASP.NET Core, SQL Server e integración de servicios de inteligencia artificial.