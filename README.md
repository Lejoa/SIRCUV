# SIRCUV

Sircuv es un proyecto académico que busca crear un prototipo de sistema de recomendación para la Universidad del Valle, teniendo como objetivo realizar sugerencias de contenido bibliografíco.

La implementación y base del sistema para construir las nuevas funcionalidades toma como base el proyecto [MovieGeek](https://github.com/practical-recommender-systems/moviegeek) y el libro guía [Practical Recommender Systems](https://www.manning.com/books/practical-recommender-systems) de [Kim Falk](https://kimfalk.org/) .

## ¡Agradecimientos y créditos!
La base del proyecto no sería posible sin:
* [MovieTweetings](https://github.com/sidooms/MovieTweetings)
* [themoviedb.org](https://www.themoviedb.org)
* [Kim Falk](https://kimfalk.org/) 
## Configuración y despliegue del proyecto
### Instalar Python 3.x
El proyecto requiere tener la versión de Python 3.x  
### Clonar Código fuente
```bash
git clone https://github.com/Lejoa/SIRCUV.git
```
### Crear un ambiente virtual para el proyecto
Verificar que virtualenv se encuentre instalado.
```bash
virtualenv --version
```
Una vez verificado que virtualenv se encuentra instalado, se crea un ambiente usando los siguientes comandos:

```bash
cd moviegeek
virtualenv -p python3 sircuv
source sircuv/bin/activate
```
### Instalar las diferentes dependencias del proyecto

Se usa pip para instalar las dependencias definidas en el archivo:
```bash
pip3 install -r requirements.txt
```
### Configurar la base de datos con PostgreSQL

Se debe crear una base de datos con el nombre `moviegeek` y configurar la
conexión de Django para establecer la comunicación.

Abrir el archivo `prs_project/settings.py` 

Actualizar el siguiente fragmento:

```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.postgresql',
        'NAME': 'moviegeek',                      
        'USER': 'db_user',
        'PASSWORD': 'db_user_password',
        'HOST': 'db_host',
        'PORT': 'db_port_number',
    }
}
```
Actualizar los campos USER, PASSWORD, HOST, and PORT:
* USER(db_user): Usar el nombre de usuario que creo la base de datos de MovieGeek.
* PASSWORD(db_user_password): Usar la contraseña del usuario que creo la base de datos MovieGeek.
* HOST (db_host): localhost (Para el caso de que se encuente instalado en tu pc personal)
* PORT (db_port_number): 5432 (Es el puerto por defecto)

### Crear y poblar la base de datos de MovieGeek

Para crear y poblar la base de datos de MovieGeek se deben ejecutar los siguientes comandos:

```bash
python3 manage.py makemigrations
python3 manage.py migrate --run-syncdb
```

```bash
python3 populate_moviegeek.py
```

###  Crear un ID en themoviedb.org

Se debe crear un ID en themoviedb.org para hacer uso de sus imagenes.

* Dirigirse a  [https://www.themoviedb.org/account/signup](https://www.themoviedb.org/account/signup) 
* Crear un usuario
* Acceder y dirigirse a la configuración de la cuenta[create an API](https://www.themoviedb.org/settings/api). Se puede acceder a la configuración haciendo click en el avatar ubicado en la esquina superior derecha(El avatar por defecto es un circulo azul con un logo en el).Entonces ahí podra ver las configuraciones sobre la izquierda. 
* Crear un archivo en el directorio de moviegeek llamado ".prs".
* Abrir .prs y añadir { "themoviedb_apikey": <INSERT YOUR APIKEY HERE>}
Recuerde remover el "<" y ">" cuando haya finalizado, El archivo debería contener algo similar a esto: 
{"themoviedb_apikey": "6d88c9a24b1bc9a60b374d3fe2cd92ac"}

### Iniciar el servidor web

Para iniciar el servidor de desarrollo, corra el siguiente comando:
```bash
python3 manage.py runserver 127.0.0.1:8000
```
Correr el servidor hará que la aplicación este disponible[http://127.0.0.1:8000](http://127.0.0.1:8000) 

ALERTA: Si otras aplicaciones están haciendo uso del puerto 8000 se debe cambiar a otro(8001).

### Puesta en marcha de todas las funcionalidades del proyecto

```bash
python3 populate_ratings.py
```

Se pobla la base de datos con información: usuario, película y una acción(vistas, compras y más detalles ).

```bash
python3 populate_logs.py
```

Se calcula calificaciones de los usuarios de acuerdo a los eventos que fueron registrados por los usuarios (Anterior script)
```bash
python3 -m builder.implicit_ratings_calculator
```

Se calcula y crean las reglas de asociación (Sección Association Rules)
```bash
python3 -m builder.association_rules_calculator
```

Se realiza una implementación de Clustering (Versión optimizada de K-Means)
```bash
python3 -m builder.user_cluster_calculator
```

Se calcula el filtrado colaborativo
```bash
python3 -m builder.item_similarity_calculator
```

Se descarga de la descripción de las películas, si se presenta problemas 
se debe cambiar la línea time.sleep(1) en el archivo. Es indispensable ejecutar
este script para poder calcular la recomendación basada en contenido hecha por el algoritmo LDA.
populate_sample_of_descriptions.py
```bash
python3 populate_sample_of_descriptions.py
```

Se genera un modelo LDA con la librería Gensim  (Sección Content-based recs del usuario) - (Sección Similar-content en el front del localhost )
```bash
python3 -m builder.lda_model_calculator
```
Después de su ejecución se puede verificar viendo las
recomendaciones  de un usuario http://localhost:8000/rec/cb/user/50307185658/

Se genera el modelo de Matrix
```bash
python3 -m builder.matrix_factorization_calculator
```

Se genera el modelo de para el algoritmo hibrido FWLS (Basado en contenido + colaborativo) 
```bash
python3 -m builder.fwls_calculator
```

Se genera el modelo de para el algoritmo BPRS
```bash
python3 -m builder.bpr_calculator
```


## Ejecución de pruebas
```bash
python3 -m evaluator.coverage –cf
```


### Detener el servidor web

Cuando se quiere detener el servidor web, se puede realizar los siguientes pasos:

* Cerrar el servidor presionando Ctrl -c
* Salir del ambiente virtual

Non-Anaconda users
```bash
deactivate
```

### Reiniciar el servidor web

Para reiniciar el servidor web:

*   *Non-Anaconda users*:
    ```bash
    cd moviegeek
    source sircuv/bin/activate
    ```
    
Iniciar el servidor web otra vez ejecutando el siguiente comando: 
```bash
python3 manage.py runserver 127.0.0.1:8000
```
