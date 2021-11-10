# Income Prediction and Deploy with Django

## Introduction :
In this project I will build ML system available with REST API using Python 3.6 and Django 2.2.4. 

Data Source : https://archive.ics.uci.edu/ml/datasets/Adult

Abstarct : Predict whether income exceeds $50K/yr based on census data. Also known as "Census Income" dataset.



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
![django](https://user-images.githubusercontent.com/26963240/140719522-f58e707e-02a0-4344-a24d-6dae593eb0d2.png)


Add source files to the repository by commiting new files:
git add backend/
git commit -am "setup django project"
git push

and those new files are adding :
- The outer servet root directory is a container for the project.
- backend/server/manage.py : a command-line utility that lets you interact with this Django project in various                             ways
- the inner server/ directory is the actual python package for the project
- backend/server/server/_init_.py : an empty file that tells Python that this directory should be considered                                       a Python package
- backend/server/server/settings.py : settings/configuration for this Django project
- backend/server/server/urls.py : the URL declarations for this Django project; a “table of contents” of the                                    Django-powered site
- backend/server/server/wsgi.py : an entry-point for WSGI-compatible web servers to serve the project. 

## Build Machine Learning algorithms

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

The numpy and pandas packages are used for data manipulation. 
The joblib is used for ML objects saving. 
The sklearn package offers a wide range of ML algorithms then reload Jupyter after installation.

### Add ML code and artifacts to the repository
add our notebook and files to the repository.Each file with preprocessing objects and algorithms is smaller than 100 MB, which is the GitHub file limit

        git add research/*
        
        git commit -am "add ML code and algorithms"
        
        git push

## Django models
After having a default Django project initialized and having MLalgorithms trained and ready for inference, it is time to build Django models to store information about ML algo and requests in the database , then write REST API for MLalgo with Django REST Framework.

### Create Django models
- need to create new app in backend/server directory

          python manage.py startapp endpoints

          mkdir apps  ( add apps directory to keep project clean)

          mv endpoints/ apps/  (moved it to the apps directory)

- In apps/endpoints/models.py file , define database models (Django provides object-relational mapping layer (ORM)) by defining:
1. __Endpoint__ : to keep information about new apps endpoints
2. __MLAlgorithm__ : to keep information about MLalgo used in the service
3. __MLAlgorithmStatuts__ : to keep information about ML algo statues since the status can change in time                               it can be : testing, staging, production, ab_testing.
4. __MLRequest__ : to keep information about all requests to MLalgorithms and needed to monitor MLalgo and run A/B tests

- Add app 'apps.endpoints' to INSTALLED_APPS in backend/server/server/settings.py 
- Then run migrations to apply models to the database in backend/server directory :

        python manage.py makemigrations
        
        python manage.py migrate

This commands will create tables in the database , by default Django is using SQLiteas a database but we can set a Postgres or MySQL as a databse by a configuration in backend/server/server/settings.py

### Cretae REST API for models
After defining database models we still not seeing anything new when running the web server because we need to specify REST API to the objects by using Django REST FRAMEWORK DRF :

          pip3 install djangorestframework
          
          pip3 install markdown   (Markdown support for the browsable API)
          
          pip3 install django-filter  (Filtering support)
          
and add 'rest_framework' to INSTALLED_APPS in backend/server/server/settings.py

Inorder to see changes in the browser we need to define : 
- __serializers__ : they will define how database objects are mapped in requests,
- __views__ : how models are accessed in REST API,
- __urls__  : definition of REST API URL addresses for models.

### DRF Serializers
Add serializers.py file to server/apps/endpoints directory

It will help packing and unpacking database objects into JSON objects. In Endpoints and MLAlgorithm serializers, we defined all read-only fields because, we will create and modify our objects only on the server-side.

For MLAlgorithmStatus, fields status, created_by, created_at and parent_mlalgorithm are in read and write mode, we will use the to set algorithm status by REST API. 

For MLRequest serializer there is a feedback field that is left in read and write mode - it will be needed to provide feedback about predictions to the server.

The MLAlgorithmSerializer has one filed current_status that represents the latest status from MLAlgorithmStatus.

### Views
Add views code in backend/server/endpoints/views.py file 

Cretaing a View for each model allow to retrieve single object or list of objects but not allow to create or modify ndpoints, MLAlgorithms by REST API and the code of new ML related objects will be on server side.

Allow to create MLAlgorithmStatus object by REST API but not allow to edit statuses for MLalgo. Alow to edit MLRequest objects only feedback field

### URLs
Adding URLs to backend/server/apps/endpoints/urls.py then add it endpoints urls to main urls.py file of the server (file backend/server/server/urls.py), this is the last steps tp access out models 

Then REST API routers to database modles are created and accessed by URL pattern :
     http://<server-ip>/api/v1/<object-name>

the __v1__ in the API adress needed for API versioning.
        
### Run the server
in backend/server run :
        
          python manage.py runserver

By opening http://127.0.0.1:8000/api/v1/ in the web browser a DRF view will appear 
        
![DRF](https://user-images.githubusercontent.com/26963240/140718891-d5555cba-e71c-4e3c-882d-f4ff4fd0aed7.png)
 By clicking on any URL and checking the objects we can see empty list for all objects because we didn't add anything there yet so let's add ML algorithms and endpoints .

### Add code to repository
 run in backend/server directory
        
        git add apps/endpoints
        
        git commit -am "endpoints models"
        
        git push

and we will have Everything up-to-date but incase it failed to push some refs to repository try to pull before by run : git pull. And need to identify Author identity before push by typing user.name or user.email :git config --global user.name "name"

## Add ML algorithms to the server code
        
After training two ML algo and creating Django with databse models and REST API endpoints representing ML endpoints, models and requests. It is time to 
- create ML code in the server
- write ML algo registry
- add ML algo to the server

### ML code in the server
In this section we will write code on the server-side that will use previously trained algo.
- Create new directory ML in backend/server/apps to keep all ML related code and income_classifier directory to keep our income classifiers.
          mkdir ml
          mkdir ml/income_classifier
- Create new fiels random_forest.py and empty file __init__.py and implement ML algo in random_forest.py 

The __RandomForestClassifier__ algorithm has five methods:
 1. __init__ :the constructor which loads preprocessing objects and Random Forest object (created with Jupyter notebook)
2. __preprocessing__ :the method which takes as input JSON data, converts it to Pandas DataFrame and apply pre-processing
3. __predict__: the method that calls ML for computing predictions on prepared data
4. __postprocessing__ : the method that applies post-processing on prediction values
5. __compute_prediction__ : the method that combines: preprocessing, predict and postprocessing and returns JSON object with the response.

- Add ml app to INSTALLED_APPS in backend/server/server/settings.py to enable code in the Django

### ML code tests
To check Random Forest algorithm is working as expected needs to write test case using one row from data and check if the prediction is correct.

Adding two files into ml directory empty _init_.py file and tests.py and the test is for :
  - constructing an input JSON data object
  - initializing ML algorithm
  - computing ML prediction and checking the prediction outcome.
  
Using Django tests, run in backend/server directory
          
           python manage.py test apps.ml.tests
  
### Algprithms registry
ML code ready and tested and need to connect it with the server code by creating the ML registry object and keeping information about available algorithms and endpoints.
  Add __registry.py__ file in the backend/server/apps/ml/ directory

The registry keeps simple dict object with a mapping of algorithm id to algorithm object, and to check if the code is working as expected, we can add test case in the backend/server/apps/ml/tests.py file
Now needs to specify one place in the server code which will add ML algo to the registry when the server is starting in backend/server/server/wsgi.py file.
        
Run server to check enpoints and algorithms :
![enpoint](https://user-images.githubusercontent.com/26963240/140739094-1aff14f0-9c37-4e16-9013-2c70ab986af2.png)
 
![ml algo](https://user-images.githubusercontent.com/26963240/140739117-6cb3a9f6-2474-4288-ae6e-acb5e0183157.png)
  
### Add code to repository
commit a new code: 
        git add apps/ml/
        
        git commit -am "add ml code"
        
        git push
Since we have ML algo in the database and can access information with REST API, now need to do __predictions__
 
## Predictions 
 After creating Ml algo in Jupyter notebook , creating  Django app with database models and REST API and added the ML code to the server code plus create ML registry, we need to add a view to handle request in the server and forwardingthem to ML code , then add API URL to the view and write tests for predictions.
        
 ### Predictions View
Create the view for predictions that can accept POST request with JSON data and forward it to the correct ML algo.
 
Add code of class PredictView in backend/server/apps/endpoints/views.py.And predict url in backend/server/apps/endpoints/urls.py
- The __PredictView__ accepts only POST requests. It is available at: https://<server_ip/>api/v1/<endpoint_name>/predict
- The __endpoint_name__ is defining the endpoint that we are trying to reach. In local development the ML algorithm can be accessed at: http://127.0.0.1:8000/api/v1/income_classifier/predict
- The __income_classifier__ is the endpoint name (at http://127.0.0.1:8000/api/v1/endpoints).
Plus we can can specify algorithm status or version in the URL since by default, there is a used production status by including them in the URL, for example:
http://127.0.0.1:8000/api/v1/income_classifier/predict?status=testing&version=1.1.1.

There is routing of the request to correct ML algorithm based on endpoint name, status and version. If the algorithm is selected properly, the JSON request is forwarded to the algorithm object and prediction is computed.

To check if is it working please go to http://127.0.0.1:8000/api/v1/income_classifier/predict and provide example JSON input

![predict](https://user-images.githubusercontent.com/26963240/141123449-f25e9872-d7bd-4a73-afa9-c29b91814cf2.png)

By clicking the POST buttom the result will be the predictable class and with that the MLweb service is working correctly !

![result_predict](https://user-images.githubusercontent.com/26963240/141124132-dd18157a-bd77-4be3-b39c-375f7fdea7d4.png)

### Add test for PredictView
it is a simple test case that will check if the predicted view correctly responds to correct datt in backend/server/endpoints/tests.py and run it in backend/server directory
      python manage.py test apps.endpoints.tests

Probably will need more tests to cover situations where wrong endpoints are selected in the URL or data is in the wrong format.

### Add code to the repository
before moving to A/B testing let's add code to the repository in backend/server directory
        
       git commit -am "add predict view"
  
       git push
  
## A/B testing  
 
Now having our ML algo created , Django web service with ML code database models for endpoints algorithms and requests created and predict view the routing requests to ML algo created.
        
It is time to :
- add the second ML algorithm the Extra Trees based to the web service
- create database mosel and REST API view for A/B tests information
- write python script for sending requests
  
 ## Add Extra Trees based algorithm
add new file extra_trees.py in backend/server/apps/ml/income_classifer directory

the code will be similar to RandomForestClassifier with changed path for reading the model and add new test method to MLtests class in backend/server/apps/ml/tests.py file then run the test
      
        python manage.py test apps.ml.tests
the algo is working and we need to add it to our ML registry in backend/server/server/wsgi.py file      
add :
        from apps.ml.income_classifier.extra_trees import ExtraTreesClassifier # import ExtraTrees ML algorithm   and applay other modification     

Now let see the changes and the two registred ML algorithms by running in backend/server:
        
        python manage.py runserver
http://127.0.0.1:8000/api/v1/mlalgorithms
    
![two ML algo](https://user-images.githubusercontent.com/26963240/141130656-820e9356-8208-4184-a9f6-0f4b8266c814.png)

        
        
        
        
        
        
        
        
        
        
        
        
        



