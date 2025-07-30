## 🗺️ Django REST Framework Learning Guide

### 🔰 Prerequisites

Before diving into DRF, make sure you are comfortable with:

* Python basics (functions, classes, modules)
* Django fundamentals (models, views, URLs, templates, admin)

---

## 📘 Phase 1: DRF Fundamentals (Days 1–3)

### ✅ Step 1: Setup and Installation

* Install Django and DRF:

  ```bash
  pip install django djangorestframework
  ```
* Create a Django project and app
* Add `'rest_framework'` to `INSTALLED_APPS`

### ✅ Step 2: Hello API

* Create a simple model (e.g., `Book`)
* Write a basic API using:

  * `ModelSerializer`
  * `APIView`
  * URL routing

---

## 📗 Phase 2: Core Components (Days 4–7)

### ✅ Step 3: Serializers

* `Serializer` vs `ModelSerializer`
* Custom field validation
* `create()` and `update()` overrides
* Nested serializers

### ✅ Step 4: Views

* `APIView`, `GenericAPIView`
* Mixins: `ListModelMixin`, `CreateModelMixin`, etc.
* `ViewSet`, `ModelViewSet`
* Routers: `SimpleRouter`, `DefaultRouter`

---

## 📙 Phase 3: Intermediate Features (Days 8–12)

### ✅ Step 5: Authentication

* Basic and Token Authentication
* Setup token auth with:

  ```bash
  pip install djangorestframework authtoken
  ```
* JWT Authentication using `djangorestframework-simplejwt`

### ✅ Step 6: Permissions

* `IsAuthenticated`, `IsAdminUser`, `AllowAny`
* Custom permission classes
* Object-level permissions

### ✅ Step 7: Pagination

* `PageNumberPagination`, `LimitOffsetPagination`
* Customize response format

### ✅ Step 8: Filtering, Searching, Ordering

* Install and use `django-filter`
* Use `SearchFilter` and `OrderingFilter`

---

## 📒 Phase 4: Advanced Concepts (Days 13–17)

### ✅ Step 9: Throttling

* `AnonRateThrottle`, `UserRateThrottle`
* Custom throttle classes

### ✅ Step 10: Exception Handling

* Customize `exception_handler`
* Use `ValidationError` and global exception responses

### ✅ Step 11: API Versioning

* URL Path Versioning
* Header Versioning

### ✅ Step 12: Testing APIs

* `APIClient` from `rest_framework.test`
* Write unit tests for CRUD APIs

---

## 📕 Phase 5: Production-Ready APIs (Days 18–21)

### ✅ Step 13: API Documentation

* Use `drf-yasg` or `drf-spectacular` to auto-generate Swagger UI

### ✅ Step 14: CORS & Security

* Install `django-cors-headers`
* Setup secure access, rate limiting

### ✅ Step 15: Deployment Tips

* Host on Heroku or DigitalOcean
* Use NGINX + Gunicorn or Docker
* Use `.env` for secrets

---

## 🧪 Practice Projects (Optional but recommended)

1. **Task Manager API** – CRUD tasks with user login
2. **Blog API** – Posts, comments, author-based permissions
3. **E-commerce API** – Products, categories, cart, orders
4. **Todo App with JWT** – Frontend integration (React or Flutter)

---

If you’d like, I can also:

* Recommend a project at your current level
* Create a DRF roadmap graphic
* Share learning resources (courses, docs, GitHub examples)

Would you like any of those?
