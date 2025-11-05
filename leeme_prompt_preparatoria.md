¡Absolutamente\! Aquí tienes la **lista de procedimientos y tareas** detalladas para configurar tu proyecto de preparatoria con **Python, Django, y VS Code**, enfocándote inicialmente en el modelo `Profesor`.

Dado que la lista es extensa y cubre muchos pasos de configuración, te sugiero un diagrama de la estructura final del proyecto para una mejor comprensión visual de las carpetas y archivos.

-----

## 🛠️ Procedimientos de Configuración Inicial

Estos pasos deben realizarse en la terminal de tu sistema operativo o en la terminal integrada de VS Code.

| \# | Procedimiento | Comando(s) o Acción |
| :--- | :--- | :--- |
| **1** | **Crear la carpeta del proyecto** (`UIII_Preparatoria_0656`) | `mkdir UIII_Preparatoria_0656` |
| **2** | **Abrir VS Code sobre la carpeta** | `cd UIII_Preparatoria_0656` luego `code .` (si tienes el PATH configurado) o abrir manualmente. |
| **3** | **Abrir la terminal en VS Code** | En VS Code: **Terminal** $\rightarrow$ **New Terminal** (o Ctrl + \` ) |
| **4** | **Crear la carpeta del entorno virtual** (`.venv`) | `python -m venv .venv` |
| **5** | **Activar el entorno virtual** | **Windows:** `.venv\Scripts\activate` <br> **Linux/macOS:** `source .venv/bin/activate` |
| **6** | **Activar el intérprete de Python** | *La activación del entorno virtual (Paso 5) ya establece el intérprete.* Asegúrate de que VS Code lo haya detectado y seleccionado. |
| **7** | **Instalar Django** | `pip install django` |
| **8** | **Crear el proyecto backend** (`backend_Preparatoria` **sin duplicar la carpeta**) | `django-admin startproject backend_Preparatoria .` (el punto evita la carpeta anidada) |
| **9** | **Ejecutar el servidor en el puerto 8656** | `python manage.py runserver 8656` |
| **10** | **Copiar y pegar el link en el navegador** | Navegar a: `http://127.0.0.1:8656/` |
| **11** | **Crear la aplicación** (`app_Preparatoria`) | `python manage.py startapp app_Preparatoria` |

-----

## 🏗️ Estructura y Código (Backend)

Aquí se detalla la creación de archivos y la implementación de la lógica inicial, siguiendo los pasos 12 al 28.

### 12\. Modelo `models.py` (ya proporcionado)

El archivo `app_Preparatoria/models.py` ya contiene los tres modelos. **Asegúrate de que este código esté exactamente así:**

```python
from django.db import models

# ==========================================
# MODELO: PROFESOR
# ==========================================
class Profesor(models.Model):
    nombre_profesor = models.CharField(max_length=50, unique=True)
    apellido_profesor = models.CharField(max_length=50, unique=True)
    correo_profesor = models.EmailField(max_length=100)
    telefono = models.CharField(max_length=15)
    especialidad = models.CharField(max_length=50, default="")
    fecha_contratacion = models.DateField(auto_now_add=True)
    activo = models.BooleanField(default=True)

    def __str__(self):
        return f"{self.nombre_profesor} {self.apellido_profesor}"

# ==========================================
# MODELO: CURSO
# ==========================================
class Curso(models.Model):
    nombre_curso = models.CharField(max_length=50)
    codigo = models.CharField(max_length=10)
    descripcion = models.TextField(max_length=100)
    creditos = models.PositiveIntegerField()
    horario = models.CharField(max_length=50)
    aula = models.CharField(max_length=20)
    profesor = models.ForeignKey(Profesor, related_name="cursos", on_delete=models.CASCADE)

    def __str__(self):
        return f"{self.nombre_curso} ({self.codigo})"

# ==========================================
# MODELO: ESTUDIANTE
# ==========================================
class Estudiante(models.Model):
    nombre_estudiante = models.CharField(max_length=50)
    apellido_estudiante = models.CharField(max_length=50)
    matricula = models.CharField(max_length=10, unique=True)
    correo_estudiante = models.EmailField(max_length=100)
    fecha_nacimiento = models.DateField()
    fecha_inscripcion = models.DateField(auto_now_add=True)
    cursos = models.ManyToManyField(Curso, related_name="estudiantes")

    def __str__(self):
        return f"{self.nombre_estudiante} {self.apellido_estudiante}"
```

### 14\. Vistas `views.py` (CRUD Profesor)

Implementa las funciones en `app_Preparatoria/views.py`.

```python
from django.shortcuts import render, redirect, get_object_or_404
from .models import Profesor
from django.urls import reverse

# 14. Funciones solicitadas para el CRUD de Profesor

def inicio_profesor(request):
    """Muestra la lista de todos los profesores."""
    profesores = Profesor.objects.all()
    context = {'profesores': profesores}
    return render(request, 'profesor/ver_profesor.html', context)

def agregar_profesor(request):
    """Gestiona la adición de un nuevo profesor."""
    if request.method == 'POST':
        # No hay validación de datos (Punto 30)
        nombre = request.POST.get('nombre_profesor')
        apellido = request.POST.get('apellido_profesor')
        correo = request.POST.get('correo_profesor')
        telefono = request.POST.get('telefono')
        especialidad = request.POST.get('especialidad')
        # La fecha de contratación se añade automáticamente
        
        # Crear y guardar el objeto Profesor
        Profesor.objects.create(
            nombre_profesor=nombre,
            apellido_profesor=apellido,
            correo_profesor=correo,
            telefono=telefono,
            especialidad=especialidad
        )
        return redirect('ver_profesor') # Redirige a la lista después de guardar

    return render(request, 'profesor/agregar_profesor.html')

def actualizar_profesor(request, profesor_id):
    """Muestra el formulario para editar un profesor."""
    profesor = get_object_or_404(Profesor, pk=profesor_id)
    context = {'profesor': profesor}
    return render(request, 'profesor/actualizar_profesor.html', context)

def realizar_actualizacion_profesor(request, profesor_id):
    """Procesa el formulario de actualización de un profesor."""
    profesor = get_object_or_404(Profesor, pk=profesor_id)
    if request.method == 'POST':
        # No hay validación de datos (Punto 30)
        profesor.nombre_profesor = request.POST.get('nombre_profesor')
        profesor.apellido_profesor = request.POST.get('apellido_profesor')
        profesor.correo_profesor = request.POST.get('correo_profesor')
        profesor.telefono = request.POST.get('telefono')
        profesor.especialidad = request.POST.get('especialidad')
        # El campo 'activo' se maneja con un checkbox
        profesor.activo = request.POST.get('activo') == 'on'
        
        profesor.save()
        return redirect('ver_profesor')

    # Si por alguna razón no es POST, redirige al formulario de actualización
    return redirect('actualizar_profesor', profesor_id=profesor_id)


def borrar_profesor(request, profesor_id):
    """Gestiona la eliminación de un profesor."""
    profesor = get_object_or_404(Profesor, pk=profesor_id)
    if request.method == 'POST':
        profesor.delete()
        return redirect('ver_profesor')
        
    # El archivo borrar_profesor.html debe ser una página de confirmación
    context = {'profesor': profesor}
    return render(request, 'profesor/borrar_profesor.html', context)

def inicio_sistema(request):
    """Vista para la página de inicio general del sistema."""
    return render(request, 'inicio.html')
```

### 24\. Archivo `urls.py` en `app_Preparatoria`

Crea el archivo `app_Preparatoria/urls.py` y añade el código para mapear las funciones del CRUD de Profesor.

```python
from django.urls import path
from . import views

urlpatterns = [
    # Rutas generales
    path('', views.inicio_sistema, name='inicio_sistema'),
    
    # Rutas para el modelo PROFESOR
    path('profesor/', views.inicio_profesor, name='ver_profesor'),
    path('profesor/agregar/', views.agregar_profesor, name='agregar_profesor'),
    path('profesor/actualizar/<int:profesor_id>/', views.actualizar_profesor, name='actualizar_profesor'),
    path('profesor/actualizar_guardar/<int:profesor_id>/', views.realizar_actualizacion_profesor, name='realizar_actualizacion_profesor'),
    path('profesor/borrar/<int:profesor_id>/', views.borrar_profesor, name='borrar_profesor'),
]
```

### 25 & 26. Configuración en `backend_Preparatoria/settings.py` y `urls.py`

**En `backend_Preparatoria/settings.py` (Punto 25):**

Agrega la app a `INSTALLED_APPS`:

```python
# settings.py
INSTALLED_APPS = [
    # ... otras apps de Django
    'app_Preparatoria', # ¡Asegúrate de que esté aquí!
]
```

**En `backend_Preparatoria/urls.py` (Punto 26):**

Enlaza con las URLs de `app_Preparatoria`.

```python
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    # Enlace a las URLs de la aplicación (Punto 26)
    path('', include('app_Preparatoria.urls')), 
]
```

### 27\. Registrar Modelos y Migraciones

**En `app_Preparatoria/admin.py` (Punto 27):**

Registra los modelos para que aparezcan en el panel de administración.

```python
from django.contrib import admin
from .models import Profesor, Curso, Estudiante

# Registrar modelos (Punto 27)
admin.site.register(Profesor)
admin.site.register(Curso)
admin.site.register(Estudiante)
```

**Comandos de migración (Punto 27 y 33):**

Ejecuta en la terminal de VS Code:

1.  Crear migraciones: `python manage.py makemigrations`
2.  Aplicar migraciones: `python manage.py migrate`
3.  *Opcional: Crear superusuario para el Admin:* `python manage.py createsuperuser`
4.  **Ejecutar el servidor (Punto 9 y 33):** `python manage.py runserver 8656`

-----

## 🎨 Estructura y Contenido (Frontend - HTML)

### 15, 16, 21, 22. Estructura de Carpetas y Archivos HTML

Crea la siguiente estructura dentro de `app_Preparatoria/`:

```
app_Preparatoria/
├── templates/
│   ├── base.html       (Punto 16, 17)
│   ├── header.html     (Punto 16)
│   ├── navbar.html     (Punto 16, 18)
│   ├── footer.html     (Punto 16, 19)
│   ├── inicio.html     (Punto 16, 20)
│   └── profesor/       (Punto 21)
│       ├── agregar_profesor.html   (Punto 22)
│       ├── ver_profesor.html       (Punto 22)
│       ├── actualizar_profesor.html (Punto 22)
│       └── borrar_profesor.html    (Punto 22)
```

### 17\. `base.html` (Bootstrap)

Añade la estructura básica con Bootstrap (colores suaves, atractivos y modernos - Punto 29).

```html
<!doctype html>
<html lang="es">
<head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <title>UIII Preparatoria</title>
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.3/dist/css/bootstrap.min.css" rel="stylesheet" integrity="sha384-QWTKZyjpPEjISv5WaRU9OFeRpok6YctnYmDr5pNlyT2bRjXh0JMhjY6hW+ALEwIH" crossorigin="anonymous">
    <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootstrap-icons@1.11.3/font/bootstrap-icons.min.css">
    
    <style>
        /* Estilos suaves y modernos (Punto 29) */
        :root {
            --bs-primary: #6c757d; /* Gris suave */
            --bs-secondary: #e9ecef; /* Blanco grisáceo */
            --bs-success: #20c997; /* Menta */
            --bs-info: #6610f2; /* Púrpura */
        }
        .footer-fixed {
            position: fixed;
            left: 0;
            bottom: 0;
            width: 100%;
            text-align: center;
        }
        body {
            padding-bottom: 60px; /* Espacio para el footer fijo */
        }
    </style>
</head>
<body>
    {% include 'header.html' %}
    
    <div class="container-fluid">
        {% include 'navbar.html' %}
        
        <main class="py-4">
            {% block content %}
            {% endblock %}
        </main>
        
    </div>

    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.3/dist/js/bootstrap.bundle.min.js" integrity="sha384-YvpcrYf0tY3lHB60NNkmXc5s9fDVZLESaAA55NDzOxhy9GkcIdslK1eN7N6jIeHz" crossorigin="anonymous"></script>
    
    {% include 'footer.html' %}
</body>
</html>
```

### 18\. `navbar.html`

Implementa la barra de navegación con las opciones y los iconos solicitados.

```html
<nav class="navbar navbar-expand-lg navbar-dark bg-secondary mb-4" style="--bs-bg-opacity: .9;">
    <div class="container-fluid">
        <a class="navbar-brand" href="{% url 'inicio_sistema' %}">
            <i class="bi bi-gear-fill"></i> **Sistema de Administración Preparatoria**
        </a>
        <button class="navbar-toggler" type="button" data-bs-toggle="collapse" data-bs-target="#navbarNavDropdown" aria-controls="navbarNavDropdown" aria-expanded="false" aria-label="Toggle navigation">
            <span class="navbar-toggler-icon"></span>
        </button>
        <div class="collapse navbar-collapse" id="navbarNavDropdown">
            <ul class="navbar-nav me-auto">
                <li class="nav-item">
                    <a class="nav-link" href="{% url 'inicio_sistema' %}"><i class="bi bi-house-door-fill"></i> Inicio</a>
                </li>

                <li class="nav-item dropdown">
                    <a class="nav-link dropdown-toggle" href="#" id="profesorDropdown" role="button" data-bs-toggle="dropdown" aria-expanded="false">
                        <i class="bi bi-person-video2"></i> Profesor
                    </a>
                    <ul class="dropdown-menu" aria-labelledby="profesorDropdown">
                        <li><a class="dropdown-item" href="{% url 'agregar_profesor' %}">Agregar Profesor</a></li>
                        <li><a class="dropdown-item" href="{% url 'ver_profesor' %}">Ver Profesor</a></li>
                        <li><a class="dropdown-item" href="#">Actualizar Profesor (Desde Ver)</a></li>
                        <li><a class="dropdown-item" href="#">Borrar Profesor (Desde Ver)</a></li>
                    </ul>
                </li>

                <li class="nav-item dropdown">
                    <a class="nav-link dropdown-toggle" href="#" id="cursoDropdown" role="button" data-bs-toggle="dropdown" aria-expanded="false">
                        <i class="bi bi-book-fill"></i> Curso
                    </a>
                    <ul class="dropdown-menu" aria-labelledby="cursoDropdown">
                        <li><a class="dropdown-item" href="#">Agregar Curso</a></li>
                        <li><a class="dropdown-item" href="#">Ver Curso</a></li>
                        <li><a class="dropdown-item" href="#">Actualizar Curso</a></li>
                        <li><a class="dropdown-item" href="#">Borrar Curso</a></li>
                    </ul>
                </li>

                <li class="nav-item dropdown">
                    <a class="nav-link dropdown-toggle" href="#" id="estudianteDropdown" role="button" data-bs-toggle="dropdown" aria-expanded="false">
                        <i class="bi bi-person-badge-fill"></i> Estudiante
                    </a>
                    <ul class="dropdown-menu" aria-labelledby="estudianteDropdown">
                        <li><a class="dropdown-item" href="#">Agregar Estudiante</a></li>
                        <li><a class="dropdown-item" href="#">Ver Estudiante</a></li>
                        <li><a class="dropdown-item" href="#">Actualizar Estudiante</a></li>
                        <li><a class="dropdown-item" href="#">Borrar Estudiante</a></li>
                    </ul>
                </li>
            </ul>
        </div>
    </div>
</nav>
```

### 19\. `footer.html`

Pie de página fijo con la información solicitada.

```html
<footer class="footer-fixed bg-dark text-white py-3">
    <div class="container-fluid">
        <div class="row">
            <div class="col-md-4 text-start">
                &copy; Derechos de Autor {{ "now"|date:"Y" }}
            </div>
            <div class="col-md-4 text-center">
                Fecha del Sistema: {{ "now"|date:"d/m/Y H:i" }}
            </div>
            <div class="col-md-4 text-end">
                Creado por **Aaron Mulato, CBTis 128**
            </div>
        </div>
    </div>
</footer>
```

### 20\. `inicio.html`

Página de inicio con información del sistema e imagen.

```html
{% extends 'base.html' %}

{% block content %}
<div class="row align-items-center">
    <div class="col-lg-6">
        <h1 class="display-4 text-primary">Sistema de Administración Preparatoria</h1>
        <p class="lead">
            Bienvenido al sistema de gestión y control de la preparatoria, diseñado para administrar la información de **Profesores**, **Cursos** y **Estudiantes**.
        </p>
        <p class="text-muted">
            Este sistema utiliza el framework **Django** con el lenguaje **Python**, asegurando un desarrollo robusto y escalable para la administración académica.
        </p>
        <hr class="my-4">
        <p>Utilice la barra de navegación superior para acceder a las distintas funcionalidades de la plataforma.</p>
    </div>
    <div class="col-lg-6">
        

[Image of a high school campus or classroom]

    </div>
</div>
{% endblock %}
```

### 22\. CRUD de Profesor (Archivos HTML)

Implementa los formularios y la tabla de listado.

**`profesor/agregar_profesor.html`**

```html
{% extends 'base.html' %}

{% block content %}
<div class="row justify-content-center">
    <div class="col-md-8">
        <div class="card shadow">
            <div class="card-header bg-primary text-white">
                <h3 class="mb-0"><i class="bi bi-person-plus-fill"></i> Agregar Nuevo Profesor</h3>
            </div>
            <div class="card-body">
                <form method="POST" action="{% url 'agregar_profesor' %}">
                    {% csrf_token %}
                    <div class="row mb-3">
                        <div class="col-md-6">
                            <label for="nombre_profesor" class="form-label">Nombre</label>
                            <input type="text" class="form-control" id="nombre_profesor" name="nombre_profesor" required>
                        </div>
                        <div class="col-md-6">
                            <label for="apellido_profesor" class="form-label">Apellido</label>
                            <input type="text" class="form-control" id="apellido_profesor" name="apellido_profesor" required>
                        </div>
                    </div>
                    
                    <div class="row mb-3">
                        <div class="col-md-6">
                            <label for="correo_profesor" class="form-label">Correo Electrónico</label>
                            <input type="email" class="form-control" id="correo_profesor" name="correo_profesor" required>
                        </div>
                        <div class="col-md-6">
                            <label for="telefono" class="form-label">Teléfono</label>
                            <input type="text" class="form-control" id="telefono" name="telefono">
                        </div>
                    </div>
                    
                    <div class="mb-3">
                        <label for="especialidad" class="form-label">Especialidad</label>
                        <input type="text" class="form-control" id="especialidad" name="especialidad">
                    </div>
                    
                    <div class="d-grid gap-2 d-md-flex justify-content-md-end">
                        <button type="submit" class="btn btn-success"><i class="bi bi-save-fill"></i> Guardar Profesor</button>
                        <a href="{% url 'ver_profesor' %}" class="btn btn-secondary"><i class="bi bi-x-circle-fill"></i> Cancelar</a>
                    </div>
                </form>
            </div>
        </div>
    </div>
</div>
{% endblock %}
```

**`profesor/ver_profesor.html`**

```html
{% extends 'base.html' %}

{% block content %}
<h2 class="mb-4 text-secondary"><i class="bi bi-people-fill"></i> Listado de Profesores</h2>

<div class="table-responsive">
    <table class="table table-striped table-hover shadow-sm">
        <thead class="bg-dark text-white">
            <tr>
                <th>ID</th>
                <th>Nombre</th>
                <th>Apellido</th>
                <th>Correo</th>
                <th>Especialidad</th>
                <th>Activo</th>
                <th>Acciones</th>
            </tr>
        </thead>
        <tbody>
            {% for profesor in profesores %}
            <tr>
                <td>{{ profesor.id }}</td>
                <td>{{ profesor.nombre_profesor }}</td>
                <td>{{ profesor.apellido_profesor }}</td>
                <td>{{ profesor.correo_profesor }}</td>
                <td>{{ profesor.especialidad }}</td>
                <td>
                    {% if profesor.activo %}
                        <span class="badge bg-success">Sí</span>
                    {% else %}
                        <span class="badge bg-danger">No</span>
                    {% endif %}
                </td>
                <td>
                    <a href="#" class="btn btn-sm btn-info text-white me-1"><i class="bi bi-eye-fill"></i> Ver</a>
                    <a href="{% url 'actualizar_profesor' profesor.id %}" class="btn btn-sm btn-warning text-dark me-1"><i class="bi bi-pencil-fill"></i> Editar</a>
                    <a href="{% url 'borrar_profesor' profesor.id %}" class="btn btn-sm btn-danger"><i class="bi bi-trash-fill"></i> Borrar</a>
                </td>
            </tr>
            {% empty %}
            <tr>
                <td colspan="7" class="text-center">No hay profesores registrados.</td>
            </tr>
            {% endfor %}
        </tbody>
    </table>
</div>

<div class="mt-3">
    <a href="{% url 'agregar_profesor' %}" class="btn btn-primary"><i class="bi bi-person-plus-fill"></i> Agregar Profesor</a>
</div>

{% endblock %}
```

**`profesor/actualizar_profesor.html`**

```html
{% extends 'base.html' %}

{% block content %}
<div class="row justify-content-center">
    <div class="col-md-8">
        <div class="card shadow">
            <div class="card-header bg-warning text-dark">
                <h3 class="mb-0"><i class="bi bi-pencil-square"></i> Actualizar Profesor: {{ profesor.nombre_profesor }} {{ profesor.apellido_profesor }}</h3>
            </div>
            <div class="card-body">
                <form method="POST" action="{% url 'realizar_actualizacion_profesor' profesor.id %}">
                    {% csrf_token %}
                    <div class="row mb-3">
                        <div class="col-md-6">
                            <label for="nombre_profesor" class="form-label">Nombre</label>
                            <input type="text" class="form-control" id="nombre_profesor" name="nombre_profesor" value="{{ profesor.nombre_profesor }}" required>
                        </div>
                        <div class="col-md-6">
                            <label for="apellido_profesor" class="form-label">Apellido</label>
                            <input type="text" class="form-control" id="apellido_profesor" name="apellido_profesor" value="{{ profesor.apellido_profesor }}" required>
                        </div>
                    </div>
                    
                    <div class="row mb-3">
                        <div class="col-md-6">
                            <label for="correo_profesor" class="form-label">Correo Electrónico</label>
                            <input type="email" class="form-control" id="correo_profesor" name="correo_profesor" value="{{ profesor.correo_profesor }}" required>
                        </div>
                        <div class="col-md-6">
                            <label for="telefono" class="form-label">Teléfono</label>
                            <input type="text" class="form-control" id="telefono" name="telefono" value="{{ profesor.telefono }}">
                        </div>
                    </div>
                    
                    <div class="mb-3">
                        <label for="especialidad" class="form-label">Especialidad</label>
                        <input type="text" class="form-control" id="especialidad" name="especialidad" value="{{ profesor.especialidad }}">
                    </div>
                    
                    <div class="mb-3 form-check">
                        <input type="checkbox" class="form-check-input" id="activo" name="activo" {% if profesor.activo %}checked{% endif %}>
                        <label class="form-check-label" for="activo">Profesor Activo</label>
                    </div>

                    <div class="d-grid gap-2 d-md-flex justify-content-md-end">
                        <button type="submit" class="btn btn-success"><i class="bi bi-arrow-repeat"></i> Actualizar</button>
                        <a href="{% url 'ver_profesor' %}" class="btn btn-secondary"><i class="bi bi-x-circle-fill"></i> Cancelar</a>
                    </div>
                </form>
            </div>
        </div>
    </div>
</div>
{% endblock %}
```

**`profesor/borrar_profesor.html`**

```html
{% extends 'base.html' %}

{% block content %}
<div class="row justify-content-center">
    <div class="col-md-6">
        <div class="card border-danger shadow">
            <div class="card-header bg-danger text-white">
                <h3 class="mb-0"><i class="bi bi-exclamation-triangle-fill"></i> Confirmar Eliminación</h3>
            </div>
            <div class="card-body">
                <p class="card-text">¿Estás seguro de que deseas **eliminar permanentemente** al siguiente profesor?</p>
                
                <ul class="list-group mb-4">
                    <li class="list-group-item">**ID:** {{ profesor.id }}</li>
                    <li class="list-group-item">**Nombre:** {{ profesor.nombre_profesor }} {{ profesor.apellido_profesor }}</li>
                    <li class="list-group-item">**Especialidad:** {{ profesor.especialidad }}</li>
                </ul>
                
                <form method="POST" action="{% url 'borrar_profesor' profesor.id %}">
                    {% csrf_token %}
                    <div class="d-grid gap-2 d-md-flex justify-content-md-end">
                        <button type="submit" class="btn btn-danger"><i class="bi bi-trash-fill"></i> Confirmar Borrar</button>
                        <a href="{% url 'ver_profesor' %}" class="btn btn-secondary"><i class="bi bi-x-circle-fill"></i> Cancelar</a>
                    </div>
                </form>
            </div>
        </div>
    </div>
</div>
{% endblock %}
```

-----

**Siguiente paso que puedo hacer por ti:**

Si deseas, puedo proporcionarte los comandos específicos para **crear el proyecto y la app, realizar las migraciones y ejecutar el servidor en una secuencia única** para que puedas empezar a copiar y pegar el código. ¿Te gustaría esa secuencia de comandos?
