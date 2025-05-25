# Django REST Framework (DRF) - Basic Guide

Django REST Framework is a powerful toolkit for building Web APIs in Django. It provides a flexible and feature-rich framework for creating RESTful APIs with minimal code.

## Table of Contents

- [Installation](#installation)
- [Quick Start](#quick-start)
- [Key Concepts](#key-concepts)
- [Basic Example](#basic-example)
- [Serializers](#serializers)
- [ViewSets and Views](#viewsets-and-views)
- [URL Routing](#url-routing)
- [Authentication](#authentication)
- [Permissions](#permissions)
- [Common Patterns](#common-patterns)
- [Testing](#testing)
- [Resources](#resources)

## Installation

```bash
# Install Django and DRF
pip install django
pip install djangorestframework

# Optional: Install additional packages
pip install markdown       # Markdown support for browsable API
pip install django-filter  # Filtering support
```

## Quick Start

### 1. Add DRF to Django Settings

```python
# settings.py
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'rest_framework',  # Add this
    'your_app',
]

REST_FRAMEWORK = {
    'DEFAULT_PERMISSION_CLASSES': [
        'rest_framework.permissions.DjangoModelPermissionsOrAnonReadOnly'
    ],
    'DEFAULT_PAGINATION_CLASS': 'rest_framework.pagination.PageNumberPagination',
    'PAGE_SIZE': 20
}
```

### 2. Create a Simple Model

```python
# models.py
from django.db import models

class Book(models.Model):
    title = models.CharField(max_length=200)
    author = models.CharField(max_length=100)
    isbn = models.CharField(max_length=13, unique=True)
    publication_date = models.DateField()
    pages = models.IntegerField()
    
    def __str__(self):
        return self.title
```

## Key Concepts

### Serializers
Convert between Django model instances and Python data types (JSON, XML, etc.)

### Views/ViewSets
Handle HTTP requests and return HTTP responses

### Routers
Automatically generate URL patterns for ViewSets

### Permissions
Control access to API endpoints

### Authentication
Verify user identity

## Basic Example

### Create a Serializer

```python
# serializers.py
from rest_framework import serializers
from .models import Book

class BookSerializer(serializers.ModelSerializer):
    class Meta:
        model = Book
        fields = ['id', 'title', 'author', 'isbn', 'publication_date', 'pages']
        
    def validate_isbn(self, value):
        """Custom validation for ISBN"""
        if len(value) != 13:
            raise serializers.ValidationError("ISBN must be 13 characters long")
        return value
```

### Create Views

```python
# views.py
from rest_framework import viewsets
from rest_framework.decorators import action
from rest_framework.response import Response
from .models import Book
from .serializers import BookSerializer

class BookViewSet(viewsets.ModelViewSet):
    queryset = Book.objects.all()
    serializer_class = BookSerializer
    
    @action(detail=False, methods=['get'])
    def recent_books(self, request):
        """Custom endpoint for recent books"""
        recent = Book.objects.filter(
            publication_date__year=2024
        )
        serializer = self.get_serializer(recent, many=True)
        return Response(serializer.data)
```

### URL Configuration

```python
# urls.py
from django.urls import path, include
from rest_framework.routers import DefaultRouter
from . import views

router = DefaultRouter()
router.register(r'books', views.BookViewSet)

urlpatterns = [
    path('api/', include(router.urls)),
    path('api-auth/', include('rest_framework.urls')),
]
```

## Serializers

### Types of Serializers

```python
# Basic Serializer
class BookSerializer(serializers.Serializer):
    title = serializers.CharField(max_length=200)
    author = serializers.CharField(max_length=100)
    
    def create(self, validated_data):
        return Book.objects.create(**validated_data)
    
    def update(self, instance, validated_data):
        instance.title = validated_data.get('title', instance.title)
        instance.author = validated_data.get('author', instance.author)
        instance.save()
        return instance

# ModelSerializer (Recommended)
class BookSerializer(serializers.ModelSerializer):
    class Meta:
        model = Book
        fields = '__all__'  # or specify fields: ['id', 'title', 'author']
        read_only_fields = ['id']
```

### Nested Serializers

```python
class AuthorSerializer(serializers.ModelSerializer):
    class Meta:
        model = Author
        fields = ['id', 'name', 'email']

class BookSerializer(serializers.ModelSerializer):
    author = AuthorSerializer(read_only=True)
    
    class Meta:
        model = Book
        fields = ['id', 'title', 'author', 'isbn']
```

## ViewSets and Views

### Function-Based Views

```python
from rest_framework.decorators import api_view
from rest_framework.response import Response

@api_view(['GET', 'POST'])
def book_list(request):
    if request.method == 'GET':
        books = Book.objects.all()
        serializer = BookSerializer(books, many=True)
        return Response(serializer.data)
    
    elif request.method == 'POST':
        serializer = BookSerializer(data=request.data)
        if serializer.is_valid():
            serializer.save()
            return Response(serializer.data, status=201)
        return Response(serializer.errors, status=400)
```

### Class-Based Views

```python
from rest_framework.views import APIView
from rest_framework import status

class BookList(APIView):
    def get(self, request):
        books = Book.objects.all()
        serializer = BookSerializer(books, many=True)
        return Response(serializer.data)
    
    def post(self, request):
        serializer = BookSerializer(data=request.data)
        if serializer.is_valid():
            serializer.save()
            return Response(serializer.data, status=status.HTTP_201_CREATED)
        return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)
```

## URL Routing

### Manual URL Patterns

```python
from django.urls import path
from . import views

urlpatterns = [
    path('books/', views.BookList.as_view()),
    path('books/<int:pk>/', views.BookDetail.as_view()),
]
```

### Router (Automatic)

```python
from rest_framework.routers import DefaultRouter

router = DefaultRouter()
router.register(r'books', BookViewSet)
router.register(r'authors', AuthorViewSet)

urlpatterns = router.urls
```

## Authentication

### Basic Authentication Setup

```python
# settings.py
REST_FRAMEWORK = {
    'DEFAULT_AUTHENTICATION_CLASSES': [
        'rest_framework.authentication.SessionAuthentication',
        'rest_framework.authentication.TokenAuthentication',
    ],
}

INSTALLED_APPS = [
    # ...
    'rest_framework.authtoken',
]
```

### Token Authentication

```python
# Create tokens for users
from django.contrib.auth.models import User
from rest_framework.authtoken.models import Token

user = User.objects.get(username='john')
token = Token.objects.create(user=user)
print(token.key)

# Use in API calls
# Header: Authorization: Token 9944b09199c62bcf9418ad846dd0e4bbdfc6ee4b
```

## Permissions

### Built-in Permission Classes

```python
from rest_framework.permissions import IsAuthenticated, IsAuthenticatedOrReadOnly

class BookViewSet(viewsets.ModelViewSet):
    queryset = Book.objects.all()
    serializer_class = BookSerializer
    permission_classes = [IsAuthenticatedOrReadOnly]
```

### Custom Permissions

```python
from rest_framework import permissions

class IsOwnerOrReadOnly(permissions.BasePermission):
    def has_object_permission(self, request, view, obj):
        # Read permissions for any request
        if request.method in permissions.SAFE_METHODS:
            return True
        
        # Write permissions only for owner
        return obj.owner == request.user
```

## Common Patterns

### Filtering and Searching

```python
from django_filters.rest_framework import DjangoFilterBackend
from rest_framework import filters

class BookViewSet(viewsets.ModelViewSet):
    queryset = Book.objects.all()
    serializer_class = BookSerializer
    filter_backends = [DjangoFilterBackend, filters.SearchFilter]
    filterset_fields = ['author', 'publication_date']
    search_fields = ['title', 'author']
```

### Pagination

```python
# settings.py
REST_FRAMEWORK = {
    'DEFAULT_PAGINATION_CLASS': 'rest_framework.pagination.PageNumberPagination',
    'PAGE_SIZE': 20
}

# Custom pagination
from rest_framework.pagination import PageNumberPagination

class BookPagination(PageNumberPagination):
    page_size = 10
    page_size_query_param = 'page_size'
    max_page_size = 100
```

## Testing

### Basic API Testing

```python
from rest_framework.test import APITestCase
from rest_framework import status
from django.contrib.auth.models import User
from .models import Book

class BookAPITestCase(APITestCase):
    def setUp(self):
        self.user = User.objects.create_user(
            username='testuser',
            password='testpass'
        )
        self.book = Book.objects.create(
            title='Test Book',
            author='Test Author',
            isbn='1234567890123',
            publication_date='2024-01-01',
            pages=200
        )
    
    def test_get_book_list(self):
        response = self.client.get('/api/books/')
        self.assertEqual(response.status_code, status.HTTP_200_OK)
        self.assertEqual(len(response.data['results']), 1)
    
    def test_create_book(self):
        self.client.force_authenticate(user=self.user)
        data = {
            'title': 'New Book',
            'author': 'New Author',
            'isbn': '9876543210987',
            'publication_date': '2024-02-01',
            'pages': 300
        }
        response = self.client.post('/api/books/', data)
        self.assertEqual(response.status_code, status.HTTP_201_CREATED)
```

## API Endpoints

With the basic setup above, you'll have these endpoints:

- `GET /api/books/` - List all books
- `POST /api/books/` - Create a new book
- `GET /api/books/{id}/` - Get a specific book
- `PUT /api/books/{id}/` - Update a book
- `PATCH /api/books/{id}/` - Partially update a book
- `DELETE /api/books/{id}/` - Delete a book
- `GET /api/books/recent_books/` - Custom endpoint for recent books

## Best Practices

1. **Use ModelSerializer** when working with Django models
2. **Implement proper validation** in serializers
3. **Use ViewSets** for CRUD operations
4. **Apply appropriate permissions** to protect your API
5. **Implement pagination** for large datasets
6. **Use filtering and searching** for better user experience
7. **Write comprehensive tests** for your API endpoints
8. **Version your API** for backward compatibility
9. **Use proper HTTP status codes**
10. **Document your API** using tools like Swagger/OpenAPI

## Resources

- [Django REST Framework Documentation](https://www.django-rest-framework.org/)
- [Django Documentation](https://docs.djangoproject.com/)
- [REST API Design Best Practices](https://restfulapi.net/)
- [HTTP Status Codes](https://httpstatuses.com/)

## Next Steps

1. Set up authentication and permissions
2. Add filtering, searching, and pagination
3. Implement custom serializers and validators
4. Create comprehensive tests
5. Add API documentation
6. Deploy your API

---

This guide covers the fundamental concepts of Django REST Framework. For more advanced topics like custom authentication, complex serializations, and performance optimization, refer to the official DRF documentation.
