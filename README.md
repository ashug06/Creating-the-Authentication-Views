# Creating-the-Authentication-Views
Introduction to Django Rest Framework

Overview
Django REST Framework (DRF) is a powerful and flexible toolkit for building Web APIs in Django. It is widely used in modern web development due to its ease of use, robust features, and comprehensive documentation. In this chapter, we will explore the key concepts and components of DRF, understand its importance, and create a simple API for managing MCQs (Multiple Choice Questions) in our "1V1 MCQ Battle" application.

Importance of Django REST Framework
Ease of Use: DRF simplifies the process of building APIs with Django, providing useful tools and abstractions that reduce boilerplate code.

Flexibility: It offers flexibility to customize and extend functionalities according to specific needs, such as authentication, serialization, and viewsets.

Integration: DRF seamlessly integrates with Django, leveraging its existing models, views, and URL routing.

Serialization: It provides powerful serialization mechanisms to convert complex data types like Django models into native Python datatypes, JSON, and other content types.

Browsable API: DRF includes a browsable API that allows developers to interact with their API through a web interface, facilitating easier debugging and testing.

Authentication and Permissions: It supports various authentication schemes (e.g., Token, JWT) and fine-grained permission controls to secure APIs.

Setting Up Django REST Framework
To get started with DRF, we need to install it and add it to our Django project.

1. Installation
First, install DRF using pip:

pip install djangorestframework
Next, add rest_framework to the INSTALLED_APPS in settings.py:

INSTALLED_APPS = [
    ...
    'rest_framework',
    'auth_app',  # Ensure our auth app is included
]
Conclusion
Django REST Framework provides a robust and flexible toolkit for building Web APIs with Django.













Introduction
In Django, serializers are used to convert complex data types, such as querysets and model instances, into native Python datatypes that can then be easily rendered into JSON, XML, or other content types. They also handle deserialization, where parsed data is converted back into complex types after first validating the incoming data. In this chapter, we'll explore the different types of serializers in Django and create examples for user registration and login functionalities.

Types of Serializers
Django REST framework provides several types of serializers:

ModelSerializer: This serializer automatically generates fields and validators based on a Django model. It is a shortcut for creating serializers that deal with model instances and querysets.
Serializer: This is the base class for defining custom serializers. It provides complete control over how data is converted to and from its JSON representation. Unlike ModelSerializer, it does not make any assumptions about the structure of the input data.
For this chapter, we'll focus on using the Serializer class to give you a better understanding of custom serialization and validation.

Creating Custom Serializers
We'll create two custom serializers:

RegistrationSerializer: For handling user registration.
LoginSerializer: For handling user login.
These serializers will not automatically handle saving or validating model instances but will demonstrate how to define fields and validation manually.

Registration Serializer
The registration serializer will handle user sign-up by capturing first_name, last_name, email, and password fields.

# serializers.py
from rest_framework import serializers

class RegistrationSerializer(serializers.Serializer):
    first_name = serializers.CharField(
        max_length=30,
        required=True,
        error_messages={
            "required": "First Name is required",
            "blank": "First Name is required",
        }
    )
    last_name = serializers.CharField(
        max_length=30,
        required=True,
        error_messages={
            "required": "Last Name is required",
            "blank": "Last Name is required",
        }
    )
    email = serializers.EmailField(
        required=True,
        error_messages={
            "required": "Email is required",
            "blank": "Email is required",
            "invalid": "Enter a valid email address",
        }
    )
    password = serializers.CharField(
        write_only=True,
        required=True,
        error_messages={
            "required": "Password is required",
            "blank": "Password is required",
        }
    )
In this serializer:

We define fields first_name, last_name, email, and password.
We specify that each field is required and provide custom error messages for the required and blank validation errors.
Login Serializer
The login serializer will handle user authentication by capturing email and password.

# serializers.py

class LoginSerializer(serializers.Serializer):
    email = serializers.EmailField(
        required=True,
        error_messages={
            "required": "Email is required",
            "blank": "Email is required",
            "invalid": "Enter a valid email address",
        }
    )
    password = serializers.CharField(
        write_only=True,
        required=True,
        error_messages={
            "required": "Password is required",
            "blank": "Password is required",
        }
    )
In this serializer:

We define fields email and password.
We specify that each field is required and provide custom error messages for the required, blank, and invalid validation errors.
Conclusion
In this chapter, we covered how to create custom serializers in Django for user registration and login functionalities. Custom serializers give you complete control over the validation and transformation of data, making them highly flexible for various use cases. This is a crucial step in building a robust backend for our 1V1 MCQ Battle application.

For more information, refer to the Django REST framework documentation.









Overview
In web development, entities refer to the key components or objects that represent data in your application, such as users, products, or posts. In Django, these entities are typically represented as models. The Object-Relational Mapping (ORM) in Django allows you to interact with your database using Python objects, providing a high-level abstraction for database operations. This chapter will cover the basics of entities and ORM in Django, how to define models, perform CRUD operations, and use Django's powerful querying capabilities.

Importance of Entities and ORM
Abstraction: ORM abstracts the database interactions, allowing developers to work with Python objects instead of writing raw SQL queries.

Productivity: It increases developer productivity by reducing the amount of boilerplate code needed for database operations.

Consistency: ORM ensures that the application logic is consistent with the database schema, reducing errors and improving maintainability.

Portability: Since ORM abstracts the database layer, it is easier to switch between different database systems without changing the application code.

Defining Models in Django
Models in Django are defined as Python classes that subclass django.db.models.Model. Each model represents a table in the database, and each attribute of the model represents a field in that table.

Lets explore the concept of models and ORM by creating a custom User model in the auth_app. We will add fields like first_name, last_name, email, password, created_at, and updated_at. We will also explain various field types and their usage in Django models, followed by an explanation of migrations and relevant commands.

Creating a Custom User Model
To create a custom User model, we need to follow these steps:

Define the model with relevant fields.
Update the settings to use the User model.
Create and apply migrations to reflect the changes in the database.
1. Define the Model with Relevant Fields
First, let's define our User model in the auth_app's models.py:

from django.db import models
from django.contrib.auth.models import AbstractBaseUser, BaseUserManager
from django.utils import timezone

class UserManager(BaseUserManager):
    def create_user(self, email, password=None, **extra_fields):
        if not email:
            raise ValueError('The Email field must be set')
        email = self.normalize_email(email)
        user = self.model(email=email, **extra_fields)
        user.set_password(password)
        user.save(using=self._db)
        return user

class User(AbstractBaseUser):
    first_name = models.CharField(max_length=30)
    last_name = models.CharField(max_length=30)
    email = models.EmailField(unique=True)
    password = models.CharField(max_length=128)
    created_at = models.DateTimeField(auto_now_add=True)
    updated_at = models.DateTimeField(auto_now=True)
    is_active = models.BooleanField(default=True)
    is_staff = models.BooleanField(default=False)
    is_superuser = models.BooleanField(default=False)

    objects = UserManager()

    USERNAME_FIELD = 'email'
    REQUIRED_FIELDS = ['first_name', 'last_name']
Explanation of Field Types
CharField: A string field, for small to large-sized strings.

max_length: The maximum length (in characters) of the field.
EmailField: A CharField that checks that the value is a valid email address.

DateTimeField: A date and time field.

auto_now_add: Automatically set the field to the current date and time when the object is created.
auto_now: Automatically set the field to the current date and time every time the object is saved.
BooleanField: A true/false field.

2. Update the Settings
Next, update the settings.py to use the custom User model:

# settings.py
AUTH_USER_MODEL = 'auth_app.User'
3. Create and Apply Migrations
Migrations are Django’s way of propagating changes you make to your models into the database schema. Let's create and apply migrations.

Create Migrations
python manage.py makemigrations auth_app
This command will generate migration files for any changes detected in the auth_app models.

Apply Migrations
python manage.py migrate
This command will apply the migrations to the database, creating the necessary tables and fields.

Understanding Migrations
Migrations in Django are a way of dealing with changes to your models over time in a consistent and easy way. They are intended to be mostly automatic, but you'll need to know when and how to use them.

Migration Commands
makemigrations: Creates new migration(s) based on the changes detected to your models.
migrate: Applies the migrations to the database.
showmigrations: Displays a list of all migrations and their status.
sqlmigrate: Displays the SQL statements for a migration.
check: Checks the entire Django project for potential problems.
Example Commands
Create Initial Migrations

python manage.py makemigrations
This generates migration files that describe the changes to your models.

Apply Migrations

python manage.py migrate
This applies the migrations to your database, creating the necessary schema.

Show Migrations

python manage.py showmigrations
This lists all migrations and indicates which ones have been applied.

View SQL for Migrations

python manage.py sqlmigrate auth_app 0001
This shows the SQL commands that will be executed for the specified migration.

Conclusion
In this chapter, we have learned how to create a custom User model in Django with various field types. We also explored different field types available in Django models and understood their usage. Additionally, we delved into the concept of migrations, how to create and apply them, and the essential commands for managing migrations. This knowledge forms the foundation for managing your database schema effectively using Django's ORM.


















JWT & Authentication in Django

Introduction
Authentication is a fundamental aspect of web applications. JSON Web Tokens (JWT) provide a compact and self-contained way for securely transmitting information between parties as a JSON object. This chapter will explain JWT, its benefits, and how to implement JWT authentication in a Django project.

What is JWT?
JSON Web Token (JWT) is an open standard (RFC 7519) that defines a compact and self-contained way for securely transmitting information between parties as a JSON object. This information can be verified and trusted because it is digitally signed. JWTs can be signed using a secret (with the HMAC algorithm) or a public/private key pair using RSA or ECDSA.

Structure of JWT
A JWT is composed of three parts:

Header: Contains metadata about the type of token and the signing algorithm used.
Payload: Contains the claims, which are statements about an entity (typically, the user) and additional data.
Signature: Used to verify the integrity of the token and ensure it has not been tampered with.
The JWT is encoded as a Base64URL string consisting of the header, payload, and signature, separated by dots.

Benefits of JWT
Compact: Because of their size, JWTs can be sent via URL, POST parameters, or inside an HTTP header, and it is especially useful in mobile environments.
Self-contained: The payload contains all the necessary information about the user, avoiding multiple database lookups.
Security: JWTs are signed, ensuring that the claims cannot be altered after the token is issued.
Implementing JWT Authentication in Django
Setup
First, ensure you have Django and Django REST framework installed. You will also need the djangorestframework-simplejwt package for JWT support.

pip install djangorestframework-simplejwt
Add these packages to your INSTALLED_APPS in settings.py:

# settings.py

INSTALLED_APPS = [
    ...
    'rest_framework',
    'rest_framework_simplejwt',
    ...
]
Configuring Django REST Framework
Configure Django REST framework to use JWT authentication in settings.py:

# settings.py

REST_FRAMEWORK = {
    'DEFAULT_AUTHENTICATION_CLASSES': (
        'rest_framework_simplejwt.authentication.JWTAuthentication',
    ),
}
Creating the Authentication Views
Create views to handle user registration and login using APIView. In your auth_app app's views.py, define the views:

# views.py
from auth_app.models import User
from rest_framework.views import APIView
from rest_framework.response import Response
from rest_framework import status
from rest_framework_simplejwt.tokens import RefreshToken
from .serializers import RegistrationSerializer, LoginSerializer
from rest_framework.permissions import IsAuthenticated


class RegisterView(APIView):
    def post(self, request):
        serializer = RegistrationSerializer(data=request.data)
        if serializer.is_valid():

            # Check if user already exists
            user = User.objects.filter(email=serializer.validated_data["email"]).first()
            if user is not None:
                return Response(
                    {"error": "User already exists"}, status=status.HTTP_400_BAD_REQUEST
                )

            # Create user
            user = User.objects.create(
                first_name=serializer.validated_data["first_name"],
                last_name=serializer.validated_data["last_name"],
                email=serializer.validated_data["email"],
            )
            user.set_password(serializer.validated_data["password"])
            user.save()
            return Response(
                {
                    "first_name": user.first_name,
                    "last_name": user.last_name,
                    "email": user.email,
                },
                status=status.HTTP_201_CREATED,
            )
        return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)


class LoginView(APIView):
    def post(self, request):
        serializer = LoginSerializer(data=request.data)
        if serializer.is_valid():
            email = serializer.validated_data["email"]
            password = serializer.validated_data["password"]
            user = User.objects.filter(email=email).first()
            if user is None:
                return Response(
                    {"error": "User not found"}, status=status.HTTP_404_NOT_FOUND
                )
            if not user.check_password(password):
                return Response(
                    {"error": "Invalid password"}, status=status.HTTP_400_BAD_REQUEST
                )
            refresh = RefreshToken.for_user(user)
            return Response(
                {
                    "refresh": str(refresh),
                    "access": str(refresh.access_token),
                },
                status=status.HTTP_200_OK,
            )
        return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)


class ProtectedView(APIView):
    permission_classes = [IsAuthenticated]

    def get(self, request):
        return Response(
            data={"message": "You have accessed a protected view!"},
            status=status.HTTP_200_OK,
        )
Routing the Views
Update your urls.py file to include routes for the authentication endpoints:

# urls.py
from django.contrib import admin
from django.urls import path

from auth_app.views import LoginView, ProtectedView, RegisterView

urlpatterns = [
    path("admin/", admin.site.urls),
    path("register", RegisterView.as_view(), name="register"),
    path("login", LoginView.as_view(), name="login"),
    path("protected", ProtectedView.as_view(), name="protected"),
]
Testing the API
Register a New User:

Endpoint: POST /register/
Payload:
{
  "first_name": "John",
  "last_name": "Doe",
  "email": "john.doe@example.com",
  "password": "securepassword123"
}
Login a User:

Endpoint: POST /login/
Payload:
{
  "email": "john.doe@example.com",
  "password": "securepassword123"
}
Response:
{
  "refresh": "your-refresh-token",
  "access": "your-access-token"
}
Access Protected View:

Endpoint: GET /protected/
Headers:
Authorization: Bearer your-access-token
Conclusion
In this chapter, we covered how to create registration and login APIs using APIView from Django REST framework and JWT for authentication. We also ensured that these endpoints are protected and only accessible to authenticated users. By following the steps outlined in this chapter, you can build a secure and efficient authentication system for your Django application.

For more information, refer to the Django REST framework documentation and the djangorestframework-simplejwt documentation.




















Setting up MCQ APIs

Introduction
In this chapter, we'll cover how to create a CRUD API for managing multiple-choice questions (MCQs) in Django using APIView from Django REST framework. This API will only be accessible to authenticated users. Each MCQ will have the following fields:

id: A UUID that uniquely identifies each MCQ.
body: The question text.
explanation: A text field explaining the correct answer.
options: A JSON field containing the possible answer options. Each option will have body and is_correct fields.
Setting Up the Project
Ensure you have Django and Django REST framework installed. You will also need the djangorestframework-simplejwt package for JWT authentication.

Add these packages to your INSTALLED_APPS in settings.py:

# settings.py

INSTALLED_APPS = [
    ...
    'rest_framework',
    'rest_framework_simplejwt',
    ...
]
Configure Django REST framework to use JWT authentication in settings.py:

# settings.py

REST_FRAMEWORK = {
    'DEFAULT_AUTHENTICATION_CLASSES': (
        'rest_framework_simplejwt.authentication.JWTAuthentication',
    )
}
Create the "mcqs" App
Navigate to your project directory:

cd mcqbattle
Create the new app:

python manage.py startapp mcqs
Register the new app in your project settings: Open settings.py and add 'mcqs' to the INSTALLED_APPS list:

INSTALLED_APPS = [
	...
    'mcqs',  # Add this line
   	...
]
Creating the MCQ Model
First, create the MCQ model. In mcqs/models.py, define the model with the specified fields:

# mcqs/models.py
import uuid
from django.db import models

class MCQ(models.Model):
    id = models.UUIDField(primary_key=True, default=uuid.uuid4, editable=False)
    body = models.TextField()
    explanation = models.TextField()
    options = models.JSONField()
Run the migrations to create the MCQ table in your database:

python manage.py makemigrations
python manage.py migrate
Creating the Serializers
Create a serializers.py file in mcqs folder and add the following code to define the serializer for the MCQ model without using ModelSerializer:

# serializers.py
from rest_framework import serializers

class OptionSerializer(serializers.Serializer):
    body = serializers.CharField(max_length=1000)
    is_correct = serializers.BooleanField()

class MCQSerializer(serializers.Serializer):
    id = serializers.UUIDField(read_only=True)
    body = serializers.CharField(max_length=2000)
    explanation = serializers.CharField(max_length=2000)
    options = OptionSerializer(many=True)
Creating the Views
Create views to handle the CRUD operations for the MCQ model using APIView. In your app's views.py, define the views:

# views.py
from rest_framework.views import APIView
from rest_framework.response import Response
from rest_framework import status
from .models import MCQ
from .serializers import MCQSerializer
from rest_framework.permissions import IsAuthenticated

class MCQListCreateView(APIView):
    permission_classes = [IsAuthenticated]

    def get(self, request):
        mcqs = MCQ.objects.all()
        serializer = MCQSerializer(mcqs, many=True)
        return Response(serializer.data, status=status.HTTP_200_OK)

    def post(self, request):
        serializer = MCQSerializer(data=request.data)
        if serializer.is_valid():
            mcq = MCQ.objects.create(
                body=serializer.validated_data['body'],
                explanation=serializer.validated_data['explanation'],
                options=serializer.validated_data['options']
            )
            mcq.save()
            return Response(serializer.data, status=status.HTTP_201_CREATED)
        return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)

class MCQRetrieveUpdateDestroyView(APIView):
    permission_classes = [IsAuthenticated]

    def get_object(self, pk):
        try:
            return MCQ.objects.get(pk=pk)
        except MCQ.DoesNotExist:
            return None

    def get(self, request, pk):
        mcq = self.get_object(pk)
        if mcq is None:
            return Response(status=status.HTTP_404_NOT_FOUND)
        serializer = MCQSerializer(mcq)
        return Response(serializer.data, status=status.HTTP_200_OK)

    def put(self, request, pk):
        mcq = self.get_object(pk)
        if mcq is None:
            return Response(status=status.HTTP_404_NOT_FOUND)
        serializer = MCQSerializer(mcq, data=request.data)
        if serializer.is_valid():
            mcq.body = serializer.validated_data['body']
            mcq.explanation = serializer.validated_data['explanation']
            mcq.options = serializer.validated_data['options']
            mcq.save()
            return Response(serializer.data, status=status.HTTP_200_OK)
        return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)

    def delete(self, request, pk):
        mcq = self.get_object(pk)
        if mcq is None:
            return Response(status=status.HTTP_404_NOT_FOUND)
        mcq.delete()
        return Response(status=status.HTTP_204_NO_CONTENT)
Routing the Views
Update your urls.py file to include routes for the MCQ CRUD API:

# urls.py
from django.contrib import admin
from django.urls import path

from auth_app.views import LoginView, ProtectedView, RegisterView
from mcqs.views import MCQListCreateView, MCQRetrieveUpdateDestroyView

urlpatterns = [
    path("admin/", admin.site.urls),
    path("register", RegisterView.as_view(), name="register"),
    path("login", LoginView.as_view(), name="login"),
    path("protected", ProtectedView.as_view(), name="protected"),
    path("mcqs", MCQListCreateView.as_view(), name="mcq-list-create"),
    path("mcqs/<uuid:pk>", MCQRetrieveUpdateDestroyView.as_view(), name="mcq-detail"),
]
Testing the API
List and Create MCQs:

Endpoint: GET /mcqs/
Endpoint: POST /mcqs/
Payload for POST:
{
  "body": "What is the capital of France?",
  "explanation": "The capital of France is Paris.",
  "options": [
    { "body": "Paris", "is_correct": true },
    { "body": "London", "is_correct": false },
    { "body": "Berlin", "is_correct": false },
    { "body": "Madrid", "is_correct": false }
  ]
}
Retrieve, Update, and Delete MCQs:

Endpoint: GET /mcqs/<uuid:pk>/
Endpoint: PUT /mcqs/<uuid:pk>/
Endpoint: DELETE /mcqs/<uuid:pk>/
Payload for PUT:
{
  "body": "What is the capital of France?",
  "explanation": "The capital of France is Paris.",
  "options": [
    { "body": "Paris", "is_correct": true },
    { "body": "London", "is_correct": false },
    { "body": "Berlin", "is_correct": false },
    { "body": "Madrid", "is_correct": false }
  ]
}
Conclusion
In this chapter, we covered how to create a CRUD API for managing MCQs in Django using APIView from Django REST framework. This API is accessible only to authenticated users, ensuring secure access to the data. We defined the MCQ model, created serializers, views, and routes, and configured JWT authentication to protect the API. By following the steps outlined in this chapter, you can build a robust and secure backend for managing MCQs in your 1V1 MCQ Battle application.

For more information, refer to the Django REST framework documentation and the djangorestframework-simplejwt documentation.
