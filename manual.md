📖 MANUAL DE PROYECTO: FERRETERÍA EL PATITO 🦆
¡Bienvenido(a) al manual de configuración y desarrollo de la aplicación web "Ferretería El Patito"! 🛠️🛒
Este documento detalla cada paso necesario para poner en marcha y entender el proyecto. Desde la configuración inicial del entorno hasta la implementación de modelos, vistas y plantillas. Se ha diseñado pensando en la legibilidad y facilidad de uso para su publicación en GitHub. ¡Vamos a ello! 👇
1. ⚙️ CONFIGURACIÓN DEL ENTORNO DE DESARROLLO
Esta sección te guiará a través de los pasos iniciales para preparar tu sistema y comenzar a trabajar con el proyecto. ¡Es la base! 🧱
1.1. 📁 Crear la Carpeta del Proyecto
Crea la carpeta principal para tu proyecto.
Acción: Navega a la ruta deseada (ej. C:\IAWeb_5J\Unidad III\) y crea una carpeta con el nombre UIII_FerreteriaElPatito_0290.
code
Bash
cd C:\IAWeb_5J\Unidad III
mkdir UIII_FerreteriaElPatito_0290
1.2. 💻 Abrir VS Code en la Carpeta del Proyecto
Abre el entorno de desarrollo VS Code sobre la carpeta que acabas de crear.
Acción: Abre VS Code, ve a Archivo > Abrir carpeta... y selecciona C:\IAWeb_5J\Unidad III\UIII_FerreteriaElPatito_0290.
1.3. 🚀 Abrir la Terminal en VS Code
Accede a la terminal integrada de VS Code.
Acción: Presiona Ctrl+Ñ o ve a Terminal > Nueva terminal. Asegúrate de que la terminal esté en la ruta raíz del proyecto: C:\IAWeb_5J\Unidad III\UIII_FerreteriaElPatito_0290.
1.4. 🐍 Crear el Entorno Virtual .venv
Es crucial trabajar con un entorno virtual para aislar las dependencias del proyecto. ¡Así mantendrás todo limpio! ✨
Acción: Ejecuta el siguiente comando en la terminal.
code
Bash
python -m venv .venv
1.5. 🟢 Activar el Entorno Virtual (Windows)
Activa el entorno virtual para poder instalar las librerías del proyecto.
Acción: Ejecuta el siguiente comando en la terminal.
code
Bash
.venv\Scripts\activate.bat
1.6. ✨ Activar el Intérprete de Python en VS Code
Asegúrate de que VS Code esté utilizando el intérprete de Python del entorno virtual.
Acción: Haz clic en el intérprete actual en la barra de estado inferior de VS Code (usualmente muestra "Python X.X.X") y selecciona la opción que está dentro de .venv (ej. Python 3.x.x (.venv)).
1.7. 🌐 Instalar Django
Instala el framework Django dentro de tu entorno virtual. ¡El corazón de la aplicación! ❤️
Acción: Ejecuta el siguiente comando en la terminal.
code
Bash
pip install django
1.8. 🏗️ Crear el Proyecto Django backend_Ferreteria
Inicializa el proyecto Django principal. El punto . al final evita crear una carpeta anidada.
Acción: Ejecuta el siguiente comando en la terminal.
code
Bash
django-admin startproject backend_Ferreteria .
1.9. ▶️ Ejecutar el Servidor de Desarrollo
Verifica que Django se haya instalado y el proyecto se haya creado correctamente.
Acción: Ejecuta el servidor en el puerto 8023.
code
Bash
python manage.py runserver 8023
Verificación: Abre tu navegador y navega a http://127.0.0.1:8023. Deberías ver la página de bienvenida de Django. ¡Si es así, todo va viento en popa! 🌬️
1.10. ➕ Crear la Aplicación Django app_Ferreteria
Crea la aplicación específica para la lógica de la ferretería. ¡Aquí es donde vivirá tu negocio! 🏪
Acción: Abre una nueva terminal (o detén el servidor con Ctrl+C y luego ejecuta el comando).
code
Bash
python manage.py startapp app_Ferreteria
2. 🗄️ DESARROLLO DE MODELOS DE DATOS
En esta sección, definiremos la estructura de la base de datos de la ferretería utilizando los modelos de Django. ¡Es como construir los cimientos! 🏗️
2.1. 📝 Definir Modelos en models.py
Define los modelos Usuario, Categoria, Sucursal, Producto, Carrito, Pedido y DetallePedido.
Ubicación: Abre el archivo UIII_FerreteriaElPatito_0290\app_Ferreteria\models.py.
Código: Pega el siguiente código, reemplazando el contenido existente.
code
Python
from django.db import models
from django.contrib.auth.hashers import make_password # Para hashear contraseñas
from django.contrib.auth.models import BaseUserManager, AbstractUser

# --- Managers Personalizados (Opcional, pero recomendado para control total) ---
# Si decides usar el modelo Usuario tal cual como está, sin heredar de AbstractUser,
# este CustomUserManager es menos relevante para la autenticación directa de Django,
# pero puede ser útil si quieres una gestión más fina desde el shell o admin.
# Para este manual, nos basaremos en el modelo Usuario simple y su método check_password.

class CustomUserManager(BaseUserManager):
    def create_user(self, correo, contrasena=None, **extra_fields):
        if not correo:
            raise ValueError('El correo electrónico es obligatorio')
        correo = self.normalize_email(correo)
        user = self.model(correo=correo, **extra_fields)
        user.contrasena = make_password(contrasena) # Hashea la contraseña
        user.save(using=self._db)
        return user

    def create_superuser(self, correo, contrasena=None, **extra_fields):
        extra_fields.setdefault('is_staff', True)
        extra_fields.setdefault('is_superuser', True)
        extra_fields.setdefault('is_active', True)

        if extra_fields.get('is_staff') is not True:
            raise ValueError('Superuser must have is_staff=True.')
        if extra_fields.get('is_superuser') is not True:
            raise ValueError('Superuser must have is_superuser=True.')
        return self.create_user(correo, contrasena, **extra_fields)

# --- Modelos de la Aplicación ---

class Usuario(models.Model):
    id = models.AutoField(primary_key=True)
    nombre = models.CharField(max_length=100, unique=True)
    correo = models.EmailField(unique=True)
    contrasena = models.CharField(max_length=255) # Aquí se guardará el hash de la contraseña
    direccion = models.CharField(max_length=255)
    referencia = models.CharField(max_length=255, blank=True, null=True) # Campo opcional

    # objects = CustomUserManager() # Descomentar si se hereda de AbstractUser y se usa este manager

    def __str__(self):
        return self.nombre

    # Método para verificar la contraseña hasheada
    def check_password(self, raw_password):
        from django.contrib.auth.hashers import check_password
        return check_password(raw_password, self.contrasena)

class Categoria(models.Model):
    id = models.AutoField(primary_key=True)
    nombre = models.CharField(max_length=100, unique=True)
    descripcion = models.TextField(blank=True, null=True)

    def __str__(self):
        return self.nombre

class Sucursal(models.Model):
    id = models.AutoField(primary_key=True)
    direccion = models.CharField(max_length=255, unique=True)
    telefono = models.CharField(max_length=20, unique=True)
    correo = models.EmailField(unique=True)

    def __str__(self):
        return self.direccion

class Producto(models.Model):
    id = models.AutoField(primary_key=True)
    nombre = models.CharField(max_length=100)
    stock = models.IntegerField(default=0)
    tamano = models.CharField(max_length=50, blank=True, null=True)
    precio = models.FloatField(default=0.0)
    sucursal = models.ForeignKey(Sucursal, on_delete=models.CASCADE, related_name='productos')
    categoria = models.ForeignKey(Categoria, on_delete=models.SET_NULL, null=True, blank=True, related_name='productos')
    imagen_url = models.URLField(max_length=500, blank=True, null=True) # Para mostrar imágenes externas

    def __str__(self):
        return f"{self.nombre} ({self.tamano}) - ${self.precio} en {self.sucursal.direccion}"

class Carrito(models.Model):
    id = models.AutoField(primary_key=True)
    usuario = models.ForeignKey(Usuario, on_delete=models.CASCADE, related_name='carritos')
    producto = models.ForeignKey(Producto, on_delete=models.CASCADE, related_name='en_carritos')
    cantidad = models.IntegerField(default=1)

    def __str__(self):
        return f"Carrito de {self.usuario.nombre} - {self.producto.nombre} x {self.cantidad}"

    def get_total_item(self):
        return self.cantidad * self.producto.precio

class Pedido(models.Model):
    ESTADO_CHOICES = [
        ('PENDIENTE', 'Pendiente de Pago'),
        ('PAGADO', 'Pagado'),
        ('ENVIADO', 'Enviado'),
        ('ENTREGADO', 'Entregado'),
        ('CANCELADO', 'Cancelado'),
    ]

    id = models.AutoField(primary_key=True)
    usuario = models.ForeignKey(Usuario, on_delete=models.CASCADE, related_name='pedidos')
    fecha_creacion = models.DateTimeField(auto_now_add=True)
    total_productos = models.FloatField(default=0.0)
    costo_envio = models.FloatField(default=50.0)
    total_final = models.FloatField(default=0.0)
    estado = models.CharField(max_length=20, choices=ESTADO_CHOICES, default='PENDIENTE')
    direccion_envio = models.CharField(max_length=255)
    metodo_pago = models.CharField(max_length=50, blank=True, null=True) # Simulado
    
    def __str__(self):
        return f"Pedido #{self.id} de {self.usuario.nombre} - {self.estado}"

class DetallePedido(models.Model):
    id = models.AutoField(primary_key=True)
    pedido = models.ForeignKey(Pedido, on_delete=models.CASCADE, related_name='detalles')
    producto = models.ForeignKey(Producto, on_delete=models.CASCADE)
    cantidad = models.IntegerField(default=1)
    precio_unitario = models.FloatField(default=0.0) # Precio al momento de la compra

    def __str__(self):
        return f"Detalle Pedido #{self.pedido.id} - {self.producto.nombre} x {self.cantidad}"

    def get_total_detalle(self):
        return self.cantidad * self.precio_unitario
2.2. 💾 Realizar Migraciones de la Base de Datos
Crea y aplica los cambios de los modelos a la base de datos. ¡Esto transforma tus definiciones en tablas reales! 📊
Acción: Ejecuta los siguientes comandos en la terminal.
code
Bash
python manage.py makemigrations
python manage.py migrate
Crear Superusuario: Después de las migraciones iniciales, necesitarás un superusuario para acceder al panel de administración y empezar a agregar datos. ¡Es tu llave maestra! 🔑
code
Bash
python manage.py createsuperuser
Sigue las instrucciones en la terminal para crear el usuario (correo y contraseña).
3. 🎯 VISTAS, PLANTILLAS Y CONFIGURACIÓN DE URLS
Aquí implementaremos la lógica de la aplicación, las interfaces de usuario y cómo se enlazan. ¡Es donde la magia sucede para el usuario! 🪄
3.1. 👩‍💻 Crear las Vistas en views.py
Implementa las funciones que manejarán la lógica de cada página (renderizado, autenticación, carrito, pedidos).
Ubicación: Abre el archivo UIII_FerreteriaElPatito_0290\app_Ferreteria\views.py.
Código: Pega el siguiente código, reemplazando el contenido existente.
code
Python
from django.shortcuts import render, redirect, get_object_or_404
from .models import Sucursal, Producto, Usuario, Carrito, Categoria, Pedido, DetallePedido
from django.contrib.auth.hashers import make_password, check_password # Para hashear y verificar contraseñas
from django.contrib import messages
from django.db import transaction # Para transacciones de base de datos
from django.utils import timezone # Para la fecha del pedido

def inicio(request):
    return render(request, 'inicio.html')

def ver_productos(request, categoria_id=None):
    if categoria_id:
        productos = Producto.objects.filter(categoria__id=categoria_id).select_related('sucursal', 'categoria')
        categoria_actual = get_object_or_404(Categoria, id=categoria_id)
    else:
        productos = Producto.objects.all().select_related('sucursal', 'categoria')
        categoria_actual = None
    
    categorias = Categoria.objects.all()
    return render(request, 'productos.html', {'productos': productos, 'categorias': categorias, 'categoria_actual': categoria_actual})

def ver_sucursales(request):
    sucursales = Sucursal.objects.all().prefetch_related('productos')
    return render(request, 'sucursales.html', {'sucursales': sucursales})

def ver_carrito(request):
    if 'usuario_id' not in request.session:
        messages.info(request, 'Debes iniciar sesión para ver tu carrito.')
        return redirect('iniciar_sesion')

    usuario_actual = get_object_or_404(Usuario, id=request.session['usuario_id'])
    carrito_items = Carrito.objects.filter(usuario=usuario_actual).select_related('producto')

    total_envio = 50.00
    subtotal_productos = sum(item.get_total_item() for item in carrito_items)
    total_final = subtotal_productos + total_envio if carrito_items else 0.00
    
    return render(request, 'carrito.html', {
        'carrito_items': carrito_items,
        'total_envio': total_envio,
        'subtotal_productos': subtotal_productos,
        'total_final': total_final
    })

def iniciar_sesion(request):
    if request.method == 'POST':
        correo = request.POST['correo']
        contrasena = request.POST['contrasena']
        
        try:
            usuario = Usuario.objects.get(correo=correo)
            if check_password(contrasena, usuario.contrasena): # Verificar contraseña hasheada
                request.session['usuario_id'] = usuario.id
                messages.success(request, f'¡Bienvenido {usuario.nombre}! Sesión iniciada correctamente.')
                return redirect('ver_productos')
            else:
                messages.error(request, 'Contraseña incorrecta.')
        except Usuario.DoesNotExist:
            messages.error(request, 'Correo electrónico no registrado o incorrecto.')
            
    return render(request, 'iniciar.html')

def crear_cuenta(request):
    if request.method == 'POST':
        nombre = request.POST['nombre']
        correo = request.POST['correo']
        contrasena = request.POST['contrasena']
        contrasena_confirm = request.POST['contrasena_confirm'] # Campo adicional para confirmación
        direccion = request.POST['direccion']
        referencia = request.POST.get('referencia', '') # Hacemos referencia opcional

        if not (nombre and correo and contrasena and contrasena_confirm and direccion):
            messages.error(request, 'Todos los campos obligatorios deben ser rellenados.')
            return render(request, 'crear_cuenta.html')

        if contrasena != contrasena_confirm:
            messages.error(request, 'Las contraseñas no coinciden.')
            return render(request, 'crear_cuenta.html')

        if Usuario.objects.filter(correo=correo).exists():
            messages.error(request, 'El correo electrónico ya está registrado.')
        elif Usuario.objects.filter(nombre=nombre).exists():
            messages.error(request, 'El nombre de usuario ya está en uso.')
        else:
            # Hashear la contraseña antes de guardar
            hashed_password = make_password(contrasena)
            Usuario.objects.create(
                nombre=nombre,
                correo=correo,
                contrasena=hashed_password,
                direccion=direccion,
                referencia=referencia
            )
            messages.success(request, 'Cuenta creada exitosamente. ¡Ahora puedes iniciar sesión!')
            return redirect('iniciar_sesion')
    return render(request, 'crear_cuenta.html')

def cerrar_sesion(request):
    if 'usuario_id' in request.session:
        del request.session['usuario_id']
    messages.info(request, 'Has cerrado sesión.')
    return render(request, 'cerrar.html') # Redirigir a la página de cerrado de sesión

def agregar_al_carrito(request, producto_id):
    if 'usuario_id' not in request.session:
        messages.error(request, 'Debes iniciar sesión para agregar productos al carrito.')
        return redirect('iniciar_sesion')

    producto = get_object_or_404(Producto, id=producto_id)
    usuario_actual = get_object_or_404(Usuario, id=request.session['usuario_id'])

    if producto.stock <= 0:
        messages.warning(request, f'El producto "{producto.nombre}" está agotado.')
        return redirect('ver_productos')

    carrito_item, created = Carrito.objects.get_or_create(usuario=usuario_actual, producto=producto)
    if not created:
        if (carrito_item.cantidad + 1) > producto.stock:
            messages.warning(request, f'No hay suficiente stock de "{producto.nombre}" para agregar más.')
        else:
            carrito_item.cantidad += 1
            carrito_item.save()
            messages.success(request, f'"{producto.nombre}" agregado al carrito (Cantidad: {carrito_item.cantidad}).')
    else:
        messages.success(request, f'"{producto.nombre}" agregado al carrito (Cantidad: 1).')

    return redirect('ver_productos')

def actualizar_cantidad_carrito(request, item_id):
    if 'usuario_id' not in request.session:
        return redirect('iniciar_sesion')

    carrito_item = get_object_or_404(Carrito, id=item_id)
    if carrito_item.usuario.id != request.session['usuario_id']:
        messages.error(request, 'No tienes permiso para modificar este item del carrito.')
        return redirect('ver_carrito')
    
    if request.method == 'POST':
        try:
            nueva_cantidad = int(request.POST.get('cantidad', 1))
            if nueva_cantidad <= 0:
                carrito_item.delete() # Si la cantidad es 0 o menos, eliminar del carrito
                messages.info(request, f'"{carrito_item.producto.nombre}" eliminado del carrito.')
            elif nueva_cantidad > carrito_item.producto.stock:
                messages.warning(request, f'No hay suficiente stock de "{carrito_item.producto.nombre}" para la cantidad solicitada.')
            else:
                carrito_item.cantidad = nueva_cantidad
                carrito_item.save()
                messages.success(request, f'Cantidad de "{carrito_item.producto.nombre}" actualizada a {nueva_cantidad}.')
        except ValueError:
            messages.error(request, 'Cantidad no válida.')
    return redirect('ver_carrito')

def eliminar_del_carrito(request, item_id):
    if 'usuario_id' not in request.session:
        return redirect('iniciar_sesion')

    carrito_item = get_object_or_404(Carrito, id=item_id)
    if carrito_item.usuario.id != request.session['usuario_id']:
        messages.error(request, 'No tienes permiso para eliminar este item del carrito.')
        return redirect('ver_carrito')
    
    if request.method == 'POST':
        carrito_item.delete()
        messages.info(request, f'"{carrito_item.producto.nombre}" eliminado del carrito.')
    return redirect('ver_carrito')

def comprar_carrito(request):
    if 'usuario_id' not in request.session:
        messages.error(request, 'Debes iniciar sesión para realizar una compra.')
        return redirect('iniciar_sesion')
    
    usuario_actual = get_object_or_404(Usuario, id=request.session['usuario_id'])
    carrito_items = Carrito.objects.filter(usuario=usuario_actual).select_related('producto')

    if not carrito_items.exists():
        messages.warning(request, 'Tu carrito está vacío. No se puede realizar la compra.')
        return redirect('ver_carrito')
    
    # Simulación de un proceso de pago, aquí se podría integrar una pasarela real.
    # Por ahora, simplemente creamos un pedido y movemos los ítems.
    
    try:
        with transaction.atomic(): # Aseguramos que todas las operaciones se completen o ninguna
            total_productos = sum(item.get_total_item() for item in carrito_items)
            costo_envio = 50.00
            total_final = total_productos + costo_envio

            # 1. Crear el Pedido
            pedido = Pedido.objects.create(
                usuario=usuario_actual,
                total_productos=total_productos,
                costo_envio=costo_envio,
                total_final=total_final,
                estado='PAGADO', # Asumimos pago exitoso en la simulación
                direccion_envio=usuario_actual.direccion, # Usamos la dirección del usuario
                metodo_pago='Tarjeta Simulada' # Simulado
            )

            # 2. Mover los ítems del carrito a DetallePedido y reducir stock
            for item in carrito_items:
                DetallePedido.objects.create(
                    pedido=pedido,
                    producto=item.producto,
                    cantidad=item.cantidad,
                    precio_unitario=item.producto.precio
                )
                # Reducir stock del producto
                item.producto.stock -= item.cantidad
                item.producto.save()
            
            # 3. Vaciar el carrito
            carrito_items.delete()

            messages.success(request, f'¡Compra realizada con éxito! Tu Pedido #{pedido.id} ha sido procesado.')
            return redirect('ver_pedidos') # Redirigir a una página de historial de pedidos
    except Exception as e:
        messages.error(request, f'Hubo un error al procesar tu compra: {e}. Por favor, inténtalo de nuevo.')
        return redirect('ver_carrito')

def ver_pedidos(request):
    if 'usuario_id' not in request.session:
        messages.error(request, 'Debes iniciar sesión para ver tus pedidos.')
        return redirect('iniciar_sesion')
    
    usuario_actual = get_object_or_404(Usuario, id=request.session['usuario_id'])
    pedidos = Pedido.objects.filter(usuario=usuario_actual).order_by('-fecha_creacion')
    
    return render(request, 'pedidos.html', {'pedidos': pedidos})

def ver_detalle_pedido(request, pedido_id):
    if 'usuario_id' not in request.session:
        messages.error(request, 'Debes iniciar sesión para ver los detalles del pedido.')
        return redirect('iniciar_sesion')
    
    pedido = get_object_or_or_404(Pedido, id=pedido_id, usuario__id=request.session['usuario_id'])
    detalles = DetallePedido.objects.filter(pedido=pedido).select_related('producto')
    
    return render(request, 'detalle_pedido.html', {'pedido': pedido, 'detalles': detalles})
3.2. 📂 Crear la Carpeta templates
Esta carpeta contendrá todos los archivos HTML de las vistas. ¡Donde se diseñarán tus páginas! 🎨
Acción: Crea una carpeta llamada templates dentro de UIII_FerreteriaElPatito_0290\app_Ferreteria.
code
Bash
mkdir app_Ferreteria\templates
3.3. 📄 Crear los Archivos HTML (Plantillas)
Crea los siguientes archivos HTML dentro de app_Ferreteria\templates\.
3.3.1. 📜 base.html (Plantilla Base)
Esta plantilla define la estructura general de todas las páginas, incluyendo la navegación y estilos globales.
Ubicación: UIII_FerreteriaElPatito_0290\app_Ferreteria\templates\base.html
Código:
code
Html
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>{% block title %}Ferretería el Patito{% endblock %}</title>
    {% load static %}
    <!-- Bootstrap CSS -->
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.3/dist/css/bootstrap.min.css" rel="stylesheet" integrity="sha384-QWTKZyjpPEjISv5WaRU9OFeRpok6YctnYmDr5pNlyT2bRjXh0JMhjY6hW+ALEwIH" crossorigin="anonymous">
    <!-- Font Awesome para iconos -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.2/css/all.min.css" integrity="sha512-SnH5WK+bZxgPHs44uWIX+LLJAJ9/2PkPKZ5QiAj6Ta86w+fsb2TkcmfRyVX3pBnMFcV7oQPJkl9QevSCWr3W6A==" crossorigin="anonymous" referrerpolicy="no-referrer" />
    <style>
        body {
            background-color: #F2F2F2; /* Color de fondo general */
            font-family: Arial, sans-serif;
            display: flex;
            flex-direction: column;
            min-height: 100vh;
        }

        header {
            background-color: #E4730D; /* Naranja fuerte */
            color: #F2F2F2;
            padding: 20px;
            text-align: center;
            position: fixed;
            top: 0;
            width: 100%;
            z-index: 1000;
        }
        
        nav {
            background-color: #E4730D; /* Naranja fuerte */
            color: #F2F2F2;
            padding: 10px 0;
            position: fixed;
            text-align: center;
            top: 125px; /* Debajo del header */
            width: 100%;
            z-index: 999;
            box-shadow: 0 2px 5px rgba(0,0,0,0.1);
        }

        nav a {
            color: #F2F2F2;
            text-decoration: none;
            padding: 10px 30px;
            transition: background-color 0.3s ease; /* Transición suave para hover */
        }
        nav a:hover {
            background-color: #f7931e; /* Naranja más claro al pasar el ratón */
            border-radius: 5px;
            color: #F2F2F2; /* Asegurar color del texto en hover */
        }

        .contenido {
            color: #373737; /* Gris oscuro */
            margin-top: 200px; /* Ajuste para el header y navbar fijos */
            text-align: center;
            padding: 20px;
            flex: 1; /* Para que el contenido ocupe el espacio restante */
        }

        .espacio {
            display: flex;
            justify-content: center; /* Centrar los cuadros */
            flex-wrap: wrap; /* Para que los cuadros se envuelvan en pantallas pequeñas */
            gap: 20px; /* Espacio entre los cuadros */
            margin-top: 20px;
        }

        .cuadro {
            width: 100%; /* Por defecto 100% para móviles */
            max-width: 300px; /* Ancho máximo para el efecto de columna */
            background-color: white;
            padding: 20px;
            box-shadow: 0 4px 8px rgba(0,0,0,0.1); /* Sombra suave */
            border-radius: 8px; /* Bordes redondeados */
            display: flex; /* Para que el contenido interno se alinee bien */
            flex-direction: column;
            justify-content: space-between;
        }
        @media (min-width: 768px) {
            .cuadro {
                max-width: 32%; /* Tres columnas en pantallas medianas y grandes */
            }
        }
        @media (min-width: 992px) {
            .cuadro {
                max-width: 30%; /* Ligeramente más pequeños para tres columnas */
            }
        }


        .cuadrito {
            width: 100%; /* Ocupa todo el ancho del cuadro padre */
            min-height: 150px; /* Altura mínima para asegurar el espacio */
            background-color: #F2F2F2; /* Fondo del "cuadrito" */
            padding: 15px;
            margin: 0 auto; /* Centrar horizontalmente */
            border-radius: 5px;
            display: flex;
            flex-direction: column;
            justify-content: space-between; /* Para espaciar el contenido */
            text-align: left; /* Contenido del cuadrito alineado a la izquierda */
        }
        .cuadrito h2 {
            text-align: center;
            color: #E4730D;
            margin-bottom: 15px;
        }
        .cuadrito h3 {
            font-size: 1em;
            margin-bottom: 8px;
        }


        footer {
            background-color: #373737; /* Gris oscuro */
            color: #F2F2F2;
            text-align: center;
            padding: 15px;
            width: 100%;
            margin-top: auto; /* Para que el footer se pegue al final */
            box-shadow: 0 -2px 5px rgba(0,0,0,0.1);
        }

        ul {
            list-style-type: none;
            padding: 0;
            margin: 0;
        }
        .btn-custom {
            background-color: #E4730D; /* Color del botón principal */
            color: white;
            padding: 8px 15px;
            border-radius: 5px;
            text-decoration: none;
            display: inline-block; /* Para que se ajuste al texto */
            margin-top: 10px;
            transition: background-color 0.3s ease;
            border: none;
        }
        .btn-custom:hover {
            background-color: #f7931e;
            color: white;
            cursor: pointer;
        }
        .btn-secondary-custom {
            background-color: #373737; /* Color del botón secundario */
            color: white;
            padding: 8px 15px;
            border-radius: 5px;
            text-decoration: none;
            display: inline-block;
            margin-top: 10px;
            transition: background-color 0.3s ease;
            border: none;
        }
        .btn-secondary-custom:hover {
            background-color: #555555;
            color: white;
            cursor: pointer;
        }

        /* Estilos para mensajes de Django */
        .messages {
            list-style: none;
            padding: 0;
            margin-top: 20px;
            max-width: 800px;
            margin-left: auto;
            margin-right: auto;
        }
        .messages li {
            padding: 10px 15px;
            margin-bottom: 10px;
            border-radius: 5px;
            text-align: center;
            font-weight: bold;
        }
        .messages .success {
            background-color: #d4edda;
            color: #155724;
            border: 1px solid #c3e6cb;
        }
        .messages .error {
            background-color: #f8d7da;
            color: #721c24;
            border: 1px solid #f5c6cb;
        }
        .messages .info {
            background-color: #d1ecf1;
            color: #0c5460;
            border: 1px solid #bee5eb;
        }
        .messages .warning {
            background-color: #fff3cd;
            color: #856404;
            border: 1px solid #ffeeba;
        }
        .product-image {
            max-width: 100%;
            height: 150px;
            object-fit: contain; /* Ajustar la imagen dentro del contenedor */
            margin-bottom: 10px;
            border-radius: 5px;
        }
    </style>
</head>
<body>
    <header>
        <h1>Ferretería el Patito</h1>
    </header>

    <nav>
        <a href="{% url 'ver_productos' %}"> <img src="{% static 'img/buscar.png' %}" alt="Buscar" style="width:20px; vertical-align: middle;"> Buscar Productos</a>
        <a href="{% url 'ver_sucursales' %}">Sucursales</a>
        <a href="{% url 'ver_carrito' %}">Carrito</a>
        {% if request.session.usuario_id %}
            <a href="{% url 'ver_pedidos' %}">Mis Pedidos</a>
            <a href="{% url 'cerrar_sesion' %}">Cerrar Sesión</a>
        {% else %}
            <a href="{% url 'iniciar_sesion' %}">Iniciar Sesión</a>
            <a href="{% url 'crear_cuenta' %}">Crear Cuenta</a>
        {% endif %}
    </nav>

    <div class="contenido">
        {% if messages %}
            <ul class="messages">
                {% for message in messages %}
                    <li class="{{ message.tags }}">{{ message }}</li>
                {% endfor %}
            </ul>
        {% endif %}
        {% block content %}
        {% endblock %}
    </div>

    <footer>
        <p>Angel Adrian Muela Dueñez<br>5 J</p>
    </footer>

    <!-- Bootstrap JS -->
    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.3/dist/js/bootstrap.bundle.min.js" integrity="sha384-YvpcrYf0tY3lHB60NNkmXc5s9fDVZLESaAA55NDzOxhy9GkcIdslK1eN7N6jIeHz" crossorigin="anonymous"></script>
</body>
</html>
3.3.2. 🏠 inicio.html (Página de Inicio)
La página de bienvenida de la ferretería. ¡Tu escaparate principal! 🛍️
Ubicación: UIII_FerreteriaElPatito_0290\app_Ferreteria\templates\inicio.html
Código:
code
Html
{% extends 'base.html' %}
{% load static %}

{% block title %}Inicio - Ferretería el Patito{% endblock %}

{% block content %}
    <div class="text-center my-5">
        <h1 class="display-4">Bienvenido a Ferretería el Patito</h1>
        <p class="lead">Tu tienda de confianza para todas tus necesidades de herramientas y materiales.</p>
    </div>
    <div class="row justify-content-center">
        <div class="col-md-8">
            <p>En Ferretería el Patito, nos dedicamos a ofrecerte la más amplia gama de productos de ferretería con la mejor calidad y al mejor precio. Ya seas un profesional o un entusiasta del bricolaje, aquí encontrarás todo lo que necesitas.</p>
            <p>Explora nuestras categorías de productos, descubre nuestras sucursales y gestiona tu carrito de compras para una experiencia de usuario inigualable. Nuestro compromiso es tu satisfacción y la durabilidad de tus proyectos.</p>
            
            <div class="card mt-4">
                <div class="card-body text-center">
                    <h5 class="card-title">Imagen Representativa de Ferretería el Patito</h5>
                    <img src="{% static 'img/ferreteria_principal.png' %}" class="img-fluid rounded shadow-sm mt-3" alt="Imagen de Ferretería el Patito" style="max-height: 400px; object-fit: cover;">
                    <p class="card-text mt-3 text-muted">Imagen cortesía de Freepik. (Esta imagen debe existir en tu carpeta `static/img` de la app)</p>
                </div>
            </div>
        </div>
    </div>
{% endblock %}
3.3.3. 🛒 carrito.html (Carrito de Compras)
Muestra los productos que el usuario ha añadido a su carrito. ¡Donde se guardan los tesoros! 💰
Ubicación: UIII_FerreteriaElPatito_0290\app_Ferreteria\templates\carrito.html
Código:
code
Html
{% extends 'base.html' %}
{% load static %}

{% block title %}Carrito de Compras{% endblock %}

{% block content %}
    <h1>Tu Carrito</h1>

    <div class="espacio">
        <div class="cuadro" style="max-width: 600px; width: 100%;">
            <div class="cuadrito">
                {% if carrito_items %}
                    <h2>Productos en tu carrito:</h2>
                    <table class="table table-striped">
                        <thead>
                            <tr>
                                <th>Producto</th>
                                <th>Tamaño</th>
                                <th>Precio Unitario</th>
                                <th>Cantidad</th>
                                <th>Total</th>
                                <th>Acciones</th>
                            </tr>
                        </thead>
                        <tbody>
                            {% for item in carrito_items %}
                                <tr>
                                    <td>{{ item.producto.nombre }}</td>
                                    <td>{{ item.producto.tamano|default:"N/A" }}</td>
                                    <td>${{ item.producto.precio|floatformat:2 }}</td>
                                    <td>
                                        <form action="{% url 'actualizar_cantidad_carrito' item.id %}" method="post" style="display: inline-flex; align-items: center;">
                                            {% csrf_token %}
                                            <input type="number" name="cantidad" value="{{ item.cantidad }}" min="1" max="{{ item.producto.stock }}" style="width: 60px; text-align: center; margin-right: 5px;" class="form-control form-control-sm">
                                            <button type="submit" class="btn btn-sm btn-outline-secondary">Actualizar</button>
                                        </form>
                                    </td>
                                    <td>${{ item.get_total_item|floatformat:2 }}</td>
                                    <td>
                                        <form action="{% url 'eliminar_del_carrito' item.id %}" method="post">
                                            {% csrf_token %}
                                            <button type="submit" class="btn btn-sm btn-danger"><i class="fas fa-trash"></i> Eliminar</button>
                                        </form>
                                    </td>
                                </tr>
                            {% endfor %}
                        </tbody>
                    </table>
                    <hr>
                    <div style="text-align: right;">
                        <p>Subtotal de Productos: <strong>${{ subtotal_productos|floatformat:2 }}</strong></p>
                        <p>Costo de Envío: <strong>${{ total_envio|floatformat:2 }}</strong></p>
                        <h3>Total a Pagar: <strong>${{ total_final|floatformat:2 }}</strong></h3>
                        <a href="{% url 'comprar_carrito' %}" class="btn-custom" style="width: 100%; margin-top: 20px; font-size: 1.2em;">Proceder a la Compra</a>
                    </div>
                {% else %}
                    <div class="alert alert-info" role="alert" style="margin-top: 20px;">
                        Tu carrito está vacío. ¡Empieza a agregar productos!
                    </div>
                    <a href="{% url 'ver_productos' %}" class="btn-secondary-custom" style="margin-top: 20px;">Ver Productos</a>
                {% endif %}
            </div>
        </div>
    </div>
{% endblock %}
3.3.4. 🚪 cerrar.html (Página de Cerrar Sesión)
Una página simple para confirmar el cierre de sesión. ¡Hasta pronto! 👋
Ubicación: UIII_FerreteriaElPatito_0290\app_Ferreteria\templates\cerrar.html
Código:
code
Html
{% extends 'base.html' %}
{% load static %}

{% block title %}Cerrar Sesión{% endblock %}

{% block content %}
    <h1>Cerrar Sesión</h1>

    <div class="espacio">
        <div class="cuadro" style="max-width: 400px; width: 100%;">
            <div class="cuadrito" style="text-align: center;">
                <h2>¡Has cerrado sesión!</h2>
                <p>Esperamos verte de nuevo pronto.</p>
                <a href="{% url 'iniciar_sesion' %}" class="btn-custom">Iniciar Sesión</a>
                <a href="{% url 'ver_productos' %}" class="btn-secondary-custom" style="margin-left: 10px;">Ver Productos</a>
            </div>
        </div>
    </div>
{% endblock %}
3.3.5. 🔑 iniciar.html (Página de Iniciar Sesión)
Formulario para que los usuarios accedan a su cuenta.
Ubicación: UIII_FerreteriaElPatito_0290\app_Ferreteria\templates\iniciar.html
Código:
code
Html
{% extends 'base.html' %}
{% load static %}

{% block title %}Iniciar Sesión{% endblock %}

{% block content %}
    <h1>Iniciar Sesión</h1>

    <div class="espacio">
        <div class="cuadro" style="max-width: 400px; width: 100%; height: auto;">
            <div class="cuadrito" style="min-height: 250px; text-align: center;">
                <form method="post" action="{% url 'iniciar_sesion' %}">
                    {% csrf_token %}
                    <div class="mb-3">
                        <label for="correo" class="form-label" style="display: block; text-align: left;">Correo electrónico:</label>
                        <input type="email" class="form-control" id="correo" name="correo" placeholder="correo@ejemplo.com" required>
                    </div>
                    <div class="mb-3">
                        <label for="contrasena" class="form-label" style="display: block; text-align: left;">Contraseña:</label>
                        <input type="password" class="form-control" id="contrasena" name="contrasena" placeholder="**********" required>
                    </div>
                    <button type="submit" class="btn-custom" style="width: 100%; margin-top: 15px;">Iniciar Sesión</button>
                </form>
                <hr>
                <p style="margin-top: 15px;">¿No tienes cuenta?</p>
                <a href="{% url 'crear_cuenta' %}" class="btn-secondary-custom" style="width: 100%;">Crear cuenta</a>
            </div>
        </div>
    </div>
{% endblock %}
3.3.6. 🛍️ productos.html (Catálogo de Productos)
Muestra la lista de productos disponibles, con filtrado por categoría. ¡El corazón de tu inventario! 💖
Ubicación: UIII_FerreteriaElPatito_0290\app_Ferreteria\templates\productos.html
Código:
code
Html
{% extends 'base.html' %}
{% load static %}

{% block title %}Nuestros Productos{% endblock %}

{% block content %}
    <h1>Nuestros Productos</h1>

    <div class="d-flex justify-content-center mb-4">
        <div class="dropdown">
            <button class="btn btn-secondary-custom dropdown-toggle" type="button" id="dropdownMenuButton" data-bs-toggle="dropdown" aria-expanded="false">
                {% if categoria_actual %}
                    Categoría: {{ categoria_actual.nombre }}
                {% else %}
                    Todas las Categorías
                {% endif %}
            </button>
            <ul class="dropdown-menu" aria-labelledby="dropdownMenuButton">
                <li><a class="dropdown-item" href="{% url 'ver_productos' %}">Todas las Categorías</a></li>
                {% for cat in categorias %}
                    <li><a class="dropdown-item" href="{% url 'ver_productos_por_categoria' cat.id %}">{{ cat.nombre }}</a></li>
                {% endfor %}
            </ul>
        </div>
    </div>

    <div class="espacio">
        {% if productos %}
            {% for producto in productos %}
                <div class="cuadro">
                    <div class="cuadrito">
                        {% if producto.imagen_url %}
                            <img src="{{ producto.imagen_url }}" alt="{{ producto.nombre }}" class="product-image">
                        {% else %}
                            <img src="{% static 'img/default_product.png' %}" alt="Sin Imagen" class="product-image">
                        {% endif %}
                        <h2>{{ producto.nombre }}</h2>
                        <p><strong>Stock:</strong> {{ producto.stock }}</p>
                        <p><strong>Tamaño:</strong> {{ producto.tamano|default:"N/A" }}</p>
                        <p><strong>Precio:</strong> ${{ producto.precio|floatformat:2 }}</p>
                        <p><strong>Sucursal:</strong> {{ producto.sucursal.direccion }}</p>
                        <p><strong>Categoría:</strong> {{ producto.categoria.nombre|default:"General" }}</p>
                        <hr>
                        {% if producto.stock > 0 %}
                            <a href="{% url 'agregar_al_carrito' producto.id %}" class="btn-custom" style="width: 100%;">Agregar al Carrito</a>
                        {% else %}
                            <button class="btn-secondary-custom" style="width: 100%; cursor: not-allowed;" disabled>Agotado</button>
                        {% endif %}
                    </div>
                </div>
            {% endfor %}
        {% else %}
            <div class="alert alert-info" role="alert" style="width: 80%;">
                No hay productos disponibles para mostrar {% if categoria_actual %}en la categoría "{{ categoria_actual.nombre }}"{% endif %}.
            </div>
        {% endif %}
    </div>
{% endblock %}
3.3.7. 📍 sucursales.html (Información de Sucursales)
Muestra los detalles de las diferentes sucursales de la ferretería. ¡Encuentra la más cercana! 🗺️
Ubicación: UIII_FerreteriaElPatito_0290\app_Ferreteria\templates\sucursales.html
Código:
code
Html
{% extends 'base.html' %}
{% load static %}

{% block title %}Nuestras Sucursales{% endblock %}

{% block content %}
    <h1>Nuestras Sucursales</h1>
    <h3>En Ciudad Juárez</h3>

    <div class="espacio">
        {% if sucursales %}
            {% for sucursal in sucursales %}
                <div class="cuadro" style="height: auto;">
                    <div class="cuadrito">
                        <h2>Sucursal {{ sucursal.direccion }}</h2>
                        <p><strong>Dirección:</strong> {{ sucursal.direccion }}</p>
                        <p><strong>Teléfono:</strong> {{ sucursal.telefono }}</p>
                        <p><strong>Correo:</strong> {{ sucursal.correo }}</p>
                        <p><strong>Productos Destacados:</strong></p>
                        {% if sucursal.productos.all %}
                            <ul>
                                {% for producto in sucursal.productos.all|slice:":3" %} {# Mostrar solo 3 productos destacados #}
                                    <li>- {{ producto.nombre }} ({{ producto.tamano|default:"N/A" }})</li>
                                {% endfor %}
                                {% if sucursal.productos.count > 3 %}
                                    <li>...y más productos disponibles.</li>
                                {% endif %}
                            </ul>
                        {% else %}
                            <p>No hay productos registrados para esta sucursal.</p>
                        {% endif %}
                        <hr>
                        <a href="{% url 'ver_productos' %}" class="btn-custom" style="width: 100%;">Ver Productos de esta Sucursal</a>
                    </div>
                </div>
            {% endfor %}
        {% else %}
            <div class="alert alert-info" role="alert" style="width: 80%;">
                No hay sucursales registradas en este momento.
            </div>
        {% endif %}
    </div>
{% endblock %}
3.3.8. 📝 crear_cuenta.html (Registro de Usuarios)
Formulario para que los nuevos usuarios creen una cuenta. Incluye confirmación de contraseña y validación básica. ¡El primer paso para ser cliente! 🧑‍💻
Ubicación: UIII_FerreteriaElPatito_0290\app_Ferreteria\templates\crear_cuenta.html
Código:
code
Html
{% extends 'base.html' %}
{% load static %}

{% block title %}Crear Cuenta{% endblock %}

{% block content %}
    <h1>Crear Nueva Cuenta</h1>

    <div class="espacio">
        <div class="cuadro" style="max-width: 450px; width: 100%; height: auto;">
            <div class="cuadrito" style="min-height: 350px; text-align: center;">
                <form method="post" action="{% url 'crear_cuenta' %}">
                    {% csrf_token %}
                    <div class="mb-3">
                        <label for="nombre" class="form-label" style="display: block; text-align: left;">Nombre de Usuario:</label>
                        <input type="text" class="form-control" id="nombre" name="nombre" placeholder="Tu Nombre de Usuario" required>
                    </div>
                    <div class="mb-3">
                        <label for="correo" class="form-label" style="display: block; text-align: left;">Correo electrónico:</label>
                        <input type="email" class="form-control" id="correo" name="correo" placeholder="correo@ejemplo.com" required>
                    </div>
                    <div class="mb-3">
                        <label for="contrasena" class="form-label" style="display: block; text-align: left;">Contraseña:</label>
                        <input type="password" class="form-control" id="contrasena" name="contrasena" placeholder="**********" required>
                    </div>
                    <div class="mb-3">
                        <label for="contrasena_confirm" class="form-label" style="display: block; text-align: left;">Confirmar Contraseña:</label>
                        <input type="password" class="form-control" id="contrasena_confirm" name="contrasena_confirm" placeholder="**********" required>
                    </div>
                    <div class="mb-3">
                        <label for="direccion" class="form-label" style="display: block; text-align: left;">Dirección:</label>
                        <input type="text" class="form-control" id="direccion" name="direccion" placeholder="Calle, Número, Colonia" required>
                    </div>
                    <div class="mb-3">
                        <label for="referencia" class="form-label" style="display: block; text-align: left;">Referencia (opcional):</label>
                        <input type="text" class="form-control" id="referencia" name="referencia" placeholder="Entre calles, cerca de...">
                    </div>
                    <button type="submit" class="btn-custom" style="width: 100%; margin-top: 15px;">Registrarme</button>
                </form>
                <hr>
                <p style="margin-top: 15px;">¿Ya tienes cuenta?</p>
                <a href="{% url 'iniciar_sesion' %}" class="btn-secondary-custom" style="width: 100%;">Iniciar Sesión</a>
            </div>
        </div>
    </div>
{% endblock %}
3.3.9. 📦 pedidos.html (Historial de Pedidos del Usuario)
Muestra una lista de todos los pedidos que un usuario ha realizado. ¡Tu historial de compras! 🧾
Ubicación: UIII_FerreteriaElPatito_0290\app_Ferreteria\templates\pedidos.html
Código:
code
Html
{% extends 'base.html' %}
{% load static %}

{% block title %}Mis Pedidos{% endblock %}

{% block content %}
    <h1>Mis Pedidos</h1>

    <div class="espacio">
        <div class="cuadro" style="max-width: 800px; width: 100%;">
            <div class="cuadrito">
                {% if pedidos %}
                    <h2>Historial de Compras:</h2>
                    <table class="table table-striped">
                        <thead>
                            <tr>
                                <th># Pedido</th>
                                <th>Fecha</th>
                                <th>Total</th>
                                <th>Estado</th>
                                <th>Acciones</th>
                            </tr>
                        </thead>
                        <tbody>
                            {% for pedido in pedidos %}
                                <tr>
                                    <td>{{ pedido.id }}</td>
                                    <td>{{ pedido.fecha_creacion|date:"d M Y H:i" }}</td>
                                    <td>${{ pedido.total_final|floatformat:2 }}</td>
                                    <td><span class="badge bg-secondary">{{ pedido.get_estado_display }}</span></td>
                                    <td>
                                        <a href="{% url 'ver_detalle_pedido' pedido.id %}" class="btn btn-sm btn-info"><i class="fas fa-eye"></i> Ver Detalles</a>
                                    </td>
                                </tr>
                            {% endfor %}
                        </tbody>
                    </table>
                {% else %}
                    <div class="alert alert-info" role="alert" style="margin-top: 20px;">
                        No has realizado ningún pedido aún.
                    </div>
                    <a href="{% url 'ver_productos' %}" class="btn-custom" style="margin-top: 20px;">Explorar Productos</a>
                {% endif %}
            </div>
        </div>
    </div>
{% endblock %}
3.3.10. 📝 detalle_pedido.html (Detalle de un Pedido Específico)
Muestra la información detallada de un pedido en particular, incluyendo los productos comprados.
Ubicación: UIII_FerreteriaElPatito_0290\app_Ferreteria\templates\detalle_pedido.html
Código:
code
Html
{% extends 'base.html' %}
{% load static %}

{% block title %}Detalle del Pedido #{{ pedido.id }}{% endblock %}

{% block content %}
    <h1>Detalle del Pedido #{{ pedido.id }}</h1>

    <div class="espacio">
        <div class="cuadro" style="max-width: 800px; width: 100%;">
            <div class="cuadrito">
                <h2>Información del Pedido:</h2>
                <p><strong>Fecha de Creación:</strong> {{ pedido.fecha_creacion|date:"d M Y H:i" }}</p>
                <p><strong>Estado:</strong> <span class="badge bg-primary">{{ pedido.get_estado_display }}</span></p>
                <p><strong>Dirección de Envío:</strong> {{ pedido.direccion_envio }}</p>
                <p><strong>Método de Pago:</strong> {{ pedido.metodo_pago|default:"No especificado" }}</p>
                <hr>
                <h2>Productos del Pedido:</h2>
                <table class="table table-striped">
                    <thead>
                        <tr>
                            <th>Producto</th>
                            <th>Tamaño</th>
                            <th>Cantidad</th>
                            <th>Precio Unitario</th>
                            <th>Total</th>
                        </tr>
                    </thead>
                    <tbody>
                        {% for detalle in detalles %}
                            <tr>
                                <td>{{ detalle.producto.nombre }}</td>
                                <td>{{ detalle.producto.tamano|default:"N/A" }}</td>
                                <td>{{ detalle.cantidad }}</td>
                                <td>${{ detalle.precio_unitario|floatformat:2 }}</td>
                                <td>${{ detalle.get_total_detalle|floatformat:2 }}</td>
                            </tr>
                        {% endfor %}
                    </tbody>
                </table>
                <hr>
                <div style="text-align: right;">
                    <p>Subtotal de Productos: <strong>${{ pedido.total_productos|floatformat:2 }}</strong></p>
                    <p>Costo de Envío: <strong>${{ pedido.costo_envio|floatformat:2 }}</strong></p>
                    <h3>Total Final: <strong>${{ pedido.total_final|floatformat:2 }}</strong></h3>
                </div>
                <a href="{% url 'ver_pedidos' %}" class="btn-secondary-custom" style="margin-top: 20px;">Volver a Mis Pedidos</a>
            </div>
        </div>
    </div>
{% endblock %}
3.4. 🔗 Configurar URLs de la Aplicación en app_Ferreteria\urls.py
Define las rutas URL específicas para cada vista de la aplicación. ¡Así se navega! 🗺️
Ubicación: Abre el archivo UIII_FerreteriaElPatito_0290\app_Ferreteria\urls.py.
Código: Pega el siguiente código, reemplazando el contenido existente.
code
Python
from django.urls import path
from . import views

urlpatterns = [
    path('', views.inicio, name='inicio'), 
    path('productos/', views.ver_productos, name='ver_productos'),
    path('productos/categoria/<int:categoria_id>/', views.ver_productos, name='ver_productos_por_categoria'),
    path('sucursales/', views.ver_sucursales, name='ver_sucursales'),
    path('carrito/', views.ver_carrito, name='ver_carrito'),
    path('iniciar-sesion/', views.iniciar_sesion, name='iniciar_sesion'),
    path('cerrar-sesion/', views.cerrar_sesion, name='cerrar_sesion'),
    path('crear-cuenta/', views.crear_cuenta, name='crear_cuenta'),
    path('agregar-al-carrito/<int:producto_id>/', views.agregar_al_carrito, name='agregar_al_carrito'),
    path('actualizar-cantidad-carrito/<int:item_id>/', views.actualizar_cantidad_carrito, name='actualizar_cantidad_carrito'),
    path('eliminar-del-carrito/<int:item_id>/', views.eliminar_del_carrito, name='eliminar_del_carrito'),
    path('comprar-carrito/', views.comprar_carrito, name='comprar_carrito'),
    path('pedidos/', views.ver_pedidos, name='ver_pedidos'),
    path('pedidos/<int:pedido_id>/', views.ver_detalle_pedido, name='ver_detalle_pedido'),
]
3.5. ✅ Registrar app_Ferreteria en backend_Ferreteria\settings.py
Informa a Django sobre la existencia de tu nueva aplicación. También se incluyen configuraciones para archivos estáticos y mensajes. ¡Django necesita saber de ella! 📣
Ubicación: Abre el archivo UIII_FerreteriaElPatito_0290\backend_Ferreteria\settings.py.
Acción: Agrega 'app_Ferreteria' a la lista INSTALLED_APPS y asegura que las configuraciones de STATIC_URL y STATICFILES_DIRS sean correctas. También se añaden MESSAGE_TAGS para un estilo consistente de los mensajes de Django.
code
Python
# ... (otras configuraciones) ...

INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'app_Ferreteria',  # ¡IMPORTANTE: Agrega esta línea!
]

# ... (otras configuraciones) ...

# Configuración para static files
import os
STATIC_URL = '/static/'
STATICFILES_DIRS = [
    os.path.join(BASE_DIR, 'app_Ferreteria/static'),
]

# Configuración de MESSAGE_TAGS para estilos personalizados en base.html
from django.contrib.messages import constants as messages
MESSAGE_TAGS = {
    messages.DEBUG: 'alert-info',
    messages.INFO: 'info',
    messages.SUCCESS: 'success',
    messages.WARNING: 'warning',
    messages.ERROR: 'error',
}

# Opcional: Si se hubiera usado un modelo de usuario personalizado que hereda de AbstractUser
# AUTH_USER_MODEL = 'app_Ferreteria.Usuario'
3.6. 🌐 Enlazar URLs de app_Ferreteria en backend_Ferreteria\urls.py
Conecta las URLs de tu aplicación app_Ferreteria con el proyecto principal. ¡La puerta de entrada a tu app! 🚪
Ubicación: Abre el archivo UIII_FerreteriaElPatito_0290\backend_Ferreteria\urls.py.
Acción: Agrega path('', include('app_Ferreteria.urls')) a la lista urlpatterns. También se añade la configuración de static para servir archivos estáticos en modo depuración.
code
Python
from django.contrib import admin
from django.urls import path, include
from django.conf import settings
from django.conf.urls.static import static # Importar para archivos estáticos

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', include('app_Ferreteria.urls')), # ¡IMPORTANTE: Agrega esta línea!
]

# Servir archivos estáticos y de medios durante el desarrollo
if settings.DEBUG:
    urlpatterns += static(settings.STATIC_URL, document_root=settings.STATIC_ROOT)
    # urlpatterns += static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT) # Si usaras archivos de medios
3.7. ⚙️ Registrar Modelos en admin.py
Habilita la administración de tus modelos a través del panel de administración de Django. ¡Tu centro de control! 🎮
Ubicación: Abre el archivo UIII_FerreteriaElPatito_0290\app_Ferreteria\admin.py.
Código: Pega el siguiente código, reemplazando el contenido existente.
code
Python
from django.contrib import admin
from .models import Sucursal, Producto, Usuario, Carrito, Categoria, Pedido, DetallePedido

# Registra tus modelos aquí para que sean accesibles en el panel de administración
admin.site.register(Sucursal)
admin.site.register(Producto)
admin.site.register(Usuario)
admin.site.register(Carrito)
admin.site.register(Categoria)
admin.site.register(Pedido)
admin.site.register(DetallePedido)
Migraciones y Superusuario: Después de registrar los modelos, es una buena práctica volver a ejecutar makemigrations y migrate por si hay cambios en la estructura de admin.py que requieran actualización, y asegurarte de que tu superusuario está actualizado.
code
Bash
python manage.py makemigrations
python manage.py migrate
python manage.py createsuperuser # Si no lo hiciste antes o necesitas actualizar
4. 🖼️ ARCHIVOS ESTÁTICOS (IMÁGENES)
Prepara la carpeta para almacenar tus imágenes y otros archivos estáticos. ¡Los elementos visuales de tu tienda! 📸
4.1. 📁 Crear la Carpeta static y img
Crea la estructura de carpetas necesaria para los archivos estáticos.
Acción: Dentro de app_Ferreteria, crea una carpeta llamada static, y dentro de static, crea otra llamada img.
Estructura resultante: UIII_FerreteriaElPatito_0290\app_Ferreteria\static\img\
Imágenes Necesarias: Coloca los archivos buscar.png, ferreteria_principal.png y default_product.png dentro de la carpeta img. Estas imágenes son referenciadas en las plantillas HTML.
buscar.png: Icono para el enlace "Buscar Productos".
ferreteria_principal.png: Imagen principal para la página de inicio.
default_product.png: Imagen de reemplazo para productos que no tienen una imagen_url definida.
Aquí tienes una representación de cómo se vería la imagen principal de la ferretería:
Generated Image November 18, 2025 - 6:23PM.png
Generated Image November 18, 2025 - 6:23PM.png
5. 🚀 EJECUCIÓN FINAL DEL PROYECTO
Una vez completados todos los pasos, tu proyecto está listo para ser ejecutado y probado. ¡El momento de la verdad! 🥳
5.1. ▶️ Ejecutar el Servidor en el Puerto 8036
Inicia el servidor de desarrollo en el puerto especificado.
Acción: Asegúrate de que tu entorno virtual esté activado y ejecuta el siguiente comando en la terminal.
code
Bash
python manage.py runserver 8036
Verificación: Abre tu navegador y navega a http://127.0.0.1:8036. ¡Tu aplicación de ferretería debería estar completamente funcional! 🎉
Panel de Administración: Puedes acceder al panel de administración en http://127.0.0.1:8036/admin usando las credenciales del superusuario que creaste. Desde allí, podrás añadir sucursales, categorías, productos, usuarios (y ver carritos, pedidos, detalles de pedidos) para poblar la base de datos y probar todas las funcionalidades.
6. 🌳 ESTRUCTURA DE ARCHIVOS DEL PROYECTO (RESUMEN VISUAL)
Aquí se detalla la estructura final de tu proyecto, lo que te ayudará a navegar por el código y verificar que todos los archivos estén en su lugar correcto. ¡Una guía clara! 🧭
code
Code
UIII_FerreteriaElPatito_0290/
├── .venv/                      # Entorno virtual
├── backend_Ferreteria/         # Proyecto Django principal
│   ├── __init__.py
│   ├── asgi.py
│   ├── settings.py             # Configuración del proyecto
│   ├── urls.py                 # URLs principales
│   └── wsgi.py
├── app_Ferreteria/             # Aplicación Django de la ferretería
│   ├── migrations/             # Migraciones de la base de datos
│   ├── static/                 # Archivos estáticos
│   │   └── img/
│   │       ├── buscar.png
│   │       ├── default_product.png
│   │       └── ferreteria_principal.png
│   ├── templates/              # Plantillas HTML
│   │   ├── base.html
│   │   ├── carrito.html
│   │   ├── cerrar.html
│   │   ├── crear_cuenta.html
│   │   ├── detalle_pedido.html
│   │   ├── iniciar.html
│   │   ├── inicio.html
│   │   ├── pedidos.html
│   │   ├── productos.html
│   │   └── sucursales.html
│   ├── __init__.py
│   ├── admin.py                # Registro de modelos en el admin
│   ├── apps.py
│   ├── models.py               # Definición de modelos de datos
│   ├── tests.py
│   ├── urls.py                 # URLs de la aplicación
│   └── views.py                # Lógica de las vistas
├── db.sqlite3                  # Base de datos SQLite (generada por Django)
├── manage.py                   # Utilidad de línea de comandos de Django
└── README.md                   # (Este manual, si lo copias aquí)
¡Eso es todo! 🎉 Este manual debería ser perfecto para tu repositorio de GitHub: claro, conciso y fácil de seguir. ¡Mucho éxito con tu "Ferretería El Patito"! 🦆✨
