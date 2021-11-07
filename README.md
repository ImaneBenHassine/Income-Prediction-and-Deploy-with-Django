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
- Download Python : This will install Python and pip the Python package manager.
- Add Python to the Path Variable
- Back in the command prompt :
- pip install virtualenv or py -m venv venv ( using virtual environment to not mess with packages from other projects)
- Before starting Django project let’s get the empty repository from the Github : 
- git clone https://github.com/pplonski/my_ml_service.git (with .gitignore adding Python template it will prevent git from tracking unimportant or unsafe files like .env files)
- cd my_ml_service
- ls -l ( if it not working probably needs to add C:\Program Files\Git\usr\bin tp the path variable)
- virtualenv env
- env\Scripts\activate.bat ( need to activate the environment every time starting work on your project in the new terminal)
Start Django project : 
- py -m pip install Django (The Django is installed in version Django-3.2.9 and  add in the requirements.txt in backend directory with Django version)
cd backend
django-admin startproject server (if problems running django-admin try python -m django)
cd server (The Django project name is set to server)
pip3 install djangorestframework ( for django rest framework)
python manage.py runserver

may change in backend/server/server/apps/accounts/apps.py
class AccountsConfig(AppConfig):
    name = 'apps.accounts'
class NotesConfig(AppConfig):
    name = 'apps.notes'
 
 pip install numpy --upgrade
 pip install joblib --upgrade
 pip install pandas --upgrade
 
