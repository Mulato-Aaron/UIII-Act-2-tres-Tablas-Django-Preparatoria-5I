Perfecto ✅
Aquí tienes **la lista completa y ordenada** con todos los **procedimientos del proyecto Django "Preparatoria" (Primera Parte)**, tal como lo solicitaste:

---

## 🧩 **Primera Parte del Proyecto: Preparatoria**

**Lenguaje:** Python
**Framework:** Django
**Editor:** Visual Studio Code

---

### ⚙️ **Procedimientos paso a paso**

1. **Crear la carpeta del proyecto:**

   * Nombre: `UIII_Preparatoria_0656`.

2. **Abrir VS Code:**

   * Abrir VS Code sobre la carpeta `UIIII_Preparatoria_0656`.

3. **Abrir la terminal en VS Code:**

   * Menú superior → **Terminal → Nueva terminal**.

4. **Crear el entorno virtual (.venv):**

   ```bash
   python -m venv .venv
   ```

5. **Activar el entorno virtual:**

   * En Windows:

     ```bash
     .venv\Scripts\activate
     ```
   * En macOS/Linux:

     ```bash
     source .venv/bin/activate
     ```

6. **Activar el intérprete de Python en VS Code:**

   * Presiona **Ctrl + Shift + P** → escribe **Python: Select Interpreter** → selecciona `.venv`.

7. **Instalar Django:**

   ```bash
   pip install django
   ```

8. **Crear el proyecto principal (backend_Preparatoria):**

   ```bash
   django-admin startproject backend_Preparatoria .
   ```

   *(Asegúrate de no crear una carpeta duplicada.)*

9. **Ejecutar el servidor en el puerto 8656:**

   ```bash
   python manage.py runserver 8656
   ```

10. **Copiar y pegar el enlace en el navegador:**

    ```
    http://127.0.0.1:8656/
    ```

11. **Crear la aplicación:**

    ```bash
    python manage.py startapp app_Preparatoria
    ```

---

### 🧱 **MODELO: `models.py`**

Ubicado en `app_Preparatoria/models.py`

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

---

### 👨‍🏫 **MODELO: PROFESOR**

12. **Trabajar primero con el modelo Profesor.**

13. **Crear las funciones en `views.py` de `app_Preparatoria`:**

* `inicio_profesor`
* `agregar_profesor`
* `actualizar_profesor`
* `realizar_actualizacion_profesor`
* `borrar_profesor`

14. **Crear la carpeta `templates` dentro de `app_Preparatoria`.**

15. **Dentro de `templates`, crear los siguientes archivos HTML:**

* `base.html`
* `header.html`
* `navbar.html`
* `footer.html`
* `inicio.html`

16. **En `base.html`:**

* Incluir Bootstrap (CSS y JS).

17. **En `navbar.html`:**

* Incluir el menú:

  * “Sistema de Administración Preparatoria”
  * “Inicio”
  * “Profesor” → Submenú: Agregar, Ver, Actualizar, Borrar
  * “Curso” → Submenú: Agregar, Ver, Actualizar, Borrar
  * “Estudiante” → Submenú: Agregar, Ver, Actualizar, Borrar
  * Agregar íconos a las opciones principales.

18. **En `footer.html`:**

* Incluir:

  * Derechos de autor
  * Fecha del sistema
  * Texto: “Creado por Aaron Nava, CBTis 128”
* Mantener el footer fijo al final de la página.

19. **En `inicio.html`:**

* Colocar información general del sistema.
* Agregar una imagen representativa de la preparatoria.

---

### 📂 **Plantillas de Profesor**

20. Crear subcarpeta:
    `app_Preparatoria/templates/profesor/`

21. Dentro, crear los siguientes archivos HTML:

* `agregar_profesor.html`
* `ver_profesor.html` (mostrar en tabla con botones Ver, Editar, Borrar)
* `actualizar_profesor.html`
* `borrar_profesor.html`

---

### 🌐 **Configuración de URLs**

22. **Crear `urls.py` dentro de `app_Preparatoria`:**

* Con las rutas para las funciones CRUD del modelo Profesor.

23. **Agregar la app en `settings.py`:**

```python
INSTALLED_APPS = [
    ...,
    'app_Preparatoria',
]
```

24. **Configurar las URLs en `backend_Preparatoria/urls.py`:**

```python
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', include('app_Preparatoria.urls')),
]
```

25. **Registrar modelos en `admin.py`:**

```python
from django.contrib import admin
from .models import Profesor, Curso, Estudiante

admin.site.register(Profesor)
admin.site.register(Curso)
admin.site.register(Estudiante)
```

26. **Realizar migraciones:**

```bash
python manage.py makemigrations
python manage.py migrate
```

---

### 🎨 **Diseño y Estilo**

* Usar **colores suaves y modernos.**
* No validar datos en formularios.
* Asegurarse de tener toda la estructura completa antes de iniciar el servidor.

---

### 🚀 **Ejecución final**

Ejecutar el servidor nuevamente:

```bash
python manage.py runserver 8656
```

---

¿Deseas que te genere **todo el código base (views.py, urls.py y plantillas HTML)** para este modelo “Profesor”?
Puedo darte una versión funcional y visualmente agradable con Bootstrap.

UIII_Preparatoria_0656/
│
├── .venv/                              ← Entorno virtual
│
├── backend_Preparatoria/               ← Carpeta principal del proyecto Django
│   ├── __init__.py
│   ├── asgi.py
│   ├── settings.py
│   ├── urls.py
│   └── wsgi.py
│
├── app_Preparatoria/                   ← Aplicación principal
│   ├── __init__.py
│   ├── admin.py
│   ├── apps.py
│   ├── models.py
│   ├── tests.py
│   ├── views.py
│   ├── urls.py                         ← Archivo creado por ti para las rutas CRUD
│   │
│   ├── migrations/                     ← Carpeta generada automáticamente
│   │   └── __init__.py
│   │
│   └── templates/                      ← Carpeta para las vistas HTML
│       ├── base.html                   ← Estructura principal (con Bootstrap)
│       ├── header.html                 ← Encabezado del sitio
│       ├── navbar.html                 ← Barra de navegación con menús
│       ├── footer.html                 ← Pie de página fijo
│       ├── inicio.html                 ← Página principal del sistema
│       │
│       └── profesor/                   ← Subcarpeta para las vistas del modelo Profesor
│           ├── agregar_profesor.html
│           ├── ver_profesor.html
│           ├── actualizar_profesor.html
│           └── borrar_profesor.html
│
├── manage.py                           ← Archivo principal de Django
│
└── requirements.txt                    ← (Opcional) Dependencias del proyecto

