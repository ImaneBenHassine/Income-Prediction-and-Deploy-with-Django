# Income Prediction and Deploy with Django

## Introduction :
In this project I will build ML system available with REST API.  for building the ML service I will use Python 3.6 and Django 2.2.4. 


There are many ways of how ML algorithms can be used:
- by runing  the ML algorithm locally to compute predictions on prepared test data and share predictions with others. It is easy and fast in implementation but hard to govern, monitor, scale and collaborate.
-  by using hard-code the ML algorithm in the system's code. For simple ML algorithms, like Decision Trees or Linear Regression similar to the first approach - it is easy to implement with many drawbacks.
- by making the ML algorithm available by REST API, RPC or WebSockets. It requires the implementation of the server which handles requests and forwards them to ML algorithms.
- by using a commercial vendor for deploying ML algorithms - it can be in the cloud or on-premise. Good when you have a standard ML algorithm so the vendor can handle it and pay to the vendor (it can be pricy).

and it should be enough to build ML system which:
can handle many API endpoints,
each API endpoint can have several ML algorithms with different versions,
ML code and artifacts (files with ML parameters) are stored in the code repository (git),
supports fast deployments and continuous integration (tests for both: server and ML code),
supports monitoring and algorithm diagnostic (support A/B tests),
is scalable (deployed with containers),
has a user interface.

## Steps:
- 1.setup environment for development
- 2.install required packages,
- 3.start the Django project.

Installation : 
- Back in the anaconda prompt :
- pip install virtualenv or py -m venv venv ( using virtual environment to not mess with packages from other projects)
- Before starting Django project let’s get the empty repository from the Github : 
- git clone https://github.com/pplonski/my_ml_service.git (with .gitignore adding Python template it will prevent git from tracking unimportant or unsafe files like .env files)
- cd my_ml_service
- ls -l ( if it not working probably needs to add C:\Program Files\Git\usr\bin tp the path variable)
- venv\Scripts\activate.bat ( need to activate the environment every time starting work on your project in the new terminal)
Start Django project : 
- py -m pip install Django==2.2.4 
- mkdir backend (create backend directory)
- cd backend
- django-admin startproject server (The Django project name is set to server)
- cd server ( run initiated server)
- python manage.py runserver
- ALLOWED_HOSTS = ['127.0.0.1'] in setting.py

The environment is successfully set up.

Add source files to the repository by commiting new files:
git add backend/
git commit -am "setup django project"
git push

and those new files are adding :
- The outer servet root directory is a container for the project.
- backend/server/manage.py : a command-line utility that lets you interact with this Django project in various                             ways
- the inner server/ directory is the actual python package for the project
- backend/server/server/__init__.py : an empty file that tells Python that this directory should be considered                                       a Python package
- backend/server/server/settings.py : settings/configuration for this Django project
- backend/server/server/urls.py : the URL declarations for this Django project; a “table of contents” of the                                    Django-powered site
- backend/server/server/wsgi.py : an entry-point for WSGI-compatible web servers to serve the project. 

## Buil Machine Learning algorithms

There will be 3 steps in this level : 
- setup Jupyter notebook
- build two ML algorithms
- save pre-processing details and algorithms.

### Jupyter notebook
pip3 install jupyter notebook
- then set Jupyter to use local virtualenv:
ipython kernel install --user --name=venv

- create a research directory where I will put Jupiter files :
mkdir research
cd research
- then start Jupyter :
jupyter notebook
- start a new notebook and select the correct kernel, venv in our case 

### Train ML algorithms
- install packages : pip3 install numpy pandas sklearn joblib
- 
The numpy and pandas packages are used for data manipulation. 
The joblib is used for ML objects saving. 
The sklearn package offers a wide range of ML algorithms then reload Jupyter after installation.
