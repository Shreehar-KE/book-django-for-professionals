# Django for Professionals 4.0 - Willian S. Vincent

## Chapter 2: Docker Hello, World!
- `docker --version`
- `docker run hello-world`
- `docker info`
- Image: Read-Only Template, Container: Instance of an Image - similar to class & object
- *`dockerfile`* - steps to create an image
- `FROM, ENV, WORKDIR, COPY, RUN `
- *`.dockerignore`* - similar to *`gitignore`*
- `docker build .` - to build the image
- *`docker-compose.yml`* - list of instructions used to run the image as a container
    ```
    # version: "2.27.1-desktop.1" - version in docker-compose.yml is obsolete -->
    services:
        web:
            build: .
            ports:
                - "8000:8000"
            command: python manage.py runserver 0.0.0.0:8000
            volumes:
                - .:/code
    ```
- `docker compose up` & `docker compose down` to start & stop the container

## Chapter 3: PostgreSQL
- `-d` with `docker compose up` for detach mode
- `exec` with `docker compose up` to execute commands
  - `docker compose exec web python manage.py createsuperuser`
- adding `psycopg2-binary==2.9.3` in *`requirements.txt`*
- *`docker-compose.yml`* for PostgreSQL
    ```
    # version: "2.27.1-desktop.1" - version in docker-compose.yml is obsolete -->
    services:
        web:
            build: .
            command: python /code/manage.py runserver 0.0.0.0:8000
            volumes:
                - .:/code
            ports:
                - 8000:8000
            depends_on:
                - db
        db:
            image: postgres:16
            volumes:
                - postgres_data:/var/lib/postgresql/data/
            environment:
                - "POSTGRES_HOST_AUTH_METHOD=trust"
    volumes:
        postgres_data:
    ```
- `DATABASES` in *`settings.py`*
    ```py
    DATABASES = {
            "default": {
            "ENGINE": "django.db.backends.postgresql",
            "NAME": "postgres",
            "USER": "postgres",
            "PASSWORD": "postgres",
            "HOST": "db", # set in docker-compose.yml
            "PORT": 5432, # default postgres port
        }
    }
    ```
- `docker-compose up -d --build` to force build new image instead of cache


## Chapter 4: Bookstore Project
- `AbstractUser` from `auth.models` to create a Customer User Model
- `AUTH_USER_MODEL` in `settings.py`
- Customizing `UserCreationForm` & `UserChangeForm` from `auth.forms` in `accounts/forms.py`
  - `get_user_model()` from `django.contrib.auth`
- modifying `admin.py` to create `CustomUserAdmin` based on `UserAdmin` from `auth.admin`
  - `add_form`, `form`, `model`, `list_display`, `fieldsets`, `add_fieldsets`
- registering both `CustomUser` & `CustomUserAdmin` in `admin.py` 
- `TestCase` from `django.test`
  - `test`prefix for every method
  - `python manage.py test`

## Chapter 5: Pages App
- Using single project-level templates directory vs App-level templates directory
  - `TEMPLATES` list - `"DIRS": [BASE_DIR / "templates"]`
- project-level *`urls.py`* vs app-level *`urls.py`*
  - `include()` from `django.urls`
  - `as_view()` for CBVs
- Generic Class Based View : `django.views.generic`
  - *`TemplateView`*
- `SimpleTestCase` from `django.test`
  - for webpages without db
  - `assertTemplateUsed()`
- `reverse()` from `django.urls`
- `def setUp()` - to load the response into a response variable
- `resolve()` from `django.urls`
  - `self.assertEqual(view.func.__name__, HomePageView.as_view().__name__)`

## Chapter 6: User Registration
- `django.contrib.auth.urls`
- `{% %}` & `{{ }}`
- *`templates/registration/`* for *`login.html`*
- `LOGIN_REDIRECT_URL` & `LOGOUT_REDIRECT_URL` in *`settings.py`*
- URL paths are loaded top-to-bottom
- `SignupPageView` in `accounts` app 
  - *`templates/registration/signup.html`*
  - app-level *`urls.py`* 
  - `django.views.generic.CreateView`, `CustomUserCreationForm`, `success_url`

## Chapter 7: Static Assets
- `STATIC_URL`, `STATICFILES_DIR`
  - {% load static %}
- `STATIC_ROOT`, `STATICFILES_STORAGE`
  - `python manage.py collecstatic`
- **django-crispy-forms** & **crispy-bootstrap5**
  - `{% load crispy_form_tags %}`
  - `CRISPY_ALLOWED_TEMPLATE_PACKS`, `CRISPY_TEMPLATE_PACK`

## Chapter 8: Advanced User Registration
- **django-allauth**
  - **django.contrib.sites** framework, **allauth** & **allauth.account** apps
  - `SITE_ID`
  - `AUTHENTICATION_BACKENDS`
    ```py
    AUTHENTICATION_BACKENDS = (
        "django.contrib.auth.backends.ModelBackend",
        "allauth.account.auth_backends.AuthenticationBackend",
        )
    ```
  - `allauth.account.middleware.AccountMiddleware` middleware
  - `EMAIL_BACKEND = "django.core.mail.backends.console.EmailBackend"`
  - `ACCOUNT_LOGOUT_REDIRECT`
  - allauth urls
    - `allauth.urls` instead of `django.contrib.auth.urls` & `accounts.urls`
    - *`templates/registration/`* -> *`templates/account/`*
    - `account_logout`, `account_login`, `account_signup` urls
  - `ACCOUNT_SESSION_REMEMBER`
  - *`templates/account/logout.html`*
  - `ACCOUNT_SIGNUP_PASSWORD_ENTER_TWICE`
  - Email Only Login
    - `ACCOUNT_USERNAME_REQUIRED`, `ACCOUNT_AUTHENTICATION_METHOD`, `ACCOUNT_EMAIL_REQUIRED`, `ACCOUNT_UNIQUE_EMAIL`