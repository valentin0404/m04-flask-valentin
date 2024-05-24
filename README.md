# Introducción
Este proyecto web está desarrollado utilizando Flask, un framework web basado en Python para la diferente lógica que requiere la aplicación. Jinja2, biblioteca para Python que utilizo para generar contenido HTML dinámico.

# Tecnologías Utilizadas
- [**Python**](https://es.wikipedia.org/wiki/Python): Lenguaje utilizado para la lógica de la aplicación.
- [**Flask**](https://es.wikipedia.org/wiki/Flask): Framework para Python para la estructura base de construir la aplicación.
- [**Jinja2**](https://en.wikipedia.org/wiki/Jinja_(template_engine)): Biblioteca para la lógica de la aplicación de la presentación visual, generando HTML dinámico basado en datos.
- [**Bootstrap**](https://en.wikipedia.org/wiki/Bootstrap_(front-end_framework)): Framework para maquetar / diseñar el código HTML de manera responsiva y para avanzar mucho más rápido en este tema, a aparte de la finalidad de aprender maś.

# Estructura del Proyecto
- app.py: Archivo principal de la aplicación Flask que define las rutas, la lógica de la aplicación y la interacción con Jinja2.
- index.html: Plantilla principal de la aplicación que define el diseño general de la página web.
- templates/index.html: Plantilla de inicio para la presentación mínima del proyecto.
- templates/lavanguardia.html: Plantilla para mostrar noticias dependiendo de la categoría escogida de La Vanguardia.
- static/css/style.css: Hoja de estilos CSS para personalizar la apariencia de la aplicación.
(opcional) Otras carpetas o archivos para recursos adicionales como imágenes, fuentes, etc.
- rss/lavanguardia/*.xml: XML formato RSS para efectuar pruebas en local para no acudir a cargar en línea.

# Uso de Flask
El archivo app.py contiene el código principal de la aplicación Flask. Aquí se definen las rutas de la aplicación, que representan diferentes URL o endpoints y la lógica asociada a cada una. Las rutas utilizan el decorador @app.route() para mapear una URL a una función.

Por ejemplo:
```python
    @app.route("/")
    def index():
        return render_template("index.html")
```
Esta ruta mapea la URL raíz (/) a la función index(), que renderiza la plantilla index.html utilizando la función render_template() de Flask.

# Uso de Jinja2
Las plantillas Jinja2 se utilizan para generar HTML dinámico. Estas plantillas contienen código HTML estático combinado con funciones de condición o iterando sobre datos masivos con for en Jinja2 para inyectar datos desde Flask. Las plantillas se almacenan en archivos .html.

Por ejemplo:
```html
{% for item in rss.entries %} <!--Jinja-->
    <div class="col d-flex">
        <div class="card h-100">
            {% if item.media_content %} <!--Jinja-->
                <img src="{{item.media_content[0].url}}" class="card-img-top" alt="{{item.title}}">
            {% endif %} <!--Jinja-->
            <div class="card-body d-flex flex-column">
                <h5 class="card-title">
                    <a href="{{item.link}}" class="text-decoration-none">{{item.title}}</a> <!--Jinja-->
                </h5>
                <div class="mt-auto">
                    <p class="card-text"><small class="text-muted">Modificat a {{ item.modified | date }} <!--Jinja--></small></p>
                    <p class="card-text mt-auto"><small class="text-muted">{{ item.description }} <!--Jinja--></small></p>
                    <p class="card-text"><small class="text-muted">Categoria: {{ item.category }} <!--Jinja--></small></p>
                    <p class="card-text"><small class="text-muted">Escrit per {{ item.author}} <!--Jinja--></small></p>
                </div>
            </div>
            <div class="card-footer">
                <small class="text-muted">{{ item.published | date }} <!--Jinja--></small>
            </div>
        </div>
    </div>
{% endfor %}<!--Jinja-->
```

# Uso de Bootstrap
El código utiliza Bootstrap 5.1.3, un framework CSS para crear interfaces web responsivas, la finalidad de estas aplicación a mi punto de vista. Bootstrap proporciona una amplia colección de componentes prediseñados como carruseles, tarjetas, etc., que se pueden integrar fácilmente en las plantillas.

# Sección de La Vanguardia
La sección de La Vanguardia utiliza una ruta específica (/lavanguardia/<seccio>) para mostrar noticias de una categoría determinada. La función correspondiente en app.py recupera las noticias usando la librería *feedparser* y las pasa a la plantilla lavanguardia.html. La plantilla entonces itera sobre las noticias y las muestra en tarjetas usando Bootstrap.

```python
@app.route('/lavanguardia/<seccio>')
def lavanguardia(seccio):
    rss = get_rss_lavanguardia(seccio)
    return render_template("lavanguardia.html", rss = rss)

def get_rss_lavanguardia(seccio):
    # versió on descarrega l'XML de la web
    # xml = f"https://www.lavanguardia.com/rss/{seccio}.xml"
    
    # versió que fa servir l'XML descarregat
    xml = f"./rss/lavanguardia/{seccio}.xml"
    
    rss = feedparser.parse(xml)
    return rss
```

También utilizamos en app.py una función para formatear las fechas del fichero formato RSS:
```python
def format_date(value, format='%d %b %Y %H:%M'):
    try:
        # Intentamos convertir el valor de la fecha con el primer formato (fecha de creación)
        return datetime.datetime.strptime(value, '%d %b %Y %H:%M:%S %z').strftime(format)
    except ValueError:
        # Si hay un error, intentamos con el segundo formato (fecha de modificación)
        return datetime.datetime.strptime(value, '%Y-%m-%dT%H:%M:%S%z').strftime(format)
```

# Resumen
Este proyecto web demuestra cómo combinar Flask y Jinja2 para crear una aplicación web dinámica que recupera y muestra noticias de La Vanguardia. Aprovechando Bootstrap para un diseño visualmente responsivo y rápido de hacer.