# Python Django + Dash

Setting up a local django project with dash apps embedded in html.

## Installation

It's reccomended to use virtual environment to setup a venv first.
https://packaging.python.org/guides/installing-using-pip-and-virtual-environments/
```bash
#install dash and create project
pip install Django
python -m django --version
django-admin startproject dashboard

#install dash for django
pip install django_plotly_dash

```

## Setup django_plotly_dash in the django project
Open the django project, and add django_plotly_dash to INSTALLED_APPS in your Django settings.py file.
```python
INSTALLED_APPS = [
    ...
    'django_plotly_dash.apps.DjangoPlotlyDashConfig',
    ...
    ]
```
Now we have to replace the contents of the 'urls.py' file with the following code:
```python
from django.contrib import admin
from django.urls import path, include

from django.views.generic import TemplateView

import dashboard.dash_example

urlpatterns = [
    path('', TemplateView.as_view(template_name='index.html'), name='home'),
    path('admin/', admin.site.urls),
    path('django_plotly_dash/', include('django_plotly_dash.urls')),
]
```
Now we'll create a folder called 'templates' in which we can store our html files which will be served to the web server. Make sure you are in the root directory of your django project (the folder in which the manage.py file is located). Now create a folder called 'templates'.

Inside the templates folder, create a file called 'index.html'. Inside the index.html place some text.
```
<h1>This is working:D!</h1>
```

To use the html templates, we need to tell django where the templates folder is located. Navigate to the 'settings.py' file, and add the following to the TEMPLATES block.
```
TEMPLATES = [
    {
        ...
        'DIRS': [os.path.join(BASE_DIR, 'templates')],
        ...
]
```
Final step, now run the following command inside the root directory of your django project.
```
python manage.py migrate
```

To check if everything is working, run the django server by running the following command in the root directory of your django project.
```
python manage.py runserver
```
If everything compiled succesfully, open the browser and navigate to.
```
http://127.0.0.1:8000/
```
You should now see the index.html page.

## Implementing a dash app in the html page
Inside the same folder as 'urls.py', 'settings.py' etc, create a new file called 'dash_example.py' and paste the following code in that file.
```python
import dash
import dash_core_components as dcc
import dash_html_components as html

from django_plotly_dash import DjangoDash

external_stylesheets = ['https://codepen.io/chriddyp/pen/bWLwgP.css']

app = DjangoDash('example_1')

app.layout = html.Div(children=[
    html.H1(children='Hello Dash from Peter'),

    html.Div(children='''
        Dash: A web application framework for Python.
    '''),

    dcc.Graph(
        id='example-graph',
        figure={
            'data': [
                {'x': [1, 2, 3], 'y': [4, 1, 2], 'type': 'bar', 'name': 'SF'},
                {'x': [1, 2, 3], 'y': [2, 4, 5], 'type': 'bar', 'name': u'Montréal'},
            ],
            'layout': {
                'title': 'Dash Data Visualization'
            }
        }
    )
])

```
Finally add the following code to the index.html file.
``` 
{% load plotly_dash %}

<h1>Django Dash</h1>
{% plotly_app name="example_1" ratio=1%}
```


The ```{% load plotly_dash %}``` tells django that there will be made use of dash apps inside the html template.

The ```{% plotly_app name="example_1" ratio=1%}``` tells django that the dash app with the name 'example_1' needs to be embedded in the html page. 
##
Now navigate to the browser and you should see the dashplot inside your index.html page!

