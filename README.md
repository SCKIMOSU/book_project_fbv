# Django와 Django REST Framework(DRF)

Django와 Django REST framework(DRF) 차이와 역할 비교

---

### **1. 기본 개념 차이**

| 구분 | Django | Django REST Framework (DRF) |
| --- | --- | --- |
| **목적** | 웹 사이트/웹 앱 개발 (HTML 기반) | API 서버 개발 (JSON 기반) |
| **출력** | HTML, 템플릿 중심 | JSON, XML 등 데이터 중심 |
| **주요 사용처** | 웹페이지 렌더링, Admin 등 | 프론트엔드/모바일 앱용 API |
| **기반 기술** | Django 자체 View, Template 등 | Django 기반, 확장 라이브러리 |

---

### **2. 예제 비교**

### Django View (HTML 응답)

```python
from django.shortcuts import render

def book_list(request):
    books = Book.objects.all()
    return render(request, 'book_list.html', {'books': books})

```

### DRF APIView (JSON 응답)

```python
from rest_framework.views import APIView
from rest_framework.response import Response
from .models import Book
from .serializers import BookSerializer

class BookListAPIView(APIView):
    def get(self, request):
        books = Book.objects.all()
        serializer = BookSerializer(books, many=True)
        return Response(serializer.data)

```

---

### **3. 장고 + DRF 함께 쓰는 이유**

| 기능 | Django | DRF |
| --- | --- | --- |
| 관리자 페이지 | O | X |
| 사용자 인증 | O | O (확장됨) |
| HTML 렌더링 | O | 제한적 |
| API 제공 (JSON) | X | O |
| CRUD API 자동화 | X | O (ModelViewSet, Router) |

---

### **4. 결론**

- **Django**: 웹사이트(HTML 중심) 구축에 적합. 관리자 페이지, 폼 처리 강력.
- **DRF**: 프론트엔드 (React, Vue) 또는 모바일 앱(Android/iOS)과의 **API 통신**에 필수.

두 개는 **같은 프로젝트에서 함께 사용**될 수 있고, 보통은:

- Django로 웹 관리자/Admin,
- DRF로 외부 API 서버를 구축.

## ✅ JSON 기본 구조

JSON (JavaScript Object Notation) 데이터를 **텍스트 형식으로 구조화**하여 저장하거나 전송할 때 사용하는 대표적인 포맷

특히 Django REST Framework(DRF)에서는 **API 응답 포맷**으로 자주 사용됨

---

JSON 규칙 

- *키(key)**와 **값(value)**의 쌍
- *문자열은 항상 큰따옴표(")**로
- 배열, 중첩 객체도 포함 가능

### 🔹 예시

```json
{
  "name": "Alice",
  "age": 25,
  "is_student": false,
  "skills": ["Python", "Django", "REST"],
  "profile": {
    "university": "KMU",
    "major": "AI"
  }
}

```

---

## ✅ Python <-> JSON 변환

### 🔸 Python 객체 → JSON 문자열

```python
import json

data = {"name": "Alice", "age": 25}
json_str = json.dumps(data)
print(json_str)  # '{"name": "Alice", "age": 25}'

```

### 🔸 JSON 문자열 → Python 객체

```python
json_str = '{"name": "Alice", "age": 25}'
data = json.loads(json_str)
print(data["name"])  # Alice

```

---

## ✅ DRF에서 JSON 응답 예

### 🔸 Serializer를 통해 JSON 반환

```python
from rest_framework.views import APIView
from rest_framework.response import Response
from .models import Book
from .serializers import BookSerializer

class BookListAPIView(APIView):
    def get(self, request):
        books = Book.objects.all()
        serializer = BookSerializer(books, many=True)
        return Response(serializer.data)  # 자동으로 JSON으로 변환

```

### 🔸 클라이언트로 전송되는 JSON 응답 예

```json
[
  {
    "id": 1,
    "title": "Django Basics",
    "author": "Kim",
    "published_date": "2025-01-01"
  },
  {
    "id": 2,
    "title": "DRF Advanced",
    "author": "Lee",
    "published_date": "2025-04-12"
  }
]

```

---

## ✅ JSON의 특징

| 항목 | 설명 |
| --- | --- |
| 경량 텍스트 기반 | 네트워크 통신에 적합 |
| 언어 독립 | 거의 모든 언어에서 지원 |
| 구조화된 데이터 표현 | 객체, 배열 표현 가능 |
| DRF에서 기본 출력 형식 | `Response()` 객체는 JSON 반환 |

---

## JSON과 ORM 데이터 비교

 JSON 데이터:

```json
{
  "title": "Django",
  "author": "Kim",
  "published_date": "2025-05-18"
}

```

JSON이 Django에서 `Book` 모델에 해당한다고 가정하면, 이를 ORM으로 표현했을 때의 형태(데이터 구조)는 다음과 같다.

---

## ✅ 1. 모델 정의 (기본 가정)

```python
# models.py
from django.db import models

class Book(models.Model):
    title = models.CharField(max_length=200)
    author = models.CharField(max_length=100)
    published_date = models.DateField()

```

---

## ✅ 2. ORM 데이터 형식 예시

### 🔹 ORM 객체 생성 코드

```python
book = Book.objects.create(
    title="Django",
    author="Kim",
    published_date="2025-05-18"
)

```

### 🔹 ORM 객체 상태

```python
print(book)
# <Book: Book object (1)>

print(book.title)          # "Django"
print(book.author)         # "Kim"
print(book.published_date) # datetime.date(2025, 5, 18)

print(type(book))          # <class 'book.models.Book'>

```

---

## ✅ 3. ORM 객체의 실제 내부 구조

ORM 객체는 **클래스 인스턴스**이며 내부적으로 다음과 같은 필드를 가짐

```python
{
  'id': 1,
  'title': 'Django',
  'author': 'Kim',
  'published_date': datetime.date(2025, 5, 18)
}

```

> id 필드는 모델 생성 시 자동 추가되며,
> 
> 
> `published_date`는 `str`이 아닌 `datetime.date` 타입으로 저장됨.
> 

---

## ✅ 4. 비교 요약

| 항목 | JSON 입력 | ORM 객체 내부 |
| --- | --- | --- |
| `"title": "Django"` | 문자열 | 문자열 |
| `"author": "Kim"` | 문자열 | 문자열 |
| `"published_date": "2025-05-18"` | 문자열 (`ISO 형식`) | `datetime.date(2025, 5, 18)` |
| 없음 | 없음 | `id: int` (자동 생성됨) |

---

## ✅ 시각적 정리

```
JSON 입력 데이터 (str)
↓ (serializer.validated_data)
Python dict + datetime 객체 포함
↓ (serializer.save())
Django ORM 객체 (Model 인스턴스)
\
```

---

## 리스트(`list`)와 배열(`array`)

리스트(`list`)와 배열(`array`)은 Python에서 데이터를 순차적으로 저장할 수 있는 구조

 **기능과 성능에서 큰 차이가 있음**.

| 구분 | 리스트 (`list`) | 배열 (`array`, 주로 `numpy.array`) |
| --- | --- | --- |
| **자료형** | 다양한 자료형 가능 (예: int, str 혼합) | 동일한 자료형만 허용 (예: 전부 float) |
| **사용 목적** | 일반적인 용도 | 수치 연산, 과학 계산 등 |
| **속도 및 성능** | 느림 (내부적으로 유연성 있음) | 빠름 (메모리 일관성과 벡터 연산 지원) |
| **기본 제공 여부** | Python 기본 자료형 | `numpy` 설치 필요 |
| **연산 방식** | 반복문으로 연산 필요 | 벡터화 연산 가능 (`a + b`처럼 직접 연산) |
| **메모리 사용** | 상대적으로 더 많음 | 더 적음 (C 기반 연산) |

### 예시 비교:

```python
# 리스트
a = [1, 2, 3]
b = [4, 5, 6]
print(a + b)  # [1, 2, 3, 4, 5, 6] (리스트 연결)

# 배열 (numpy)
import numpy as np
a = np.array([1, 2, 3])
b = np.array([4, 5, 6])
print(a + b)  # [5 7 9] (원소별 덧셈)

```

### 요약:

- **리스트**는 범용적인 자료 구조.
- *배열 (`numpy`)**은 수학, 과학 계산에 최적화된 자료 구조.

---

# 📘 Django + DRF `Book API` 프로젝트 만들기 (FBV 기반)

---

## ✅ 0. 가상환경 생성 및 프로젝트 시작

### ① 가상환경 생성 (선택)

```bash
python -m venv venv
source venv/bin/activate   # macOS/Linux
venv\Scripts\activate      # Windows

```

### ② Django 및 DRF 설치

```bash
pip install django djangorestframework

```

### ③ Django 프로젝트 생성

```bash
django-admin startproject book_project
cd book_project

```

### ④ 앱 생성

```bash
python manage.py startapp book

```

---

## ✅ 1. settings.py 설정

### 🔹 `book_project/settings.py` 수정

```python
INSTALLED_APPS = [
    ...
    'rest_framework',
    'book',  # 앱 등록
]

```

---

## ✅ 2. Book 모델 정의

### 🔹 `book/models.py`

```python
from django.db import models

class Book(models.Model):
    title = models.CharField(max_length=100)
    author = models.CharField(max_length=100)
    published_year = models.IntegerField()

    def __str__(self):
        return self.title

```

### 🔹 마이그레이션 반영

```bash
python manage.py makemigrations
python manage.py migrate

```

---

## ✅ 3. Serializer 생성

### 🔹 `book/serializers.py`

```python
from rest_framework import serializers
from .models import Book

class BookSerializer(serializers.ModelSerializer):
    class Meta:
        model = Book
        fields = '__all__'

```

---

## ✅ 4. 뷰(Views) 작성 (FBV)

### 🔹 `book/views.py`

```python
from rest_framework.decorators import api_view
from rest_framework.response import Response
from rest_framework import status
from .models import Book
from .serializers import BookSerializer

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
            return Response(serializer.data, status=status.HTTP_201_CREATED)
        return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)

@api_view(['GET', 'PUT', 'DELETE'])
def book_detail(request, pk):
    try:
        book = Book.objects.get(pk=pk)
    except Book.DoesNotExist:
        return Response({'error': 'Book not found'}, status=status.HTTP_404_NOT_FOUND)

    if request.method == 'GET':
        serializer = BookSerializer(book)
        return Response(serializer.data)

    elif request.method == 'PUT':
        serializer = BookSerializer(book, data=request.data)
        if serializer.is_valid():
            serializer.save()
            return Response(serializer.data)
        return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)

    elif request.method == 'DELETE':
        book.delete()
        return Response(status=status.HTTP_204_NO_CONTENT)

```

---

## ✅ 5. URL 연결

### 🔹 `book/urls.py` 생성

```python
from django.urls import path
from .views import book_list, book_detail

urlpatterns = [
    path('books/', book_list, name='book-list'),
    path('books/<int:pk>/', book_detail, name='book-detail'),
]

```

### 🔹 `book_project/urls.py` 수정

```python
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('api/', include('book.urls')),  # /api/books/...
]

```

---

## ✅ 6. 서버 실행

```bash
python manage.py runserver

```

### 브라우저 접속 확인:

- 전체 조회: [http://127.0.0.1:8000/api/books/](http://127.0.0.1:8000/api/books/)
- 단일 조회: [http://127.0.0.1:8000/api/books/1/](http://127.0.0.1:8000/api/books/1/)

---

## ✅ 7. Postman 또는 curl 테스트

### 📌 GET 요청

```bash
curl http://127.0.0.1:8000/api/books/

```

### 📌 POST 요청

```bash
curl -X POST http://127.0.0.1:8000/api/books/ \
  -H "Content-Type: application/json" \
  -d '{"title": "Django REST", "author": "홍길동", "published_year": 2023}'

```

---

## ✅ 프로젝트 구조 요약

```
book_project/
├── book_project/
│   ├── settings.py
│   └── urls.py
├── book/
│   ├── models.py
│   ├── serializers.py
│   ├── views.py
│   └── urls.py
└── manage.py

```

---
