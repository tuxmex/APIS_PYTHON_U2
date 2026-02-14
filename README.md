# 📚 Guía Completa de Ejercicios - APIs de Terceros

## Unidad 2: Implementación de Interfaces de Programación de Aplicaciones de Terceros

Esta guía contiene instrucciones paso a paso para completar todos los ejercicios prácticos de la Unidad 2 usando Flask y APIs gratuitas.

---

## 📋 Tabla de Contenidos

1. [Configuración Inicial](#-configuración-inicial)
2. [Ejercicio 1.1: Sistema de Clima por Ubicación](#-ejercicio-11-sistema-de-clima-por-ubicación)
3. [Ejercicio 1.2: Buscador de Lugares Cercanos](#-ejercicio-12-buscador-de-lugares-cercanos)
4. [Ejercicio 2.1: Analizador de Reddit](#-ejercicio-21-analizador-de-reddit)
5. [Ejercicio 2.2: Dashboard de GitHub](#-ejercicio-22-dashboard-de-github)
6. [Ejercicio 3.1: API REST con SQLite](#-ejercicio-31-api-rest-con-sqlite)
7. [Ejercicio 3.2: Chat con Firebase](#-ejercicio-32-chat-con-firebase)
8. [Ejercicio 4.1: Buscador de Libros](#-ejercicio-41-buscador-de-libros)
9. [Ejercicio 4.2: Conversor de Divisas](#-ejercicio-42-conversor-de-divisas)
10. [Ejercicio 5.1: Buscador de Películas con TMDB API](-ejercicio-51-buscador-de-peliculas-con-tmdb-api)
11. [Ejercicio 5.2: Buscador de Música con Spotify Web API](#-ejercicio-52-buscador-de-música-con-spotify-web-api)
12. [Solución de Problemas](#-solución-de-problemas)

---

## 🚀 Configuración Inicial

### Requisitos Previos
- Python 3.8 o superior instalado
- Editor de código (VS Code, PyCharm, etc.)
- Navegador web moderno
- Conexión a Internet

### Paso 1: Crear el Entorno de Trabajo

```bash
# Crear carpeta del proyecto
mkdir ejercicios-apis
cd ejercicios-apis

# Crear entorno virtual
python -m venv venv

# Activar entorno virtual
# En Windows:
venv\Scripts\activate

# En Mac/Linux:
source venv/bin/activate
```

### Paso 2: Instalar Dependencias Básicas

```bash
# Instalar Flask y requests
pip install flask requests

# Crear archivo requirements.txt
pip freeze > requirements.txt
```

### Paso 3: Estructura de Carpetas

```bash
# Crear estructura de directorios
mkdir templates static
mkdir static/css static/js static/img
```

Tu estructura debe verse así:
```
ejercicios-apis/
├── venv/
├── templates/
├── static/
│   ├── css/
│   ├── js/
│   └── img/
├── app.py
└── requirements.txt
```

---

## 🌍 Ejercicio 1.1: Sistema de Clima por Ubicación

### Objetivo
Desarrollar una aplicación que obtenga la ubicación del usuario y muestre el clima actual.

### APIs Utilizadas
- **OpenWeatherMap API**: Para datos meteorológicos
- **ipapi**: Para geolocalización por IP

### Paso 1: Registrarse en OpenWeatherMap

1. Ir a [https://openweathermap.org/api](https://openweathermap.org/api)
2. Hacer clic en "Sign Up" (esquina superior derecha)
3. Completar el formulario de registro
4. Verificar email
5. Ir a "API keys" en tu cuenta
6. Copiar tu API key (ejemplo: `abc123def456ghi789`)

### Paso 2: Crear el Archivo Python

**Archivo: `clima_app.py`**

```python
from flask import Flask, render_template, jsonify
import requests

app = Flask(__name__)

# Reemplaza con tu API key de OpenWeatherMap
WEATHER_API_KEY = 'TU_API_KEY_AQUI'

@app.route('/')
def index():
    return render_template('clima.html')

@app.route('/api/clima')
def obtener_clima():
    try:
        # 1. Obtener ubicación por IP
        ip_response = requests.get('https://ipapi.co/json/')
        ubicacion = ip_response.json()
        
        ciudad = ubicacion.get('city', 'Ciudad desconocida')
        lat = ubicacion.get('latitude')
        lon = ubicacion.get('longitude')
        
        # 2. Obtener clima de esa ubicación
        weather_url = f'https://api.openweathermap.org/data/2.5/weather'
        params = {
            'lat': lat,
            'lon': lon,
            'appid': WEATHER_API_KEY,
            'units': 'metric',
            'lang': 'es'
        }
        
        clima_response = requests.get(weather_url, params=params)
        clima = clima_response.json()
        
        resultado = {
            'ciudad': ciudad,
            'pais': ubicacion.get('country_name'),
            'temperatura': clima['main']['temp'],
            'descripcion': clima['weather'][0]['description'],
            'humedad': clima['main']['humidity'],
            'viento': clima['wind']['speed'],
            'icono': clima['weather'][0]['icon']
        }
        
        return jsonify(resultado)
        
    except Exception as e:
        return jsonify({'error': str(e)}), 500

if __name__ == '__main__':
    app.run(debug=True)
```

### Paso 3: Crear la Interfaz HTML

**Archivo: `templates/clima.html`**

```html
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Clima por Ubicación</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }
        
        body {
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            min-height: 100vh;
            display: flex;
            justify-content: center;
            align-items: center;
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            padding: 20px;
        }
        
        .clima-card {
            background: rgba(255, 255, 255, 0.1);
            backdrop-filter: blur(10px);
            border-radius: 20px;
            padding: 40px;
            max-width: 500px;
            width: 100%;
            color: white;
            box-shadow: 0 8px 32px rgba(0, 0, 0, 0.3);
        }
        
        h1 {
            text-align: center;
            margin-bottom: 30px;
            font-size: 2.5em;
        }
        
        button {
            width: 100%;
            padding: 15px;
            background: white;
            color: #667eea;
            border: none;
            border-radius: 10px;
            font-size: 18px;
            font-weight: bold;
            cursor: pointer;
            transition: transform 0.2s;
        }
        
        button:hover {
            transform: scale(1.05);
        }
        
        button:active {
            transform: scale(0.98);
        }
        
        #resultado {
            margin-top: 30px;
            text-align: center;
        }
        
        .temperatura {
            font-size: 72px;
            font-weight: bold;
            margin: 20px 0;
        }
        
        .ciudad {
            font-size: 28px;
            margin-bottom: 10px;
        }
        
        .descripcion {
            font-size: 20px;
            margin-bottom: 20px;
            text-transform: capitalize;
        }
        
        .detalles {
            display: flex;
            justify-content: space-around;
            margin-top: 20px;
            padding-top: 20px;
            border-top: 1px solid rgba(255, 255, 255, 0.3);
        }
        
        .detalle-item {
            text-align: center;
        }
        
        .detalle-valor {
            font-size: 24px;
            font-weight: bold;
        }
        
        .detalle-label {
            font-size: 14px;
            opacity: 0.8;
            margin-top: 5px;
        }
        
        .loading {
            text-align: center;
            padding: 20px;
        }
        
        .spinner {
            border: 3px solid rgba(255, 255, 255, 0.3);
            border-top: 3px solid white;
            border-radius: 50%;
            width: 40px;
            height: 40px;
            animation: spin 1s linear infinite;
            margin: 20px auto;
        }
        
        @keyframes spin {
            0% { transform: rotate(0deg); }
            100% { transform: rotate(360deg); }
        }
        
        .error {
            background: rgba(255, 0, 0, 0.2);
            padding: 15px;
            border-radius: 10px;
            margin-top: 20px;
        }
    </style>
</head>
<body>
    <div class="clima-card">
        <h1>🌍 Clima en tu Ubicación</h1>
        <button onclick="obtenerClima()">Obtener Clima Actual</button>
        <div id="resultado"></div>
    </div>

    <script>
        async function obtenerClima() {
            const resultado = document.getElementById('resultado');
            
            // Mostrar loading
            resultado.innerHTML = `
                <div class="loading">
                    <div class="spinner"></div>
                    <p>Obteniendo tu ubicación y clima...</p>
                </div>
            `;
            
            try {
                const response = await fetch('/api/clima');
                const data = await response.json();
                
                if (data.error) {
                    resultado.innerHTML = `
                        <div class="error">
                            <p>❌ Error: ${data.error}</p>
                        </div>
                    `;
                    return;
                }
                
                resultado.innerHTML = `
                    <div class="ciudad">${data.ciudad}, ${data.pais}</div>
                    <div class="temperatura">${Math.round(data.temperatura)}°C</div>
                    <div class="descripcion">${data.descripcion}</div>
                    <img src="https://openweathermap.org/img/wn/${data.icono}@2x.png" alt="Clima">
                    <div class="detalles">
                        <div class="detalle-item">
                            <div class="detalle-valor">💧 ${data.humedad}%</div>
                            <div class="detalle-label">Humedad</div>
                        </div>
                        <div class="detalle-item">
                            <div class="detalle-valor">💨 ${data.viento} m/s</div>
                            <div class="detalle-label">Viento</div>
                        </div>
                    </div>
                `;
            } catch (error) {
                resultado.innerHTML = `
                    <div class="error">
                        <p>❌ Error al obtener datos del clima</p>
                        <p style="font-size: 14px; margin-top: 10px;">${error.message}</p>
                    </div>
                `;
            }
        }
    </script>
</body>
</html>
```

### Paso 4: Ejecutar la Aplicación

```bash
# Asegúrate de estar en el entorno virtual
# Ejecutar la aplicación
python clima_app.py
```

### Paso 5: Probar la Aplicación

1. Abrir navegador en `http://127.0.0.1:5000`
2. Hacer clic en "Obtener Clima Actual"
3. Verificar que se muestre tu ubicación y clima

### 🎯 Resultado Esperado
- La aplicación detecta tu ubicación automáticamente
- Muestra temperatura, descripción del clima, humedad y viento
- Interfaz moderna con degradado y efectos visuales

---

### Si te falla ejecutalo sin ipapi
```python
from flask import Flask, render_template, jsonify
import requests

app = Flask(__name__)

WEATHER_API_KEY = 'TU_API_KEY_AQUI'

@app.route('/')
def index():
    return render_template('clima.html')

@app.route('/api/clima')
def obtener_clima():
    # Usar coordenadas fijas (cambiar por tu ciudad)
    ciudades = {
        'mexico': {'lat': 19.4326, 'lon': -99.1332, 'nombre': 'Ciudad de México'},
        'monterrey': {'lat': 25.6866, 'lon': -100.3161, 'nombre': 'Monterrey'},
        'guadalajara': {'lat': 20.6597, 'lon': -103.3496, 'nombre': 'Guadalajara'}
    }
    
    ciudad = ciudades['mexico']  # Cambiar según tu ubicación
    
    try:
        url = 'https://api.openweathermap.org/data/2.5/weather'
        params = {
            'lat': ciudad['lat'],
            'lon': ciudad['lon'],
            'appid': WEATHER_API_KEY,
            'units': 'metric',
            'lang': 'es'
        }
        
        response = requests.get(url, params=params)
        clima = response.json()
        
        return jsonify({
            'ciudad': ciudad['nombre'],
            'pais': 'México',
            'temperatura': clima['main']['temp'],
            'descripcion': clima['weather'][0]['description'],
            'humedad': clima['main']['humidity'],
            'viento': clima['wind']['speed'],
            'icono': clima['weather'][0]['icon']
        })
        
    except Exception as e:
        return jsonify({'error': str(e)}), 500

if __name__ == '__main__':
    app.run(debug=True)
```

## 📍 Ejercicio 1.2: Buscador de Lugares Cercanos

### Objetivo
Crear una aplicación que muestre lugares cercanos (restaurantes, hospitales, tiendas) usando Overpass API.

### API Utilizada
- **Overpass API** (OpenStreetMap): API gratuita sin registro

### Paso 1: Crear el Archivo Python

**Archivo: `lugares_app.py`**

```python
from flask import Flask, render_template, request, jsonify
import requests

app = Flask(__name__)

@app.route('/')
def index():
    return render_template('lugares.html')

@app.route('/api/lugares')
def buscar_lugares():
    lat = request.args.get('lat', type=float)
    lon = request.args.get('lon', type=float)
    tipo = request.args.get('tipo', 'restaurant')
    radio = request.args.get('radio', 1000, type=int)
    
    # Mapeo de tipos a queries de OSM
    tipos_osm = {
        'restaurant': 'amenity=restaurant',
        'hospital': 'amenity=hospital',
        'cafe': 'amenity=cafe',
        'farmacia': 'amenity=pharmacy',
        'tienda': 'shop=supermarket',
        'gasolinera': 'amenity=fuel',
        'banco': 'amenity=bank',
        'hotel': 'tourism=hotel'
    }
    
    query = tipos_osm.get(tipo, 'amenity=restaurant')
    
    # Consulta Overpass API
    overpass_url = "http://overpass-api.de/api/interpreter"
    overpass_query = f"""
    [out:json];
    (
      node[{query}](around:{radio},{lat},{lon});
      way[{query}](around:{radio},{lat},{lon});
    );
    out center;
    """
    
    try:
        response = requests.get(overpass_url, params={'data': overpass_query}, timeout=30)
        data = response.json()
        
        lugares = []
        for elemento in data['elements'][:20]:  # Limitar a 20 resultados
            if 'center' in elemento:
                coords = elemento['center']
            elif 'lat' in elemento:
                coords = {'lat': elemento['lat'], 'lon': elemento['lon']}
            else:
                continue
            
            tags = elemento.get('tags', {})
            lugares.append({
                'nombre': tags.get('name', 'Sin nombre'),
                'direccion': tags.get('addr:street', '') + ' ' + tags.get('addr:housenumber', ''),
                'lat': coords['lat'],
                'lon': coords['lon'],
                'tipo': tags.get('amenity') or tags.get('shop') or tags.get('tourism', ''),
                'telefono': tags.get('phone', ''),
                'horario': tags.get('opening_hours', '')
            })
        
        return jsonify(lugares)
        
    except Exception as e:
        return jsonify({'error': str(e)}), 500

if __name__ == '__main__':
    app.run(debug=True)
```

### Paso 2: Crear la Interfaz HTML

**Archivo: `templates/lugares.html`**

```html
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Buscador de Lugares Cercanos</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }
        
        body {
            font-family: Arial, sans-serif;
            background: #f5f5f5;
        }
        
        .container {
            max-width: 1200px;
            margin: 0 auto;
            padding: 20px;
        }
        
        header {
            background: linear-gradient(135deg, #2196F3, #1976D2);
            color: white;
            padding: 30px;
            border-radius: 10px;
            margin-bottom: 20px;
            text-align: center;
        }
        
        h1 {
            font-size: 2.5em;
            margin-bottom: 10px;
        }
        
        .controles {
            background: white;
            padding: 20px;
            border-radius: 10px;
            box-shadow: 0 2px 10px rgba(0,0,0,0.1);
            margin-bottom: 20px;
        }
        
        .control-group {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
            gap: 15px;
            margin-bottom: 15px;
        }
        
        label {
            display: block;
            margin-bottom: 5px;
            font-weight: bold;
            color: #333;
        }
        
        select, input {
            width: 100%;
            padding: 10px;
            border: 2px solid #ddd;
            border-radius: 5px;
            font-size: 16px;
        }
        
        button {
            width: 100%;
            padding: 15px;
            background: #2196F3;
            color: white;
            border: none;
            border-radius: 5px;
            font-size: 18px;
            font-weight: bold;
            cursor: pointer;
            transition: background 0.3s;
        }
        
        button:hover {
            background: #1976D2;
        }
        
        button:disabled {
            background: #ccc;
            cursor: not-allowed;
        }
        
        #resultados {
            display: grid;
            grid-template-columns: repeat(auto-fill, minmax(300px, 1fr));
            gap: 20px;
        }
        
        .lugar-card {
            background: white;
            padding: 20px;
            border-radius: 10px;
            box-shadow: 0 2px 10px rgba(0,0,0,0.1);
            transition: transform 0.2s;
        }
        
        .lugar-card:hover {
            transform: translateY(-5px);
            box-shadow: 0 4px 20px rgba(0,0,0,0.15);
        }
        
        .lugar-nombre {
            font-size: 20px;
            font-weight: bold;
            color: #2196F3;
            margin-bottom: 10px;
        }
        
        .lugar-info {
            color: #666;
            margin: 5px 0;
            display: flex;
            align-items: center;
        }
        
        .lugar-info span {
            margin-right: 5px;
        }
        
        .loading {
            text-align: center;
            padding: 40px;
            font-size: 18px;
            color: #666;
        }
        
        .error {
            background: #ffebee;
            color: #c62828;
            padding: 15px;
            border-radius: 5px;
            margin: 20px 0;
        }
        
        .no-resultados {
            text-align: center;
            padding: 40px;
            color: #666;
            font-size: 18px;
        }
    </style>
</head>
<body>
    <div class="container">
        <header>
            <h1>📍 Buscador de Lugares Cercanos</h1>
            <p>Encuentra restaurantes, hospitales, tiendas y más cerca de ti</p>
        </header>
        
        <div class="controles">
            <div class="control-group">
                <div>
                    <label for="tipo">Tipo de Lugar:</label>
                    <select id="tipo">
                        <option value="restaurant">🍽️ Restaurantes</option>
                        <option value="cafe">☕ Cafeterías</option>
                        <option value="hospital">🏥 Hospitales</option>
                        <option value="farmacia">💊 Farmacias</option>
                        <option value="tienda">🛒 Supermercados</option>
                        <option value="gasolinera">⛽ Gasolineras</option>
                        <option value="banco">🏦 Bancos</option>
                        <option value="hotel">🏨 Hoteles</option>
                    </select>
                </div>
                
                <div>
                    <label for="radio">Radio de búsqueda:</label>
                    <select id="radio">
                        <option value="500">500 metros</option>
                        <option value="1000" selected>1 kilómetro</option>
                        <option value="2000">2 kilómetros</option>
                        <option value="5000">5 kilómetros</option>
                    </select>
                </div>
            </div>
            
            <button onclick="buscarLugares()" id="btnBuscar">Buscar Lugares Cercanos</button>
        </div>
        
        <div id="resultados"></div>
    </div>

    <script>
        let miUbicacion = null;
        
        // Obtener ubicación al cargar la página
        if (navigator.geolocation) {
            navigator.geolocation.getCurrentPosition(
                position => {
                    miUbicacion = {
                        lat: position.coords.latitude,
                        lon: position.coords.longitude
                    };
                    console.log('Ubicación obtenida:', miUbicacion);
                },
                error => {
                    console.error('Error al obtener ubicación:', error);
                    // Ubicación por defecto (ejemplo: Ciudad de México)
                    miUbicacion = { lat: 19.4326, lon: -99.1332 };
                }
            );
        }
        
        async function buscarLugares() {
            const resultados = document.getElementById('resultados');
            const btnBuscar = document.getElementById('btnBuscar');
            
            if (!miUbicacion) {
                alert('No se pudo obtener tu ubicación. Por favor, permite el acceso a la ubicación.');
                return;
            }
            
            const tipo = document.getElementById('tipo').value;
            const radio = document.getElementById('radio').value;
            
            btnBuscar.disabled = true;
            resultados.innerHTML = '<div class="loading">🔍 Buscando lugares cercanos...</div>';
            
            try {
                const url = `/api/lugares?lat=${miUbicacion.lat}&lon=${miUbicacion.lon}&tipo=${tipo}&radio=${radio}`;
                const response = await fetch(url);
                const data = await response.json();
                
                if (data.error) {
                    resultados.innerHTML = `<div class="error">❌ Error: ${data.error}</div>`;
                    return;
                }
                
                if (data.length === 0) {
                    resultados.innerHTML = `
                        <div class="no-resultados">
                            😔 No se encontraron lugares de este tipo en el radio especificado.
                            <br>Intenta ampliar el radio de búsqueda.
                        </div>
                    `;
                    return;
                }
                
                resultados.innerHTML = data.map(lugar => `
                    <div class="lugar-card">
                        <div class="lugar-nombre">${lugar.nombre}</div>
                        ${lugar.direccion ? `<div class="lugar-info"><span>📍</span>${lugar.direccion}</div>` : ''}
                        ${lugar.telefono ? `<div class="lugar-info"><span>📞</span>${lugar.telefono}</div>` : ''}
                        ${lugar.horario ? `<div class="lugar-info"><span>🕐</span>${lugar.horario}</div>` : ''}
                        <div class="lugar-info">
                            <span>🗺️</span>
                            <a href="https://www.google.com/maps?q=${lugar.lat},${lugar.lon}" target="_blank">
                                Ver en Google Maps
                            </a>
                        </div>
                    </div>
                `).join('');
                
            } catch (error) {
                resultados.innerHTML = `<div class="error">❌ Error al buscar lugares: ${error.message}</div>`;
            } finally {
                btnBuscar.disabled = false;
            }
        }
    </script>
</body>
</html>
```

### Paso 3: Ejecutar y Probar

```bash
python lugares_app.py
```

1. Abrir `http://127.0.0.1:5000`
2. Permitir acceso a la ubicación cuando el navegador lo solicite
3. Seleccionar tipo de lugar y radio
4. Hacer clic en "Buscar Lugares Cercanos"

### 🎯 Resultado Esperado
- Muestra lugares cercanos según el tipo seleccionado
- Cards con información de cada lugar
- Enlaces a Google Maps para cada ubicación

---

## 📱 Ejercicio 2.1: Analizador de Reddit

### Objetivo
Analizar posts y tendencias de Reddit usando su API pública.

### API Utilizada
- **Reddit JSON API**: Sin autenticación requerida para lectura básica

### Paso 1: Crear el Archivo Python

**Archivo: `reddit_app.py`**

```python
from flask import Flask, render_template, request, jsonify
import requests
from datetime import datetime

app = Flask(__name__)

@app.route('/')
def index():
    return render_template('reddit.html')

@app.route('/api/reddit/posts')
def obtener_posts_reddit():
    subreddit = request.args.get('subreddit', 'python')
    filtro = request.args.get('filtro', 'hot')  # hot, new, top
    limit = request.args.get('limit', 10, type=int)
    
    # Reddit permite llamadas sin autenticación (limitadas)
    url = f'https://www.reddit.com/r/{subreddit}/{filtro}.json'
    headers = {'User-Agent': 'Mozilla/5.0 (FlaskApp/1.0)'}
    
    try:
        response = requests.get(url, headers=headers, params={'limit': limit})
        
        if response.status_code == 404:
            return jsonify({'error': 'Subreddit no encontrado'}), 404
            
        data = response.json()
        
        posts = []
        for post in data['data']['children']:
            post_data = post['data']
            
            # Convertir timestamp a fecha legible
            fecha = datetime.fromtimestamp(post_data['created_utc'])
            
            posts.append({
                'titulo': post_data['title'],
                'autor': post_data['author'],
                'puntos': post_data['score'],
                'comentarios': post_data['num_comments'],
                'url': f"https://reddit.com{post_data['permalink']}",
                'url_completa': post_data.get('url', ''),
                'fecha': fecha.strftime('%Y-%m-%d %H:%M'),
                'thumbnail': post_data.get('thumbnail') if post_data.get('thumbnail') not in ['self', 'default', ''] else None,
                'selftext': post_data.get('selftext', '')[:200] + '...' if post_data.get('selftext') else ''
            })
        
        return jsonify({
            'subreddit': subreddit,
            'posts': posts
        })
        
    except Exception as e:
        return jsonify({'error': str(e)}), 500

@app.route('/api/reddit/buscar')
def buscar_reddit():
    query = request.args.get('q', '')
    limit = request.args.get('limit', 10, type=int)
    
    if not query:
        return jsonify({'error': 'Consulta requerida'}), 400
    
    url = f'https://www.reddit.com/search.json'
    headers = {'User-Agent': 'Mozilla/5.0 (FlaskApp/1.0)'}
    params = {'q': query, 'limit': limit}
    
    try:
        response = requests.get(url, headers=headers, params=params)
        data = response.json()
        
        resultados = []
        for post in data['data']['children']:
            post_data = post['data']
            fecha = datetime.fromtimestamp(post_data['created_utc'])
            
            resultados.append({
                'titulo': post_data['title'],
                'subreddit': post_data['subreddit'],
                'autor': post_data['author'],
                'puntos': post_data['score'],
                'comentarios': post_data['num_comments'],
                'url': f"https://reddit.com{post_data['permalink']}",
                'fecha': fecha.strftime('%Y-%m-%d %H:%M')
            })
        
        return jsonify(resultados)
        
    except Exception as e:
        return jsonify({'error': str(e)}), 500

@app.route('/api/reddit/subreddits/populares')
def subreddits_populares():
    """Lista de subreddits populares en español e inglés"""
    subreddits = [
        {'nombre': 'python', 'descripcion': 'Python programming'},
        {'nombre': 'learnprogramming', 'descripcion': 'Aprender programación'},
        {'nombre': 'webdev', 'descripcion': 'Desarrollo web'},
        {'nombre': 'javascript', 'descripcion': 'JavaScript'},
        {'nombre': 'flask', 'descripcion': 'Flask framework'},
        {'nombre': 'technology', 'descripcion': 'Tecnología'},
        {'nombre': 'programming', 'descripcion': 'Programación general'},
        {'nombre': 'mexico', 'descripcion': 'México'},
        {'nombre': 'argentina', 'descripcion': 'Argentina'},
        {'nombre': 'es', 'descripcion': 'Español'}
    ]
    return jsonify(subreddits)

if __name__ == '__main__':
    app.run(debug=True)
```

### Paso 2: Crear la Interfaz HTML

**Archivo: `templates/reddit.html`**

```html
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Analizador de Reddit</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }
        
        body {
            font-family: Arial, sans-serif;
            background: #DAE0E6;
        }
        
        .container {
            max-width: 1000px;
            margin: 0 auto;
            padding: 20px;
        }
        
        header {
            background: #FF4500;
            color: white;
            padding: 20px;
            border-radius: 5px;
            margin-bottom: 20px;
        }
        
        h1 {
            display: flex;
            align-items: center;
            gap: 10px;
        }
        
        .controles {
            background: white;
            padding: 20px;
            border-radius: 5px;
            margin-bottom: 20px;
            box-shadow: 0 2px 4px rgba(0,0,0,0.1);
        }
        
        .input-group {
            display: flex;
            gap: 10px;
            margin-bottom: 15px;
            flex-wrap: wrap;
        }
        
        input, select, button {
            padding: 10px;
            border: 1px solid #ddd;
            border-radius: 4px;
            font-size: 14px;
        }
        
        input {
            flex: 1;
            min-width: 200px;
        }
        
        button {
            background: #FF4500;
            color: white;
            border: none;
            cursor: pointer;
            font-weight: bold;
        }
        
        button:hover {
            background: #E03D00;
        }
        
        .tabs {
            display: flex;
            gap: 10px;
            margin-bottom: 20px;
        }
        
        .tab {
            padding: 10px 20px;
            background: white;
            border: none;
            border-radius: 5px 5px 0 0;
            cursor: pointer;
            font-weight: bold;
        }
        
        .tab.active {
            background: #FF4500;
            color: white;
        }
        
        .post {
            background: white;
            padding: 15px;
            margin-bottom: 10px;
            border-radius: 5px;
            border-left: 3px solid #FF4500;
            box-shadow: 0 1px 3px rgba(0,0,0,0.1);
        }
        
        .post-header {
            display: flex;
            justify-content: space-between;
            align-items: start;
            margin-bottom: 10px;
        }
        
        .post-titulo {
            font-size: 18px;
            font-weight: bold;
            color: #1c1c1c;
            margin-bottom: 5px;
            flex: 1;
        }
        
        .post-titulo:hover {
            color: #FF4500;
        }
        
        .post-meta {
            display: flex;
            gap: 15px;
            font-size: 12px;
            color: #7c7c7c;
            flex-wrap: wrap;
        }
        
        .post-meta span {
            display: flex;
            align-items: center;
            gap: 5px;
        }
        
        .post-stats {
            display: flex;
            gap: 15px;
            margin-top: 10px;
            padding-top: 10px;
            border-top: 1px solid #eee;
        }
        
        .stat {
            display: flex;
            align-items: center;
            gap: 5px;
            font-size: 14px;
            color: #666;
        }
        
        .loading {
            text-align: center;
            padding: 40px;
            color: #666;
        }
        
        .error {
            background: #ffebee;
            color: #c62828;
            padding: 15px;
            border-radius: 5px;
            margin: 20px 0;
        }
        
        .subreddit-tag {
            background: #FF4500;
            color: white;
            padding: 3px 8px;
            border-radius: 3px;
            font-size: 12px;
            font-weight: bold;
        }
    </style>
</head>
<body>
    <div class="container">
        <header>
            <h1>
                <span>🤖</span>
                <span>Analizador de Reddit</span>
            </h1>
            <p>Explora subreddits y busca contenido en Reddit</p>
        </header>
        
        <div class="tabs">
            <button class="tab active" onclick="cambiarTab('subreddit')">Por Subreddit</button>
            <button class="tab" onclick="cambiarTab('busqueda')">Buscar</button>
        </div>
        
        <div class="controles" id="controles-subreddit">
            <div class="input-group">
                <input type="text" id="subreddit" placeholder="Nombre del subreddit (ej: python)" value="python">
                <select id="filtro">
                    <option value="hot">🔥 Hot</option>
                    <option value="new">🆕 Nuevos</option>
                    <option value="top">⭐ Top</option>
                </select>
                <select id="limit">
                    <option value="10">10 posts</option>
                    <option value="25">25 posts</option>
                    <option value="50">50 posts</option>
                </select>
                <button onclick="cargarSubreddit()">Cargar Posts</button>
            </div>
        </div>
        
        <div class="controles" id="controles-busqueda" style="display: none;">
            <div class="input-group">
                <input type="text" id="busqueda" placeholder="Buscar en Reddit...">
                <button onclick="buscarReddit()">Buscar</button>
            </div>
        </div>
        
        <div id="resultados"></div>
    </div>

    <script>
        function cambiarTab(tab) {
            // Actualizar tabs activos
            document.querySelectorAll('.tab').forEach(t => t.classList.remove('active'));
            event.target.classList.add('active');
            
            // Mostrar/ocultar controles
            if (tab === 'subreddit') {
                document.getElementById('controles-subreddit').style.display = 'block';
                document.getElementById('controles-busqueda').style.display = 'none';
            } else {
                document.getElementById('controles-subreddit').style.display = 'none';
                document.getElementById('controles-busqueda').style.display = 'block';
            }
            
            document.getElementById('resultados').innerHTML = '';
        }
        
        async function cargarSubreddit() {
            const resultados = document.getElementById('resultados');
            const subreddit = document.getElementById('subreddit').value;
            const filtro = document.getElementById('filtro').value;
            const limit = document.getElementById('limit').value;
            
            if (!subreddit) {
                alert('Por favor ingresa un subreddit');
                return;
            }
            
            resultados.innerHTML = '<div class="loading">⏳ Cargando posts...</div>';
            
            try {
                const url = `/api/reddit/posts?subreddit=${subreddit}&filtro=${filtro}&limit=${limit}`;
                const response = await fetch(url);
                const data = await response.json();
                
                if (data.error) {
                    resultados.innerHTML = `<div class="error">❌ ${data.error}</div>`;
                    return;
                }
                
                mostrarPosts(data.posts, data.subreddit);
                
            } catch (error) {
                resultados.innerHTML = `<div class="error">❌ Error: ${error.message}</div>`;
            }
        }
        
        async function buscarReddit() {
            const resultados = document.getElementById('resultados');
            const query = document.getElementById('busqueda').value;
            
            if (!query) {
                alert('Por favor ingresa un término de búsqueda');
                return;
            }
            
            resultados.innerHTML = '<div class="loading">🔍 Buscando...</div>';
            
            try {
                const url = `/api/reddit/buscar?q=${encodeURIComponent(query)}`;
                const response = await fetch(url);
                const data = await response.json();
                
                if (data.error) {
                    resultados.innerHTML = `<div class="error">❌ ${data.error}</div>`;
                    return;
                }
                
                mostrarPosts(data);
                
            } catch (error) {
                resultados.innerHTML = `<div class="error">❌ Error: ${error.message}</div>`;
            }
        }
        
        function mostrarPosts(posts, subreddit = null) {
            const resultados = document.getElementById('resultados');
            
            if (posts.length === 0) {
                resultados.innerHTML = '<div class="loading">No se encontraron posts</div>';
                return;
            }
            
            resultados.innerHTML = posts.map(post => `
                <div class="post">
                    <div class="post-header">
                        <div style="flex: 1;">
                            <a href="${post.url}" target="_blank" style="text-decoration: none;">
                                <div class="post-titulo">${post.titulo}</div>
                            </a>
                            ${post.selftext ? `<p style="color: #666; margin-top: 10px;">${post.selftext}</p>` : ''}
                        </div>
                        ${post.thumbnail ? `<img src="${post.thumbnail}" style="width: 70px; height: 70px; object-fit: cover; border-radius: 5px;">` : ''}
                    </div>
                    <div class="post-meta">
                        ${post.subreddit ? `<span class="subreddit-tag">r/${post.subreddit}</span>` : ''}
                        <span>👤 u/${post.autor}</span>
                        <span>📅 ${post.fecha}</span>
                    </div>
                    <div class="post-stats">
                        <div class="stat">
                            <span>⬆️</span>
                            <span>${post.puntos.toLocaleString()} puntos</span>
                        </div>
                        <div class="stat">
                            <span>💬</span>
                            <span>${post.comentarios} comentarios</span>
                        </div>
                    </div>
                </div>
            `).join('');
        }
        
        // Cargar posts por defecto al iniciar
        window.onload = () => cargarSubreddit();
    </script>
</body>
</html>
```

### Paso 3: Ejecutar y Probar

```bash
python reddit_app.py
```

1. Abrir `http://127.0.0.1:5000`
2. Probar con diferentes subreddits (python, webdev, technology, etc.)
3. Cambiar entre filtros (Hot, New, Top)
4. Probar la búsqueda global

### 🎯 Resultado Esperado
- Visualización de posts de Reddit con puntos y comentarios
- Cambio entre diferentes subreddits y filtros
- Búsqueda global en Reddit

---

## 🐙 Ejercicio 2.2: Dashboard de GitHub

### Objetivo
Crear un dashboard que muestre estadísticas de usuarios y repositorios de GitHub.

### API Utilizada
- **GitHub REST API**: Sin autenticación para consultas básicas

### Paso 1: Crear el Archivo Python

**Archivo: `github_app.py`**

```python
from flask import Flask, render_template, request, jsonify
import requests

app = Flask(__name__)

# GitHub API base URL
GITHUB_API = 'https://api.github.com'

@app.route('/')
def index():
    return render_template('github.html')

@app.route('/api/github/usuario/<username>')
def obtener_usuario_github(username):
    try:
        # Obtener información del usuario
        user_response = requests.get(f'{GITHUB_API}/users/{username}')
        
        if user_response.status_code == 404:
            return jsonify({'error': 'Usuario no encontrado'}), 404
        
        usuario = user_response.json()
        
        # Obtener repositorios
        repos_response = requests.get(f'{GITHUB_API}/users/{username}/repos?per_page=100')
        repos = repos_response.json()
        
        # Calcular estadísticas
        total_stars = sum(repo['stargazers_count'] for repo in repos)
        total_forks = sum(repo['forks_count'] for repo in repos)
        
        # Contar lenguajes
        lenguajes = {}
        for repo in repos:
            lang = repo['language']
            if lang:
                lenguajes[lang] = lenguajes.get(lang, 0) + 1
        
        # Top 3 lenguajes
        top_lenguajes = sorted(lenguajes.items(), key=lambda x: x[1], reverse=True)[:3]
        
        resultado = {
            'nombre': usuario.get('name') or username,
            'username': usuario['login'],
            'bio': usuario.get('bio'),
            'avatar': usuario['avatar_url'],
            'repositorios': usuario['public_repos'],
            'seguidores': usuario['followers'],
            'siguiendo': usuario['following'],
            'ubicacion': usuario.get('location'),
            'empresa': usuario.get('company'),
            'blog': usuario.get('blog'),
            'twitter': usuario.get('twitter_username'),
            'creado': usuario['created_at'][:10],
            'total_stars': total_stars,
            'total_forks': total_forks,
            'lenguajes': dict(lenguajes),
            'top_lenguajes': [{'lenguaje': l[0], 'repos': l[1]} for l in top_lenguajes],
            'repos_destacados': [
                {
                    'nombre': repo['name'],
                    'descripcion': repo['description'],
                    'stars': repo['stargazers_count'],
                    'forks': repo['forks_count'],
                    'lenguaje': repo['language'],
                    'url': repo['html_url'],
                    'actualizado': repo['updated_at'][:10]
                }
                for repo in sorted(repos, key=lambda x: x['stargazers_count'], reverse=True)[:5]
            ]
        }
        
        return jsonify(resultado)
        
    except Exception as e:
        return jsonify({'error': str(e)}), 500

@app.route('/api/github/trending')
def repositorios_trending():
    """Buscar repos más populares de la última semana"""
    from datetime import datetime, timedelta
    
    fecha = (datetime.now() - timedelta(days=7)).strftime('%Y-%m-%d')
    url = f'{GITHUB_API}/search/repositories'
    params = {
        'q': f'created:>{fecha}',
        'sort': 'stars',
        'order': 'desc',
        'per_page': 10
    }
    
    try:
        response = requests.get(url, params=params)
        data = response.json()
        
        repos = [
            {
                'nombre': repo['full_name'],
                'descripcion': repo['description'],
                'stars': repo['stargazers_count'],
                'forks': repo['forks_count'],
                'lenguaje': repo['language'],
                'url': repo['html_url'],
                'propietario': repo['owner']['login'],
                'avatar': repo['owner']['avatar_url']
            }
            for repo in data.get('items', [])
        ]
        
        return jsonify(repos)
        
    except Exception as e:
        return jsonify({'error': str(e)}), 500

@app.route('/api/github/buscar/repos')
def buscar_repos():
    query = request.args.get('q', '')
    lenguaje = request.args.get('lenguaje', '')
    
    if not query:
        return jsonify({'error': 'Consulta requerida'}), 400
    
    # Construir query
    search_query = query
    if lenguaje:
        search_query += f' language:{lenguaje}'
    
    params = {
        'q': search_query,
        'sort': 'stars',
        'order': 'desc',
        'per_page': 15
    }
    
    try:
        response = requests.get(f'{GITHUB_API}/search/repositories', params=params)
        data = response.json()
        
        repos = [
            {
                'nombre': repo['full_name'],
                'descripcion': repo['description'],
                'stars': repo['stargazers_count'],
                'lenguaje': repo['language'],
                'url': repo['html_url']
            }
            for repo in data.get('items', [])
        ]
        
        return jsonify(repos)
        
    except Exception as e:
        return jsonify({'error': str(e)}), 500

if __name__ == '__main__':
    app.run(debug=True)
```

### Paso 2: Crear la Interfaz HTML

**Archivo: `templates/github.html`**

```html
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>GitHub Dashboard</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }
        
        body {
            font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Arial, sans-serif;
            background: #0d1117;
            color: #c9d1d9;
        }
        
        .container {
            max-width: 1200px;
            margin: 0 auto;
            padding: 20px;
        }
        
        header {
            background: #161b22;
            padding: 20px;
            border-radius: 6px;
            margin-bottom: 20px;
            border: 1px solid #30363d;
        }
        
        h1 {
            color: #58a6ff;
            display: flex;
            align-items: center;
            gap: 10px;
        }
        
        .search-box {
            background: #161b22;
            padding: 20px;
            border-radius: 6px;
            margin-bottom: 20px;
            border: 1px solid #30363d;
        }
        
        input {
            width: 100%;
            padding: 12px;
            background: #0d1117;
            border: 1px solid #30363d;
            border-radius: 6px;
            color: #c9d1d9;
            font-size: 16px;
        }
        
        input:focus {
            outline: none;
            border-color: #58a6ff;
        }
        
        button {
            margin-top: 10px;
            width: 100%;
            padding: 12px;
            background: #238636;
            color: white;
            border: none;
            border-radius: 6px;
            font-size: 16px;
            font-weight: bold;
            cursor: pointer;
        }
        
        button:hover {
            background: #2ea043;
        }
        
        .user-card {
            background: #161b22;
            padding: 30px;
            border-radius: 6px;
            border: 1px solid #30363d;
            margin-bottom: 20px;
        }
        
        .user-header {
            display: flex;
            gap: 20px;
            margin-bottom: 20px;
        }
        
        .avatar {
            width: 100px;
            height: 100px;
            border-radius: 50%;
            border: 3px solid #58a6ff;
        }
        
        .user-info h2 {
            color: #58a6ff;
            margin-bottom: 5px;
        }
        
        .user-bio {
            color: #8b949e;
            margin: 10px 0;
        }
        
        .stats {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(150px, 1fr));
            gap: 15px;
            margin-top: 20px;
        }
        
        .stat-box {
            background: #0d1117;
            padding: 15px;
            border-radius: 6px;
            text-align: center;
            border: 1px solid #30363d;
        }
        
        .stat-value {
            font-size: 24px;
            font-weight: bold;
            color: #58a6ff;
        }
        
        .stat-label {
            font-size: 12px;
            color: #8b949e;
            margin-top: 5px;
        }
        
        .repos-section {
            background: #161b22;
            padding: 20px;
            border-radius: 6px;
            border: 1px solid #30363d;
        }
        
        .repo-card {
            background: #0d1117;
            padding: 15px;
            margin-bottom: 10px;
            border-radius: 6px;
            border: 1px solid #30363d;
            transition: border-color 0.2s;
        }
        
        .repo-card:hover {
            border-color: #58a6ff;
        }
        
        .repo-name {
            color: #58a6ff;
            font-weight: bold;
            font-size: 16px;
            margin-bottom: 5px;
        }
        
        .repo-desc {
            color: #8b949e;
            font-size: 14px;
            margin-bottom: 10px;
        }
        
        .repo-stats {
            display: flex;
            gap: 15px;
            font-size: 12px;
            color: #8b949e;
        }
        
        .repo-stats span {
            display: flex;
            align-items: center;
            gap: 5px;
        }
        
        .loading {
            text-align: center;
            padding: 40px;
            color: #8b949e;
        }
        
        .error {
            background: #da3633;
            color: white;
            padding: 15px;
            border-radius: 6px;
            margin: 20px 0;
        }
        
        .language-tag {
            display: inline-block;
            padding: 3px 8px;
            border-radius: 12px;
            font-size: 12px;
            font-weight: bold;
            background: #238636;
            color: white;
        }
    </style>
</head>
<body>
    <div class="container">
        <header>
            <h1>
                <span>🐙</span>
                <span>GitHub Dashboard</span>
            </h1>
            <p>Explora usuarios y repositorios de GitHub</p>
        </header>
        
        <div class="search-box">
            <input 
                type="text" 
                id="username" 
                placeholder="Ingresa un nombre de usuario de GitHub (ej: torvalds, gaearon, etc.)"
                onkeypress="if(event.key === 'Enter') buscarUsuario()"
            >
            <button onclick="buscarUsuario()">Buscar Usuario</button>
        </div>
        
        <div id="resultados"></div>
    </div>

    <script>
        async function buscarUsuario() {
            const resultados = document.getElementById('resultados');
            const username = document.getElementById('username').value.trim();
            
            if (!username) {
                alert('Por favor ingresa un nombre de usuario');
                return;
            }
            
            resultados.innerHTML = '<div class="loading">🔍 Buscando usuario...</div>';
            
            try {
                const response = await fetch(`/api/github/usuario/${username}`);
                const data = await response.json();
                
                if (data.error) {
                    resultados.innerHTML = `<div class="error">❌ ${data.error}</div>`;
                    return;
                }
                
                mostrarUsuario(data);
                
            } catch (error) {
                resultados.innerHTML = `<div class="error">❌ Error: ${error.message}</div>`;
            }
        }
        
        function mostrarUsuario(data) {
            const resultados = document.getElementById('resultados');
            
            resultados.innerHTML = `
                <div class="user-card">
                    <div class="user-header">
                        <img src="${data.avatar}" alt="${data.nombre}" class="avatar">
                        <div class="user-info">
                            <h2>${data.nombre}</h2>
                            <p style="color: #8b949e;">@${data.username}</p>
                            ${data.bio ? `<p class="user-bio">${data.bio}</p>` : ''}
                            ${data.ubicacion ? `<p style="color: #8b949e; font-size: 14px;">📍 ${data.ubicacion}</p>` : ''}
                            ${data.blog ? `<p style="color: #58a6ff; font-size: 14px;"><a href="${data.blog}" target="_blank">🔗 ${data.blog}</a></p>` : ''}
                        </div>
                    </div>
                    
                    <div class="stats">
                        <div class="stat-box">
                            <div class="stat-value">${data.repositorios}</div>
                            <div class="stat-label">Repositorios</div>
                        </div>
                        <div class="stat-box">
                            <div class="stat-value">${data.seguidores.toLocaleString()}</div>
                            <div class="stat-label">Seguidores</div>
                        </div>
                        <div class="stat-box">
                            <div class="stat-value">${data.siguiendo.toLocaleString()}</div>
                            <div class="stat-label">Siguiendo</div>
                        </div>
                        <div class="stat-box">
                            <div class="stat-value">⭐ ${data.total_stars.toLocaleString()}</div>
                            <div class="stat-label">Total Stars</div>
                        </div>
                        <div class="stat-box">
                            <div class="stat-value">🍴 ${data.total_forks.toLocaleString()}</div>
                            <div class="stat-label">Total Forks</div>
                        </div>
                    </div>
                    
                    ${data.top_lenguajes.length > 0 ? `
                    <div style="margin-top: 20px;">
                        <h3 style="color: #58a6ff; margin-bottom: 10px;">Top Lenguajes</h3>
                        <div style="display: flex; gap: 10px;">
                            ${data.top_lenguajes.map(l => `
                                <span class="language-tag">${l.lenguaje} (${l.repos})</span>
                            `).join('')}
                        </div>
                    </div>
                    ` : ''}
                </div>
                
                <div class="repos-section">
                    <h3 style="color: #58a6ff; margin-bottom: 15px;">🌟 Repositorios Destacados</h3>
                    ${data.repos_destacados.map(repo => `
                        <div class="repo-card">
                            <a href="${repo.url}" target="_blank" style="text-decoration: none;">
                                <div class="repo-name">${repo.nombre}</div>
                            </a>
                            ${repo.descripcion ? `<div class="repo-desc">${repo.descripcion}</div>` : ''}
                            <div class="repo-stats">
                                ${repo.lenguaje ? `<span class="language-tag">${repo.lenguaje}</span>` : ''}
                                <span>⭐ ${repo.stars.toLocaleString()}</span>
                                <span>🍴 ${repo.forks.toLocaleString()}</span>
                                <span>📅 ${repo.actualizado}</span>
                            </div>
                        </div>
                    `).join('')}
                </div>
            `;
        }
        
        // Ejemplos de usuarios para probar
        const ejemplos = ['torvalds', 'gaearon', 'tj', 'sindresorhus', 'addyosmani'];
        console.log('Usuarios de ejemplo para probar:', ejemplos.join(', '));
    </script>
</body>
</html>
```

### Paso 3: Ejecutar y Probar

```bash
python github_app.py
```

Usuarios de ejemplo para probar:
- `torvalds` (Linus Torvalds - creador de Linux)
- `gaearon` (Dan Abramov - React team)
- `tj` (TJ Holowaychuk)
- `sindresorhus` (Sindre Sorhus)

### 🎯 Resultado Esperado
- Dashboard completo con información del usuario
- Estadísticas de repositorios, stars y forks
- Top lenguajes de programación usados
- Repositorios destacados ordenados por popularidad

---

## 💾 Ejercicio 3.1: API REST con SQLite

### Objetivo
Crear una API REST completa (CRUD) para gestionar productos usando SQLite.

### Paso 1: Crear el Archivo Python

**Archivo: `productos_api.py`**

```python
from flask import Flask, request, jsonify, render_template
import sqlite3
from datetime import datetime

app = Flask(__name__)
DATABASE = 'productos.db'

def init_db():
    """Inicializar base de datos"""
    conn = sqlite3.connect(DATABASE)
    cursor = conn.cursor()
    cursor.execute('''
        CREATE TABLE IF NOT EXISTS productos (
            id INTEGER PRIMARY KEY AUTOINCREMENT,
            nombre TEXT NOT NULL,
            descripcion TEXT,
            precio REAL NOT NULL,
            stock INTEGER DEFAULT 0,
            categoria TEXT,
            fecha_creacion TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
            fecha_actualizacion TIMESTAMP DEFAULT CURRENT_TIMESTAMP
        )
    ''')
    
    # Insertar datos de ejemplo si la tabla está vacía
    cursor.execute('SELECT COUNT(*) FROM productos')
    if cursor.fetchone()[0] == 0:
        productos_ejemplo = [
            ('Laptop HP', 'Laptop HP 15.6" Core i5', 15999.99, 10, 'Electrónica'),
            ('Mouse Logitech', 'Mouse inalámbrico Logitech M185', 299.99, 50, 'Accesorios'),
            ('Teclado Mecánico', 'Teclado mecánico RGB', 1299.99, 25, 'Accesorios'),
            ('Monitor Samsung', 'Monitor 24" Full HD', 3499.99, 15, 'Electrónica'),
            ('Webcam', 'Webcam 1080p con micrófono', 899.99, 30, 'Accesorios')
        ]
        cursor.executemany('''
            INSERT INTO productos (nombre, descripcion, precio, stock, categoria)
            VALUES (?, ?, ?, ?, ?)
        ''', productos_ejemplo)
    
    conn.commit()
    conn.close()

def get_db():
    """Obtener conexión a la base de datos"""
    conn = sqlite3.connect(DATABASE)
    conn.row_factory = sqlite3.Row  # Para acceder a las columnas por nombre
    return conn

@app.route('/')
def index():
    return render_template('productos.html')

# CREATE - Crear producto
@app.route('/api/productos', methods=['POST'])
def crear_producto():
    data = request.json
    
    # Validar datos requeridos
    if not data.get('nombre') or not data.get('precio'):
        return jsonify({'error': 'Nombre y precio son requeridos'}), 400
    
    try:
        conn = get_db()
        cursor = conn.cursor()
        
        cursor.execute('''
            INSERT INTO productos (nombre, descripcion, precio, stock, categoria)
            VALUES (?, ?, ?, ?, ?)
        ''', (
            data['nombre'],
            data.get('descripcion', ''),
            float(data['precio']),
            int(data.get('stock', 0)),
            data.get('categoria', 'General')
        ))
        
        conn.commit()
        producto_id = cursor.lastrowid
        conn.close()
        
        return jsonify({
            'id': producto_id,
            'mensaje': 'Producto creado exitosamente'
        }), 201
        
    except Exception as e:
        return jsonify({'error': str(e)}), 500

# READ - Obtener todos los productos
@app.route('/api/productos', methods=['GET'])
def obtener_productos():
    # Parámetros de filtrado y ordenamiento
    categoria = request.args.get('categoria')
    orden = request.args.get('orden', 'nombre')
    orden_dir = request.args.get('dir', 'ASC')
    
    # Validar orden
    if orden not in ['nombre', 'precio', 'stock', 'fecha_creacion']:
        orden = 'nombre'
    
    if orden_dir.upper() not in ['ASC', 'DESC']:
        orden_dir = 'ASC'
    
    try:
        conn = get_db()
        cursor = conn.cursor()
        
        query = 'SELECT * FROM productos'
        params = []
        
        if categoria:
            query += ' WHERE categoria = ?'
            params.append(categoria)
        
        query += f' ORDER BY {orden} {orden_dir}'
        
        cursor.execute(query, params)
        productos = [dict(row) for row in cursor.fetchall()]
        conn.close()
        
        return jsonify(productos)
        
    except Exception as e:
        return jsonify({'error': str(e)}), 500

# READ - Obtener un producto específico
@app.route('/api/productos/<int:id>', methods=['GET'])
def obtener_producto(id):
    try:
        conn = get_db()
        cursor = conn.cursor()
        
        cursor.execute('SELECT * FROM productos WHERE id = ?', (id,))
        producto = cursor.fetchone()
        conn.close()
        
        if producto is None:
            return jsonify({'error': 'Producto no encontrado'}), 404
        
        return jsonify(dict(producto))
        
    except Exception as e:
        return jsonify({'error': str(e)}), 500

# UPDATE - Actualizar producto
@app.route('/api/productos/<int:id>', methods=['PUT'])
def actualizar_producto(id):
    data = request.json
    
    try:
        conn = get_db()
        cursor = conn.cursor()
        
        # Verificar que existe
        cursor.execute('SELECT * FROM productos WHERE id = ?', (id,))
        if cursor.fetchone() is None:
            conn.close()
            return jsonify({'error': 'Producto no encontrado'}), 404
        
        # Actualizar
        cursor.execute('''
            UPDATE productos 
            SET nombre = ?, descripcion = ?, precio = ?, stock = ?, 
                categoria = ?, fecha_actualizacion = CURRENT_TIMESTAMP
            WHERE id = ?
        ''', (
            data.get('nombre'),
            data.get('descripcion'),
            float(data.get('precio')),
            int(data.get('stock')),
            data.get('categoria'),
            id
        ))
        
        conn.commit()
        conn.close()
        
        return jsonify({'mensaje': 'Producto actualizado exitosamente'})
        
    except Exception as e:
        return jsonify({'error': str(e)}), 500

# DELETE - Eliminar producto
@app.route('/api/productos/<int:id>', methods=['DELETE'])
def eliminar_producto(id):
    try:
        conn = get_db()
        cursor = conn.cursor()
        
        cursor.execute('DELETE FROM productos WHERE id = ?', (id,))
        
        if cursor.rowcount == 0:
            conn.close()
            return jsonify({'error': 'Producto no encontrado'}), 404
        
        conn.commit()
        conn.close()
        
        return jsonify({'mensaje': 'Producto eliminado exitosamente'})
        
    except Exception as e:
        return jsonify({'error': str(e)}), 500

# ESTADÍSTICAS
@app.route('/api/productos/stats', methods=['GET'])
def estadisticas():
    try:
        conn = get_db()
        cursor = conn.cursor()
        
        # Estadísticas generales
        cursor.execute('''
            SELECT 
                COUNT(*) as total,
                AVG(precio) as precio_promedio,
                SUM(stock) as stock_total,
                MIN(precio) as precio_min,
                MAX(precio) as precio_max
            FROM productos
        ''')
        stats_generales = dict(cursor.fetchone())
        
        # Estadísticas por categoría
        cursor.execute('''
            SELECT 
                categoria,
                COUNT(*) as cantidad,
                AVG(precio) as precio_promedio,
                SUM(stock) as stock_total
            FROM productos
            GROUP BY categoria
        ''')
        stats_categoria = [dict(row) for row in cursor.fetchall()]
        
        conn.close()
        
        return jsonify({
            'generales': stats_generales,
            'por_categoria': stats_categoria
        })
        
    except Exception as e:
        return jsonify({'error': str(e)}), 500

# Obtener categorías únicas
@app.route('/api/categorias', methods=['GET'])
def obtener_categorias():
    try:
        conn = get_db()
        cursor = conn.cursor()
        
        cursor.execute('SELECT DISTINCT categoria FROM productos ORDER BY categoria')
        categorias = [row[0] for row in cursor.fetchall()]
        conn.close()
        
        return jsonify(categorias)
        
    except Exception as e:
        return jsonify({'error': str(e)}), 500

if __name__ == '__main__':
    init_db()
    print("✅ Base de datos inicializada")
    print("📊 Ejecutando API en http://127.0.0.1:5000")
    app.run(debug=True)
```

### Paso 2: Crear la Interfaz HTML

**Archivo: `templates/productos.html`**

```html
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>API REST - Gestión de Productos</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }
        
        body {
            font-family: Arial, sans-serif;
            background: #f5f5f5;
            padding: 20px;
        }
        
        .container {
            max-width: 1400px;
            margin: 0 auto;
        }
        
        header {
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            color: white;
            padding: 30px;
            border-radius: 10px;
            margin-bottom: 20px;
        }
        
        h1 {
            margin-bottom: 10px;
        }
        
        .stats-grid {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
            gap: 15px;
            margin-bottom: 20px;
        }
        
        .stat-card {
            background: white;
            padding: 20px;
            border-radius: 8px;
            box-shadow: 0 2px 5px rgba(0,0,0,0.1);
        }
        
        .stat-value {
            font-size: 32px;
            font-weight: bold;
            color: #667eea;
        }
        
        .stat-label {
            color: #666;
            margin-top: 5px;
        }
        
        .controls {
            background: white;
            padding: 20px;
            border-radius: 8px;
            margin-bottom: 20px;
            box-shadow: 0 2px 5px rgba(0,0,0,0.1);
        }
        
        .btn {
            padding: 10px 20px;
            border: none;
            border-radius: 5px;
            cursor: pointer;
            font-size: 14px;
            font-weight: bold;
            margin-right: 10px;
        }
        
        .btn-primary {
            background: #667eea;
            color: white;
        }
        
        .btn-success {
            background: #10b981;
            color: white;
        }
        
        .btn-danger {
            background: #ef4444;
            color: white;
        }
        
        .btn:hover {
            opacity: 0.9;
        }
        
        table {
            width: 100%;
            background: white;
            border-radius: 8px;
            overflow: hidden;
            box-shadow: 0 2px 5px rgba(0,0,0,0.1);
        }
        
        th, td {
            padding: 15px;
            text-align: left;
        }
        
        th {
            background: #667eea;
            color: white;
            font-weight: bold;
        }
        
        tr:nth-child(even) {
            background: #f9f9f9;
        }
        
        tr:hover {
            background: #f0f0f0;
        }
        
        .modal {
            display: none;
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: rgba(0,0,0,0.5);
            z-index: 1000;
        }
        
        .modal-content {
            background: white;
            max-width: 500px;
            margin: 100px auto;
            padding: 30px;
            border-radius: 10px;
        }
        
        .form-group {
            margin-bottom: 15px;
        }
        
        label {
            display: block;
            margin-bottom: 5px;
            font-weight: bold;
            color: #333;
        }
        
        input, textarea, select {
            width: 100%;
            padding: 10px;
            border: 1px solid #ddd;
            border-radius: 5px;
            font-size: 14px;
        }
        
        textarea {
            resize: vertical;
            min-height: 100px;
        }
    </style>
</head>
<body>
    <div class="container">
        <header>
            <h1>💾 API REST - Gestión de Productos</h1>
            <p>Sistema CRUD completo con SQLite</p>
        </header>
        
        <div class="stats-grid" id="stats"></div>
        
        <div class="controls">
            <button class="btn btn-success" onclick="abrirModalNuevo()">➕ Nuevo Producto</button>
            <button class="btn btn-primary" onclick="cargarProductos()">🔄 Recargar</button>
            <select id="filtroCategoria" onchange="cargarProductos()">
                <option value="">Todas las categorías</option>
            </select>
        </div>
        
        <table>
            <thead>
                <tr>
                    <th>ID</th>
                    <th>Nombre</th>
                    <th>Descripción</th>
                    <th>Precio</th>
                    <th>Stock</th>
                    <th>Categoría</th>
                    <th>Acciones</th>
                </tr>
            </thead>
            <tbody id="productosTable">
                <tr>
                    <td colspan="7" style="text-align: center;">Cargando...</td>
                </tr>
            </tbody>
        </table>
    </div>
    
    <!-- Modal para Crear/Editar -->
    <div id="modal" class="modal">
        <div class="modal-content">
            <h2 id="modalTitulo">Nuevo Producto</h2>
            <form id="productoForm">
                <input type="hidden" id="productoId">
                
                <div class="form-group">
                    <label>Nombre *</label>
                    <input type="text" id="nombre" required>
                </div>
                
                <div class="form-group">
                    <label>Descripción</label>
                    <textarea id="descripcion"></textarea>
                </div>
                
                <div class="form-group">
                    <label>Precio *</label>
                    <input type="number" id="precio" step="0.01" required>
                </div>
                
                <div class="form-group">
                    <label>Stock</label>
                    <input type="number" id="stock" value="0">
                </div>
                
                <div class="form-group">
                    <label>Categoría</label>
                    <input type="text" id="categoria">
                </div>
                
                <div style="margin-top: 20px;">
                    <button type="submit" class="btn btn-success">Guardar</button>
                    <button type="button" class="btn" onclick="cerrarModal()">Cancelar</button>
                </div>
            </form>
        </div>
    </div>

    <script>
        // Cargar datos al iniciar
        window.onload = () => {
            cargarEstadisticas();
            cargarCategorias();
            cargarProductos();
        };
        
        async function cargarEstadisticas() {
            try {
                const response = await fetch('/api/productos/stats');
                const data = await response.json();
                
                const statsDiv = document.getElementById('stats');
                statsDiv.innerHTML = `
                    <div class="stat-card">
                        <div class="stat-value">${data.generales.total}</div>
                        <div class="stat-label">Total Productos</div>
                    </div>
                    <div class="stat-card">
                        <div class="stat-value">$${data.generales.precio_promedio.toFixed(2)}</div>
                        <div class="stat-label">Precio Promedio</div>
                    </div>
                    <div class="stat-card">
                        <div class="stat-value">${data.generales.stock_total}</div>
                        <div class="stat-label">Stock Total</div>
                    </div>
                    <div class="stat-card">
                        <div class="stat-value">${data.por_categoria.length}</div>
                        <div class="stat-label">Categorías</div>
                    </div>
                `;
            } catch (error) {
                console.error('Error al cargar estadísticas:', error);
            }
        }
        
        async function cargarCategorias() {
            try {
                const response = await fetch('/api/categorias');
                const categorias = await response.json();
                
                const select = document.getElementById('filtroCategoria');
                categorias.forEach(cat => {
                    const option = document.createElement('option');
                    option.value = cat;
                    option.textContent = cat;
                    select.appendChild(option);
                });
            } catch (error) {
                console.error('Error al cargar categorías:', error);
            }
        }
        
        async function cargarProductos() {
            const tabla = document.getElementById('productosTable');
            const categoria = document.getElementById('filtroCategoria').value;
            
            tabla.innerHTML = '<tr><td colspan="7" style="text-align: center;">Cargando...</td></tr>';
            
            try {
                let url = '/api/productos';
                if (categoria) {
                    url += `?categoria=${categoria}`;
                }
                
                const response = await fetch(url);
                const productos = await response.json();
                
                if (productos.length === 0) {
                    tabla.innerHTML = '<tr><td colspan="7" style="text-align: center;">No hay productos</td></tr>';
                    return;
                }
                
                tabla.innerHTML = productos.map(p => `
                    <tr>
                        <td>${p.id}</td>
                        <td><strong>${p.nombre}</strong></td>
                        <td>${p.descripcion || '-'}</td>
                        <td>$${p.precio.toFixed(2)}</td>
                        <td>${p.stock}</td>
                        <td><span style="background: #667eea; color: white; padding: 3px 10px; border-radius: 12px; font-size: 12px;">${p.categoria}</span></td>
                        <td>
                            <button class="btn btn-primary" onclick="editarProducto(${p.id})">✏️</button>
                            <button class="btn btn-danger" onclick="eliminarProducto(${p.id})">🗑️</button>
                        </td>
                    </tr>
                `).join('');
                
                cargarEstadisticas(); // Actualizar estadísticas
                
            } catch (error) {
                tabla.innerHTML = '<tr><td colspan="7" style="text-align: center; color: red;">Error al cargar productos</td></tr>';
                console.error('Error:', error);
            }
        }
        
        function abrirModalNuevo() {
            document.getElementById('modalTitulo').textContent = 'Nuevo Producto';
            document.getElementById('productoForm').reset();
            document.getElementById('productoId').value = '';
            document.getElementById('modal').style.display = 'block';
        }
        
        async function editarProducto(id) {
            try {
                const response = await fetch(`/api/productos/${id}`);
                const producto = await response.json();
                
                document.getElementById('modalTitulo').textContent = 'Editar Producto';
                document.getElementById('productoId').value = producto.id;
                document.getElementById('nombre').value = producto.nombre;
                document.getElementById('descripcion').value = producto.descripcion || '';
                document.getElementById('precio').value = producto.precio;
                document.getElementById('stock').value = producto.stock;
                document.getElementById('categoria').value = producto.categoria;
                
                document.getElementById('modal').style.display = 'block';
            } catch (error) {
                alert('Error al cargar producto');
            }
        }
        
        function cerrarModal() {
            document.getElementById('modal').style.display = 'none';
        }
        
        document.getElementById('productoForm').onsubmit = async (e) => {
            e.preventDefault();
            
            const id = document.getElementById('productoId').value;
            const data = {
                nombre: document.getElementById('nombre').value,
                descripcion: document.getElementById('descripcion').value,
                precio: parseFloat(document.getElementById('precio').value),
                stock: parseInt(document.getElementById('stock').value),
                categoria: document.getElementById('categoria').value
            };
            
            try {
                let response;
                if (id) {
                    // Actualizar
                    response = await fetch(`/api/productos/${id}`, {
                        method: 'PUT',
                        headers: {'Content-Type': 'application/json'},
                        body: JSON.stringify(data)
                    });
                } else {
                    // Crear
                    response = await fetch('/api/productos', {
                        method: 'POST',
                        headers: {'Content-Type': 'application/json'},
                        body: JSON.stringify(data)
                    });
                }
                
                if (response.ok) {
                    cerrarModal();
                    cargarProductos();
                    cargarCategorias(); // Recargar por si hay nueva categoría
                    alert(id ? 'Producto actualizado' : 'Producto creado');
                } else {
                    const error = await response.json();
                    alert('Error: ' + error.error);
                }
            } catch (error) {
                alert('Error al guardar producto');
            }
        };
        
        async function eliminarProducto(id) {
            if (!confirm('¿Estás seguro de eliminar este producto?')) {
                return;
            }
            
            try {
                const response = await fetch(`/api/productos/${id}`, {
                    method: 'DELETE'
                });
                
                if (response.ok) {
                    cargarProductos();
                    alert('Producto eliminado');
                } else {
                    alert('Error al eliminar producto');
                }
            } catch (error) {
                alert('Error al eliminar producto');
            }
        }
    </script>
</body>
</html>
```

### Paso 3: Ejecutar y Probar

```bash
python productos_api.py
```

### 🎯 Funcionalidades Disponibles

1. **CREATE**: Crear nuevos productos
2. **READ**: Listar todos los productos
3. **UPDATE**: Editar productos existentes
4. **DELETE**: Eliminar productos
5. **Filtrado**: Por categoría
6. **Estadísticas**: Dashboard con métricas

### 🧪 Pruebas con cURL

```bash
# Obtener todos los productos
curl http://127.0.0.1:5000/api/productos

# Obtener un producto específico
curl http://127.0.0.1:5000/api/productos/1

# Crear un producto
curl -X POST http://127.0.0.1:5000/api/productos \
  -H "Content-Type: application/json" \
  -d '{"nombre":"Nuevo Producto","precio":999.99,"stock":10,"categoria":"Test"}'

# Actualizar un producto
curl -X PUT http://127.0.0.1:5000/api/productos/1 \
  -H "Content-Type: application/json" \
  -d '{"nombre":"Producto Actualizado","precio":1299.99,"stock":15,"categoria":"Electrónica"}'

# Eliminar un producto
curl -X DELETE http://127.0.0.1:5000/api/productos/1

# Obtener estadísticas
curl http://127.0.0.1:5000/api/productos/stats
```

---

## 🔥 Ejercicio 3.2: Chat en Tiempo Real con Firebase

### Objetivo
Integrar Firebase Realtime Database para crear un sistema de chat.

### Requisitos Previos
1. Cuenta de Google
2. Proyecto en Firebase Console

### Paso 1: Configurar Firebase

1. Ir a [Firebase Console](https://console.firebase.google.com/)
2. Crear nuevo proyecto
3. Ir a "Realtime Database" > "Crear base de datos"
4. Elegir modo "Test" (para desarrollo)
5. Ir a "Configuración del proyecto" > "Cuentas de servicio"
6. Generar nueva clave privada (descargar JSON)
7. Guardar el archivo como `firebase-credentials.json`

### Paso 2: Instalar Firebase Admin SDK

```bash
pip install firebase-admin
```

### Paso 3: Crear el Archivo Python

**Archivo: `chat_app.py`**

```python
from flask import Flask, render_template, request, jsonify
import firebase_admin
from firebase_admin import credentials, db
from datetime import datetime
import os

app = Flask(__name__)

# Inicializar Firebase
if not firebase_admin._apps:
    # Reemplazar con tu archivo de credenciales
    if os.path.exists('firebase-credentials.json'):
        cred = credentials.Certificate('firebase-credentials.json')
        firebase_admin.initialize_app(cred, {
            'databaseURL': 'https://TU-PROYECTO.firebaseio.com'  # Reemplazar con tu URL
        })
    else:
        print("⚠️ No se encontró firebase-credentials.json")
        print("Crea un proyecto en Firebase y descarga las credenciales")

@app.route('/')
def index():
    return render_template('chat.html')

@app.route('/api/mensajes', methods=['GET'])
def obtener_mensajes():
    """Obtener los últimos mensajes"""
    try:
        ref = db.reference('mensajes')
        mensajes = ref.order_by_child('timestamp').limit_to_last(50).get()
        
        if mensajes:
            # Convertir a lista ordenada
            mensajes_lista = []
            for key, value in mensajes.items():
                value['id'] = key
                mensajes_lista.append(value)
            
            # Ordenar por timestamp
            mensajes_lista.sort(key=lambda x: x.get('timestamp', ''))
            return jsonify(mensajes_lista)
        
        return jsonify([])
        
    except Exception as e:
        return jsonify({'error': str(e)}), 500

@app.route('/api/mensajes', methods=['POST'])
def enviar_mensaje():
    """Enviar un nuevo mensaje"""
    data = request.json
    
    if not data.get('usuario') or not data.get('texto'):
        return jsonify({'error': 'Usuario y texto requeridos'}), 400
    
    try:
        ref = db.reference('mensajes')
        nuevo_mensaje = {
            'usuario': data['usuario'],
            'texto': data['texto'],
            'timestamp': datetime.now().isoformat(),
            'avatar': data.get('avatar', '👤')
        }
        
        # Guardar mensaje
        nueva_ref = ref.push(nuevo_mensaje)
        nuevo_mensaje['id'] = nueva_ref.key
        
        return jsonify(nuevo_mensaje), 201
        
    except Exception as e:
        return jsonify({'error': str(e)}), 500

@app.route('/api/mensajes/<mensaje_id>', methods=['DELETE'])
def eliminar_mensaje(mensaje_id):
    """Eliminar un mensaje"""
    try:
        ref = db.reference(f'mensajes/{mensaje_id}')
        ref.delete()
        return jsonify({'mensaje': 'Mensaje eliminado'}), 200
        
    except Exception as e:
        return jsonify({'error': str(e)}), 500

@app.route('/api/usuarios/online', methods=['POST'])
def registrar_usuario_online():
    """Registrar usuario como online"""
    data = request.json
    usuario = data.get('usuario')
    
    if not usuario:
        return jsonify({'error': 'Usuario requerido'}), 400
    
    try:
        ref = db.reference(f'usuarios_online/{usuario}')
        ref.set({
            'ultima_actividad': datetime.now().isoformat(),
            'online': True
        })
        
        return jsonify({'mensaje': 'Usuario registrado'}), 200
        
    except Exception as e:
        return jsonify({'error': str(e)}), 500

@app.route('/api/usuarios/online', methods=['GET'])
def obtener_usuarios_online():
    """Obtener lista de usuarios online"""
    try:
        ref = db.reference('usuarios_online')
        usuarios = ref.get()
        
        if usuarios:
            return jsonify(list(usuarios.keys()))
        
        return jsonify([])
        
    except Exception as e:
        return jsonify({'error': str(e)}), 500

if __name__ == '__main__':
    print("🔥 Firebase Chat App")
    print("📝 Asegúrate de tener firebase-credentials.json configurado")
    app.run(debug=True)
```

### Paso 4: Crear la Interfaz HTML

**Archivo: `templates/chat.html`**

```html
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Chat en Tiempo Real - Firebase</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }
        
        body {
            font-family: Arial, sans-serif;
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            height: 100vh;
            display: flex;
            justify-content: center;
            align-items: center;
        }
        
        .chat-container {
            width: 90%;
            max-width: 800px;
            height: 600px;
            background: white;
            border-radius: 15px;
            box-shadow: 0 10px 40px rgba(0,0,0,0.3);
            display: flex;
            flex-direction: column;
            overflow: hidden;
        }
        
        .chat-header {
            background: #667eea;
            color: white;
            padding: 20px;
            text-align: center;
        }
        
        .chat-header h1 {
            margin-bottom: 5px;
        }
        
        .online-users {
            font-size: 14px;
            opacity: 0.9;
        }
        
        .messages-container {
            flex: 1;
            padding: 20px;
            overflow-y: auto;
            background: #f5f5f5;
        }
        
        .message {
            margin-bottom: 15px;
            display: flex;
            gap: 10px;
            animation: slideIn 0.3s ease;
        }
        
        @keyframes slideIn {
            from {
                opacity: 0;
                transform: translateY(10px);
            }
            to {
                opacity: 1;
                transform: translateY(0);
            }
        }
        
        .message.own {
            flex-direction: row-reverse;
        }
        
        .message-avatar {
            font-size: 30px;
            flex-shrink: 0;
        }
        
        .message-content {
            max-width: 70%;
        }
        
        .message-user {
            font-weight: bold;
            font-size: 14px;
            margin-bottom: 3px;
            color: #667eea;
        }
        
        .message.own .message-user {
            text-align: right;
            color: #764ba2;
        }
        
        .message-bubble {
            background: white;
            padding: 10px 15px;
            border-radius: 10px;
            box-shadow: 0 2px 5px rgba(0,0,0,0.1);
            word-wrap: break-word;
        }
        
        .message.own .message-bubble {
            background: #667eea;
            color: white;
        }
        
        .message-time {
            font-size: 11px;
            color: #999;
            margin-top: 3px;
        }
        
        .chat-input {
            padding: 20px;
            background: white;
            border-top: 1px solid #e0e0e0;
        }
        
        .input-group {
            display: flex;
            gap: 10px;
        }
        
        input[type="text"] {
            flex: 1;
            padding: 12px;
            border: 2px solid #e0e0e0;
            border-radius: 25px;
            font-size: 14px;
            outline: none;
        }
        
        input[type="text"]:focus {
            border-color: #667eea;
        }
        
        button {
            padding: 12px 25px;
            background: #667eea;
            color: white;
            border: none;
            border-radius: 25px;
            cursor: pointer;
            font-weight: bold;
            transition: background 0.3s;
        }
        
        button:hover {
            background: #5568d3;
        }
        
        .modal {
            display: flex;
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: rgba(0,0,0,0.5);
            justify-content: center;
            align-items: center;
        }
        
        .modal-content {
            background: white;
            padding: 40px;
            border-radius: 15px;
            text-align: center;
        }
        
        .modal-content h2 {
            margin-bottom: 20px;
            color: #667eea;
        }
        
        .modal-content input {
            width: 100%;
            padding: 12px;
            border: 2px solid #e0e0e0;
            border-radius: 8px;
            font-size: 16px;
            margin-bottom: 20px;
        }
        
        .avatar-selector {
            display: grid;
            grid-template-columns: repeat(5, 1fr);
            gap: 10px;
            margin-bottom: 20px;
        }
        
        .avatar-option {
            font-size: 30px;
            cursor: pointer;
            padding: 10px;
            border: 2px solid transparent;
            border-radius: 8px;
            transition: all 0.2s;
        }
        
        .avatar-option:hover {
            transform: scale(1.1);
        }
        
        .avatar-option.selected {
            border-color: #667eea;
            background: #f0f0f0;
        }
    </style>
</head>
<body>
    <!-- Modal de Inicio -->
    <div id="loginModal" class="modal">
        <div class="modal-content">
            <h2>🔥 Bienvenido al Chat</h2>
            <p style="margin-bottom: 20px;">Elige tu avatar y nombre de usuario</p>
            
            <div class="avatar-selector" id="avatarSelector">
                <div class="avatar-option selected" onclick="seleccionarAvatar('👤')">👤</div>
                <div class="avatar-option" onclick="seleccionarAvatar('😀')">😀</div>
                <div class="avatar-option" onclick="seleccionarAvatar('🦊')">🦊</div>
                <div class="avatar-option" onclick="seleccionarAvatar('🐱')">🐱</div>
                <div class="avatar-option" onclick="seleccionarAvatar('🐶')">🐶</div>
                <div class="avatar-option" onclick="seleccionarAvatar('🦁')">🦁</div>
                <div class="avatar-option" onclick="seleccionarAvatar('🐼')">🐼</div>
                <div class="avatar-option" onclick="seleccionarAvatar('🐨')">🐨</div>
                <div class="avatar-option" onclick="seleccionarAvatar('🐸')">🐸</div>
                <div class="avatar-option" onclick="seleccionarAvatar('🦄')">🦄</div>
            </div>
            
            <input type="text" id="nombreUsuario" placeholder="Tu nombre de usuario" maxlength="20">
            <button onclick="iniciarChat()">Entrar al Chat</button>
        </div>
    </div>
    
    <!-- Ventana de Chat -->
    <div class="chat-container" style="display: none;" id="chatContainer">
        <div class="chat-header">
            <h1>🔥 Chat en Tiempo Real</h1>
            <div class="online-users" id="onlineUsers">Conectando...</div>
        </div>
        
        <div class="messages-container" id="messagesContainer">
            <div style="text-align: center; color: #999; padding: 20px;">
                No hay mensajes aún. ¡Sé el primero en escribir!
            </div>
        </div>
        
        <div class="chat-input">
            <div class="input-group">
                <input 
                    type="text" 
                    id="messageInput" 
                    placeholder="Escribe un mensaje..."
                    onkeypress="if(event.key === 'Enter') enviarMensaje()"
                >
                <button onclick="enviarMensaje()">Enviar</button>
            </div>
        </div>
    </div>

    <script>
        let miUsuario = '';
        let miAvatar = '👤';
        let mensajesActuales = new Set();
        
        function seleccionarAvatar(avatar) {
            miAvatar = avatar;
            document.querySelectorAll('.avatar-option').forEach(el => {
                el.classList.remove('selected');
            });
            event.target.classList.add('selected');
        }
        
        function iniciarChat() {
            const nombre = document.getElementById('nombreUsuario').value.trim();
            
            if (!nombre) {
                alert('Por favor ingresa tu nombre de usuario');
                return;
            }
            
            miUsuario = nombre;
            document.getElementById('loginModal').style.display = 'none';
            document.getElementById('chatContainer').style.display = 'flex';
            
            // Registrar como online
            registrarUsuarioOnline();
            
            // Cargar mensajes
            cargarMensajes();
            
            // Actualizar cada 2 segundos
            setInterval(cargarMensajes, 2000);
            setInterval(actualizarUsuariosOnline, 5000);
        }
        
        async function registrarUsuarioOnline() {
            try {
                await fetch('/api/usuarios/online', {
                    method: 'POST',
                    headers: {'Content-Type': 'application/json'},
                    body: JSON.stringify({ usuario: miUsuario })
                });
            } catch (error) {
                console.error('Error al registrar usuario:', error);
            }
        }
        
        async function actualizarUsuariosOnline() {
            try {
                const response = await fetch('/api/usuarios/online');
                const usuarios = await response.json();
                
                document.getElementById('onlineUsers').textContent = 
                    `👥 ${usuarios.length} usuario(s) online: ${usuarios.join(', ')}`;
            } catch (error) {
                console.error('Error al obtener usuarios online:', error);
            }
        }
        
        async function cargarMensajes() {
            try {
                const response = await fetch('/api/mensajes');
                const mensajes = await response.json();
                
                const container = document.getElementById('messagesContainer');
                
                // Si hay nuevos mensajes
                if (mensajes.length > 0) {
                    // Verificar si hay mensajes nuevos
                    const hayNuevos = mensajes.some(m => !mensajesActuales.has(m.id));
                    
                    if (hayNuevos) {
                        container.innerHTML = mensajes.map(msg => {
                            mensajesActuales.add(msg.id);
                            const esPropio = msg.usuario === miUsuario;
                            const fecha = new Date(msg.timestamp);
                            const tiempo = fecha.toLocaleTimeString('es-MX', { 
                                hour: '2-digit', 
                                minute: '2-digit' 
                            });
                            
                            return `
                                <div class="message ${esPropio ? 'own' : ''}">
                                    <div class="message-avatar">${msg.avatar || '👤'}</div>
                                    <div class="message-content">
                                        <div class="message-user">${msg.usuario}</div>
                                        <div class="message-bubble">
                                            ${msg.texto}
                                        </div>
                                        <div class="message-time">${tiempo}</div>
                                    </div>
                                </div>
                            `;
                        }).join('');
                        
                        // Scroll al final
                        container.scrollTop = container.scrollHeight;
                    }
                }
            } catch (error) {
                console.error('Error al cargar mensajes:', error);
            }
        }
        
        async function enviarMensaje() {
            const input = document.getElementById('messageInput');
            const texto = input.value.trim();
            
            if (!texto) return;
            
            try {
                const response = await fetch('/api/mensajes', {
                    method: 'POST',
                    headers: {'Content-Type': 'application/json'},
                    body: JSON.stringify({
                        usuario: miUsuario,
                        texto: texto,
                        avatar: miAvatar
                    })
                });
                
                if (response.ok) {
                    input.value = '';
                    cargarMensajes();
                }
            } catch (error) {
                console.error('Error al enviar mensaje:', error);
                alert('Error al enviar mensaje');
            }
        }
    </script>
</body>
</html>
```

### Paso 5: Ejecutar

```bash
python chat_app.py
```

**Importante**: Reemplazar en `chat_app.py`:
- `'databaseURL': 'https://TU-PROYECTO.firebaseio.com'` con tu URL real

### 🎯 Resultado Esperado
- Chat en tiempo real con múltiples usuarios
- Avatars personalizables
- Indicador de usuarios online
- Actualizaciones automáticas cada 2 segundos

---

## 📚 Ejercicio 4.1: Buscador de Libros (Google Books API)

### Objetivo
Crear un buscador de libros con información detallada.

### Paso 1: Crear el Archivo Python

**Archivo: `libros_app.py`**

```python
from flask import Flask, render_template, request, jsonify
import requests

app = Flask(__name__)

# Google Books API (gratuita, sin autenticación)
GOOGLE_BOOKS_API = 'https://www.googleapis.com/books/v1/volumes'
TMDB_IMAGE_BASE = 'https://image.tmdb.org/t/p/w500'

@app.route('/')
def index():
    return render_template('libros.html')

@app.route('/api/libros/buscar')
def buscar_libros():
    query = request.args.get('q', '')
    categoria = request.args.get('categoria', '')
    max_results = request.args.get('max', 20, type=int)
    
    if not query:
        return jsonify({'error': 'Consulta requerida'}), 400
    
    # Construir query
    search_query = query
    if categoria:
        search_query += f'+subject:{categoria}'
    
    params = {
        'q': search_query,
        'maxResults': min(max_results, 40),
        'printType': 'books',
        'langRestrict': 'es'
    }
    
    try:
        response = requests.get(GOOGLE_BOOKS_API, params=params)
        data = response.json()
        
        if 'items' not in data:
            return jsonify([])
        
        libros = []
        for item in data['items']:
            info = item.get('volumeInfo', {})
            venta = item.get('saleInfo', {})
            
            libro = {
                'id': item['id'],
                'titulo': info.get('title', 'Sin título'),
                'autores': info.get('authors', []),
                'descripcion': info.get('description', '')[:300] + '...' if info.get('description') else '',
                'editorial': info.get('publisher', ''),
                'fecha_publicacion': info.get('publishedDate', ''),
                'paginas': info.get('pageCount', 0),
                'categorias': info.get('categories', []),
                'imagen': info.get('imageLinks', {}).get('thumbnail', ''),
                'preview_link': info.get('previewLink', ''),
                'rating': info.get('averageRating', 0),
                'precio': venta.get('listPrice', {}).get('amount', 0),
                'moneda': venta.get('listPrice', {}).get('currencyCode', ''),
                'disponible': venta.get('saleability') == 'FOR_SALE'
            }
            
            libros.append(libro)
        
        return jsonify(libros)
        
    except Exception as e:
        return jsonify({'error': str(e)}), 500

@app.route('/api/libros/<book_id>')
def detalle_libro(book_id):
    try:
        response = requests.get(f'{GOOGLE_BOOKS_API}/{book_id}')
        data = response.json()
        
        info = data.get('volumeInfo', {})
        venta = data.get('saleInfo', {})
        
        detalle = {
            'titulo': info.get('title'),
            'subtitulo': info.get('subtitle'),
            'autores': info.get('authors', []),
            'descripcion': info.get('description', ''),
            'editorial': info.get('publisher'),
            'fecha_publicacion': info.get('publishedDate'),
            'paginas': info.get('pageCount'),
            'categorias': info.get('categories', []),
            'imagen_grande': info.get('imageLinks', {}).get('large') or 
                             info.get('imageLinks', {}).get('thumbnail'),
            'idioma': info.get('language'),
            'preview_link': info.get('previewLink'),
            'rating': info.get('averageRating'),
            'ratings_count': info.get('ratingsCount'),
            'precio': venta.get('listPrice', {}).get('amount'),
            'moneda': venta.get('listPrice', {}).get('currencyCode'),
            'comprable': venta.get('saleability') == 'FOR_SALE',
            'link_compra': venta.get('buyLink')
        }
        
        return jsonify(detalle)
        
    except Exception as e:
        return jsonify({'error': str(e)}), 500

@app.route('/api/libros/categorias')
def categorias_populares():
    categorias = [
        'Fiction', 'Science', 'History', 'Biography',
        'Technology', 'Business', 'Self-Help', 'Poetry',
        'Mystery', 'Romance', 'Fantasy', 'Science Fiction',
        'Programming', 'Education', 'Health', 'Art'
    ]
    return jsonify(categorias)

if __name__ == '__main__':
    print("📚 Buscador de Libros - Google Books API")
    app.run(debug=True)
```

### Paso 2: Crear la Interfaz HTML

**Archivo: `templates/libros.html`**

```html
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Buscador de Libros</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }
        
        body {
            font-family: Arial, sans-serif;
            background: #f5f5f5;
        }
        
        header {
            background: linear-gradient(135deg, #6B46C1 0%, #9333EA 100%);
            color: white;
            padding: 40px 20px;
            text-align: center;
        }
        
        h1 {
            font-size: 2.5em;
            margin-bottom: 10px;
        }
        
        .search-box {
            max-width: 800px;
            margin: -30px auto 40px;
            padding: 0 20px;
        }
        
        .search-form {
            background: white;
            padding: 25px;
            border-radius: 15px;
            box-shadow: 0 10px 40px rgba(0,0,0,0.1);
            display: flex;
            gap: 10px;
            flex-wrap: wrap;
        }
        
        input[type="text"] {
            flex: 1;
            min-width: 250px;
            padding: 12px 20px;
            border: 2px solid #ddd;
            border-radius: 8px;
            font-size: 16px;
        }
        
        select {
            padding: 12px 20px;
            border: 2px solid #ddd;
            border-radius: 8px;
            font-size: 16px;
            background: white;
        }
        
        button {
            padding: 12px 30px;
            background: #9333EA;
            color: white;
            border: none;
            border-radius: 8px;
            font-size: 16px;
            font-weight: bold;
            cursor: pointer;
        }
        
        button:hover {
            background: #7C3AED;
        }
        
        .container {
            max-width: 1400px;
            margin: 0 auto;
            padding: 20px;
        }
        
        .libros-grid {
            display: grid;
            grid-template-columns: repeat(auto-fill, minmax(250px, 1fr));
            gap: 25px;
        }
        
        .libro-card {
            background: white;
            border-radius: 10px;
            overflow: hidden;
            box-shadow: 0 2px 10px rgba(0,0,0,0.1);
            transition: transform 0.3s, box-shadow 0.3s;
            cursor: pointer;
        }
        
        .libro-card:hover {
            transform: translateY(-5px);
            box-shadow: 0 5px 20px rgba(0,0,0,0.2);
        }
        
        .libro-imagen {
            width: 100%;
            height: 300px;
            object-fit: cover;
            background: #f0f0f0;
        }
        
        .libro-info {
            padding: 15px;
        }
        
        .libro-titulo {
            font-size: 16px;
            font-weight: bold;
            margin-bottom: 8px;
            line-height: 1.3;
            color: #333;
            height: 40px;
            overflow: hidden;
        }
        
        .libro-autores {
            font-size: 14px;
            color: #666;
            margin-bottom: 8px;
        }
        
        .libro-footer {
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin-top: 10px;
            padding-top: 10px;
            border-top: 1px solid #eee;
        }
        
        .rating {
            color: #FFC107;
            font-weight: bold;
        }
        
        .paginas {
            font-size: 12px;
            color: #999;
        }
        
        .loading {
            text-align: center;
            padding: 60px 20px;
            font-size: 18px;
            color: #666;
        }
    </style>
</head>
<body>
    <header>
        <h1>📚 Buscador de Libros</h1>
        <p>Explora millones de libros con Google Books API</p>
    </header>
    
    <div class="search-box">
        <div class="search-form">
            <input 
                type="text" 
                id="busqueda" 
                placeholder="Buscar libros por título, autor o tema..."
                onkeypress="if(event.key === 'Enter') buscarLibros()"
            >
            <select id="categoria">
                <option value="">Todas las categorías</option>
            </select>
            <button onclick="buscarLibros()">Buscar</button>
        </div>
    </div>
    
    <div class="container">
        <div id="resultados" class="libros-grid"></div>
    </div>

    <script>
        // Cargar categorías al iniciar
        window.onload = async () => {
            try {
                const response = await fetch('/api/libros/categorias');
                const categorias = await response.json();
                
                const select = document.getElementById('categoria');
                categorias.forEach(cat => {
                    const option = document.createElement('option');
                    option.value = cat;
                    option.textContent = cat;
                    select.appendChild(option);
                });
            } catch (error) {
                console.error('Error al cargar categorías:', error);
            }
        };
        
        async function buscarLibros() {
            const resultados = document.getElementById('resultados');
            const busqueda = document.getElementById('busqueda').value.trim();
            const categoria = document.getElementById('categoria').value;
            
            if (!busqueda) {
                alert('Por favor ingresa un término de búsqueda');
                return;
            }
            
            resultados.innerHTML = '<div class="loading">🔍 Buscando libros...</div>';
            
            try {
                let url = `/api/libros/buscar?q=${encodeURIComponent(busqueda)}`;
                if (categoria) {
                    url += `&categoria=${categoria}`;
                }
                
                const response = await fetch(url);
                const libros = await response.json();
                
                if (libros.error) {
                    resultados.innerHTML = `<div class="loading" style="color: red;">❌ ${libros.error}</div>`;
                    return;
                }
                
                if (libros.length === 0) {
                    resultados.innerHTML = '<div class="loading">No se encontraron libros</div>';
                    return;
                }
                
                resultados.innerHTML = libros.map(libro => `
                    <div class="libro-card" onclick="verDetalle('${libro.id}')">
                        <img 
                            src="${libro.imagen || '/static/img/no-book.png'}" 
                            alt="${libro.titulo}"
                            class="libro-imagen"
                        >
                        <div class="libro-info">
                            <div class="libro-titulo">${libro.titulo}</div>
                            <div class="libro-autores">${libro.autores.slice(0, 2).join(', ')}</div>
                            <div class="libro-footer">
                                ${libro.rating > 0 ? `<span class="rating">⭐ ${libro.rating}</span>` : '<span></span>'}
                                ${libro.paginas > 0 ? `<span class="paginas">${libro.paginas} páginas</span>` : ''}
                            </div>
                        </div>
                    </div>
                `).join('');
                
            } catch (error) {
                resultados.innerHTML = '<div class="loading" style="color: red;">❌ Error al buscar libros</div>';
                console.error('Error:', error);
            }
        }
        
        async function verDetalle(libroId) {
            try {
                const response = await fetch(`/api/libros/${libroId}`);
                const libro = await response.json();
                
                // Mostrar en modal o nueva ventana
                alert(`Título: ${libro.titulo}\n\nAutores: ${libro.autores.join(', ')}\n\n${libro.descripcion}`);
                
                // Abrir preview
                if (libro.preview_link) {
                    window.open(libro.preview_link, '_blank');
                }
                
            } catch (error) {
                console.error('Error al ver detalle:', error);
            }
        }
    </script>
</body>
</html>
```

### Paso 3: Ejecutar

```bash
python libros_app.py
```

### 🎯 Resultado Esperado
- Búsqueda de libros por título, autor o tema
- Filtrado por categoría
- Información completa de cada libro
- Enlaces a preview de Google Books

---

## 💰 Ejercicio 4.2: Conversor de Divisas

### Objetivo
Crear un conversor de monedas en tiempo real.

### Paso 1: Registrarse en ExchangeRate-API

1. Ir a [ExchangeRate-API](https://www.exchangerate-api.com/)
2. Crear cuenta gratuita (1500 requests/mes)
3. Copiar tu API key

### Paso 2: Crear el Archivo Python

**Archivo: `divisas_app.py`**

```python
from flask import Flask, render_template, request, jsonify
import requests

app = Flask(__name__)

# ExchangeRate-API (obtener en https://www.exchangerate-api.com/)
API_KEY = 'TU_API_KEY_AQUI'
BASE_URL = 'https://v6.exchangerate-api.com/v6'

@app.route('/')
def index():
    return render_template('divisas.html')

@app.route('/api/divisas/tasas/<moneda_base>')
def obtener_tasas(moneda_base):
    """Obtener todas las tasas de cambio para una moneda base"""
    try:
        url = f'{BASE_URL}/{API_KEY}/latest/{moneda_base.upper()}'
        response = requests.get(url)
        data = response.json()
        
        if data['result'] != 'success':
            return jsonify({'error': 'Error al obtener tasas'}), 400
        
        return jsonify({
            'moneda_base': data['base_code'],
            'tasas': data['conversion_rates'],
            'ultima_actualizacion': data['time_last_update_utc']
        })
        
    except Exception as e:
        return jsonify({'error': str(e)}), 500

@app.route('/api/divisas/convertir')
def convertir():
    """Convertir entre dos monedas"""
    monto = request.args.get('monto', type=float)
    de = request.args.get('de', 'USD').upper()
    a = request.args.get('a', 'MXN').upper()
    
    if not monto:
        return jsonify({'error': 'Monto requerido'}), 400
    
    try:
        url = f'{BASE_URL}/{API_KEY}/pair/{de}/{a}/{monto}'
        response = requests.get(url)
        data = response.json()
        
        if data['result'] != 'success':
            return jsonify({'error': 'Error en conversión'}), 400
        
        return jsonify({
            'monto_original': monto,
            'moneda_origen': de,
            'moneda_destino': a,
            'monto_convertido': data['conversion_result'],
            'tasa_conversion': data['conversion_rate'],
            'ultima_actualizacion': data['time_last_update_utc']
        })
        
    except Exception as e:
        return jsonify({'error': str(e)}), 500

@app.route('/api/divisas/monedas')
def listar_monedas():
    """Lista de monedas más comunes"""
    monedas = {
        'USD': {'nombre': 'Dólar Estadounidense', 'simbolo': '$', 'bandera': '🇺🇸'},
        'EUR': {'nombre': 'Euro', 'simbolo': '€', 'bandera': '🇪🇺'},
        'GBP': {'nombre': 'Libra Esterlina', 'simbolo': '£', 'bandera': '🇬🇧'},
        'JPY': {'nombre': 'Yen Japonés', 'simbolo': '¥', 'bandera': '🇯🇵'},
        'MXN': {'nombre': 'Peso Mexicano', 'simbolo': '$', 'bandera': '🇲🇽'},
        'CAD': {'nombre': 'Dólar Canadiense', 'simbolo': '$', 'bandera': '🇨🇦'},
        'AUD': {'nombre': 'Dólar Australiano', 'simbolo': '$', 'bandera': '🇦🇺'},
        'CHF': {'nombre': 'Franco Suizo', 'simbolo': 'Fr', 'bandera': '🇨🇭'},
        'CNY': {'nombre': 'Yuan Chino', 'simbolo': '¥', 'bandera': '🇨🇳'},
        'BRL': {'nombre': 'Real Brasileño', 'simbolo': 'R$', 'bandera': '🇧🇷'},
        'ARS': {'nombre': 'Peso Argentino', 'simbolo': '$', 'bandera': '🇦🇷'},
        'COP': {'nombre': 'Peso Colombiano', 'simbolo': '$', 'bandera': '🇨🇴'},
        'CLP': {'nombre': 'Peso Chileno', 'simbolo': '$', 'bandera': '🇨🇱'}
    }
    return jsonify(monedas)

if __name__ == '__main__':
    print("💰 Conversor de Divisas")
    print("🔑 API Key:", API_KEY)
    app.run(debug=True)
```

### Paso 3: Crear la Interfaz HTML

**Archivo: `templates/divisas.html`**

```html
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Conversor de Divisas</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }
        
        body {
            font-family: Arial, sans-serif;
            background: linear-gradient(135deg, #11998e 0%, #38ef7d 100%);
            min-height: 100vh;
            display: flex;
            justify-content: center;
            align-items: center;
            padding: 20px;
        }
        
        .converter-container {
            background: white;
            padding: 40px;
            border-radius: 20px;
            box-shadow: 0 20px 60px rgba(0,0,0,0.3);
            max-width: 500px;
            width: 100%;
        }
        
        h1 {
            text-align: center;
            color: #11998e;
            margin-bottom: 10px;
        }
        
        .subtitle {
            text-align: center;
            color: #666;
            margin-bottom: 30px;
            font-size: 14px;
        }
        
        .input-group {
            margin-bottom: 20px;
        }
        
        label {
            display: block;
            margin-bottom: 8px;
            font-weight: bold;
            color: #333;
        }
        
        input[type="number"] {
            width: 100%;
            padding: 15px;
            border: 2px solid #ddd;
            border-radius: 10px;
            font-size: 24px;
            text-align: center;
            font-weight: bold;
        }
        
        input[type="number"]:focus {
            outline: none;
            border-color: #11998e;
        }
        
        select {
            width: 100%;
            padding: 15px;
            border: 2px solid #ddd;
            border-radius: 10px;
            font-size: 16px;
            background: white;
            cursor: pointer;
        }
        
        select:focus {
            outline: none;
            border-color: #11998e;
        }
        
        .swap-button {
            width: 100%;
            padding: 10px;
            background: #f0f0f0;
            border: none;
            border-radius: 10px;
            font-size: 24px;
            cursor: pointer;
            margin: 15px 0;
            transition: background 0.3s;
        }
        
        .swap-button:hover {
            background: #e0e0e0;
        }
        
        .convert-button {
            width: 100%;
            padding: 18px;
            background: linear-gradient(135deg, #11998e 0%, #38ef7d 100%);
            color: white;
            border: none;
            border-radius: 10px;
            font-size: 18px;
            font-weight: bold;
            cursor: pointer;
            margin-top: 20px;
            transition: transform 0.2s;
        }
        
        .convert-button:hover {
            transform: scale(1.02);
        }
        
        .result {
            margin-top: 30px;
            padding: 25px;
            background: #f8f9fa;
            border-radius: 15px;
            text-align: center;
            display: none;
        }
        
        .result.show {
            display: block;
        }
        
        .result-amount {
            font-size: 36px;
            font-weight: bold;
            color: #11998e;
            margin-bottom: 10px;
        }
        
        .result-rate {
            color: #666;
            font-size: 14px;
        }
        
        .loading {
            text-align: center;
            padding: 20px;
            color: #11998e;
        }
        
        .error {
            background: #ffebee;
            color: #c62828;
            padding: 15px;
            border-radius: 10px;
            margin-top: 20px;
            text-align: center;
        }
    </style>
</head>
<body>
    <div class="converter-container">
        <h1>💰 Conversor de Divisas</h1>
        <p class="subtitle">Tasas de cambio en tiempo real</p>
        
        <div class="input-group">
            <label>Monto</label>
            <input type="number" id="monto" value="100" step="0.01">
        </div>
        
        <div class="input-group">
            <label>De</label>
            <select id="monedaDe"></select>
        </div>
        
        <button class="swap-button" onclick="intercambiarMonedas()">⇅</button>
        
        <div class="input-group">
            <label>A</label>
            <select id="monedaA"></select>
        </div>
        
        <button class="convert-button" onclick="convertir()">
            Convertir
        </button>
        
        <div id="resultado" class="result"></div>
    </div>

    <script>
        let monedas = {};
        
        // Cargar monedas al iniciar
        window.onload = async () => {
            try {
                const response = await fetch('/api/divisas/monedas');
                monedas = await response.json();
                
                const selectDe = document.getElementById('monedaDe');
                const selectA = document.getElementById('monedaA');
                
                Object.keys(monedas).forEach(codigo => {
                    const info = monedas[codigo];
                    
                    const optionDe = document.createElement('option');
                    optionDe.value = codigo;
                    optionDe.textContent = `${info.bandera} ${codigo} - ${info.nombre}`;
                    selectDe.appendChild(optionDe);
                    
                    const optionA = document.createElement('option');
                    optionA.value = codigo;
                    optionA.textContent = `${info.bandera} ${codigo} - ${info.nombre}`;
                    selectA.appendChild(optionA);
                });
                
                // Valores por defecto
                selectDe.value = 'USD';
                selectA.value = 'MXN';
                
            } catch (error) {
                console.error('Error al cargar monedas:', error);
            }
        };
        
        function intercambiarMonedas() {
            const selectDe = document.getElementById('monedaDe');
            const selectA = document.getElementById('monedaA');
            
            const temp = selectDe.value;
            selectDe.value = selectA.value;
            selectA.value = temp;
        }
        
        async function convertir() {
            const resultado = document.getElementById('resultado');
            const monto = document.getElementById('monto').value;
            const de = document.getElementById('monedaDe').value;
            const a = document.getElementById('monedaA').value;
            
            if (!monto || monto <= 0) {
                alert('Por favor ingresa un monto válido');
                return;
            }
            
            resultado.innerHTML = '<div class="loading">⏳ Convirtiendo...</div>';
            resultado.classList.add('show');
            
            try {
                const url = `/api/divisas/convertir?monto=${monto}&de=${de}&a=${a}`;
                const response = await fetch(url);
                const data = await response.json();
                
                if (data.error) {
                    resultado.innerHTML = `<div class="error">❌ ${data.error}</div>`;
                    return;
                }
                
                const infoA = monedas[a];
                
                resultado.innerHTML = `
                    <div class="result-amount">
                        ${infoA.simbolo} ${data.monto_convertido.toLocaleString('es-MX', {
                            minimumFractionDigits: 2,
                            maximumFractionDigits: 2
                        })} ${a}
                    </div>
                    <div class="result-rate">
                        Tasa de cambio: 1 ${de} = ${data.tasa_conversion.toFixed(4)} ${a}
                    </div>
                    <div class="result-rate" style="margin-top: 10px;">
                        ${new Date(data.ultima_actualizacion).toLocaleString('es-MX')}
                    </div>
                `;
                
            } catch (error) {
                resultado.innerHTML = '<div class="error">❌ Error al convertir</div>';
                console.error('Error:', error);
            }
        }
        
        // Convertir automáticamente cuando cambia el monto
        document.getElementById('monto').addEventListener('input', convertir);
        document.getElementById('monedaDe').addEventListener('change', convertir);
        document.getElementById('monedaA').addEventListener('change', convertir);
    </script>
</body>
</html>
```

### Paso 4: Ejecutar

```bash
python divisas_app.py
```

### 🎯 Resultado Esperado
- Conversión en tiempo real
- Soporte para múltiples monedas
- Interfaz intuitiva con banderas
- Botón de intercambio rápido

---
# 🎬 Ejercicios de APIs de Streaming

## Ejercicio 5.1: Buscador de Películas con TMDB API

### 🎯 Objetivo
Crear una aplicación para buscar películas y series usando The Movie Database (TMDB) API.

---

## 📝 Paso 1: Registrarse en TMDB

1. Ve a [https://www.themoviedb.org/](https://www.themoviedb.org/)
2. Haz clic en "Únete a TMDB" (esquina superior derecha)
3. Completa el registro y verifica tu email
4. Ve a tu perfil → Configuración → API
5. Solicita una API key (selecciona "Developer")
6. Llena el formulario (puedes poner información de prueba)
7. Copia tu API key

**URL de documentación:** https://developers.themoviedb.org/3

---

## 💻 Paso 2: Crear el Archivo Python

**Archivo: `peliculas_app.py`**

```python
from flask import Flask, render_template, request, jsonify
import requests
from datetime import datetime

app = Flask(__name__)

# TMDB API Key (obtener en https://www.themoviedb.org/settings/api)
TMDB_API_KEY = 'TU_API_KEY_AQUI'
TMDB_BASE_URL = 'https://api.themoviedb.org/3'
TMDB_IMAGE_BASE = 'https://image.tmdb.org/t/p/w500'

@app.route('/')
def index():
    return render_template('peliculas.html')

@app.route('/api/peliculas/buscar')
def buscar_peliculas():
    """Buscar películas por título"""
    query = request.args.get('q', '')
    page = request.args.get('page', 1, type=int)
    
    if not query:
        return jsonify({'error': 'Consulta requerida'}), 400
    
    try:
        url = f'{TMDB_BASE_URL}/search/movie'
        params = {
            'api_key': TMDB_API_KEY,
            'query': query,
            'language': 'es-MX',
            'page': page,
            'include_adult': False
        }
        
        response = requests.get(url, params=params, timeout=10)
        
        if response.status_code != 200:
            return jsonify({'error': 'Error al buscar películas'}), 500
        
        data = response.json()
        
        peliculas = []
        for movie in data.get('results', []):
            peliculas.append({
                'id': movie['id'],
                'titulo': movie['title'],
                'titulo_original': movie['original_title'],
                'descripcion': movie['overview'],
                'poster': f"{TMDB_IMAGE_BASE}{movie['poster_path']}" if movie.get('poster_path') else None,
                'backdrop': f"https://image.tmdb.org/t/p/original{movie['backdrop_path']}" if movie.get('backdrop_path') else None,
                'fecha_estreno': movie.get('release_date', ''),
                'popularidad': movie.get('popularity', 0),
                'calificacion': movie.get('vote_average', 0),
                'votos': movie.get('vote_count', 0)
            })
        
        return jsonify({
            'peliculas': peliculas,
            'pagina': data['page'],
            'total_paginas': data['total_pages'],
            'total_resultados': data['total_results']
        })
        
    except Exception as e:
        print(f"Error en buscar_peliculas: {e}")
        return jsonify({'error': str(e)}), 500

@app.route('/api/peliculas/<int:movie_id>')
def detalle_pelicula(movie_id):
    """Obtener detalles completos de una película"""
    try:
        # Información básica + créditos, videos y similares
        url = f'{TMDB_BASE_URL}/movie/{movie_id}'
        params = {
            'api_key': TMDB_API_KEY,
            'language': 'es-MX',
            'append_to_response': 'credits,videos,similar,recommendations'
        }
        
        response = requests.get(url, params=params, timeout=10)
        
        if response.status_code == 404:
            return jsonify({'error': 'Película no encontrada'}), 404
        
        if response.status_code != 200:
            return jsonify({'error': 'Error al obtener detalles'}), 500
        
        movie = response.json()
        
        # Procesar reparto
        cast = [
            {
                'nombre': actor['name'],
                'personaje': actor['character'],
                'foto': f"{TMDB_IMAGE_BASE}{actor['profile_path']}" if actor.get('profile_path') else None,
                'orden': actor.get('order', 999)
            }
            for actor in movie.get('credits', {}).get('cast', [])[:10]
        ]
        
        # Procesar crew (director, guionista, etc.)
        crew = movie.get('credits', {}).get('crew', [])
        director = next((c['name'] for c in crew if c['job'] == 'Director'), None)
        guionistas = [c['name'] for c in crew if c['job'] in ['Writer', 'Screenplay']][:3]
        
        # Procesar videos (trailers)
        videos = [
            {
                'nombre': video['name'],
                'tipo': video['type'],
                'sitio': video['site'],
                'key': video['key'],
                'url': f"https://www.youtube.com/watch?v={video['key']}" if video['site'] == 'YouTube' else None
            }
            for video in movie.get('videos', {}).get('results', [])
            if video['site'] == 'YouTube' and video['type'] in ['Trailer', 'Teaser']
        ]
        
        # Películas similares
        similares = [
            {
                'id': sim['id'],
                'titulo': sim['title'],
                'poster': f"{TMDB_IMAGE_BASE}{sim['poster_path']}" if sim.get('poster_path') else None,
                'calificacion': sim.get('vote_average', 0)
            }
            for sim in movie.get('similar', {}).get('results', [])[:6]
        ]
        
        # Recomendaciones
        recomendaciones = [
            {
                'id': rec['id'],
                'titulo': rec['title'],
                'poster': f"{TMDB_IMAGE_BASE}{rec['poster_path']}" if rec.get('poster_path') else None,
                'calificacion': rec.get('vote_average', 0)
            }
            for rec in movie.get('recommendations', {}).get('results', [])[:6]
        ]
        
        detalle = {
            'id': movie['id'],
            'titulo': movie['title'],
            'titulo_original': movie['original_title'],
            'descripcion': movie['overview'],
            'tagline': movie.get('tagline', ''),
            'poster': f"{TMDB_IMAGE_BASE}{movie['poster_path']}" if movie.get('poster_path') else None,
            'backdrop': f"https://image.tmdb.org/t/p/original{movie['backdrop_path']}" if movie.get('backdrop_path') else None,
            'fecha_estreno': movie.get('release_date', ''),
            'duracion': movie.get('runtime', 0),
            'presupuesto': movie.get('budget', 0),
            'ingresos': movie.get('revenue', 0),
            'calificacion': movie.get('vote_average', 0),
            'votos': movie.get('vote_count', 0),
            'popularidad': movie.get('popularity', 0),
            'generos': [g['name'] for g in movie.get('genres', [])],
            'productoras': [p['name'] for p in movie.get('production_companies', [])],
            'paises': [p['name'] for p in movie.get('production_countries', [])],
            'idiomas': [l['english_name'] for l in movie.get('spoken_languages', [])],
            'director': director,
            'guionistas': guionistas,
            'reparto': cast,
            'trailers': videos,
            'similares': similares,
            'recomendaciones': recomendaciones,
            'homepage': movie.get('homepage', ''),
            'imdb_id': movie.get('imdb_id', '')
        }
        
        return jsonify(detalle)
        
    except Exception as e:
        print(f"Error en detalle_pelicula: {e}")
        return jsonify({'error': str(e)}), 500

@app.route('/api/peliculas/populares')
def peliculas_populares():
    """Obtener películas populares"""
    page = request.args.get('page', 1, type=int)
    
    try:
        url = f'{TMDB_BASE_URL}/movie/popular'
        params = {
            'api_key': TMDB_API_KEY,
            'language': 'es-MX',
            'page': page
        }
        
        response = requests.get(url, params=params, timeout=10)
        data = response.json()
        
        peliculas = [
            {
                'id': movie['id'],
                'titulo': movie['title'],
                'poster': f"{TMDB_IMAGE_BASE}{movie['poster_path']}" if movie.get('poster_path') else None,
                'calificacion': movie.get('vote_average', 0),
                'fecha_estreno': movie.get('release_date', '')
            }
            for movie in data.get('results', [])
        ]
        
        return jsonify({
            'peliculas': peliculas,
            'pagina': data['page'],
            'total_paginas': data['total_pages']
        })
        
    except Exception as e:
        print(f"Error en peliculas_populares: {e}")
        return jsonify({'error': str(e)}), 500

@app.route('/api/peliculas/cartelera')
def peliculas_cartelera():
    """Obtener películas en cartelera"""
    try:
        url = f'{TMDB_BASE_URL}/movie/now_playing'
        params = {
            'api_key': TMDB_API_KEY,
            'language': 'es-MX',
            'region': 'MX'
        }
        
        response = requests.get(url, params=params, timeout=10)
        data = response.json()
        
        peliculas = [
            {
                'id': movie['id'],
                'titulo': movie['title'],
                'poster': f"{TMDB_IMAGE_BASE}{movie['poster_path']}" if movie.get('poster_path') else None,
                'calificacion': movie.get('vote_average', 0)
            }
            for movie in data.get('results', [])[:10]
        ]
        
        return jsonify(peliculas)
        
    except Exception as e:
        print(f"Error en peliculas_cartelera: {e}")
        return jsonify({'error': str(e)}), 500

@app.route('/api/series/buscar')
def buscar_series():
    """Buscar series de TV"""
    query = request.args.get('q', '')
    
    if not query:
        return jsonify({'error': 'Consulta requerida'}), 400
    
    try:
        url = f'{TMDB_BASE_URL}/search/tv'
        params = {
            'api_key': TMDB_API_KEY,
            'query': query,
            'language': 'es-MX'
        }
        
        response = requests.get(url, params=params, timeout=10)
        data = response.json()
        
        series = [
            {
                'id': show['id'],
                'nombre': show['name'],
                'descripcion': show.get('overview', ''),
                'poster': f"{TMDB_IMAGE_BASE}{show['poster_path']}" if show.get('poster_path') else None,
                'primera_fecha': show.get('first_air_date', ''),
                'calificacion': show.get('vote_average', 0)
            }
            for show in data.get('results', [])
        ]
        
        return jsonify(series)
        
    except Exception as e:
        print(f"Error en buscar_series: {e}")
        return jsonify({'error': str(e)}), 500

@app.route('/api/generos/peliculas')
def generos_peliculas():
    """Obtener lista de géneros de películas"""
    try:
        url = f'{TMDB_BASE_URL}/genre/movie/list'
        params = {
            'api_key': TMDB_API_KEY,
            'language': 'es-MX'
        }
        
        response = requests.get(url, params=params, timeout=10)
        data = response.json()
        
        return jsonify(data.get('genres', []))
        
    except Exception as e:
        print(f"Error en generos_peliculas: {e}")
        return jsonify({'error': str(e)}), 500

if __name__ == '__main__':
    print("=" * 60)
    print("🎬 Buscador de Películas - TMDB API")
    print("=" * 60)
    
    if TMDB_API_KEY == 'TU_API_KEY_AQUI':
        print("⚠️  ADVERTENCIA: API Key no configurada")
        print("   Obtén tu key en: https://www.themoviedb.org/settings/api")
    else:
        print(f"✅ API Key configurada: {TMDB_API_KEY[:10]}...")
    
    print("🌐 Servidor corriendo en: http://127.0.0.1:5000")
    print("=" * 60)
    
    app.run(debug=True)
```

---

## 🎨 Paso 3: Crear la Interfaz HTML

**Archivo: `templates/peliculas.html`**

```html
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Buscador de Películas - TMDB</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }
        
        body {
            font-family: Arial, sans-serif;
            background: #141414;
            color: white;
        }
        
        header {
            background: linear-gradient(to bottom, #000000, #141414);
            padding: 20px;
            position: sticky;
            top: 0;
            z-index: 100;
            box-shadow: 0 2px 10px rgba(0,0,0,0.5);
        }
        
        .header-content {
            max-width: 1400px;
            margin: 0 auto;
            display: flex;
            justify-content: space-between;
            align-items: center;
            gap: 20px;
            flex-wrap: wrap;
        }
        
        h1 {
            color: #E50914;
            font-size: 2em;
        }
        
        .search-box {
            flex: 1;
            min-width: 300px;
            max-width: 600px;
        }
        
        .search-form {
            display: flex;
            gap: 10px;
        }
        
        input[type="text"] {
            flex: 1;
            padding: 12px 20px;
            border: 2px solid #333;
            border-radius: 5px;
            background: #222;
            color: white;
            font-size: 16px;
        }
        
        input[type="text"]:focus {
            outline: none;
            border-color: #E50914;
        }
        
        button {
            padding: 12px 25px;
            background: #E50914;
            color: white;
            border: none;
            border-radius: 5px;
            font-weight: bold;
            cursor: pointer;
            transition: background 0.3s;
        }
        
        button:hover {
            background: #f40612;
        }
        
        .tabs {
            display: flex;
            gap: 10px;
            margin: 20px 0;
            max-width: 1400px;
            margin: 20px auto;
            padding: 0 20px;
        }
        
        .tab {
            padding: 10px 20px;
            background: #222;
            border: none;
            color: white;
            cursor: pointer;
            border-radius: 5px;
            transition: background 0.3s;
        }
        
        .tab.active {
            background: #E50914;
        }
        
        .container {
            max-width: 1400px;
            margin: 0 auto;
            padding: 20px;
        }
        
        .peliculas-grid {
            display: grid;
            grid-template-columns: repeat(auto-fill, minmax(200px, 1fr));
            gap: 20px;
            margin-top: 20px;
        }
        
        .pelicula-card {
            background: #222;
            border-radius: 10px;
            overflow: hidden;
            cursor: pointer;
            transition: transform 0.3s, box-shadow 0.3s;
        }
        
        .pelicula-card:hover {
            transform: scale(1.05);
            box-shadow: 0 8px 30px rgba(229, 9, 20, 0.4);
        }
        
        .pelicula-poster {
            width: 100%;
            height: 300px;
            object-fit: cover;
            background: #333;
        }
        
        .pelicula-info {
            padding: 15px;
        }
        
        .pelicula-titulo {
            font-weight: bold;
            margin-bottom: 8px;
            font-size: 16px;
            line-height: 1.3;
            height: 40px;
            overflow: hidden;
        }
        
        .pelicula-meta {
            display: flex;
            justify-content: space-between;
            align-items: center;
            font-size: 14px;
            color: #999;
        }
        
        .rating {
            color: #FFC107;
            font-weight: bold;
        }
        
        .loading {
            text-align: center;
            padding: 60px 20px;
            font-size: 18px;
            color: #666;
        }
        
        .error {
            background: #E50914;
            padding: 20px;
            border-radius: 10px;
            text-align: center;
            margin: 20px;
        }
        
        /* Modal */
        .modal {
            display: none;
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: rgba(0, 0, 0, 0.9);
            z-index: 1000;
            overflow-y: auto;
        }
        
        .modal.show {
            display: block;
        }
        
        .modal-content {
            max-width: 1200px;
            margin: 50px auto;
            background: #141414;
            border-radius: 10px;
            overflow: hidden;
            position: relative;
        }
        
        .modal-close {
            position: absolute;
            top: 20px;
            right: 20px;
            background: rgba(0, 0, 0, 0.7);
            color: white;
            border: none;
            width: 40px;
            height: 40px;
            border-radius: 50%;
            font-size: 24px;
            cursor: pointer;
            z-index: 10;
        }
        
        .modal-backdrop {
            width: 100%;
            height: 500px;
            object-fit: cover;
            opacity: 0.5;
        }
        
        .modal-details {
            padding: 40px;
            margin-top: -100px;
            position: relative;
        }
        
        .modal-header {
            display: flex;
            gap: 30px;
            margin-bottom: 30px;
        }
        
        .modal-poster {
            width: 300px;
            border-radius: 10px;
            box-shadow: 0 10px 40px rgba(0,0,0,0.5);
        }
        
        .modal-info h2 {
            font-size: 2.5em;
            margin-bottom: 10px;
        }
        
        .modal-tagline {
            color: #999;
            font-style: italic;
            margin-bottom: 20px;
        }
        
        .modal-meta {
            display: flex;
            gap: 20px;
            margin-bottom: 20px;
            font-size: 14px;
        }
        
        .meta-item {
            background: #222;
            padding: 8px 15px;
            border-radius: 5px;
        }
        
        .section {
            margin-top: 30px;
        }
        
        .section h3 {
            margin-bottom: 15px;
            color: #E50914;
        }
        
        .cast-grid {
            display: grid;
            grid-template-columns: repeat(auto-fill, minmax(120px, 1fr));
            gap: 15px;
        }
        
        .cast-member {
            text-align: center;
        }
        
        .cast-photo {
            width: 100%;
            height: 150px;
            object-fit: cover;
            border-radius: 10px;
            background: #333;
        }
        
        .cast-name {
            font-size: 14px;
            margin-top: 8px;
            font-weight: bold;
        }
        
        .cast-character {
            font-size: 12px;
            color: #999;
        }
        
        .trailer-list {
            display: flex;
            gap: 15px;
            overflow-x: auto;
            padding-bottom: 10px;
        }
        
        .trailer-item {
            min-width: 300px;
            background: #222;
            padding: 15px;
            border-radius: 10px;
            cursor: pointer;
            transition: background 0.3s;
        }
        
        .trailer-item:hover {
            background: #333;
        }
    </style>
</head>
<body>
    <header>
        <div class="header-content">
            <h1>🎬 CineDB</h1>
            <div class="search-box">
                <div class="search-form">
                    <input 
                        type="text" 
                        id="busqueda" 
                        placeholder="Buscar películas..."
                        onkeypress="if(event.key === 'Enter') buscarPeliculas()"
                    >
                    <button onclick="buscarPeliculas()">Buscar</button>
                </div>
            </div>
        </div>
    </header>
    
    <div class="tabs">
        <button class="tab active" onclick="cambiarTab('populares')">Populares</button>
        <button class="tab" onclick="cambiarTab('cartelera')">En Cartelera</button>
        <button class="tab" onclick="cambiarTab('busqueda')">Búsqueda</button>
    </div>
    
    <div class="container">
        <div id="resultados"></div>
    </div>
    
    <!-- Modal de detalles -->
    <div id="modal" class="modal">
        <div class="modal-content">
            <button class="modal-close" onclick="cerrarModal()">×</button>
            <div id="modalBody"></div>
        </div>
    </div>

    <script>
        let tabActual = 'populares';
        
        window.onload = () => {
            cargarPopulares();
        };
        
        function cambiarTab(tab) {
            tabActual = tab;
            
            // Actualizar tabs activos
            document.querySelectorAll('.tab').forEach(t => t.classList.remove('active'));
            event.target.classList.add('active');
            
            if (tab === 'populares') {
                cargarPopulares();
            } else if (tab === 'cartelera') {
                cargarCartelera();
            } else {
                document.getElementById('resultados').innerHTML = 
                    '<div class="loading">Usa el buscador para encontrar películas</div>';
            }
        }
        
        async function cargarPopulares() {
            const resultados = document.getElementById('resultados');
            resultados.innerHTML = '<div class="loading">🎬 Cargando películas populares...</div>';
            
            try {
                const response = await fetch('/api/peliculas/populares');
                const data = await response.json();
                
                mostrarPeliculas(data.peliculas);
                
            } catch (error) {
                resultados.innerHTML = '<div class="error">❌ Error al cargar películas</div>';
            }
        }
        
        async function cargarCartelera() {
            const resultados = document.getElementById('resultados');
            resultados.innerHTML = '<div class="loading">🎬 Cargando películas en cartelera...</div>';
            
            try {
                const response = await fetch('/api/peliculas/cartelera');
                const peliculas = await response.json();
                
                mostrarPeliculas(peliculas);
                
            } catch (error) {
                resultados.innerHTML = '<div class="error">❌ Error al cargar cartelera</div>';
            }
        }
        
        async function buscarPeliculas() {
            const resultados = document.getElementById('resultados');
            const busqueda = document.getElementById('busqueda').value.trim();
            
            if (!busqueda) {
                alert('Por favor ingresa un término de búsqueda');
                return;
            }
            
            resultados.innerHTML = '<div class="loading">🔍 Buscando películas...</div>';
            
            try {
                const response = await fetch(`/api/peliculas/buscar?q=${encodeURIComponent(busqueda)}`);
                const data = await response.json();
                
                if (data.error) {
                    resultados.innerHTML = `<div class="error">❌ ${data.error}</div>`;
                    return;
                }
                
                if (data.peliculas.length === 0) {
                    resultados.innerHTML = '<div class="loading">No se encontraron películas</div>';
                    return;
                }
                
                mostrarPeliculas(data.peliculas);
                
            } catch (error) {
                resultados.innerHTML = '<div class="error">❌ Error al buscar películas</div>';
            }
        }
        
        function mostrarPeliculas(peliculas) {
            const resultados = document.getElementById('resultados');
            
            resultados.innerHTML = `
                <div class="peliculas-grid">
                    ${peliculas.map(p => `
                        <div class="pelicula-card" onclick="verDetalle(${p.id})">
                            <img 
                                src="${p.poster || '/static/img/no-poster.png'}" 
                                alt="${p.titulo}"
                                class="pelicula-poster"
                                onerror="this.src='/static/img/no-poster.png'"
                            >
                            <div class="pelicula-info">
                                <div class="pelicula-titulo">${p.titulo}</div>
                                <div class="pelicula-meta">
                                    <span class="rating">⭐ ${p.calificacion.toFixed(1)}</span>
                                    <span>${p.fecha_estreno ? p.fecha_estreno.split('-')[0] : 'N/A'}</span>
                                </div>
                            </div>
                        </div>
                    `).join('')}
                </div>
            `;
        }
        
        async function verDetalle(peliculaId) {
            const modal = document.getElementById('modal');
            const modalBody = document.getElementById('modalBody');
            
            modalBody.innerHTML = '<div class="loading" style="padding: 100px;">Cargando detalles...</div>';
            modal.classList.add('show');
            
            try {
                const response = await fetch(`/api/peliculas/${peliculaId}`);
                const pelicula = await response.json();
                
                if (pelicula.error) {
                    modalBody.innerHTML = `<div class="error">${pelicula.error}</div>`;
                    return;
                }
                
                modalBody.innerHTML = `
                    ${pelicula.backdrop ? `<img src="${pelicula.backdrop}" class="modal-backdrop">` : ''}
                    
                    <div class="modal-details">
                        <div class="modal-header">
                            ${pelicula.poster ? `<img src="${pelicula.poster}" class="modal-poster">` : ''}
                            
                            <div class="modal-info">
                                <h2>${pelicula.titulo}</h2>
                                ${pelicula.tagline ? `<p class="modal-tagline">"${pelicula.tagline}"</p>` : ''}
                                
                                <div class="modal-meta">
                                    <div class="meta-item">⭐ ${pelicula.calificacion.toFixed(1)}/10</div>
                                    ${pelicula.duracion ? `<div class="meta-item">🕐 ${pelicula.duracion} min</div>` : ''}
                                    ${pelicula.fecha_estreno ? `<div class="meta-item">📅 ${pelicula.fecha_estreno}</div>` : ''}
                                </div>
                                
                                ${pelicula.generos.length > 0 ? `
                                    <div style="margin-top: 15px;">
                                        ${pelicula.generos.map(g => 
                                            `<span class="meta-item" style="display: inline-block; margin: 5px;">${g}</span>`
                                        ).join('')}
                                    </div>
                                ` : ''}
                                
                                <p style="margin-top: 20px; line-height: 1.6;">${pelicula.descripcion}</p>
                                
                                ${pelicula.director ? `<p style="margin-top: 15px;"><strong>Director:</strong> ${pelicula.director}</p>` : ''}
                            </div>
                        </div>
                        
                        ${pelicula.trailers.length > 0 ? `
                            <div class="section">
                                <h3>Tráilers y Videos</h3>
                                <div class="trailer-list">
                                    ${pelicula.trailers.map(t => `
                                        <div class="trailer-item" onclick="window.open('${t.url}', '_blank')">
                                            <div style="font-weight: bold; margin-bottom: 5px;">▶️ ${t.nombre}</div>
                                            <div style="color: #999; font-size: 14px;">${t.tipo}</div>
                                        </div>
                                    `).join('')}
                                </div>
                            </div>
                        ` : ''}
                        
                        ${pelicula.reparto.length > 0 ? `
                            <div class="section">
                                <h3>Reparto Principal</h3>
                                <div class="cast-grid">
                                    ${pelicula.reparto.map(actor => `
                                        <div class="cast-member">
                                            <img 
                                                src="${actor.foto || '/static/img/no-photo.png'}" 
                                                class="cast-photo"
                                                onerror="this.src='/static/img/no-photo.png'"
                                            >
                                            <div class="cast-name">${actor.nombre}</div>
                                            <div class="cast-character">${actor.personaje}</div>
                                        </div>
                                    `).join('')}
                                </div>
                            </div>
                        ` : ''}
                        
                        ${pelicula.similares.length > 0 ? `
                            <div class="section">
                                <h3>Películas Similares</h3>
                                <div class="peliculas-grid">
                                    ${pelicula.similares.map(sim => `
                                        <div class="pelicula-card" onclick="verDetalle(${sim.id})">
                                            <img src="${sim.poster}" class="pelicula-poster">
                                            <div class="pelicula-info">
                                                <div class="pelicula-titulo">${sim.titulo}</div>
                                                <div class="pelicula-meta">
                                                    <span class="rating">⭐ ${sim.calificacion.toFixed(1)}</span>
                                                </div>
                                            </div>
                                        </div>
                                    `).join('')}
                                </div>
                            </div>
                        ` : ''}
                    </div>
                `;
                
            } catch (error) {
                modalBody.innerHTML = '<div class="error">❌ Error al cargar detalles</div>';
            }
        }
        
        function cerrarModal() {
            document.getElementById('modal').classList.remove('show');
        }
        
        // Cerrar modal al hacer clic fuera
        document.getElementById('modal').onclick = function(e) {
            if (e.target === this) {
                cerrarModal();
            }
        };
    </script>
</body>
</html>
```

---

## ▶️ Paso 4: Ejecutar la Aplicación

```bash
# 1. Activar entorno virtual
source venv/bin/activate  # Linux/Mac
venv\Scripts\activate     # Windows

# 2. Instalar dependencias (si no están instaladas)
pip install flask requests

# 3. Ejecutar
python peliculas_app.py
```

---

## 🎯 Funcionalidades Implementadas

✅ Búsqueda de películas por título  
✅ Películas populares del momento  
✅ Películas en cartelera  
✅ Detalles completos de cada película  
✅ Información del reparto  
✅ Trailers de YouTube  
✅ Películas similares y recomendaciones  
✅ Modal con información detallada  
✅ Interfaz tipo Netflix

---

## 🧪 Endpoints de la API

| Endpoint | Método | Descripción |
|----------|--------|-------------|
| `/api/peliculas/buscar?q=...` | GET | Buscar películas |
| `/api/peliculas/<id>` | GET | Detalles de película |
| `/api/peliculas/populares` | GET | Películas populares |
| `/api/peliculas/cartelera` | GET | Películas en cartelera |
| `/api/series/buscar?q=...` | GET | Buscar series |
| `/api/generos/peliculas` | GET | Lista de géneros |

---

## 📸 Capturas Esperadas

La aplicación mostrará:
- Grid de pósters de películas
- Modal con backdrop, póster, descripción
- Sección de reparto con fotos
- Links a trailers de YouTube
- Películas similares

---

# 🎵 Ejercicio 5.2: Buscador de Música con Spotify Web API

## 🎯 Objetivo
Crear una aplicación para buscar música, artistas, álbumes y playlists usando Spotify Web API.

---

## 📝 Paso 1: Registrarse en Spotify for Developers

1. Ve a [Spotify for Developers](https://developer.spotify.com/)
2. Haz clic en "Dashboard" (esquina superior derecha)
3. Inicia sesión con tu cuenta de Spotify (o crea una)
4. Haz clic en "Create app"
5. Llena el formulario:
   - **App name**: Mi Buscador de Música
   - **App description**: Aplicación para buscar música
   - **Redirect URIs**: http://localhost:5000/callback
   - **API/SDK**: Web API
6. Acepta los términos y haz clic en "Create"
7. En la página de tu app, copia:
   - **Client ID**
   - **Client Secret** (haz clic en "Show client secret")

**Documentación:** https://developer.spotify.com/documentation/web-api

---

## 💻 Paso 2: Crear el Archivo Python

**Archivo: `spotify_app.py`**

```python
from flask import Flask, render_template, request, jsonify, session, redirect, url_for
import requests
import base64
from datetime import datetime, timedelta

app = Flask(__name__)
app.secret_key = 'tu_secret_key_super_secreta_aqui'  # Cambiar por una clave secreta

# Spotify API Credentials
CLIENT_ID = 'TU_CLIENT_ID_AQUI'
CLIENT_SECRET = 'TU_CLIENT_SECRET_AQUI'

# URLs de Spotify
SPOTIFY_AUTH_URL = 'https://accounts.spotify.com/authorize'
SPOTIFY_TOKEN_URL = 'https://accounts.spotify.com/api/token'
SPOTIFY_API_URL = 'https://api.spotify.com/v1'

def get_access_token():
    """
    Obtener token de acceso de Spotify usando Client Credentials Flow.
    Este método es para búsquedas públicas sin necesidad de login de usuario.
    """
    # Verificar si tenemos un token guardado y aún válido
    if 'access_token' in session and 'token_expiry' in session:
        if datetime.now() < datetime.fromisoformat(session['token_expiry']):
            return session['access_token']
    
    # Crear credenciales en base64
    auth_string = f"{CLIENT_ID}:{CLIENT_SECRET}"
    auth_bytes = auth_string.encode('utf-8')
    auth_base64 = base64.b64encode(auth_bytes).decode('utf-8')
    
    # Solicitar token
    headers = {
        'Authorization': f'Basic {auth_base64}',
        'Content-Type': 'application/x-www-form-urlencoded'
    }
    data = {'grant_type': 'client_credentials'}
    
    try:
        response = requests.post(SPOTIFY_TOKEN_URL, headers=headers, data=data, timeout=10)
        
        if response.status_code != 200:
            print(f"Error al obtener token: {response.status_code}")
            print(response.json())
            return None
        
        token_data = response.json()
        access_token = token_data['access_token']
        expires_in = token_data['expires_in']
        
        # Guardar en sesión
        session['access_token'] = access_token
        session['token_expiry'] = (datetime.now() + timedelta(seconds=expires_in - 60)).isoformat()
        
        return access_token
        
    except Exception as e:
        print(f"Error al obtener token: {e}")
        return None

@app.route('/')
def index():
    return render_template('spotify.html')

@app.route('/api/spotify/buscar')
def buscar_spotify():
    """Buscar canciones, artistas, álbumes o playlists"""
    query = request.args.get('q', '')
    tipo = request.args.get('tipo', 'track')  # track, artist, album, playlist
    limite = request.args.get('limite', 20, type=int)
    
    if not query:
        return jsonify({'error': 'Consulta requerida'}), 400
    
    token = get_access_token()
    if not token:
        return jsonify({'error': 'Error al autenticar con Spotify'}), 500
    
    try:
        url = f'{SPOTIFY_API_URL}/search'
        headers = {'Authorization': f'Bearer {token}'}
        params = {
            'q': query,
            'type': tipo,
            'limit': limite,
            'market': 'MX'
        }
        
        response = requests.get(url, headers=headers, params=params, timeout=10)
        
        if response.status_code != 200:
            return jsonify({'error': f'Error de Spotify API: {response.status_code}'}), 500
        
        data = response.json()
        resultados = []
        
        # Procesar según el tipo
        if tipo == 'track':
            for track in data.get('tracks', {}).get('items', []):
                resultados.append({
                    'id': track['id'],
                    'nombre': track['name'],
                    'artistas': [a['name'] for a in track['artists']],
                    'artista_principal': track['artists'][0]['name'] if track['artists'] else '',
                    'album': track['album']['name'],
                    'imagen': track['album']['images'][0]['url'] if track['album']['images'] else None,
                    'duracion_ms': track['duration_ms'],
                    'duracion': f"{track['duration_ms'] // 60000}:{(track['duration_ms'] % 60000) // 1000:02d}",
                    'preview_url': track['preview_url'],
                    'spotify_url': track['external_urls']['spotify'],
                    'popularidad': track['popularity'],
                    'explicito': track['explicit']
                })
        
        elif tipo == 'artist':
            for artist in data.get('artists', {}).get('items', []):
                resultados.append({
                    'id': artist['id'],
                    'nombre': artist['name'],
                    'generos': artist['genres'],
                    'popularidad': artist['popularity'],
                    'imagen': artist['images'][0]['url'] if artist['images'] else None,
                    'seguidores': artist['followers']['total'],
                    'spotify_url': artist['external_urls']['spotify']
                })
        
        elif tipo == 'album':
            for album in data.get('albums', {}).get('items', []):
                resultados.append({
                    'id': album['id'],
                    'nombre': album['name'],
                    'artistas': [a['name'] for a in album['artists']],
                    'fecha_lanzamiento': album['release_date'],
                    'total_tracks': album['total_tracks'],
                    'imagen': album['images'][0]['url'] if album['images'] else None,
                    'spotify_url': album['external_urls']['spotify'],
                    'tipo': album['album_type']
                })
        
        elif tipo == 'playlist':
            for playlist in data.get('playlists', {}).get('items', []):
                resultados.append({
                    'id': playlist['id'],
                    'nombre': playlist['name'],
                    'descripcion': playlist['description'],
                    'owner': playlist['owner']['display_name'],
                    'total_tracks': playlist['tracks']['total'],
                    'imagen': playlist['images'][0]['url'] if playlist['images'] else None,
                    'spotify_url': playlist['external_urls']['spotify'],
                    'publica': playlist['public']
                })
        
        return jsonify(resultados)
        
    except Exception as e:
        print(f"Error en buscar_spotify: {e}")
        return jsonify({'error': str(e)}), 500

@app.route('/api/spotify/artista/<artist_id>')
def info_artista(artist_id):
    """Obtener información completa de un artista"""
    token = get_access_token()
    if not token:
        return jsonify({'error': 'Error al autenticar'}), 500
    
    try:
        headers = {'Authorization': f'Bearer {token}'}
        
        # Información del artista
        artist_response = requests.get(
            f'{SPOTIFY_API_URL}/artists/{artist_id}',
            headers=headers,
            timeout=10
        )
        artist = artist_response.json()
        
        # Top tracks del artista
        top_response = requests.get(
            f'{SPOTIFY_API_URL}/artists/{artist_id}/top-tracks',
            headers=headers,
            params={'market': 'MX'},
            timeout=10
        )
        top_tracks = top_response.json().get('tracks', [])
        
        # Álbumes del artista
        albums_response = requests.get(
            f'{SPOTIFY_API_URL}/artists/{artist_id}/albums',
            headers=headers,
            params={'limit': 10, 'market': 'MX'},
            timeout=10
        )
        albums = albums_response.json().get('items', [])
        
        # Artistas relacionados
        related_response = requests.get(
            f'{SPOTIFY_API_URL}/artists/{artist_id}/related-artists',
            headers=headers,
            timeout=10
        )
        related = related_response.json().get('artists', [])
        
        resultado = {
            'id': artist['id'],
            'nombre': artist['name'],
            'generos': artist['genres'],
            'popularidad': artist['popularity'],
            'seguidores': artist['followers']['total'],
            'imagen': artist['images'][0]['url'] if artist['images'] else None,
            'spotify_url': artist['external_urls']['spotify'],
            'top_canciones': [
                {
                    'id': track['id'],
                    'nombre': track['name'],
                    'album': track['album']['name'],
                    'preview': track['preview_url'],
                    'imagen': track['album']['images'][0]['url'] if track['album']['images'] else None,
                    'duracion': f"{track['duration_ms'] // 60000}:{(track['duration_ms'] % 60000) // 1000:02d}",
                    'spotify_url': track['external_urls']['spotify']
                }
                for track in top_tracks[:10]
            ],
            'albums': [
                {
                    'id': album['id'],
                    'nombre': album['name'],
                    'fecha': album['release_date'],
                    'imagen': album['images'][0]['url'] if album['images'] else None,
                    'total_tracks': album['total_tracks'],
                    'tipo': album['album_type']
                }
                for album in albums
            ],
            'artistas_relacionados': [
                {
                    'id': rel['id'],
                    'nombre': rel['name'],
                    'imagen': rel['images'][0]['url'] if rel['images'] else None,
                    'popularidad': rel['popularity']
                }
                for rel in related[:6]
            ]
        }
        
        return jsonify(resultado)
        
    except Exception as e:
        print(f"Error en info_artista: {e}")
        return jsonify({'error': str(e)}), 500

@app.route('/api/spotify/album/<album_id>')
def info_album(album_id):
    """Obtener información de un álbum"""
    token = get_access_token()
    if not token:
        return jsonify({'error': 'Error al autenticar'}), 500
    
    try:
        headers = {'Authorization': f'Bearer {token}'}
        
        response = requests.get(
            f'{SPOTIFY_API_URL}/albums/{album_id}',
            headers=headers,
            params={'market': 'MX'},
            timeout=10
        )
        album = response.json()
        
        resultado = {
            'id': album['id'],
            'nombre': album['name'],
            'artistas': [a['name'] for a in album['artists']],
            'fecha_lanzamiento': album['release_date'],
            'total_tracks': album['total_tracks'],
            'imagen': album['images'][0]['url'] if album['images'] else None,
            'generos': album['genres'],
            'sello': album['label'],
            'popularidad': album['popularity'],
            'spotify_url': album['external_urls']['spotify'],
            'tracks': [
                {
                    'numero': track['track_number'],
                    'nombre': track['name'],
                    'duracion': f"{track['duration_ms'] // 60000}:{(track['duration_ms'] % 60000) // 1000:02d}",
                    'preview': track['preview_url'],
                    'spotify_url': track['external_urls']['spotify']
                }
                for track in album['tracks']['items']
            ]
        }
        
        return jsonify(resultado)
        
    except Exception as e:
        print(f"Error en info_album: {e}")
        return jsonify({'error': str(e)}), 500

@app.route('/api/spotify/recomendaciones')
def obtener_recomendaciones():
    """Obtener recomendaciones basadas en géneros"""
    generos = request.args.get('generos', 'pop,rock')
    limite = request.args.get('limite', 20, type=int)
    
    token = get_access_token()
    if not token:
        return jsonify({'error': 'Error al autenticar'}), 500
    
    try:
        headers = {'Authorization': f'Bearer {token}'}
        
        response = requests.get(
            f'{SPOTIFY_API_URL}/recommendations',
            headers=headers,
            params={
                'seed_genres': generos,
                'limit': limite,
                'market': 'MX'
            },
            timeout=10
        )
        data = response.json()
        
        recomendaciones = [
            {
                'id': track['id'],
                'nombre': track['name'],
                'artistas': [a['name'] for a in track['artists']],
                'album': track['album']['name'],
                'imagen': track['album']['images'][0]['url'] if track['album']['images'] else None,
                'preview_url': track['preview_url'],
                'spotify_url': track['external_urls']['spotify']
            }
            for track in data.get('tracks', [])
        ]
        
        return jsonify(recomendaciones)
        
    except Exception as e:
        print(f"Error en recomendaciones: {e}")
        return jsonify({'error': str(e)}), 500

@app.route('/api/spotify/generos')
def obtener_generos():
    """Obtener lista de géneros disponibles"""
    token = get_access_token()
    if not token:
        return jsonify({'error': 'Error al autenticar'}), 500
    
    try:
        headers = {'Authorization': f'Bearer {token}'}
        
        response = requests.get(
            f'{SPOTIFY_API_URL}/recommendations/available-genre-seeds',
            headers=headers,
            timeout=10
        )
        data = response.json()
        
        return jsonify(data.get('genres', []))
        
    except Exception as e:
        print(f"Error en obtener_generos: {e}")
        return jsonify({'error': str(e)}), 500

if __name__ == '__main__':
    print("=" * 60)
    print("🎵 Buscador de Música - Spotify Web API")
    print("=" * 60)
    
    if CLIENT_ID == 'TU_CLIENT_ID_AQUI':
        print("⚠️  ADVERTENCIA: Credenciales no configuradas")
        print("   Obtén tus credenciales en:")
        print("   https://developer.spotify.com/dashboard")
    else:
        print(f"✅ Client ID configurado: {CLIENT_ID[:20]}...")
    
    print("🌐 Servidor corriendo en: http://127.0.0.1:5000")
    print("=" * 60)
    
    app.run(debug=True)
```

---

## 🎨 Paso 3: Crear la Interfaz HTML

**Archivo: `templates/spotify.html`**

```html
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Buscador de Música - Spotify</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }
        
        body {
            font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Arial, sans-serif;
            background: linear-gradient(135deg, #1DB954 0%, #191414 100%);
            min-height: 100vh;
            color: white;
        }
        
        header {
            background: rgba(0, 0, 0, 0.8);
            padding: 20px;
            position: sticky;
            top: 0;
            z-index: 100;
            backdrop-filter: blur(10px);
        }
        
        .header-content {
            max-width: 1400px;
            margin: 0 auto;
            display: flex;
            justify-content: space-between;
            align-items: center;
            gap: 20px;
            flex-wrap: wrap;
        }
        
        h1 {
            color: #1DB954;
            font-size: 2em;
            display: flex;
            align-items: center;
            gap: 10px;
        }
        
        .search-container {
            flex: 1;
            max-width: 600px;
            min-width: 300px;
        }
        
        .search-box {
            display: flex;
            gap: 10px;
            margin-bottom: 10px;
        }
        
        input[type="text"] {
            flex: 1;
            padding: 12px 20px;
            border: none;
            border-radius: 25px;
            background: #282828;
            color: white;
            font-size: 16px;
        }
        
        input[type="text"]:focus {
            outline: 2px solid #1DB954;
        }
        
        select {
            padding: 12px 20px;
            border: none;
            border-radius: 25px;
            background: #282828;
            color: white;
            cursor: pointer;
        }
        
        button {
            padding: 12px 30px;
            background: #1DB954;
            color: white;
            border: none;
            border-radius: 25px;
            font-weight: bold;
            cursor: pointer;
            transition: transform 0.2s, background 0.3s;
        }
        
        button:hover {
            background: #1ed760;
            transform: scale(1.05);
        }
        
        .container {
            max-width: 1400px;
            margin: 0 auto;
            padding: 30px 20px;
        }
        
        .resultados-grid {
            display: grid;
            grid-template-columns: repeat(auto-fill, minmax(200px, 1fr));
            gap: 20px;
            margin-top: 20px;
        }
        
        .resultado-card {
            background: rgba(40, 40, 40, 0.8);
            backdrop-filter: blur(10px);
            border-radius: 15px;
            padding: 15px;
            cursor: pointer;
            transition: all 0.3s;
        }
        
        .resultado-card:hover {
            background: rgba(40, 40, 40, 1);
            transform: translateY(-5px);
            box-shadow: 0 10px 30px rgba(29, 185, 84, 0.3);
        }
        
        .resultado-imagen {
            width: 100%;
            aspect-ratio: 1;
            object-fit: cover;
            border-radius: 10px;
            background: #282828;
            margin-bottom: 12px;
        }
        
        .resultado-nombre {
            font-weight: bold;
            margin-bottom: 5px;
            font-size: 16px;
            overflow: hidden;
            text-overflow: ellipsis;
            white-space: nowrap;
        }
        
        .resultado-info {
            color: #b3b3b3;
            font-size: 14px;
            overflow: hidden;
            text-overflow: ellipsis;
            white-space: nowrap;
        }
        
        .resultado-meta {
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin-top: 10px;
            padding-top: 10px;
            border-top: 1px solid #404040;
            font-size: 13px;
        }
        
        .play-button {
            width: 40px;
            height: 40px;
            background: #1DB954;
            border-radius: 50%;
            display: flex;
            align-items: center;
            justify-content: center;
            cursor: pointer;
            transition: transform 0.2s;
            margin-top: 10px;
        }
        
        .play-button:hover {
            transform: scale(1.1);
        }
        
        .loading {
            text-align: center;
            padding: 60px 20px;
            font-size: 18px;
            color: #b3b3b3;
        }
        
        .error {
            background: rgba(255, 0, 0, 0.2);
            border: 2px solid #ff0000;
            padding: 20px;
            border-radius: 15px;
            text-align: center;
            margin: 20px;
        }
        
        .stats {
            display: flex;
            gap: 10px;
            font-size: 12px;
            color: #b3b3b3;
            flex-wrap: wrap;
        }
        
        .stat-badge {
            background: rgba(29, 185, 84, 0.2);
            padding: 4px 10px;
            border-radius: 12px;
        }
        
        /* Audio player personalizado */
        audio {
            width: 100%;
            height: 40px;
            margin-top: 10px;
            border-radius: 20px;
        }
        
        audio::-webkit-media-controls-panel {
            background: #282828;
        }
    </style>
</head>
<body>
    <header>
        <div class="header-content">
            <h1>
                <span>🎵</span>
                <span>Spotify Search</span>
            </h1>
            
            <div class="search-container">
                <div class="search-box">
                    <input 
                        type="text" 
                        id="busqueda" 
                        placeholder="Buscar canciones, artistas, álbumes..."
                        onkeypress="if(event.key === 'Enter') buscar()"
                    >
                    <select id="tipo">
                        <option value="track">🎵 Canciones</option>
                        <option value="artist">👤 Artistas</option>
                        <option value="album">💿 Álbumes</option>
                        <option value="playlist">📋 Playlists</option>
                    </select>
                    <button onclick="buscar()">Buscar</button>
                </div>
            </div>
        </div>
    </header>
    
    <div class="container">
        <div id="resultados"></div>
    </div>
    
    <!-- Reproductor de audio actual -->
    <div id="audioPlayer" style="position: fixed; bottom: 0; left: 0; right: 0; background: rgba(0,0,0,0.95); padding: 15px; display: none; backdrop-filter: blur(20px);">
        <div style="max-width: 1400px; margin: 0 auto; display: flex; align-items: center; gap: 15px;">
            <img id="playerImage" style="width: 60px; height: 60px; border-radius: 8px;">
            <div style="flex: 1;">
                <div id="playerTrack" style="font-weight: bold;"></div>
                <div id="playerArtist" style="color: #b3b3b3; font-size: 14px;"></div>
            </div>
            <audio id="audio" controls style="flex: 1; max-width: 400px;"></audio>
        </div>
    </div>

    <script>
        let audioActual = null;
        
        async function buscar() {
            const resultados = document.getElementById('resultados');
            const busqueda = document.getElementById('busqueda').value.trim();
            const tipo = document.getElementById('tipo').value;
            
            if (!busqueda) {
                alert('Por favor ingresa un término de búsqueda');
                return;
            }
            
            resultados.innerHTML = '<div class="loading">🔍 Buscando en Spotify...</div>';
            
            try {
                const url = `/api/spotify/buscar?q=${encodeURIComponent(busqueda)}&tipo=${tipo}`;
                const response = await fetch(url);
                const data = await response.json();
                
                if (data.error) {
                    resultados.innerHTML = `<div class="error">❌ ${data.error}</div>`;
                    return;
                }
                
                if (data.length === 0) {
                    resultados.innerHTML = '<div class="loading">No se encontraron resultados</div>';
                    return;
                }
                
                mostrarResultados(data, tipo);
                
            } catch (error) {
                resultados.innerHTML = '<div class="error">❌ Error al buscar en Spotify</div>';
                console.error('Error:', error);
            }
        }
        
        function mostrarResultados(datos, tipo) {
            const resultados = document.getElementById('resultados');
            
            if (tipo === 'track') {
                resultados.innerHTML = `
                    <div class="resultados-grid">
                        ${datos.map(track => `
                            <div class="resultado-card">
                                <img src="${track.imagen || '/static/img/no-music.png'}" class="resultado-imagen">
                                <div class="resultado-nombre">${track.nombre}</div>
                                <div class="resultado-info">${track.artista_principal}</div>
                                <div class="resultado-info" style="font-size: 12px;">${track.album}</div>
                                
                                <div class="stats">
                                    <span class="stat-badge">⏱️ ${track.duracion}</span>
                                    <span class="stat-badge">🔥 ${track.popularidad}%</span>
                                    ${track.explicito ? '<span class="stat-badge">🅴</span>' : ''}
                                </div>
                                
                                ${track.preview_url ? `
                                    <div class="play-button" onclick="reproducir('${track.preview_url}', '${track.nombre.replace(/'/g, "\\'")}', '${track.artista_principal.replace(/'/g, "\\'")}', '${track.imagen}')">
                                        ▶️
                                    </div>
                                ` : ''}
                                
                                <div class="resultado-meta">
                                    <a href="${track.spotify_url}" target="_blank" style="color: #1DB954; text-decoration: none;">
                                        Abrir en Spotify
                                    </a>
                                </div>
                            </div>
                        `).join('')}
                    </div>
                `;
            }
            else if (tipo === 'artist') {
                resultados.innerHTML = `
                    <div class="resultados-grid">
                        ${datos.map(artist => `
                            <div class="resultado-card" onclick="verArtista('${artist.id}')">
                                <img src="${artist.imagen || '/static/img/no-artist.png'}" class="resultado-imagen" style="border-radius: 50%;">
                                <div class="resultado-nombre">${artist.nombre}</div>
                                
                                <div class="stats">
                                    <span class="stat-badge">👥 ${artist.seguidores.toLocaleString()}</span>
                                    <span class="stat-badge">🔥 ${artist.popularidad}%</span>
                                </div>
                                
                                ${artist.generos.length > 0 ? `
                                    <div class="resultado-info" style="margin-top: 10px;">
                                        ${artist.generos.slice(0, 2).join(', ')}
                                    </div>
                                ` : ''}
                                
                                <div class="resultado-meta">
                                    <a href="${artist.spotify_url}" target="_blank" style="color: #1DB954; text-decoration: none;" onclick="event.stopPropagation()">
                                        Abrir en Spotify
                                    </a>
                                </div>
                            </div>
                        `).join('')}
                    </div>
                `;
            }
            else if (tipo === 'album') {
                resultados.innerHTML = `
                    <div class="resultados-grid">
                        ${datos.map(album => `
                            <div class="resultado-card" onclick="verAlbum('${album.id}')">
                                <img src="${album.imagen || '/static/img/no-album.png'}" class="resultado-imagen">
                                <div class="resultado-nombre">${album.nombre}</div>
                                <div class="resultado-info">${album.artistas.join(', ')}</div>
                                
                                <div class="stats">
                                    <span class="stat-badge">📅 ${album.fecha_lanzamiento}</span>
                                    <span class="stat-badge">🎵 ${album.total_tracks} tracks</span>
                                </div>
                                
                                <div class="resultado-meta">
                                    <span style="color: #b3b3b3; text-transform: capitalize;">${album.tipo}</span>
                                    <a href="${album.spotify_url}" target="_blank" style="color: #1DB954; text-decoration: none;" onclick="event.stopPropagation()">
                                        Spotify
                                    </a>
                                </div>
                            </div>
                        `).join('')}
                    </div>
                `;
            }
            else if (tipo === 'playlist') {
                resultados.innerHTML = `
                    <div class="resultados-grid">
                        ${datos.map(playlist => `
                            <div class="resultado-card">
                                <img src="${playlist.imagen || '/static/img/no-playlist.png'}" class="resultado-imagen">
                                <div class="resultado-nombre">${playlist.nombre}</div>
                                <div class="resultado-info">Por ${playlist.owner}</div>
                                
                                <div class="stats">
                                    <span class="stat-badge">🎵 ${playlist.total_tracks} canciones</span>
                                    ${playlist.publica ? '<span class="stat-badge">🌍 Pública</span>' : '<span class="stat-badge">🔒 Privada</span>'}
                                </div>
                                
                                ${playlist.descripcion ? `
                                    <div class="resultado-info" style="margin-top: 10px; font-size: 12px; display: -webkit-box; -webkit-line-clamp: 2; -webkit-box-orient: vertical; overflow: hidden;">
                                        ${playlist.descripcion}
                                    </div>
                                ` : ''}
                                
                                <div class="resultado-meta">
                                    <a href="${playlist.spotify_url}" target="_blank" style="color: #1DB954; text-decoration: none;">
                                        Abrir en Spotify
                                    </a>
                                </div>
                            </div>
                        `).join('')}
                    </div>
                `;
            }
        }
        
        function reproducir(previewUrl, nombre, artista, imagen) {
            const audio = document.getElementById('audio');
            const player = document.getElementById('audioPlayer');
            
            // Actualizar información
            document.getElementById('playerImage').src = imagen;
            document.getElementById('playerTrack').textContent = nombre;
            document.getElementById('playerArtist').textContent = artista;
            
            // Reproducir
            audio.src = previewUrl;
            audio.play();
            player.style.display = 'block';
        }
        
        async function verArtista(artistId) {
            const resultados = document.getElementById('resultados');
            resultados.innerHTML = '<div class="loading">Cargando información del artista...</div>';
            
            try {
                const response = await fetch(`/api/spotify/artista/${artistId}`);
                const artista = await response.json();
                
                if (artista.error) {
                    resultados.innerHTML = `<div class="error">${artista.error}</div>`;
                    return;
                }
                
                resultados.innerHTML = `
                    <div style="margin-bottom: 30px;">
                        <button onclick="location.reload()" style="background: #282828;">← Volver</button>
                    </div>
                    
                    <div style="display: flex; gap: 40px; margin-bottom: 40px; align-items: start;">
                        <img src="${artista.imagen}" style="width: 250px; height: 250px; object-fit: cover; border-radius: 50%;">
                        <div>
                            <h2 style="font-size: 3em; margin-bottom: 10px;">${artista.nombre}</h2>
                            <div style="display: flex; gap: 15px; margin-bottom: 20px;">
                                <span class="stat-badge" style="font-size: 16px;">👥 ${artista.seguidores.toLocaleString()} seguidores</span>
                                <span class="stat-badge" style="font-size: 16px;">🔥 ${artista.popularidad}% popularidad</span>
                            </div>
                            ${artista.generos.length > 0 ? `
                                <div style="margin-top: 15px;">
                                    ${artista.generos.map(g => `<span class="stat-badge">${g}</span>`).join(' ')}
                                </div>
                            ` : ''}
                        </div>
                    </div>
                    
                    <h3 style="margin-bottom: 20px; color: #1DB954;">🎵 Canciones Más Populares</h3>
                    <div class="resultados-grid">
                        ${artista.top_canciones.map((track, index) => `
                            <div class="resultado-card">
                                <div style="position: absolute; top: 10px; left: 10px; background: rgba(0,0,0,0.8); padding: 5px 10px; border-radius: 20px;">
                                    #${index + 1}
                                </div>
                                <img src="${track.imagen}" class="resultado-imagen">
                                <div class="resultado-nombre">${track.nombre}</div>
                                <div class="resultado-info">${track.album}</div>
                                <div class="stats">
                                    <span class="stat-badge">⏱️ ${track.duracion}</span>
                                </div>
                                ${track.preview ? `
                                    <div class="play-button" onclick="reproducir('${track.preview}', '${track.nombre.replace(/'/g, "\\'")}', '${artista.nombre}', '${track.imagen}')">
                                        ▶️
                                    </div>
                                ` : ''}
                            </div>
                        `).join('')}
                    </div>
                    
                    ${artista.albums.length > 0 ? `
                        <h3 style="margin: 40px 0 20px; color: #1DB954;">💿 Álbumes</h3>
                        <div class="resultados-grid">
                            ${artista.albums.map(album => `
                                <div class="resultado-card">
                                    <img src="${album.imagen}" class="resultado-imagen">
                                    <div class="resultado-nombre">${album.nombre}</div>
                                    <div class="stats">
                                        <span class="stat-badge">📅 ${album.fecha}</span>
                                        <span class="stat-badge">🎵 ${album.total_tracks}</span>
                                    </div>
                                </div>
                            `).join('')}
                        </div>
                    ` : ''}
                `;
                
            } catch (error) {
                resultados.innerHTML = '<div class="error">Error al cargar artista</div>';
            }
        }
        
        async function verAlbum(albumId) {
            const resultados = document.getElementById('resultados');
            resultados.innerHTML = '<div class="loading">Cargando álbum...</div>';
            
            try {
                const response = await fetch(`/api/spotify/album/${albumId}`);
                const album = await response.json();
                
                resultados.innerHTML = `
                    <div style="margin-bottom: 30px;">
                        <button onclick="location.reload()" style="background: #282828;">← Volver</button>
                    </div>
                    
                    <div style="display: flex; gap: 40px; margin-bottom: 40px;">
                        <img src="${album.imagen}" style="width: 250px; height: 250px; border-radius: 10px;">
                        <div>
                            <h2 style="font-size: 2.5em; margin-bottom: 10px;">${album.nombre}</h2>
                            <p style="font-size: 18px; color: #b3b3b3; margin-bottom: 15px;">${album.artistas.join(', ')}</p>
                            <div style="display: flex; gap: 15px;">
                                <span class="stat-badge" style="font-size: 14px;">📅 ${album.fecha_lanzamiento}</span>
                                <span class="stat-badge" style="font-size: 14px;">🎵 ${album.total_tracks} canciones</span>
                                <span class="stat-badge" style="font-size: 14px;">🔥 ${album.popularidad}%</span>
                            </div>
                            ${album.sello ? `<p style="margin-top: 15px; color: #b3b3b3;">Sello: ${album.sello}</p>` : ''}
                        </div>
                    </div>
                    
                    <h3 style="margin-bottom: 20px; color: #1DB954;">Canciones</h3>
                    <div style="background: rgba(40,40,40,0.5); border-radius: 15px; padding: 20px;">
                        ${album.tracks.map((track, index) => `
                            <div style="display: flex; align-items: center; gap: 15px; padding: 12px; border-radius: 8px; margin-bottom: 10px; background: ${index % 2 === 0 ? 'rgba(0,0,0,0.2)' : 'transparent'};">
                                <span style="width: 30px; text-align: center; color: #b3b3b3;">${track.numero}</span>
                                <div style="flex: 1;">
                                    <div style="font-weight: bold;">${track.nombre}</div>
                                </div>
                                <span style="color: #b3b3b3;">${track.duracion}</span>
                                ${track.preview ? `
                                    <button onclick="reproducir('${track.preview}', '${track.nombre.replace(/'/g, "\\'")}', '${album.artistas[0]}', '${album.imagen}')" style="width: 40px; height: 40px; border-radius: 50%; padding: 0;">
                                        ▶️
                                    </button>
                                ` : ''}
                            </div>
                        `).join('')}
                    </div>
                `;
                
            } catch (error) {
                resultados.innerHTML = '<div class="error">Error al cargar álbum</div>';
            }
        }
    </script>
</body>
</html>
```

---

## ▶️ Paso 4: Ejecutar la Aplicación

```bash
# Activar entorno virtual
source venv/bin/activate  # Linux/Mac
venv\Scripts\activate     # Windows

# Instalar dependencias
pip install flask requests

# Ejecutar
python spotify_app.py
```

---

## 🎯 Funcionalidades Implementadas

✅ Búsqueda de canciones con preview de 30 segundos  
✅ Búsqueda de artistas con información completa  
✅ Búsqueda de álbumes con listado de tracks  
✅ Búsqueda de playlists  
✅ Información detallada de artistas  
✅ Top canciones de cada artista  
✅ Reproductor de audio integrado  
✅ Enlaces directos a Spotify  
✅ Interfaz estilo Spotify

---

## 🧪 Endpoints de la API

| Endpoint | Método | Descripción |
|----------|--------|-------------|
| `/api/spotify/buscar?q=...&tipo=...` | GET | Buscar contenido |
| `/api/spotify/artista/<id>` | GET | Info de artista |
| `/api/spotify/album/<id>` | GET | Info de álbum |
| `/api/spotify/recomendaciones` | GET | Recomendaciones |
| `/api/spotify/generos` | GET | Lista de géneros |

---

## 🎵 Pruebas Sugeridas

1. Buscar "Queen" (artista)
2. Buscar "Bohemian Rhapsody" (canción)
3. Buscar "Abbey Road" (álbum)
4. Hacer clic en un artista para ver detalles
5. Reproducir preview de canciones

---

## ⚠️ Notas Importantes

- **Preview de audio**: Solo 30 segundos disponibles
- **Sin autenticación de usuario**: Usa Client Credentials Flow
- **Límites de API**: 
  - Sin límite estricto con Client Credentials
  - Recomendado no exceder 100 requests/minuto

---

## 🔧 Solución de Problemas

### Error 401
- Verificar Client ID y Client Secret
- Asegurarse de que las credenciales estén correctas

### No hay preview disponible
- No todas las canciones tienen preview
- Es normal, depende del acuerdo con Spotify

### Token expirado
- El token se renueva automáticamente
- Válido por 1 hora

---

¡Listo! Ahora tienes un buscador completo de música con Spotify. 🎵

## ❓ Solución de Problemas Comunes

### Error: ModuleNotFoundError

```bash
# Solución: Instalar módulos faltantes
pip install flask requests firebase-admin
```

### Error: API Key Inválida

- Verificar que la API key esté correcta
- Revisar límites de requests (algunas APIs tienen límites diarios)
- Confirmar que la API key esté activa

### Error: CORS

Si hay errores de CORS, agregar al app.py:

```python
from flask_cors import CORS

app = Flask(__name__)
CORS(app)
```

Instalar flask-cors:

```bash
pip install flask-cors
```

### Puerto Ya en Uso

```bash
# Cambiar puerto en app.run()
app.run(debug=True, port=5001)
```

### Error de Firebase

- Verificar que `firebase-credentials.json` existe
- Confirmar que la URL de la database es correcta
- Revisar permisos en Firebase Console

---

## 📦 Estructura Final del Proyecto

```
ejercicios-apis/
├── venv/
├── templates/
│   ├── clima.html
│   ├── lugares.html
│   ├── reddit.html
│   ├── github.html
│   ├── productos.html
│   ├── chat.html
│   ├── libros.html
│   ├── divisas.html
│   ├── peliculas.html
│   └── spotify.html
├── static/
│   ├── css/
│   ├── js/
│   └── img/
├── clima_app.py
├── lugares_app.py
├── reddit_app.py
├── github_app.py
├── productos_api.py
├── productos.db
├── chat_app.py
├── firebase-credentials.json
├── libros_app.py
├── divisas_app.py
├── peliculas_app.py
├── spotify_app.py
└── requirements.txt
```

---

## 🎓 Entregables del Proyecto

Para cada ejercicio, entregar:

1. **Código fuente**:
   - `app.py` con comentarios
   - Templates HTML

2. **README.md** con:
   - Instrucciones de instalación
   - API keys necesarias
   - Capturas de pantalla

3. **Video demostrativo** (2-5 minutos):
   - Ejecución de la aplicación
   - Demostración de funcionalidades

4. **Reporte técnico** con:
   - Análisis de la API utilizada
   - Diagramas de flujo
   - Pruebas realizadas
   - Conclusiones

---

## 🚀 Tips para el Éxito

1. **Probar cada endpoint individualmente** antes de integrarlo
2. **Usar herramientas como Postman** para probar APIs
3. **Leer la documentación oficial** de cada API
4. **Manejar errores apropiadamente** con try-catch
5. **Agregar loading states** en la interfaz
6. **Validar datos del usuario** antes de enviar a la API
7. **Respetar límites de rate** de las APIs
8. **Usar variables de entorno** para API keys

---

## 📚 Recursos Adicionales

- [Documentación Flask](https://flask.palletsprojects.com/)
- [Lista de APIs Públicas](https://github.com/public-apis/public-apis)
- [RapidAPI Hub](https://rapidapi.com/hub)
- [Postman](https://www.postman.com/)
- [MDN Web Docs - Fetch API](https://developer.mozilla.org/es/docs/Web/API/Fetch_API)

---

**¡Éxito en tus proyectos! 🎉**

