# Beginner Django Guide: Chapter 1 to Chapter 6

This guide explains the Django concepts used in this repository from `Chapter01` to `Chapter06`.
It is written for beginners. The goal is not only to show what the code does, but also to explain
when you should update each file and why.

This guide follows this repository exactly. That means it includes the normal book topics and also
repo-specific additions, such as the favorite post feature in `Chapter01`.

> Important: This guide does not copy secret values from `.env` files. Whenever environment
> variables are shown, placeholder values are used.

## Table of Contents

- [Chapter 0: How to Use This Guide](#chapter-0-how-to-use-this-guide)
- [Chapter 1: Blog Basics](#chapter-1-blog-basics)
- [Chapter 2: Forms, Pagination, Comments, and Email](#chapter-2-forms-pagination-comments-and-email)
- [Chapter 3: Tags, Search, Sitemap, RSS, and Template Tools](#chapter-3-tags-search-sitemap-rss-and-template-tools)
- [Chapter 4: Authentication and User Profiles](#chapter-4-authentication-and-user-profiles)
- [Chapter 5: Messages, Custom Auth, Social Login, and HTTPS Dev Setup](#chapter-5-messages-custom-auth-social-login-and-https-dev-setup)
- [Chapter 6: Image Bookmarking App](#chapter-6-image-bookmarking-app)
- [Line-by-Line Code Reading Appendix](#line-by-line-code-reading-appendix)
- [Final Reference Sections](#final-reference-sections)

---

# Chapter 0: How to Use This Guide

## What Django Is

Django is a Python web framework. A web framework helps you build websites faster by giving you
ready-made tools for common web work.

Django helps with:

- Reading a request from the browser.
- Choosing the correct Python function or class to handle that request.
- Reading and writing data in a database.
- Rendering HTML templates.
- Handling users, login, logout, passwords, and permissions.
- Serving admin pages.
- Validating forms.
- Sending emails.
- Building APIs and more advanced features later.

## Big Beginner Picture

When someone visits a Django page, this is the normal flow:

1. The browser sends a request to a URL.
2. Django checks the URL patterns in `urls.py`.
3. Django calls the matching view in `views.py`.
4. The view may read or write data using models in `models.py`.
5. The view returns an HTML response using a template.
6. The browser displays the final page.

Simple example:

```text
Browser URL
  -> urls.py
  -> views.py
  -> models.py if data is needed
  -> template.html
  -> HTML response
```

## Django MVT: Model, View, Template

Django follows a pattern called MVT.

MVT means:

```text
M = Model
V = View
T = Template
```

This is one of the most important Django concepts.

## What Is a Model?

A model is a Python class that describes database data.

Example from the blog app:

```python
class Post(models.Model):
    title = models.CharField(max_length=250)
    body = models.TextField()
```

Simple meaning:

- `Post` becomes a database table.
- `title` becomes a database column.
- `body` becomes a database column.
- Each saved `Post` object becomes one row in the table.

In this repo, examples of models are:

| Model | File | Meaning |
|---|---|---|
| `Post` | `blog/models.py` | Blog post |
| `Comment` | `blog/models.py` | Comment on a post |
| `FavouritePost` | `blog/models.py` | User favorite relationship |
| `Profile` | `account/models.py` | Extra user information |
| `Image` | `images/models.py` | Bookmarked image |

Models answer questions like:

- What data do we store?
- What fields does each row have?
- How are objects connected?
- What is the default ordering?
- What database indexes should exist?

## What Is a View?

A view is Python code that receives a request and returns a response.

Example:

```python
def post_list(request):
    posts = Post.published.all()
    return render(request, 'blog/post/list.html', {'posts': posts})
```

Simple meaning:

- The browser asks for a page.
- Django calls the view.
- The view gets data.
- The view chooses a template.
- The view returns HTML.

In Django, a view is not the HTML page. A view is the Python logic that prepares the response.

Views answer questions like:

- What should happen for this URL?
- Do we need to read data?
- Do we need to save data?
- Which template should be used?
- What context data should be sent to the template?
- Should the user be redirected?
- Should the user be logged in?

## What Is a Template?

A template is an HTML file that displays data.

Example:

```django
{% for post in posts %}
  <h2>{{ post.title }}</h2>
{% endfor %}
```

Simple meaning:

- `posts` comes from the view.
- The template loops over posts.
- `{{ post.title }}` prints each post title.

Templates answer questions like:

- What should the user see?
- Where should the title appear?
- Should we show a form?
- Should we show a button?
- Should we loop over a list?
- Should we show different HTML when the user is logged in?

## Where Does URLconf Fit in MVT?

URLconf is not part of the MVT name, but it is essential.

URLconf means URL configuration. It lives in `urls.py`.

Example:

```python
path('', views.post_list, name='post_list')
```

Simple meaning:

- If the URL path is empty inside this app, call `views.post_list`.
- Give this route the name `post_list`.

MVT with URLconf looks like this:

```text
Browser request
  -> URLconf chooses the view
  -> View uses model if needed
  -> View renders template
  -> Browser receives response
```

## MVT Example from Chapter 1

When a user opens:

```text
/blog/
```

The flow is:

1. Project `mysite/urls.py` sends `/blog/` to `blog.urls`.
2. App `blog/urls.py` matches `path('', views.post_list, name='post_list')`.
3. Django calls `post_list(request)`.
4. The view reads published posts from the `Post` model.
5. The view renders `blog/post/list.html`.
6. The browser receives HTML.

Mapped to MVT:

| MVT part | Repo file |
|---|---|
| Model | `blog/models.py` |
| View | `blog/views.py` |
| Template | `blog/templates/blog/post/list.html` |
| URLconf | `blog/urls.py` and `mysite/urls.py` |

## MVT Example from Chapter 6

When a logged-in user likes an image:

1. Browser JavaScript sends a POST request to `/images/like/`.
2. `images/urls.py` routes the request to `image_like`.
3. `image_like` checks the image ID and action.
4. The view updates the `Image.users_like` many-to-many relationship.
5. The view returns JSON.
6. JavaScript updates the button and like count on the page.

Mapped to MVT:

| MVT part | Repo file |
|---|---|
| Model | `images/models.py` |
| View | `images/views.py` |
| Template | `images/templates/images/image/detail.html` |
| URLconf | `images/urls.py` |
| JavaScript helper | `detail.html` inside `{% block domready %}` |

## MVT vs MVC

You may hear about MVC:

```text
M = Model
V = View
C = Controller
```

Django uses MVT instead:

```text
M = Model
V = View
T = Template
```

Simple comparison:

| MVC word | Similar Django part |
|---|---|
| Model | Model |
| View | Template |
| Controller | View plus URLconf |

Beginner warning:

In many frameworks, "view" means the HTML display. In Django, "view" means the Python request
handler. The HTML display is the template.

## Request and Response Basics

A request is what the browser sends to Django.

A response is what Django sends back.

Examples of requests:

```text
GET /blog/
GET /blog/search/?query=django
POST /blog/1/comment/
POST /images/like/
```

Examples of responses:

```text
HTML page
Redirect
404 not found page
JSON data
Empty response
```

In views, the first argument is usually:

```python
request
```

That object contains useful data:

| Request attribute | Meaning |
|---|---|
| `request.method` | HTTP method, such as `GET` or `POST` |
| `request.GET` | Query string data |
| `request.POST` | Submitted form data |
| `request.FILES` | Uploaded files |
| `request.user` | Current logged-in user or anonymous user |
| `request.session` | Session data for this visitor |

## GET vs POST

Use GET when reading data.

Examples:

```text
Open blog list
Open post detail
Search posts
Open image list
```

Use POST when changing data.

Examples:

```text
Create comment
Register user
Edit profile
Like image
Create image bookmark
Logout
```

Rule:

```text
GET = read
POST = create, update, delete, or perform an action
```

## Query Strings and Path Parameters

A path parameter is part of the URL path.

Example:

```text
/blog/2026/4/28/my-post/
```

The URL pattern captures:

```python
<int:year>
<int:month>
<int:day>
<slug:post>
```

A query string comes after `?`.

Example:

```text
/blog/search/?query=django
```

The view reads it with:

```python
request.GET.get('query')
```

Use path parameters for the identity of the page. Use query strings for filters, search terms, and
pagination.

## HTTP Status Codes Beginners Should Know

| Status | Meaning | Example |
|---|---|---|
| `200` | OK | Page loaded |
| `302` | Redirect | After login or after saving a form |
| `400` | Bad request | Invalid request data |
| `403` | Forbidden | CSRF failed or permission denied |
| `404` | Not found | Post/image does not exist |
| `500` | Server error | Python exception or config problem |

## CSRF in Simple Words

CSRF means Cross-Site Request Forgery.

Django protects POST requests from fake submissions created by another site.

Normal HTML forms use:

```django
{% csrf_token %}
```

AJAX/fetch requests use a header:

```javascript
headers: {'X-CSRFToken': csrftoken}
```

If you forget CSRF protection, POST requests can fail with a 403 error.

## Sessions and Cookies

A cookie is a small value stored in the browser.

A session is server-side data connected to a browser visitor.

Django login uses sessions:

1. User submits username and password.
2. Django verifies credentials.
3. Django stores the user ID in the session.
4. Browser keeps a session cookie.
5. On later requests, Django knows `request.user`.

This is why authentication needs:

```python
'django.contrib.sessions'
'django.contrib.auth'
```

and session/auth middleware.

## Middleware in Simple Words

Middleware is code that runs during request/response processing.

It can run:

- Before the view.
- After the view.
- Around the view.

Examples from settings:

```python
'django.middleware.csrf.CsrfViewMiddleware'
'django.contrib.auth.middleware.AuthenticationMiddleware'
'django.contrib.messages.middleware.MessageMiddleware'
```

Simple meaning:

| Middleware | What it helps with |
|---|---|
| `CsrfViewMiddleware` | CSRF protection |
| `AuthenticationMiddleware` | Adds `request.user` |
| `MessageMiddleware` | Temporary messages |
| `SessionMiddleware` | Sessions |
| `CommonMiddleware` | Common HTTP behavior |
| `SecurityMiddleware` | Security-related headers/settings |

Order matters. Middleware is processed in the order listed in `MIDDLEWARE`.

## Context in Simple Words

Context is a dictionary of data sent from a view to a template.

Example:

```python
return render(
    request,
    'blog/post/list.html',
    {'posts': posts}
)
```

The context is:

```python
{'posts': posts}
```

The template can now use:

```django
{% for post in posts %}
  {{ post.title }}
{% endfor %}
```

If a variable is not in context, the template cannot display it unless a context processor provides
it.

## Context Processors

Context processors add common variables to all templates.

Examples in these chapters:

```python
'django.contrib.auth.context_processors.auth'
'django.contrib.messages.context_processors.messages'
'django.template.context_processors.request'
```

Meaning:

| Context processor | Gives templates access to |
|---|---|
| `auth` | `user`, permissions |
| `messages` | `messages` |
| `request` | `request` |

That is why templates can use:

```django
{% if request.user.is_authenticated %}
```

## How to Read Import Lines

Python import lines bring code from another module into the current file.

Example:

```python
from django.shortcuts import get_object_or_404, render
```

Meaning:

- Go to `django.shortcuts`.
- Import `get_object_or_404`.
- Import `render`.
- Now this file can use those names directly.

Relative import example:

```python
from .models import Post
```

Meaning:

- The dot means "from this same app/package".
- Import `Post` from this app's `models.py`.

Parent package import example:

```python
from ..models import Post
```

Meaning:

- `..` means go up one package level.
- This appears in `blog/templatetags/blog_tags.py`.

## How to Read Decorators

A decorator is a line starting with `@` above a function or class.

Example:

```python
@login_required
def dashboard(request):
    ...
```

Simple meaning:

- Wrap this function with extra behavior.
- In this case, require login before running the view.

Another example:

```python
@require_POST
def post_comment(request, post_id):
    ...
```

Meaning:

- Only allow POST requests.
- Reject other methods.

Common decorators in chapters 1-6:

| Decorator | Meaning |
|---|---|
| `@login_required` | User must be logged in |
| `@require_POST` | Request method must be POST |
| `@admin.register(Model)` | Register model with Django admin |
| `@register.simple_tag` | Create custom template tag |
| `@register.inclusion_tag(...)` | Create custom tag that renders a template |
| `@register.filter(...)` | Create custom template filter |

## How to Read Django Model Field Lines

Model field lines usually look like this:

```python
title = models.CharField(max_length=250)
```

Read it as:

```text
Create a database column named title.
The column stores short text.
The maximum length is 250 characters.
```

Another example:

```python
created = models.DateTimeField(auto_now_add=True)
```

Read it as:

```text
Create a datetime column named created.
Automatically set it once when the row is created.
```

Another example:

```python
author = models.ForeignKey(settings.AUTH_USER_MODEL, on_delete=models.CASCADE)
```

Read it as:

```text
Create a relationship from this model to the User model.
If the user is deleted, delete this object too.
```

## Common Model Field Options

| Option | Meaning |
|---|---|
| `max_length=250` | Maximum number of characters |
| `blank=True` | Form can leave this empty |
| `null=True` | Database can store NULL |
| `default=...` | Default value when object is created |
| `unique=True` | Value must be unique in the table |
| `unique_for_date='publish'` | Value must be unique for a specific date field |
| `related_name='comments'` | Name for reverse relationship |
| `on_delete=models.CASCADE` | Delete related objects when parent is deleted |
| `upload_to='...'` | Folder path for uploaded files |
| `auto_now_add=True` | Set only when first created |
| `auto_now=True` | Update every time object is saved |
| `choices=...` | Limit values to predefined choices |

## How to Read a QuerySet Line

Example:

```python
Post.published.filter(tags__in=[tag]).exclude(id=post.id)
```

Read it from left to right:

1. Start from published posts.
2. Keep posts that have this tag.
3. Remove the current post.

QuerySet methods usually return a new QuerySet. They do not immediately change the database.

Common methods:

| Method | Meaning |
|---|---|
| `.all()` | All rows |
| `.filter(...)` | Keep matching rows |
| `.exclude(...)` | Remove matching rows |
| `.get(...)` | Get exactly one row |
| `.order_by(...)` | Sort results |
| `.annotate(...)` | Add calculated values |
| `.values_list(...)` | Return selected column values |
| `.count()` | Count rows |
| `.exists()` | Check whether at least one row exists |

## Double Underscore Lookups

Django uses double underscores for advanced lookups.

Examples:

```python
publish__year=year
publish__month=month
id__in=[1, 2, 3]
similarity__gt=0.1
```

Meanings:

| Lookup | Meaning |
|---|---|
| `publish__year` | Year part of `publish` field |
| `publish__month` | Month part of `publish` field |
| `id__in` | ID is inside this list |
| `similarity__gt` | Similarity is greater than value |

## How to Read `render`, `redirect`, and `reverse`

`render` creates an HTML response:

```python
return render(request, 'template.html', context)
```

`redirect` sends the browser to another URL:

```python
return redirect(new_image.get_absolute_url())
```

`reverse` builds a URL from a URL name:

```python
reverse('blog:post_detail', args=[self.id])
```

Why use `reverse` instead of hardcoding URLs?

- URL patterns can change.
- Named URLs are easier to maintain.
- Templates and models can build URLs consistently.

## Common Beginner Mental Model

When you see a Django feature, ask these questions:

1. Which URL starts this feature?
2. Which view handles it?
3. Does the view read or write a model?
4. Does it use a form?
5. Which template displays it?
6. Does it need login?
7. Does it need CSRF?
8. Does it need a migration?
9. Does it need settings or environment variables?

This question list works for almost every feature in chapters 1-6.

## Important Django Words

| Word | Simple meaning | Example in this repo |
|---|---|---|
| Project | Main Django configuration folder | `Chapter01/mysite/mysite` |
| App | A feature module inside a project | `blog`, `account`, `images` |
| Model | Python class that creates a database table | `Post`, `Comment`, `Profile`, `Image` |
| Field | A column in a database table | `title`, `slug`, `body`, `created` |
| View | Python code that handles a request | `post_list`, `post_detail` |
| Template | HTML file with Django template syntax | `blog/post/list.html` |
| URL pattern | A route that connects a URL to a view | `path('', views.post_list, ...)` |
| Migration | File that records database structure changes | `0001_initial.py` |
| Admin | Built-in Django dashboard for managing data | `/admin/` |
| QuerySet | A database query represented in Python | `Post.published.all()` |
| Form | Python class for validating user input | `EmailPostForm`, `CommentForm` |

## Repository Organization

This repository contains separate chapter folders.

```text
Chapter01/  Blog project basics
Chapter02/  Blog forms, pagination, comments, email
Chapter03/  Blog tags, search, sitemap, RSS, template tags
Chapter04/  Bookmarks project, authentication, user profiles
Chapter05/  Messages, custom auth backend, social login, HTTPS dev tools
Chapter06/  Image bookmarking app, bookmarklet, likes, AJAX
```

There is also a folder named `class new record`. In this repo, that folder contains a consolidated
blog project state through Chapter 3, with Docker and `.env` setup.

## Support Files You Will See in Every Django Project

Each Django project has some files that beginners often ignore at first, but they are part of the
project structure.

| File | Simple meaning | When you usually touch it |
|---|---|---|
| `manage.py` | Runs Django commands for this project | Often |
| `settings.py` | Main configuration | Often |
| `urls.py` | Main URL routing | Often |
| `wsgi.py` | Entry point for traditional production servers | Rarely while learning |
| `asgi.py` | Entry point for async/websocket-capable servers | Rarely in chapters 1-6 |
| `apps.py` | App configuration class | Sometimes when configuring app startup behavior |
| `tests.py` | Place for tests | When writing automated tests |
| `__init__.py` | Marks a folder as a Python package | Almost never manually changed |

In these chapters, most real learning work happens in:

```text
models.py
views.py
urls.py
forms.py
admin.py
templates/
settings.py
```

## Static Files

Static files are files that are not uploaded by users. Examples:

- CSS files.
- JavaScript files.
- Logo images.

The chapters use:

```python
STATIC_URL = 'static/'
```

Templates load static files like this:

```django
{% load static %}
<link href="{% static "css/base.css" %}" rel="stylesheet">
```

Use static files for project assets. Use media files for user uploads.

## Docker Files in This Repo

Each chapter folder also contains Docker-related files:

```text
Dockerfile
docker-compose.yml
do.sh
```

These help run the chapter in containers. You can learn Django without Docker first, but Docker is
useful when a chapter needs services such as PostgreSQL.

Common Docker commands from the repo README:

```bash
./do.sh build
./do.sh run
```

If you are a beginner, first understand the normal Django commands. Then use Docker when you need a
consistent environment or when the chapter setup requires extra services.

## Common Commands

Run commands from the correct project folder. For example, for Chapter 1:

```bash
cd Chapter01/mysite
python manage.py runserver
```

For Chapter 6:

```bash
cd Chapter06/bookmarks
python manage.py runserver
```

Common commands:

| Command | What it does | When to use it |
|---|---|---|
| `pip install -r requirements.txt` | Installs packages | When setting up a chapter |
| `python manage.py runserver` | Starts dev server | When testing pages in browser |
| `python manage.py makemigrations` | Creates migration files | After changing models |
| `python manage.py migrate` | Applies migrations to DB | After creating migrations or setting up DB |
| `python manage.py createsuperuser` | Creates admin user | When you need `/admin/` login |
| `python manage.py shell` | Opens Django shell | When testing ORM queries |
| `python manage.py check` | Checks project config | When debugging settings |

## When Should I Update Which File?

| You want to change... | Usually update this file |
|---|---|
| Add or change database table | `models.py`, then run migrations |
| Show model in admin | `admin.py` |
| Add a web page | `views.py`, `urls.py`, template file |
| Add a form | `forms.py`, view, template |
| Add a package | `requirements.txt`, maybe `settings.py` |
| Add a new app | `settings.py` `INSTALLED_APPS`, app files |
| Change HTML layout | Template files |
| Add uploaded media | `settings.py`, root `urls.py`, templates |
| Add login protection | View decorators or class-based view mixins |
| Add config secrets | `.env` and `settings.py`, but never commit secrets |

## The Most Important Beginner Rule

If you change `models.py`, think:

```text
Did I change database structure?
If yes:
  python manage.py makemigrations
  python manage.py migrate
```

Examples of model changes that need migrations:

- Add a new model.
- Add a field.
- Remove a field.
- Change field type.
- Change `max_length`.
- Add an index.

Examples that usually do not need migrations:

- Change a view.
- Change a template.
- Change a form label.
- Change a URL pattern.
- Change a Python helper method that does not affect fields.

---

# Chapter 1: Blog Basics

Chapter 1 is in:

```text
Chapter01/mysite
```

The main app is:

```text
Chapter01/mysite/blog
```

This chapter introduces the basic Django blog app.

## Important Files in Chapter 1

| File | Purpose |
|---|---|
| `manage.py` | Command-line tool for this Django project |
| `mysite/settings.py` | Project settings |
| `mysite/urls.py` | Main project URL routes |
| `blog/models.py` | Database models for blog posts and favorites |
| `blog/admin.py` | Admin setup for blog models |
| `blog/views.py` | Request handling logic |
| `blog/urls.py` | Blog app URL routes |
| `blog/templates/...` | HTML templates |

## Project vs App

In Chapter 1:

- `mysite` is the Django project.
- `blog` is a Django app.

The project contains global settings. The app contains one feature area.

Think like this:

```text
Project = whole website configuration
App = one feature inside the website
```

Examples:

- Blog website project: `mysite`
- Blog feature app: `blog`

## The `Post` Model

The `Post` model lives in:

```text
Chapter01/mysite/blog/models.py
```

It represents a blog post in the database.

Important fields:

```python
title = models.CharField(max_length=250)
slug = models.SlugField(max_length=250)
author = models.ForeignKey(settings.AUTH_USER_MODEL, on_delete=models.CASCADE)
body = models.TextField()
publish = models.DateTimeField(default=timezone.now)
created = models.DateTimeField(auto_now_add=True)
updated = models.DateTimeField(auto_now=True)
status = models.CharField(max_length=2, choices=Status, default=Status.DRAFT)
```

### Field Meaning

| Field | Meaning |
|---|---|
| `title` | Short title of the post |
| `slug` | URL-friendly text, usually made from title |
| `author` | User who wrote the post |
| `body` | Main post content |
| `publish` | Date/time when post should be published |
| `created` | Date/time when database row was created |
| `updated` | Date/time when row was last updated |
| `status` | Draft or Published |

## `CharField` vs `TextField`

Use `CharField` for short text.

```python
title = models.CharField(max_length=250)
```

Use `TextField` for long text.

```python
body = models.TextField()
```

Rule:

```text
Short text with length limit -> CharField
Long article/content text -> TextField
```

## `ForeignKey`

The `author` field connects a post to a user.

```python
author = models.ForeignKey(
    settings.AUTH_USER_MODEL,
    on_delete=models.CASCADE,
    related_name='blog_posts'
)
```

Simple meaning:

- One user can write many posts.
- Each post has one author.
- If the user is deleted, their posts are deleted too because of `on_delete=models.CASCADE`.
- `related_name='blog_posts'` lets you use `user.blog_posts.all()`.

## `TextChoices`

The model uses `TextChoices` for draft/published status.

```python
class Status(models.TextChoices):
    DRAFT = 'DF', 'Draft'
    PUBLISHED = 'PB', 'Published'
```

This prevents random status values. Instead of typing strings everywhere, you use:

```python
Post.Status.PUBLISHED
```

Beginner reason:

- It makes code easier to read.
- It reduces spelling mistakes.
- It gives nice choices in the admin.

## Custom Manager: `PublishedManager`

The repo has:

```python
class PublishedManager(models.Manager):
    def get_queryset(self):
        return super().get_queryset().filter(status=Post.Status.PUBLISHED)
```

Then inside `Post`:

```python
objects = models.Manager()
published = PublishedManager()
```

Meaning:

- `Post.objects.all()` gets all posts.
- `Post.published.all()` gets only published posts.

This keeps views cleaner. Instead of repeating:

```python
Post.objects.filter(status=Post.Status.PUBLISHED)
```

you can write:

```python
Post.published.all()
```

## Model `Meta`

The `Post` model has:

```python
class Meta:
    ordering = ['-publish']
    indexes = [
        models.Index(fields=['-publish']),
    ]
```

Meaning:

- `ordering = ['-publish']` shows newest posts first by default.
- The minus sign means descending order.
- The index helps database searches/sorting by publish date.

When to update `Meta`:

- Add `ordering` when you want default order.
- Add `indexes` when a field is often used for filtering or sorting.
- After adding or changing an index, run migrations.

## `__str__`

The model has:

```python
def __str__(self):
    return self.title
```

This controls how the object looks in admin and shell.

Without `__str__`, Django may show:

```text
Post object (1)
```

With `__str__`, Django shows:

```text
My First Blog Post
```

## `get_absolute_url`

The `Post` model has:

```python
def get_absolute_url(self):
    return reverse('blog:post_detail', args=[self.id])
```

This method gives the canonical URL for one object.

Simple meaning:

```text
Every post knows its own detail page URL.
```

Use it when:

- Redirecting after creating/updating an object.
- Linking to an object in templates.
- Building RSS feeds or sitemaps.

## Admin Setup

Admin setup lives in:

```text
Chapter01/mysite/blog/admin.py
```

The repo registers `Post`:

```python
@admin.register(Post)
class PostAdmin(admin.ModelAdmin):
    list_display = ['title', 'slug', 'author', 'publish', 'status']
    list_filter = ['status', 'created', 'publish', 'author']
    search_fields = ['title', 'body']
    prepopulated_fields = {'slug': ('title',)}
    raw_id_fields = ['author']
    date_hierarchy = 'publish'
    ordering = ['status', 'publish']
```

What these mean:

| Option | Meaning |
|---|---|
| `list_display` | Columns shown in admin list page |
| `list_filter` | Filters on right side |
| `search_fields` | Search box fields |
| `prepopulated_fields` | Auto-fills slug from title |
| `raw_id_fields` | Shows author as ID selector instead of big dropdown |
| `date_hierarchy` | Adds date navigation |
| `ordering` | Admin list order |

When to update `admin.py`:

- You create a new model and want to manage it in admin.
- You add fields and want them visible/searchable/filterable.
- You want a better admin editing experience.

## Migrations

When you create or change models, Django needs migrations.

Chapter 1 has migrations such as:

```text
blog/migrations/0001_initial.py
blog/migrations/0002_favouritepost.py
```

Use:

```bash
python manage.py makemigrations
python manage.py migrate
```

Beginner meaning:

- `makemigrations` creates instructions.
- `migrate` applies instructions to the database.

## ORM Basics

ORM means Object Relational Mapper. It lets you use Python instead of writing SQL directly.

Example queries:

```python
Post.objects.all()
Post.published.all()
Post.objects.filter(status=Post.Status.PUBLISHED)
Post.objects.get(id=1)
```

Common ORM methods:

| Method | Meaning |
|---|---|
| `.all()` | Get all rows |
| `.filter(...)` | Get rows matching condition |
| `.get(...)` | Get exactly one row |
| `.create(...)` | Create and save a row |
| `.order_by(...)` | Sort results |
| `.count()` | Count rows |

## Views in Chapter 1

Views live in:

```text
Chapter01/mysite/blog/views.py
```

### `post_list`

```python
def post_list(request):
    posts = Post.published.all()
    return render(request, 'blog/post/list.html', {'posts': posts})
```

Meaning:

1. Get all published posts.
2. Render the list template.
3. Send posts into the template using context.

`context` is the dictionary passed to the template:

```python
{'posts': posts}
```

In the template, you can use:

```django
{% for post in posts %}
    {{ post.title }}
{% endfor %}
```

### `post_detail`

```python
def post_detail(request, id):
    post = get_object_or_404(Post, id=id, status=Post.Status.PUBLISHED)
    return render(request, 'blog/post/detail.html', {'post': post})
```

Meaning:

- Try to find a published post by ID.
- If it does not exist, show a 404 page.

Use `get_object_or_404` when:

- The user is requesting one object.
- Missing object should be a normal "not found" page.

## URLs in Chapter 1

Blog URLs live in:

```text
Chapter01/mysite/blog/urls.py
```

Example:

```python
urlpatterns = [
    path('', views.post_list, name='post_list'),
    path('<int:id>/', views.post_detail, name='post_detail'),
]
```

Meaning:

- `/blog/` shows the post list.
- `/blog/1/` shows post with ID 1.

`app_name = 'blog'` creates a namespace. That lets you use:

```python
reverse('blog:post_detail', args=[self.id])
```

And in templates:

```django
{% url 'blog:post_detail' post.id %}
```

## Templates in Chapter 1

Templates are HTML files.

Chapter 1 templates include:

```text
blog/templates/blog/base.html
blog/templates/blog/post/list.html
blog/templates/blog/post/detail.html
blog/templates/blog/post/favourites.html
```

Common template syntax:

| Syntax | Meaning |
|---|---|
| `{{ post.title }}` | Print a value |
| `{% for post in posts %}` | Loop |
| `{% if condition %}` | Conditional logic |
| `{% url 'blog:post_detail' post.id %}` | Build a URL |
| `{% extends "blog/base.html" %}` | Use a base layout |
| `{% block content %}` | Replace a section in base template |

## Template Filters and Tags Used in These Chapters

The templates use several built-in Django filters and tags.

| Template code | Meaning |
|---|---|
| `{{ post.body|linebreaks }}` | Converts line breaks into HTML paragraphs/breaks |
| `{{ post.body|truncatewords:30 }}` | Shows only the first 30 words |
| `{{ post.body|truncatewords_html:30 }}` | Truncates HTML safely |
| `{{ total_comments|pluralize }}` | Adds `s` when count is not 1 |
| `{% empty %}` | Runs when a loop has no items |
| `{% with total=items.count %}` | Creates a temporary template variable |
| `{% include "pagination.html" with page=posts %}` | Reuses another template with a variable |
| `{% querystring page=page.next_page_number %}` | Builds a query string while preserving existing query parameters |

These are not database concepts. They only affect how data is displayed in HTML.

## Repo-Specific Feature: Favorite Posts

This repo's `Chapter01` includes a `FavouritePost` model.

```python
class FavouritePost(models.Model):
    pk = models.CompositePrimaryKey("user", "post")
    user = models.ForeignKey(settings.AUTH_USER_MODEL, on_delete=models.CASCADE)
    post = models.ForeignKey('blog.Post', on_delete=models.CASCADE)
    created = models.DateTimeField(auto_now_add=True)
```

Simple meaning:

- A user can favorite a post.
- The same user should not favorite the same post twice.
- The favorite relationship is stored in a separate table.

### Repo URL Spelling Note

The Chapter 1 URL pattern is:

```python
path('favrourite/add/<int:id>/', views.add_favourite, name='add_favourite')
```

The path text contains `favrourite`, which looks like a spelling mistake. Because URLs are public
interfaces, changing it would require updating templates and any saved links. If you fix this later,
update both `blog/urls.py` and the template link that points to `blog:add_favourite`.

### Composite Primary Key

This line is important:

```python
pk = models.CompositePrimaryKey("user", "post")
```

It means the primary key is made from both `user` and `post`.

Simple example:

```text
user 5 + post 10 = one favorite record
user 5 + post 11 = another favorite record
user 6 + post 10 = another favorite record
```

This prevents duplicate favorites for the same user and post combination.

## Favorite Views

Chapter 1 has:

```python
def add_favourite(request, id):
    post = get_object_or_404(Post, id=id)
    FavouritePost.objects.get_or_create(user=request.user, post=post)
    return HttpResponseRedirect(post.get_absolute_url())
```

Important idea:

- `get_or_create()` tries to find a row.
- If it does not exist, it creates it.
- This avoids duplicate favorite records.

Chapter 1 also has:

```python
@login_required
def favourites(request):
    favourite_posts = Post.objects.filter(
        id__in=FavouritePost.objects.filter(
            user=request.user
        ).values_list('post_id', flat=True)
    )
```

Meaning:

1. Find favorite records for the current user.
2. Extract the post IDs.
3. Get posts with those IDs.

Beginner note:

- `favourites` is protected with `@login_required`.
- If you expand the favorite feature, it is also safer to protect `add_favourite` with login.

## When to Update Files in Chapter 1

| Task | Files to update |
|---|---|
| Add a new post field | `models.py`, migrations, maybe `admin.py`, templates |
| Show a new page | `views.py`, `urls.py`, template |
| Change admin columns | `admin.py` |
| Change post URL shape | `models.py get_absolute_url`, `urls.py`, templates |
| Add a new relationship | `models.py`, migrations, views/templates |
| Add login requirement | `views.py` |

## Chapter 1 Q&A

### Q1. Why do we use `settings.AUTH_USER_MODEL` instead of importing `User`?

Because Django projects can use a custom user model. `settings.AUTH_USER_MODEL` keeps your model
more flexible.

### Q2. Why do we need `slug`?

A slug makes URLs cleaner and easier to read.

Example:

```text
/blog/my-first-post/
```

is better than:

```text
/blog/123/
```

Chapter 1 uses ID-based URLs, and Chapter 2 improves this with slugs and dates.

### Q3. What is the difference between `created`, `updated`, and `publish`?

- `created`: when the database row was first created.
- `updated`: when it was last saved.
- `publish`: when the post should be considered published.

### Q4. Why do we use `Post.published.all()`?

Because normal visitors should only see published posts, not draft posts.

### Q5. Why use `get_object_or_404`?

Because if the object does not exist, users should see a 404 page instead of a server error.

## Chapter 1 Practice

1. Add a new draft post in admin and check that it does not show in the public list.
2. Change a post to published and check that it appears.
3. Open Django shell and run `Post.published.count()`.
4. Add a new admin search field and test it.
5. Trace the flow from `/blog/1/` to `blog/urls.py`, then `views.py`, then template.

---

# Chapter 2: Forms, Pagination, Comments, and Email

Chapter 2 is in:

```text
Chapter02/mysite
```

This chapter expands the blog with:

- SEO-friendly URLs.
- Pagination.
- Class-based list view.
- Email sharing.
- Comments.
- Model forms.
- POST-only comment submission.

## Important Files in Chapter 2

| File | Purpose |
|---|---|
| `blog/models.py` | Adds better slug behavior and `Comment` model |
| `blog/forms.py` | Adds `EmailPostForm` and `CommentForm` |
| `blog/views.py` | Adds pagination, share view, comment view |
| `blog/urls.py` | Adds date/slug URLs, share URL, comment URL |
| `blog/templates/...` | Adds share/comment templates |
| `templates/pagination.html` | Reusable pagination partial |

## SEO-Friendly URLs

Chapter 1 detail URL:

```text
/blog/1/
```

Chapter 2 detail URL:

```text
/blog/2026/4/28/my-post-title/
```

The Chapter 2 `get_absolute_url` uses:

```python
return reverse(
    'blog:post_detail',
    args=[
        self.publish.year,
        self.publish.month,
        self.publish.day,
        self.slug,
    ],
)
```

This creates URLs based on date and slug.

Why this is useful:

- URLs are readable.
- URLs are better for search engines.
- URLs give users context.

## `unique_for_date`

The slug field changes to:

```python
slug = models.SlugField(max_length=250, unique_for_date='publish')
```

Meaning:

- Two posts can have the same slug if they are published on different dates.
- Two posts cannot have the same slug on the same publish date.

Example allowed:

```text
2026-04-28 / django-basics
2026-04-29 / django-basics
```

Example not allowed:

```text
2026-04-28 / django-basics
2026-04-28 / django-basics
```

## URL Pattern with Date and Slug

In `blog/urls.py`:

```python
path(
    '<int:year>/<int:month>/<int:day>/<slug:post>/',
    views.post_detail,
    name='post_detail',
)
```

This captures:

| URL part | View argument |
|---|---|
| year | `year` |
| month | `month` |
| day | `day` |
| slug | `post` |

Then the view uses them:

```python
post = get_object_or_404(
    Post,
    status=Post.Status.PUBLISHED,
    slug=post,
    publish__year=year,
    publish__month=month,
    publish__day=day,
)
```

Notice the double underscore lookups:

```python
publish__year
publish__month
publish__day
```

These are ORM field lookups.

## Pagination

Pagination means splitting a long list into pages.

In Chapter 2:

```python
paginator = Paginator(post_list, 3)
page_number = request.GET.get('page', 1)
```

Meaning:

- Show 3 posts per page.
- Read the current page number from the query string.
- If no page is provided, use page 1.

Example URLs:

```text
/blog/
/blog/?page=2
/blog/?page=3
```

## Handling Bad Page Numbers

The view handles invalid pages:

```python
try:
    posts = paginator.page(page_number)
except PageNotAnInteger:
    posts = paginator.page(1)
except EmptyPage:
    posts = paginator.page(paginator.num_pages)
```

Meaning:

- If the page is not a number, show page 1.
- If the page is too high, show the last page.

This prevents errors when users type bad URLs.

## Function-Based View vs Class-Based View

Chapter 2 includes both:

```python
def post_list(request):
    ...
```

and:

```python
class PostListView(ListView):
    queryset = Post.published.all()
    context_object_name = 'posts'
    paginate_by = 3
    template_name = 'blog/post/list.html'
```

The URL uses the class-based view:

```python
path('', views.PostListView.as_view(), name='post_list')
```

### Function-Based View

Good when:

- You want full control.
- Logic is custom.
- You are learning basics.

### Class-Based View

Good when:

- The behavior is common.
- You want less repeated code.
- Django already provides a generic view.

Beginner tip:

Learn function-based views first. Then class-based views become easier.

### `ListView` Pagination Variables

When Django's `ListView` handles pagination, templates get useful variables automatically.

In this repo, `blog/post/list.html` includes:

```django
{% include "pagination.html" with page=page_obj %}
```

`page_obj` is the current page object created by `ListView`.

In the manual function-based view, the context uses `posts` as the page object:

```python
{'posts': posts}
```

So when changing between function-based and class-based pagination, check the template variable
names carefully.

## Forms in Chapter 2

Forms live in:

```text
Chapter02/mysite/blog/forms.py
```

There are two forms:

```python
class EmailPostForm(forms.Form):
    ...
```

and:

```python
class CommentForm(forms.ModelForm):
    ...
```

## Normal Form: `EmailPostForm`

```python
class EmailPostForm(forms.Form):
    name = forms.CharField(max_length=25)
    email = forms.EmailField()
    to = forms.EmailField()
    comments = forms.CharField(required=False, widget=forms.Textarea)
```

This form is not directly tied to a model.

Use `forms.Form` when:

- You want to validate user input.
- You do not need to save directly to a database model.

## Model Form: `CommentForm`

```python
class CommentForm(forms.ModelForm):
    class Meta:
        model = Comment
        fields = ['name', 'email', 'body']
```

This form is tied to the `Comment` model.

Use `forms.ModelForm` when:

- The form maps directly to a model.
- You want Django to create form fields from model fields.
- You want easy saving with `form.save()`.

## Form Validation

The pattern is:

```python
form = EmailPostForm(request.POST)
if form.is_valid():
    cd = form.cleaned_data
```

Meaning:

- `request.POST` contains submitted form data.
- `is_valid()` checks the data.
- `cleaned_data` contains safe, validated Python values.

Do not use raw `request.POST` values directly for important logic when a form exists.

## Sharing a Post by Email

The `post_share` view:

1. Gets the post.
2. If request is `POST`, validates the form.
3. Builds the full post URL.
4. Sends email.
5. Shows success state.

Important code:

```python
post_url = request.build_absolute_uri(post.get_absolute_url())
```

`build_absolute_uri()` turns a relative URL into a full URL.

Example:

```text
/blog/2026/4/28/my-post/
```

becomes:

```text
http://127.0.0.1:8000/blog/2026/4/28/my-post/
```

## `send_mail`

Chapter 2 uses:

```python
send_mail(
    subject=subject,
    message=message,
    from_email=None,
    recipient_list=[cd['to']],
)
```

Meaning:

- `subject`: email subject line.
- `message`: email body.
- `from_email`: sender email. `None` means use default settings.
- `recipient_list`: list of recipient emails.

When email does not send, check:

- Email settings in `settings.py`.
- `.env` values if settings read from environment.
- Internet connection.
- SMTP provider restrictions.

## Chapter 2 Email Settings and `.env`

Chapter 2 already uses `python-decouple` for email settings in `mysite/settings.py`.

The settings read values like this:

```python
EMAIL_HOST_USER = config('EMAIL_HOST_USER')
EMAIL_HOST_PASSWORD = config('EMAIL_HOST_PASSWORD')
DEFAULT_FROM_EMAIL = config('DEFAULT_FROM_EMAIL')
```

Safe `.env` example:

```env
EMAIL_HOST_USER=your_email@example.com
EMAIL_HOST_PASSWORD=your_email_app_password
DEFAULT_FROM_EMAIL=your_email@example.com
```

The code uses Gmail SMTP settings:

```python
EMAIL_HOST = 'smtp.gmail.com'
EMAIL_PORT = 587
EMAIL_USE_TLS = True
```

If you use Gmail, you usually need an app password, not your normal Gmail password.

## Share and Comment Templates

Chapter 2 templates use a few important beginner patterns.

The share template checks the `sent` flag:

```django
{% if sent %}
  ...
{% else %}
  <form method="post">
    {{ form.as_p }}
    {% csrf_token %}
  </form>
{% endif %}
```

Meaning:

- Before sending, show the form.
- After sending, show a success message.
- `form.as_p` renders form fields wrapped in paragraphs.
- `{% csrf_token %}` protects POST forms from cross-site request forgery.

The comment form is included from another template:

```django
{% include "blog/post/includes/comment_form.html" %}
```

This avoids copying the same form HTML into multiple templates.

## Comment Model

Chapter 2 adds:

```python
class Comment(models.Model):
    post = models.ForeignKey(Post, on_delete=models.CASCADE, related_name='comments')
    name = models.CharField(max_length=80)
    email = models.EmailField()
    body = models.TextField()
    created = models.DateTimeField(auto_now_add=True)
    updated = models.DateTimeField(auto_now=True)
    active = models.BooleanField(default=True)
```

Meaning:

- Each comment belongs to one post.
- Each post can have many comments.
- `active` lets admin hide a comment without deleting it.
- `related_name='comments'` lets you use `post.comments.all()`.

## Comment Admin

Chapter 2 also registers comments in `blog/admin.py`:

```python
@admin.register(Comment)
class CommentAdmin(admin.ModelAdmin):
    list_display = ['name', 'email', 'post', 'created', 'active']
    list_filter = ['active', 'created', 'updated']
    search_fields = ['name', 'email', 'body']
```

This lets the admin:

- See comment author, email, post, date, and active status.
- Filter active/inactive comments.
- Search comment text.

If you add moderation fields to comments, update `CommentAdmin` too.

## Showing Comments

In `post_detail`:

```python
comments = post.comments.filter(active=True)
form = CommentForm()
```

The template receives:

```python
{
    'post': post,
    'comments': comments,
    'form': form
}
```

Meaning:

- Show only active comments.
- Show an empty form for new comments.

## Adding Comments with POST

The `post_comment` view has:

```python
@require_POST
def post_comment(request, post_id):
    ...
```

Meaning:

- This view only accepts POST requests.
- Users should not create comments using GET requests.

Why this matters:

- GET should read data.
- POST should create or change data.

## `commit=False`

The comment form saves like this:

```python
comment = form.save(commit=False)
comment.post = post
comment.save()
```

Why not just `form.save()`?

Because the form only includes:

```python
fields = ['name', 'email', 'body']
```

It does not include `post`. So the code creates the comment object but waits before saving.
Then it adds `comment.post = post`, then saves.

Simple meaning:

```text
commit=False = create Python object, but do not write it to database yet.
```

## When to Update Files in Chapter 2

| Task | Files to update |
|---|---|
| Add a new form | `forms.py`, view, template |
| Add model-backed form field | `models.py`, migrations, `forms.py`, templates |
| Add a comment property | `models.py`, migrations, admin, templates |
| Change URL shape | `urls.py`, `get_absolute_url`, templates |
| Add a POST action | `views.py`, `urls.py`, form/template |
| Change page size | `views.py` or class-based view `paginate_by` |
| Change email behavior | `views.py`, `settings.py`, maybe `.env` |

## Chapter 2 Q&A

### Q1. Why use POST for comments?

Because comments create new data. GET requests should not create or change data.

### Q2. Why use `ModelForm` for comments?

Because the comment form directly matches the `Comment` model fields.

### Q3. Why does the comment form not include `post`?

The user should not choose which post the comment belongs to. The view already knows the post.

### Q4. What is `cleaned_data`?

It is the validated form data after Django has checked types, required fields, and field rules.

### Q5. Why handle `EmptyPage`?

Because users can manually type a page number that does not exist.

## Chapter 2 Practice

1. Change pagination from 3 posts per page to 5 posts per page.
2. Add a placeholder or label to the comment form template.
3. Add a field to `Comment`, then create and apply migrations.
4. Test an invalid email in the share form.
5. Explain why `commit=False` is needed in `post_comment`.

---

# Chapter 3: Tags, Search, Sitemap, RSS, and Template Tools

Chapter 3 is in:

```text
Chapter03/mysite
```

This chapter adds more advanced blog features:

- Tags.
- Similar posts.
- Custom template tags.
- Markdown rendering.
- RSS feed.
- Sitemap.
- PostgreSQL full-text/trigram search style feature.

## Important Files in Chapter 3

| File | Purpose |
|---|---|
| `blog/models.py` | Adds `TaggableManager` |
| `blog/forms.py` | Adds `SearchForm` |
| `blog/views.py` | Adds tag filtering, similar posts, search |
| `blog/templatetags/blog_tags.py` | Custom template tags and filter |
| `blog/feeds.py` | RSS feed |
| `blog/sitemaps.py` | Sitemap entries |
| `mysite/settings.py` | Adds apps, PostgreSQL, email config |
| `mysite/urls.py` | Adds `sitemap.xml` |

## Extra Packages in Chapter 3

`Chapter03/requirements.txt` includes packages such as:

```text
django-taggit
Markdown
psycopg
python-decouple
```

What they do:

| Package | Purpose |
|---|---|
| `django-taggit` | Adds tag support to models |
| `Markdown` | Converts Markdown text to HTML |
| `psycopg` | PostgreSQL database driver |
| `python-decouple` | Reads config from environment variables |

## Tags with `django-taggit`

In `models.py`:

```python
from taggit.managers import TaggableManager

class Post(models.Model):
    ...
    tags = TaggableManager()
```

This adds tags to posts without manually creating a tag model.

Example tags:

```text
django
python
web
beginner
```

Use tags when:

- You want categories or labels.
- You want users to browse related content.
- You want to recommend similar posts.

## Tag Filtering

In `post_list`:

```python
def post_list(request, tag_slug=None):
    post_list = Post.published.all()
    tag = None
    if tag_slug:
        tag = get_object_or_404(Tag, slug=tag_slug)
        post_list = post_list.filter(tags__in=[tag])
```

Meaning:

- If no tag is provided, show all published posts.
- If a tag slug is provided, show only posts with that tag.

URL pattern:

```python
path('tag/<slug:tag_slug>/', views.post_list, name='post_list_by_tag')
```

Example:

```text
/blog/tag/django/
```

## Similar Posts

In `post_detail`, Chapter 3 finds similar posts:

```python
post_tags_ids = post.tags.values_list('id', flat=True)
similar_posts = Post.published.filter(tags__in=post_tags_ids).exclude(id=post.id)
similar_posts = similar_posts.annotate(
    same_tags=Count('tags')
).order_by('-same_tags', '-publish')[:4]
```

Simple meaning:

1. Get IDs of current post's tags.
2. Find other posts that share those tags.
3. Count how many tags match.
4. Order by most matching tags, then newest.
5. Show only 4.

Important ORM concept:

```python
annotate(same_tags=Count('tags'))
```

This adds a calculated value named `same_tags` to each result.

## Custom Template Tags

Custom template tags live in:

```text
Chapter03/mysite/blog/templatetags/blog_tags.py
```

The folder structure matters:

```text
blog/
  templatetags/
    __init__.py
    blog_tags.py
```

You need `__init__.py` so Python treats the folder as a package.

## `simple_tag`

Example:

```python
@register.simple_tag
def total_posts():
    return Post.published.count()
```

Use it in a template:

```django
{% load blog_tags %}
{% total_posts %}
```

Meaning:

- Run Python code from a template.
- Return a value.

Use `simple_tag` for small calculated values.

## `inclusion_tag`

Example:

```python
@register.inclusion_tag('blog/post/latest_posts.html')
def show_latest_posts(count=5):
    latest_posts = Post.published.order_by('-publish')[:count]
    return {'latest_posts': latest_posts}
```

Use it in a template:

```django
{% show_latest_posts 3 %}
```

Meaning:

- Run Python query.
- Render a small template.
- Insert the result into the current page.

Use `inclusion_tag` for reusable mini sections, like "latest posts".

## Custom Template Filter: Markdown

Chapter 3 has:

```python
@register.filter(name='markdown')
def markdown_format(text):
    return mark_safe(markdown.markdown(text))
```

Use it in a template:

```django
{{ post.body|markdown }}
```

Meaning:

- Convert Markdown text into HTML.
- Mark the result as safe HTML.

Important safety note:

Only mark content safe when you trust it or sanitize it. Unsafe user-generated HTML can create
security problems.

## Most Commented Posts

Chapter 3 has:

```python
@register.simple_tag
def get_most_commented_posts(count=5):
    return Post.published.annotate(
        total_comments=Count('comments')
    ).order_by('-total_comments')[:count]
```

This counts related comments and orders posts by comment count.

Use this concept when:

- Ranking posts by comments.
- Ranking images by likes.
- Showing popular content.

## RSS Feed

RSS feed code lives in:

```text
Chapter03/mysite/blog/feeds.py
```

Main class:

```python
class LatestPostsFeed(Feed):
    title = 'My blog'
    link = reverse_lazy('blog:post_list')
    description = 'New posts of my blog.'
```

Important methods:

```python
def items(self):
    return Post.published.all()[:5]

def item_title(self, item):
    return item.title

def item_description(self, item):
    return truncatewords_html(markdown.markdown(item.body), 30)

def item_pubdate(self, item):
    return item.publish
```

URL:

```python
path('feed/', LatestPostsFeed(), name='post_feed')
```

RSS is useful when:

- Readers use feed readers.
- Other websites want latest post updates.
- You want machine-readable content updates.

## Sitemap

Sitemap code lives in:

```text
Chapter03/mysite/blog/sitemaps.py
```

```python
class PostSitemap(Sitemap):
    changefreq = 'weekly'
    priority = 0.9

    def items(self):
        return Post.published.all()

    def lastmod(self, obj):
        return obj.updated
```

Project URL setup in:

```text
Chapter03/mysite/mysite/urls.py
```

```python
sitemaps = {
    'posts': PostSitemap,
}

path(
    'sitemap.xml',
    sitemap,
    {'sitemaps': sitemaps},
    name='django.contrib.sitemaps.views.sitemap',
)
```

Sitemap is useful for search engines.

## Sites Framework and `SITE_ID`

Chapter 3 settings include:

```python
SITE_ID = 1
```

and installed apps include:

```python
'django.contrib.sites',
'django.contrib.sitemaps',
```

Simple meaning:

- The sites framework lets Django know which website/domain this project represents.
- The sitemap framework uses site/domain information when building sitemap URLs.

If sitemap or feed URLs show the wrong domain, check the Site object in Django admin and the
`SITE_ID` setting.

## Sidebar Template Concepts

Chapter 3 `blog/base.html` uses custom tags in the sidebar:

```django
{% total_posts %}
{% show_latest_posts 3 %}
{% get_most_commented_posts as most_commented_posts %}
```

Important detail:

```django
{% get_most_commented_posts as most_commented_posts %}
```

This stores the result in a template variable. Then the template loops over it:

```django
{% for post in most_commented_posts %}
  ...
{% endfor %}
```

Use this pattern when a template tag returns data that you want to reuse inside the template.

## PostgreSQL Setup

Chapter 3 uses PostgreSQL:

```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.postgresql',
        'NAME': config('DB_NAME'),
        'USER': config('DB_USER'),
        'PASSWORD': config('DB_PASSWORD'),
        'HOST': config('DB_HOST'),
    }
}
```

This uses `python-decouple`:

```python
from decouple import config
```

Safe `.env` example:

```env
DB_NAME=your_database_name
DB_USER=your_database_user
DB_PASSWORD=your_database_password
DB_HOST=localhost
EMAIL_HOST_USER=your_email@example.com
EMAIL_HOST_PASSWORD=your_email_app_password
DEFAULT_FROM_EMAIL=your_email@example.com
```

Do not commit real passwords.

## `django.contrib.postgres`

Chapter 3 includes:

```python
'django.contrib.postgres',
```

This enables PostgreSQL-specific Django features.

Use PostgreSQL-specific features when:

- You need full-text search.
- You need trigram similarity.
- You need advanced PostgreSQL fields or indexes.

## Trigram Search

Chapter 3 imports:

```python
from django.contrib.postgres.search import TrigramSimilarity
```

Search view:

```python
results = (
    Post.published.annotate(
        similarity=TrigramSimilarity('title', query),
    )
    .filter(similarity__gt=0.1)
    .order_by('-similarity')
)
```

Simple meaning:

- Compare search query with post titles.
- Give each post a similarity score.
- Keep posts above a score.
- Show best matches first.

Example:

```text
Query: djnago
Can still match: django
```

This is useful for typo-tolerant search.

Important:

- Trigram search requires PostgreSQL support.
- This repo has a migration named `0005_trigram_ext.py`, which enables the trigram extension.

## Search Form

In `forms.py`:

```python
class SearchForm(forms.Form):
    query = forms.CharField()
```

In the view:

```python
if 'query' in request.GET:
    form = SearchForm(request.GET)
    if form.is_valid():
        query = form.cleaned_data['query']
```

Search uses GET because it reads data, not changes data.

Example URL:

```text
/blog/search/?query=django
```

## When to Update Files in Chapter 3

| Task | Files to update |
|---|---|
| Add tags to a model | `models.py`, `settings.py`, migrations |
| Add tag page | `urls.py`, `views.py`, templates |
| Add custom template tag | `templatetags/*.py`, template `{% load ... %}` |
| Add RSS feed | `feeds.py`, `urls.py` |
| Add sitemap entries | `sitemaps.py`, project `urls.py` |
| Add search form | `forms.py`, `views.py`, template |
| Add PostgreSQL config | `settings.py`, `.env`, requirements |
| Add new package | `requirements.txt`, maybe `settings.py` |

## Chapter 3 Q&A

### Q1. Why use tags instead of categories?

Tags are more flexible. A post can have many tags. A simple category system often gives one main
category.

### Q2. Why does similar posts use `exclude(id=post.id)`?

Because the current post should not recommend itself.

### Q3. Why use `annotate`?

Because we want to add calculated values to query results, such as number of shared tags.

### Q4. Why use GET for search?

Search does not change data. Also, query URLs can be bookmarked and shared.

### Q5. Why keep secrets in `.env`?

Because secrets should not be hardcoded in `settings.py` or committed to Git.

## Chapter 3 Practice

1. Add tags to posts in admin and open a tag page.
2. Add a new template tag that shows the newest post title.
3. Search with a misspelled word and check trigram behavior.
4. Visit `/sitemap.xml`.
5. Visit `/blog/feed/`.
6. Explain when you need PostgreSQL instead of SQLite.

---

# Chapter 4: Authentication and User Profiles

Chapter 4 is in:

```text
Chapter04/bookmarks
```

This chapter starts a new project named `bookmarks` and an app named `account`.

It introduces:

- Django authentication.
- Login/logout.
- Password change and reset views.
- User registration.
- User profile model.
- Editing user and profile data.
- Uploaded media files.

## Important Files in Chapter 4

| File | Purpose |
|---|---|
| `bookmarks/settings.py` | Project settings |
| `bookmarks/urls.py` | Project URL routes |
| `account/models.py` | `Profile` model |
| `account/forms.py` | Login, registration, edit forms |
| `account/views.py` | Login, dashboard, register, edit views |
| `account/urls.py` | Account URL routes |
| `account/templates/...` | Login, register, password templates |

## Django Auth System

Django includes a built-in authentication system.

It provides:

- User model.
- Password hashing.
- Login.
- Logout.
- Password change.
- Password reset.
- Permissions.
- Groups.

That is why you see apps like:

```python
'django.contrib.auth',
'django.contrib.sessions',
'django.contrib.messages',
```

Authentication needs sessions. Sessions remember that a user is logged in between requests.

## The `Profile` Model

In `account/models.py`:

```python
class Profile(models.Model):
    user = models.OneToOneField(
        settings.AUTH_USER_MODEL,
        on_delete=models.CASCADE
    )
    date_of_birth = models.DateField(blank=True, null=True)
    photo = models.ImageField(upload_to='users/%Y/%m/%d/', blank=True)
```

Simple meaning:

- Django already has a User model.
- The Profile model stores extra user information.
- Each user has one profile.
- Each profile belongs to one user.

Use `OneToOneField` when:

```text
One object connects to exactly one other object.
```

Example:

```text
One User -> One Profile
```

## `blank=True` vs `null=True`

In the profile model:

```python
date_of_birth = models.DateField(blank=True, null=True)
```

Meaning:

- `blank=True`: form can be empty.
- `null=True`: database can store NULL.

For text fields, often use `blank=True` without `null=True`.
For date fields, `null=True` is common when the value can be missing.

## Image Uploads

```python
photo = models.ImageField(upload_to='users/%Y/%m/%d/', blank=True)
```

This saves uploaded photos into a date-based folder.

Example:

```text
media/users/2026/04/28/photo.jpg
```

Image uploads require Pillow, which is included in Chapter 4 requirements.

## Media Settings

Chapter 4 settings include:

```python
MEDIA_URL = 'media/'
MEDIA_ROOT = BASE_DIR / 'media'
```

Meaning:

- `MEDIA_URL` is the browser URL prefix for uploaded files.
- `MEDIA_ROOT` is the filesystem folder where files are stored.

In development, project `urls.py` serves media:

```python
if settings.DEBUG:
    urlpatterns += static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT)
```

Use this only for development. Production media serving needs a real web server or storage service.

## Profile Admin

Chapter 4 registers the `Profile` model in:

```text
Chapter04/bookmarks/account/admin.py
```

The admin class is:

```python
@admin.register(Profile)
class ProfileAdmin(admin.ModelAdmin):
    list_display = ['user', 'date_of_birth', 'photo']
    raw_id_fields = ['user']
```

Meaning:

- Profiles can be managed in `/admin/`.
- The list page shows user, birth date, and photo.
- `raw_id_fields = ['user']` is useful when there are many users, because it avoids a huge dropdown.

When you add fields to `Profile`, update `ProfileAdmin` if you want those fields visible in admin.

## Account Base Template

Chapter 4 account pages extend:

```text
account/templates/base.html
```

That template uses:

```django
{% if request.user.is_authenticated %}
```

This checks whether the current visitor is logged in.

It also uses a `section` context variable:

```django
{% if section == "dashboard" %}class="selected"{% endif %}
```

Simple meaning:

- Views pass `section` to templates.
- The base template uses it to highlight the active menu item.

Example from the dashboard view:

```python
return render(request, 'account/dashboard.html', {'section': 'dashboard'})
```

## Login Form

In `forms.py`:

```python
class LoginForm(forms.Form):
    username = forms.CharField()
    password = forms.CharField(widget=forms.PasswordInput)
```

`PasswordInput` hides password text in the browser.

## Manual Login View

Chapter 4 includes a manual login view:

```python
user = authenticate(
    request,
    username=cd['username'],
    password=cd['password'],
)
```

Then:

```python
if user is not None:
    if user.is_active:
        login(request, user)
```

Meaning:

- `authenticate()` checks username/password.
- `login()` stores the user ID in the session.
- `is_active` checks whether the account is enabled.

## Built-In Auth URLs

In `account/urls.py`:

```python
path('', include('django.contrib.auth.urls')),
```

This adds built-in routes for:

- Login.
- Logout.
- Password change.
- Password reset.

Templates live in:

```text
account/templates/registration/
```

Django expects those names by default.

Examples:

```text
registration/login.html
registration/logged_out.html
registration/password_change_form.html
registration/password_reset_form.html
```

The login template also includes:

```django
<input type="hidden" name="next" value="{{ next }}" />
```

`next` tells Django where to send the user after login. Example:

```text
User tries to open /account/edit/
User is redirected to /account/login/?next=/account/edit/
After login, Django sends user back to /account/edit/
```

## Login and Logout Settings

In `settings.py`:

```python
LOGIN_REDIRECT_URL = 'dashboard'
LOGIN_URL = 'login'
LOGOUT_URL = 'logout'
```

Meaning:

- After login, go to dashboard.
- If login is required, send user to login page.
- Logout URL name is `logout`.

## `login_required`

In `views.py`:

```python
@login_required
def dashboard(request):
    return render(request, 'account/dashboard.html', {'section': 'dashboard'})
```

Meaning:

- User must be logged in to view dashboard.
- If not logged in, Django redirects to login page.

Use `@login_required` for:

- Dashboard pages.
- Profile edit pages.
- Pages that show private user data.
- Actions that modify a user's data.

## Registration Form

In `forms.py`:

```python
class UserRegistrationForm(forms.ModelForm):
    password = forms.CharField(label='Password', widget=forms.PasswordInput)
    password2 = forms.CharField(label='Repeat password', widget=forms.PasswordInput)
```

The form asks for password twice.

Validation:

```python
def clean_password2(self):
    cd = self.cleaned_data
    if cd['password'] != cd['password2']:
        raise forms.ValidationError("Passwords don't match.")
    return cd['password2']
```

Custom clean methods are named:

```text
clean_<field_name>
```

Here, it validates `password2`.

## Password Hashing with `set_password`

In register view:

```python
new_user = user_form.save(commit=False)
new_user.set_password(user_form.cleaned_data['password'])
new_user.save()
```

Never save raw passwords directly.

Wrong:

```python
new_user.password = user_form.cleaned_data['password']
```

Correct:

```python
new_user.set_password(user_form.cleaned_data['password'])
```

`set_password()` hashes the password.

## Creating Profile After Registration

After saving the user:

```python
Profile.objects.create(user=new_user)
```

This creates the matching profile row.

If you create users in another place, remember profile creation too. Chapter 5 adds a social auth
pipeline helper for this.

## Editing User and Profile

Chapter 4 uses two forms:

```python
class UserEditForm(forms.ModelForm):
    class Meta:
        model = get_user_model()
        fields = ['first_name', 'last_name', 'email']
```

and:

```python
class ProfileEditForm(forms.ModelForm):
    class Meta:
        model = Profile
        fields = ['date_of_birth', 'photo']
```

The edit view handles both:

```python
user_form = UserEditForm(instance=request.user, data=request.POST)
profile_form = ProfileEditForm(
    instance=request.user.profile,
    data=request.POST,
    files=request.FILES,
)
```

Important:

- `request.POST` contains normal form fields.
- `request.FILES` contains uploaded files.
- `instance=...` means edit existing object instead of creating a new one.

## When to Update Files in Chapter 4

| Task | Files to update |
|---|---|
| Add profile field | `models.py`, migrations, `forms.py`, template |
| Add private page | `views.py`, `urls.py`, template, add `@login_required` |
| Add upload field | `models.py`, settings media config, form, template |
| Change login redirects | `settings.py` |
| Add registration field | `forms.py`, maybe model/profile, template |
| Add auth template | `account/templates/registration/` |

## Chapter 4 Common Beginner Mistakes

### Mistake 1: Saving raw passwords

Always use:

```python
set_password()
```

### Mistake 2: Forgetting `request.FILES`

If your form uploads files, include:

```python
files=request.FILES
```

### Mistake 3: Forgetting migrations after profile model changes

If you add a field to `Profile`, run:

```bash
python manage.py makemigrations
python manage.py migrate
```

### Mistake 4: Not creating profile for every user

If a user has no profile, `request.user.profile` can fail.

### Mistake 5: Putting auth templates in the wrong folder

Django built-in auth views look for templates inside:

```text
registration/
```

## Chapter 4 Q&A

### Q1. Why not add `date_of_birth` directly to Django's User model?

Because the default User model is built in. A Profile model is a simple way to add extra fields.

### Q2. Why use `get_user_model()` in forms?

It supports custom user models better than importing `User` directly.

### Q3. What does `instance=request.user` mean?

It means the form edits the current user instead of creating a new user.

### Q4. Why does profile photo need Pillow?

Django's `ImageField` uses Pillow to work with image files.

### Q5. What is a session?

A session stores data for a browser visitor across requests. Login uses sessions to remember the
current user.

## Chapter 4 Practice

1. Register a new user.
2. Log in and open the dashboard.
3. Edit profile name and photo.
4. Try opening dashboard while logged out.
5. Explain why `Profile` uses `OneToOneField`.

---

# Chapter 5: Messages, Custom Auth, Social Login, and HTTPS Dev Setup

Chapter 5 is in:

```text
Chapter05/bookmarks
```

This chapter builds on Chapter 4.

It adds:

- Messages framework usage.
- Custom authentication backend for email login.
- Social login with Google OAuth2.
- `python-decouple` for OAuth settings.
- Development HTTPS tools.

## Important Files in Chapter 5

| File | Purpose |
|---|---|
| `account/views.py` | Adds success/error messages |
| `account/authentication.py` | Custom email auth backend and social profile helper |
| `bookmarks/settings.py` | Auth backends, social auth settings, installed apps |
| `bookmarks/urls.py` | Adds social auth URLs |
| `requirements.txt` | Adds social auth and dev HTTPS packages |

## Messages Framework

In Chapter 5 edit view:

```python
messages.success(request, 'Profile updated successfully')
```

and:

```python
messages.error(request, 'Error updating your profile')
```

Messages are temporary notifications shown to the user.

Examples:

- Profile updated successfully.
- Invalid form.
- Image added successfully.
- Password changed.

The messages framework needs:

```python
'django.contrib.messages'
```

and middleware:

```python
'django.contrib.messages.middleware.MessageMiddleware'
```

and context processor:

```python
'django.contrib.messages.context_processors.messages'
```

These are already present in settings.

## When to Use Messages

Use messages after a user action:

- Save profile.
- Upload image.
- Submit form.
- Delete item.
- Login/logout feedback.

Do not use messages for permanent page content. Messages are short temporary feedback.

## Unique Email Validation in Chapter 5 and 6

Chapter 5 and Chapter 6 forms add custom email validation.

Registration form:

```python
def clean_email(self):
    data = self.cleaned_data['email']
    if User.objects.filter(email=data).exists():
        raise forms.ValidationError('Email already in use.')
    return data
```

Edit form:

```python
def clean_email(self):
    data = self.cleaned_data['email']
    qs = User.objects.exclude(id=self.instance.id).filter(email=data)
    if qs.exists():
        raise forms.ValidationError('Email already in use.')
    return data
```

Simple meaning:

- During registration, reject an email already used by any user.
- During profile edit, reject an email used by another user.
- `exclude(id=self.instance.id)` allows the current user to keep their own email.

Important repo note:

In `Chapter05/bookmarks/account/forms.py` and `Chapter06/bookmarks/account/forms.py`, the code uses
`User.objects...`, but the file imports `get_user_model()` instead of importing `User`. If you run
this form and get `NameError: name 'User' is not defined`, fix it by using one of these patterns:

```python
User = get_user_model()
```

near the top of the file, or replace `User.objects` with:

```python
get_user_model().objects
```

For a real project, using `get_user_model()` is the more flexible style.

## Repo-Specific Profile Photo Note in Chapter 5

Chapter 4 and Chapter 6 use:

```python
photo = models.ImageField(upload_to='users/%Y/%m/%d/', blank=True)
```

But Chapter 5 in this repo temporarily has:

```python
photo = models.CharField(max_length=250, blank=True)
```

The old `ImageField` line is commented out in Chapter 5. This means Chapter 5 stores photo-like
data as text, while Chapter 4 and Chapter 6 store uploaded image files. When learning, treat this as
a repo-specific difference and always check the current chapter's `account/models.py`.

## Custom Authentication Backend

Custom backend lives in:

```text
Chapter05/bookmarks/account/authentication.py
```

Main class:

```python
class EmailAuthBackend:
    def authenticate(self, request, username=None, password=None):
        try:
            user = User.objects.get(email=username)
            if user.check_password(password):
                return user
            return None
        except (User.DoesNotExist, User.MultipleObjectsReturned):
            return None
```

Simple meaning:

- User types email in the username field.
- Backend searches for a user with that email.
- It checks the password.
- If correct, it returns the user.

## `get_user`

The backend also has:

```python
def get_user(self, user_id):
    try:
        return User.objects.get(pk=user_id)
    except User.DoesNotExist:
        return None
```

Django uses this to load a user from the session.

## Authentication Backend Order

In `settings.py`:

```python
AUTHENTICATION_BACKENDS = [
    'django.contrib.auth.backends.ModelBackend',
    'account.authentication.EmailAuthBackend',
    'social_core.backends.google.GoogleOAuth2',
]
```

Meaning:

1. Try Django's normal username/password backend.
2. Try email/password backend.
3. Try Google OAuth2 backend.

Order matters because Django checks backends in sequence.

## Important Custom Backend Notes

This repo imports:

```python
from django.contrib.auth.models import User
```

That works with Django's default User model. In projects with custom user models, prefer
`get_user_model()`.

The backend handles:

```python
User.DoesNotExist
User.MultipleObjectsReturned
```

`MultipleObjectsReturned` matters because email may not be unique unless you enforce it.

## Social Login with Google

Chapter 5 adds:

```python
'social_django',
```

and:

```python
'social_core.backends.google.GoogleOAuth2',
```

Project URLs include:

```python
path(
    'social-auth/',
    include('social_django.urls', namespace='social'),
)
```

This gives social auth routes that the library uses for login callbacks.

## Social Login Link in the Template

The login template includes a Google login link:

```django
<a href="{% url "social:begin" "google-oauth2" %}">
  Sign in with Google
</a>
```

Meaning:

- `social:begin` starts the social authentication flow.
- `"google-oauth2"` chooses the Google backend.
- The URL works because project `urls.py` includes `social_django.urls` with namespace `social`.

## OAuth2 Settings

In settings:

```python
SOCIAL_AUTH_GOOGLE_OAUTH2_KEY = config('GOOGLE_OAUTH2_KEY')
SOCIAL_AUTH_GOOGLE_OAUTH2_SECRET = config('GOOGLE_OAUTH2_SECRET')
```

Safe `.env` example:

```env
GOOGLE_OAUTH2_KEY=your-google-client-id
GOOGLE_OAUTH2_SECRET=your-google-client-secret
```

Do not commit real OAuth secrets.

## Social Auth Pipeline

Chapter 5 defines:

```python
SOCIAL_AUTH_PIPELINE = [
    'social_core.pipeline.social_auth.social_details',
    'social_core.pipeline.social_auth.social_uid',
    'social_core.pipeline.social_auth.auth_allowed',
    'social_core.pipeline.social_auth.social_user',
    'social_core.pipeline.user.get_username',
    'social_core.pipeline.user.create_user',
    'account.authentication.create_profile',
    'social_core.pipeline.social_auth.associate_user',
    'social_core.pipeline.social_auth.load_extra_data',
    'social_core.pipeline.user.user_details',
]
```

Simple meaning:

- A pipeline is a list of steps.
- Each step runs during social login.
- The repo adds `account.authentication.create_profile`.

That helper is:

```python
def create_profile(backend, user, *args, **kwargs):
    Profile.objects.get_or_create(user=user)
```

This makes sure social login users also get a profile.

## `python-decouple`

Chapter 5 uses:

```python
from decouple import config
```

Purpose:

- Keep secrets out of code.
- Read config from `.env`.
- Make local/dev/prod configuration easier.

Use it for:

- OAuth keys.
- Email passwords.
- Database credentials.
- API keys.

Do not use it as an excuse to share `.env` publicly.

## `ALLOWED_HOSTS`

Chapter 5 has:

```python
ALLOWED_HOSTS = ['mysite.com', 'localhost', '127.0.0.1']
```

Meaning:

- Django only allows requests for these hostnames.
- This is a security setting.

During development, `localhost` and `127.0.0.1` are common.

## Development HTTPS Tools

Chapter 5 requirements include:

```text
django-extensions
werkzeug
pyOpenSSL
```

These help run a development HTTPS server.

Why HTTPS is useful in development:

- OAuth providers often require secure callback URLs.
- Some browser features require HTTPS.
- You can test secure behavior closer to production.

Never treat the development server as production.

## When to Update Files in Chapter 5

| Task | Files to update |
|---|---|
| Add a message after form save | `views.py`, base template if messages are not displayed |
| Add email login | `authentication.py`, `settings.py` |
| Add social provider | `settings.py`, `urls.py`, `.env`, provider dashboard |
| Add new secret/config | `.env`, `settings.py`, maybe `.env.example` |
| Add package | `requirements.txt`, install it |
| Change login flow | `AUTHENTICATION_BACKENDS`, auth templates, views |
| Ensure profile for new login type | registration view or social pipeline |

## Chapter 5 Troubleshooting

### Problem: Email login does not work

Check:

- Is `EmailAuthBackend` in `AUTHENTICATION_BACKENDS`?
- Does the user have that email?
- Are there multiple users with the same email?
- Is the password correct?

### Problem: Google login fails

Check:

- Are OAuth key and secret present in `.env`?
- Are callback URLs configured in Google console?
- Is `social-auth/` included in project URLs?
- Is `social_django` in `INSTALLED_APPS`?
- Did you run migrations for social auth tables if required?

### Problem: Messages do not show

Check:

- Is messages middleware enabled?
- Is messages context processor enabled?
- Does the base template loop through `messages`?

## Chapter 5 Q&A

### Q1. Why create a custom auth backend?

To allow login with email address instead of only username.

### Q2. Why does backend return `None`?

Returning `None` tells Django this backend could not authenticate the user.

### Q3. Why use `get_or_create` for profile?

Because the profile may already exist. `get_or_create` avoids duplicate profile creation.

### Q4. Why use `.env` for OAuth keys?

OAuth keys are secrets. They should not be hardcoded into source code.

### Q5. What is OAuth2?

OAuth2 lets users sign in through another service, like Google, without giving your app their Google
password.

## Chapter 5 Practice

1. Add a success message to registration.
2. Try logging in with username, then email.
3. Explain the order of `AUTHENTICATION_BACKENDS`.
4. Add a safe placeholder setting using `config()`.
5. Explain why social login users need profile creation too.

---

# Chapter 6: Image Bookmarking App

Chapter 6 is in:

```text
Chapter06/bookmarks
```

This chapter adds a new app:

```text
Chapter06/bookmarks/images
```

It introduces:

- Image model.
- Downloading images from URLs.
- Bookmarklet launcher.
- Image detail and list pages.
- Many-to-many likes.
- AJAX with JSON responses.
- Infinite-scroll style partial rendering.
- Thumbnails.

## Important Files in Chapter 6

| File | Purpose |
|---|---|
| `images/models.py` | `Image` model |
| `images/forms.py` | `ImageCreateForm` |
| `images/views.py` | Create, detail, like, list views |
| `images/urls.py` | Image app routes |
| `images/admin.py` | Image admin |
| `images/templates/bookmarklet_launcher.js` | Bookmarklet launcher |
| `images/templates/images/image/*.html` | Image templates |
| `bookmarks/settings.py` | Adds `images` and `easy_thumbnails` |
| `bookmarks/urls.py` | Includes image URLs |

## Installed Apps

Chapter 6 adds:

```python
'easy_thumbnails',
'images.apps.ImagesConfig',
```

Meaning:

- `easy_thumbnails` helps create thumbnails.
- `images` is the new app.

## The `Image` Model

In `images/models.py`:

```python
class Image(models.Model):
    user = models.ForeignKey(
        settings.AUTH_USER_MODEL,
        related_name='images_created',
        on_delete=models.CASCADE,
    )
    title = models.CharField(max_length=200)
    slug = models.SlugField(max_length=200, blank=True)
    url = models.URLField(max_length=2000)
    image = models.ImageField(upload_to='images/%Y/%m/%d/')
    description = models.TextField(blank=True)
    created = models.DateTimeField(auto_now_add=True)
    users_like = models.ManyToManyField(
        settings.AUTH_USER_MODEL,
        related_name='images_liked',
        blank=True,
    )
```

## Field Meaning

| Field | Meaning |
|---|---|
| `user` | User who bookmarked/uploaded the image |
| `title` | Image title |
| `slug` | URL-friendly title |
| `url` | Original image URL |
| `image` | Downloaded image file |
| `description` | Optional description |
| `created` | Creation date/time |
| `users_like` | Users who liked the image |

## ForeignKey: Image Owner

```python
user = models.ForeignKey(
    settings.AUTH_USER_MODEL,
    related_name='images_created',
    on_delete=models.CASCADE,
)
```

Meaning:

- One user can create many images.
- Each image has one creator.
- `user.images_created.all()` gets images created by a user.

## Many-to-Many: Likes

```python
users_like = models.ManyToManyField(
    settings.AUTH_USER_MODEL,
    related_name='images_liked',
    blank=True,
)
```

Meaning:

- One image can be liked by many users.
- One user can like many images.

Examples:

```python
image.users_like.add(request.user)
image.users_like.remove(request.user)
request.user.images_liked.all()
```

Use `ManyToManyField` when both sides can have many of the other side.

## Slug Generation

The `Image` model overrides `save()`:

```python
def save(self, *args, **kwargs):
    if not self.slug:
        self.slug = slugify(self.title)
    super().save(*args, **kwargs)
```

Meaning:

- If slug is empty, create it from the title.
- Then run normal save.

Example:

```text
Title: My Nice Image
Slug: my-nice-image
```

Use this pattern when you want automatic values before saving.

## `get_absolute_url`

```python
def get_absolute_url(self):
    return reverse('images:detail', args=[self.id, self.slug])
```

The image detail URL uses ID and slug.

Example:

```text
/images/detail/5/my-nice-image/
```

## Image Admin

In `images/admin.py`:

```python
@admin.register(Image)
class ImageAdmin(admin.ModelAdmin):
    list_display = ['title', 'slug', 'image', 'created']
    list_filter = ['created']
```

This lets staff manage images in admin.

## `ImageCreateForm`

In `images/forms.py`:

```python
class ImageCreateForm(forms.ModelForm):
    class Meta:
        model = Image
        fields = ['title', 'url', 'description']
        widgets = {
            'url': forms.HiddenInput,
        }
```

The form does not show the URL field normally because the bookmarklet supplies it.

## Hidden Input

```python
'url': forms.HiddenInput
```

Meaning:

- The form still submits the URL.
- The user does not type it manually in a visible field.

Use hidden inputs only for convenience. Do not trust hidden inputs for security, because users can
change them in browser tools.

## Validating Image URL Extension

```python
def clean_url(self):
    url = self.cleaned_data['url']
    valid_extensions = ['jpg', 'jpeg', 'png']
    extension = url.rsplit('.', 1)[1].lower()
    if extension not in valid_extensions:
        raise forms.ValidationError(
            'The given URL does not match valid image extensions.'
        )
    return url
```

Meaning:

- Get the URL.
- Extract extension after the last dot.
- Allow only JPG, JPEG, PNG.
- Raise validation error if invalid.

Beginner note:

This is a simple check. Real production apps often need stronger validation because URLs can be
tricky.

## Downloading the Image

The form overrides `save()`:

```python
def save(self, force_insert=False, force_update=False, commit=True):
    image = super().save(commit=False)
    image_url = self.cleaned_data['url']
    name = slugify(image.title)
    extension = image_url.rsplit('.', 1)[1].lower()
    image_name = f'{name}.{extension}'
    response = requests.get(image_url)
    image.image.save(
        image_name,
        ContentFile(response.content),
        save=False
    )
    if commit:
        image.save()
    return image
```

Simple meaning:

1. Create image object but do not save yet.
2. Get image URL.
3. Create file name from title.
4. Download the remote file with `requests`.
5. Store downloaded bytes in Django's file field.
6. Save the object if `commit=True`.

Important classes:

| Tool | Purpose |
|---|---|
| `requests.get()` | Downloads the remote image |
| `ContentFile` | Turns bytes into a Django file-like object |
| `slugify()` | Creates safe file name text |

## Image Create View

In `images/views.py`:

```python
@login_required
def image_create(request):
    if request.method == 'POST':
        form = ImageCreateForm(data=request.POST)
        if form.is_valid():
            new_image = form.save(commit=False)
            new_image.user = request.user
            new_image.save()
            messages.success(request, 'Image added successfully')
            return redirect(new_image.get_absolute_url())
    else:
        form = ImageCreateForm(data=request.GET)
```

Important ideas:

- User must be logged in.
- GET data can pre-fill the form from bookmarklet.
- POST submits the final image.
- `commit=False` is used because `user` is not in the form.
- After saving, redirect to the new image detail page.

## Bookmarklet Launcher

File:

```text
Chapter06/bookmarks/images/templates/bookmarklet_launcher.js
```

It adds a JavaScript file to the current page:

```javascript
bookmarklet_js = document.body.appendChild(document.createElement('script'));
bookmarklet_js.src = '//127.0.0.1:8000/static/js/bookmarklet.js?r=' + Math.floor(Math.random()*9999999999999999);
```

Simple meaning:

- A bookmarklet is JavaScript saved as a browser bookmark.
- When clicked, it runs on the current page.
- It can collect image data and send it to your Django app.

Bookmarklets are useful for:

- Saving images from other websites.
- Creating quick browser tools.
- Sending current page data to your app.

## Real Bookmarklet Script

The launcher loads this static file:

```text
Chapter06/bookmarks/images/static/js/bookmarklet.js
```

That script does more than the small launcher.

It defines:

```javascript
const siteUrl = '//127.0.0.1:8000/';
const styleUrl = siteUrl + 'static/css/bookmarklet.css';
const minWidth = 250;
const minHeight = 250;
```

Meaning:

- `siteUrl` is the Django site where the bookmarklet sends users.
- `styleUrl` loads bookmarklet CSS.
- `minWidth` and `minHeight` ignore tiny images.

The script injects CSS into the current page:

```javascript
var link = document.createElement('link');
link.rel = 'stylesheet';
link.href = styleUrl + '?r=' + Math.floor(Math.random()*9999999999999999);
head.appendChild(link);
```

The random query string helps avoid browser caching while developing.

It finds images like this:

```javascript
document.querySelectorAll('img[src$=".jpg"], img[src$=".jpeg"], img[src$=".png"]')
```

Then it opens the Django create page:

```javascript
window.open(
  siteUrl + 'images/create/?url='
  + encodeURIComponent(imageSelected.src)
  + '&title='
  + encodeURIComponent(document.title),
  '_blank'
);
```

`encodeURIComponent()` is important because image URLs and page titles can contain spaces, `?`, `&`,
and other special characters.

## Dashboard Bookmarklet Button

The Chapter 6 dashboard template includes the launcher directly inside a bookmark link:

```django
<a href="javascript:{% include "bookmarklet_launcher.js" %}" class="button">
  Bookmark it
</a>
```

The user drags this link to the browser bookmarks bar. When clicked on another website, it runs the
bookmarklet JavaScript.

## Image Detail View

```python
def image_detail(request, id, slug):
    image = get_object_or_404(Image, id=id, slug=slug)
    return render(
        request,
        'images/image/detail.html',
        {'section': 'images', 'image': image},
    )
```

Meaning:

- Find image by ID and slug.
- Render detail template.
- If missing, show 404.

## Thumbnails in Image Templates

Chapter 6 templates load thumbnail tags:

```django
{% load thumbnail %}
```

Detail page:

```django
<img src="{% thumbnail image.image 300x0 %}" class="image-detail">
```

List page:

```django
{% thumbnail image.image 300x300 crop="smart" as im %}
<img src="{{ im.url }}">
```

Meaning:

- `300x0` sets width to 300 and keeps height proportional.
- `300x300 crop="smart"` creates a square thumbnail and tries to crop intelligently.
- `{% thumbnail ... as im %}` stores the thumbnail object in `im`.

## Base Template JavaScript and CSRF

Chapter 6 `base.html` loads the js-cookie library:

```html
<script src="//cdn.jsdelivr.net/npm/js-cookie@3.0.5/dist/js.cookie.min.js"></script>
```

Then it reads the CSRF token:

```javascript
const csrftoken = Cookies.get('csrftoken');
```

This token is later sent with AJAX POST requests:

```javascript
headers: {'X-CSRFToken': csrftoken}
```

Why this matters:

- Normal POST forms use `{% csrf_token %}`.
- JavaScript POST requests must also send the CSRF token.
- Without it, Django can reject the request.

The base template also defines a JavaScript block:

```django
{% block domready %}
{% endblock %}
```

Child templates put page-specific JavaScript inside this block. The base template runs it after
`DOMContentLoaded`, so the HTML exists before JavaScript tries to find elements.

## Image Like View

```python
@login_required
@require_POST
def image_like(request):
    image_id = request.POST.get('id')
    action = request.POST.get('action')
    if image_id and action:
        try:
            image = Image.objects.get(id=image_id)
            if action == 'like':
                image.users_like.add(request.user)
            else:
                image.users_like.remove(request.user)
            return JsonResponse({'status': 'ok'})
        except Image.DoesNotExist:
            pass
    return JsonResponse({'status': 'error'})
```

Important concepts:

- Login required because likes belong to users.
- POST required because liking changes data.
- AJAX sends `id` and `action`.
- Server responds with JSON.

## `JsonResponse`

```python
return JsonResponse({'status': 'ok'})
```

This sends JSON instead of HTML.

Example response:

```json
{"status": "ok"}
```

Use `JsonResponse` when JavaScript needs data from the server.

## Like Button Data Attributes

The image detail template stores data on the like link:

```django
<a href="#"
   data-id="{{ image.id }}"
   data-action="{% if request.user in users_like %}un{% endif %}like"
   class="like button">
```

Meaning:

- `data-id` stores the image ID.
- `data-action` stores either `like` or `unlike`.
- JavaScript reads these values with `likeButton.dataset.id` and `likeButton.dataset.action`.

The JavaScript sends the values using `FormData`:

```javascript
var formData = new FormData();
formData.append('id', likeButton.dataset.id);
formData.append('action', likeButton.dataset.action);
```

After a successful JSON response, JavaScript:

- Changes `like` to `unlike`, or `unlike` to `like`.
- Updates the button text.
- Adds or subtracts one from the like count.

## Image List View

```python
def image_list(request):
    images = Image.objects.all()
    paginator = Paginator(images, 8)
    page = request.GET.get('page')
    images_only = request.GET.get('images_only')
```

Meaning:

- Get all images.
- Show 8 per page.
- Read page from query string.
- Check if only image cards should be returned.

## Partial Template Rendering

If `images_only` is present:

```python
return render(
    request,
    'images/image/list_images.html',
    {'section': 'images', 'images': images},
)
```

Otherwise:

```python
return render(
    request,
    'images/image/list.html',
    {'section': 'images', 'images': images},
)
```

Meaning:

- Full page uses `list.html`.
- AJAX/infinite scroll can request only `list_images.html`.

This avoids re-rendering the whole page when loading more images.

## Infinite Scroll with `fetch`

Chapter 6 `images/image/list.html` contains JavaScript that loads more images when the user scrolls
near the bottom of the page.

Important variables:

```javascript
var page = 1;
var emptyPage = false;
var blockRequest = false;
```

Meaning:

- `page` tracks the next page number.
- `emptyPage` becomes true when the server returns no more images.
- `blockRequest` prevents multiple requests at the same time.

The request is:

```javascript
fetch('?images_only=1&page=' + page)
```

This calls the same image list URL, but asks only for the partial image HTML.

The view checks:

```python
images_only = request.GET.get('images_only')
```

If no more images exist and this is an AJAX-style request, the view returns an empty response:

```python
return HttpResponse('')
```

The JavaScript treats empty HTML as the end:

```javascript
if (html === '') {
  emptyPage = true;
}
```

This is a simple infinite-scroll pattern:

```text
Scroll near bottom -> fetch next page -> append HTML -> repeat until empty
```

## Easy Thumbnails

`easy-thumbnails` helps create resized versions of images.

Use thumbnails because:

- Original images may be large.
- Lists should load smaller images.
- Pages become faster.
- Layout becomes cleaner.

General idea:

```text
Original image -> thumbnail image -> display thumbnail in list
```

## Image URLs

In `images/urls.py`:

```python
urlpatterns = [
    path('create/', views.image_create, name='create'),
    path('detail/<int:id>/<slug:slug>/', views.image_detail, name='detail'),
    path('like/', views.image_like, name='like'),
    path('', views.image_list, name='list'),
]
```

Project URLs include:

```python
path('images/', include('images.urls', namespace='images')),
```

Final URLs:

| URL | Meaning |
|---|---|
| `/images/` | Image list |
| `/images/create/` | Create image |
| `/images/detail/1/my-image/` | Image detail |
| `/images/like/` | Like/unlike POST endpoint |

## When to Update Files in Chapter 6

| Task | Files to update |
|---|---|
| Add image field | `models.py`, migrations, form/template/admin |
| Change allowed extensions | `forms.py` |
| Change download behavior | `forms.py` |
| Add new image page | `views.py`, `urls.py`, template |
| Add AJAX action | `views.py`, `urls.py`, JavaScript, template |
| Change list page size | `views.py` paginator number |
| Add thumbnail display | template, settings if needed |
| Add app | `settings.py`, project `urls.py` |

## Chapter 6 Q&A

### Q1. Why does `ImageCreateForm` not include `user`?

Because the current logged-in user should be assigned by the view. Users should not choose another
owner from the form.

### Q2. Why use `commit=False` in `image_create`?

Because the form creates the image object first, then the view adds `new_image.user`.

### Q3. Why is the URL field hidden?

Because the bookmarklet sends the image URL automatically.

### Q4. Why does liking use POST?

Because liking changes data.

### Q5. Why return JSON for likes?

Because JavaScript can update the page without reloading.

### Q6. Why use `ManyToManyField` for likes?

Because many users can like many images.

## Chapter 6 Practice

1. Add an image through the create page.
2. Like and unlike an image while logged in.
3. Try creating an image with an invalid file extension.
4. Change image pagination from 8 to 12.
5. Explain the difference between `list.html` and `list_images.html`.
6. Trace the image creation flow from bookmarklet GET data to saved image file.

---

# Line-by-Line Code Reading Appendix

This appendix explains the most important code patterns from chapters 1-6 line by line.

It does not repeat every single file from the repository, because many files use the same patterns.
Instead, it explains the core patterns in detail. After you understand these blocks, you can read
the remaining files with the same method.

## How to Study a Code Block

When reading Django code, use this order:

1. Read the imports first. They tell you what tools the file uses.
2. Read class names and function names. They tell you the purpose.
3. Read model fields. They tell you the data structure.
4. Read decorators. They tell you extra rules.
5. Read query lines. They tell you what data is selected.
6. Read `return` lines. They tell you what response goes back.

Beginner rule:

```text
Do not try to memorize code.
Try to explain each line in normal English.
```

## `manage.py` Line by Line

Every chapter project has a `manage.py` file. You usually do not edit it, but you use it every day.

Typical structure:

```python
import os
import sys


def main():
    os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'mysite.settings')
    from django.core.management import execute_from_command_line
    execute_from_command_line(sys.argv)


if __name__ == '__main__':
    main()
```

Line-by-line meaning:

| Line | Meaning |
|---|---|
| `import os` | Imports Python tools for environment variables and operating system behavior. |
| `import sys` | Imports Python tools for command-line arguments. |
| `def main():` | Defines the main function that runs Django commands. |
| `os.environ.setdefault(...)` | Tells Django which settings file to use if no settings module is already set. |
| `'DJANGO_SETTINGS_MODULE'` | Environment variable name Django reads to find settings. |
| `'mysite.settings'` | Python path to the settings module for the project. In bookmarks chapters this is `bookmarks.settings`. |
| `from django.core.management import execute_from_command_line` | Imports Django's command runner. |
| `execute_from_command_line(sys.argv)` | Runs the command you typed, such as `runserver` or `migrate`. |
| `if __name__ == '__main__':` | Runs the code only when this file is executed directly. |
| `main()` | Calls the main function. |

When you run:

```bash
python manage.py runserver
```

`sys.argv` contains something like:

```python
['manage.py', 'runserver']
```

Django reads that and starts the development server.

## `settings.py` Core Lines Explained

Settings files are long, but most beginner work focuses on a few sections.

### `BASE_DIR`

```python
from pathlib import Path

BASE_DIR = Path(__file__).resolve().parent.parent
```

Line-by-line meaning:

| Line | Meaning |
|---|---|
| `from pathlib import Path` | Imports Python's modern path helper. |
| `__file__` | The path of the current file, meaning `settings.py`. |
| `Path(__file__)` | Turns the file path into a `Path` object. |
| `.resolve()` | Converts it to an absolute path. |
| `.parent` | Goes up one folder. |
| `.parent.parent` | Goes up two folders, usually to the project root folder where `manage.py` lives. |

Why it matters:

```python
BASE_DIR / 'db.sqlite3'
```

builds a safe path to the SQLite database file.

### `DEBUG`

```python
DEBUG = True
```

Meaning:

- Show detailed error pages.
- Serve development helpers.
- Useful while learning.
- Dangerous in production.

Beginner rule:

```text
DEBUG=True for learning and local development.
DEBUG=False for production.
```

### `ALLOWED_HOSTS`

```python
ALLOWED_HOSTS = ['mysite.com', 'localhost', '127.0.0.1']
```

Meaning:

- Django accepts requests only for these hostnames.
- `localhost` and `127.0.0.1` are local development names.
- `mysite.com` is an example real domain.

If you see a "DisallowedHost" error, check `ALLOWED_HOSTS`.

### `INSTALLED_APPS`

```python
INSTALLED_APPS = [
    'account.apps.AccountConfig',
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
]
```

Line-by-line meaning:

| Line | Meaning |
|---|---|
| `INSTALLED_APPS = [` | Starts the list of apps enabled for this project. |
| `'account.apps.AccountConfig'` | Enables your local `account` app using its app config class. |
| `'django.contrib.admin'` | Enables Django admin site. |
| `'django.contrib.auth'` | Enables users, groups, permissions, authentication. |
| `'django.contrib.contenttypes'` | Lets Django track model types. Required by auth and many features. |
| `'django.contrib.sessions'` | Enables sessions, needed for login. |
| `'django.contrib.messages'` | Enables temporary messages. |
| `'django.contrib.staticfiles'` | Enables static file handling. |
| `]` | Ends the list. |

When you create a new app, add it here.

Examples from this repo:

```python
'blog.apps.BlogConfig'
'images.apps.ImagesConfig'
'taggit'
'social_django'
'easy_thumbnails'
```

### `MIDDLEWARE`

```python
MIDDLEWARE = [
    'django.middleware.security.SecurityMiddleware',
    'django.contrib.sessions.middleware.SessionMiddleware',
    'django.middleware.common.CommonMiddleware',
    'django.middleware.csrf.CsrfViewMiddleware',
    'django.contrib.auth.middleware.AuthenticationMiddleware',
    'django.contrib.messages.middleware.MessageMiddleware',
    'django.middleware.clickjacking.XFrameOptionsMiddleware',
]
```

Line-by-line meaning:

| Middleware | Beginner meaning |
|---|---|
| `SecurityMiddleware` | Adds security-related behavior. |
| `SessionMiddleware` | Makes `request.session` work. |
| `CommonMiddleware` | Handles common web behavior, such as some redirects. |
| `CsrfViewMiddleware` | Protects POST forms and AJAX POST requests. |
| `AuthenticationMiddleware` | Adds `request.user`. |
| `MessageMiddleware` | Makes messages framework work. |
| `XFrameOptionsMiddleware` | Helps prevent clickjacking. |

Middleware order matters. For example, auth needs sessions, so session middleware appears before
authentication middleware.

### `DATABASES`

SQLite example:

```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.sqlite3',
        'NAME': BASE_DIR / 'db.sqlite3',
    }
}
```

Line-by-line meaning:

| Line | Meaning |
|---|---|
| `DATABASES = {` | Starts database configuration. |
| `'default': {` | Defines the main database connection. |
| `'ENGINE': 'django.db.backends.sqlite3'` | Use SQLite database backend. |
| `'NAME': BASE_DIR / 'db.sqlite3'` | Store the database in a file named `db.sqlite3`. |

PostgreSQL example from Chapter 3:

```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.postgresql',
        'NAME': config('DB_NAME'),
        'USER': config('DB_USER'),
        'PASSWORD': config('DB_PASSWORD'),
        'HOST': config('DB_HOST'),
    }
}
```

Line-by-line meaning:

| Line | Meaning |
|---|---|
| `'ENGINE': 'django.db.backends.postgresql'` | Use PostgreSQL instead of SQLite. |
| `config('DB_NAME')` | Read database name from `.env`. |
| `config('DB_USER')` | Read database username from `.env`. |
| `config('DB_PASSWORD')` | Read database password from `.env`. |
| `config('DB_HOST')` | Read database host from `.env`. |

### `TEMPLATES`

Important section:

```python
'APP_DIRS': True,
'OPTIONS': {
    'context_processors': [
        'django.template.context_processors.request',
        'django.contrib.auth.context_processors.auth',
        'django.contrib.messages.context_processors.messages',
    ],
},
```

Line-by-line meaning:

| Line | Meaning |
|---|---|
| `'APP_DIRS': True` | Django should look for `templates/` folders inside installed apps. |
| `'context_processors': [` | Starts the list of global template variable providers. |
| `request` context processor | Lets templates use `request`. |
| `auth` context processor | Lets templates use user/auth data. |
| `messages` context processor | Lets templates use `messages`. |

## Project `urls.py` Line by Line

Chapter 1 project URL file:

```python
from django.contrib import admin
from django.urls import include, path

urlpatterns = [
    path('admin/', admin.site.urls),
    path('blog/', include('blog.urls', namespace='blog')),
]
```

Line-by-line meaning:

| Line | Meaning |
|---|---|
| `from django.contrib import admin` | Imports Django admin site. |
| `from django.urls import include, path` | Imports URL helper functions. |
| `urlpatterns = [` | Starts the list of URL routes. |
| `path('admin/', admin.site.urls)` | Sends `/admin/` URLs to Django admin. |
| `path('blog/', include(...))` | Sends `/blog/` URLs to the blog app's URL file. |
| `include('blog.urls', namespace='blog')` | Loads `blog/urls.py` and uses `blog` as URL namespace. |

Important:

The project `urls.py` usually connects big app areas.

The app `urls.py` usually connects specific pages inside that app.

## App `urls.py` Line by Line

Chapter 2 blog URLs:

```python
from django.urls import path

from . import views

app_name = 'blog'

urlpatterns = [
    path('', views.PostListView.as_view(), name='post_list'),
    path(
        '<int:year>/<int:month>/<int:day>/<slug:post>/',
        views.post_detail,
        name='post_detail',
    ),
    path('<int:post_id>/share/', views.post_share, name='post_share'),
    path('<int:post_id>/comment/', views.post_comment, name='post_comment'),
]
```

Line-by-line meaning:

| Line | Meaning |
|---|---|
| `from django.urls import path` | Imports function for defining URL patterns. |
| `from . import views` | Imports this app's `views.py`. |
| `app_name = 'blog'` | Sets namespace for this app's URL names. |
| `urlpatterns = [` | Starts URL list. |
| `path('', ...)` | Matches the app root, such as `/blog/`. |
| `views.PostListView.as_view()` | Converts a class-based view into a callable view function. |
| `name='post_list'` | Names this route so templates can refer to it. |
| `<int:year>` | Captures an integer from the URL and passes it as `year`. |
| `<slug:post>` | Captures slug text and passes it as `post`. |
| `<int:post_id>` | Captures post ID for share/comment actions. |

## Chapter 1 `PublishedManager` Line by Line

Code:

```python
class PublishedManager(models.Manager):
    def get_queryset(self):
        return (
            super().get_queryset().filter(status=Post.Status.PUBLISHED)
        )
```

Line-by-line meaning:

| Line | Meaning |
|---|---|
| `class PublishedManager(models.Manager):` | Creates a custom model manager. |
| `models.Manager` | Django's base manager class. |
| `def get_queryset(self):` | Defines the base query used by this manager. |
| `super().get_queryset()` | Gets the normal QuerySet from Django. |
| `.filter(status=Post.Status.PUBLISHED)` | Keeps only rows where status is published. |
| `return (...)` | Returns the filtered QuerySet. |

Why this exists:

```python
Post.published.all()
```

is easier and safer than repeating:

```python
Post.objects.filter(status=Post.Status.PUBLISHED)
```

## Chapter 1 `Post` Model Line by Line

Code:

```python
class Post(models.Model):
    class Status(models.TextChoices):
        DRAFT = 'DF', 'Draft'
        PUBLISHED = 'PB', 'Published'
    title = models.CharField(max_length=250)
    slug = models.SlugField(max_length=250)
    author = models.ForeignKey(
        settings.AUTH_USER_MODEL,
        on_delete=models.CASCADE,
        related_name='blog_posts'
    )
    body = models.TextField()
    publish = models.DateTimeField(default=timezone.now)
    created = models.DateTimeField(auto_now_add=True)
    updated = models.DateTimeField(auto_now=True)
    status = models.CharField(
        max_length=2,
        choices=Status,
        default=Status.DRAFT
    )
```

Line-by-line meaning:

| Line | Meaning |
|---|---|
| `class Post(models.Model):` | Creates a model named `Post`. It becomes a database table. |
| `class Status(models.TextChoices):` | Creates allowed text choices for post status. |
| `DRAFT = 'DF', 'Draft'` | Database value is `DF`, human label is `Draft`. |
| `PUBLISHED = 'PB', 'Published'` | Database value is `PB`, human label is `Published`. |
| `title = models.CharField(max_length=250)` | Short text field for title, max 250 characters. |
| `slug = models.SlugField(max_length=250)` | URL-friendly text field. |
| `author = models.ForeignKey(...)` | Relationship to the user who wrote the post. |
| `settings.AUTH_USER_MODEL` | Points to the active user model. |
| `on_delete=models.CASCADE` | Delete posts if their author is deleted. |
| `related_name='blog_posts'` | Allows `user.blog_posts.all()`. |
| `body = models.TextField()` | Long text field for post content. |
| `publish = models.DateTimeField(default=timezone.now)` | Date/time when post is published. Default is now. |
| `created = models.DateTimeField(auto_now_add=True)` | Automatically set only when object is first created. |
| `updated = models.DateTimeField(auto_now=True)` | Automatically update every time object is saved. |
| `status = models.CharField(...)` | Stores draft/published status. |
| `max_length=2` | Status values are two characters: `DF` or `PB`. |
| `choices=Status` | Only allow values from the `Status` choices class. |
| `default=Status.DRAFT` | New posts start as draft unless changed. |

## Chapter 1 `Post` Managers and Meta Line by Line

Code:

```python
objects = models.Manager()
published = PublishedManager()

class Meta:
    ordering = ['-publish']
    indexes = [
        models.Index(fields=['-publish']),
    ]
```

Line-by-line meaning:

| Line | Meaning |
|---|---|
| `objects = models.Manager()` | Keeps Django's default manager for all posts. |
| `published = PublishedManager()` | Adds custom manager for published posts only. |
| `class Meta:` | Extra configuration for the model. |
| `ordering = ['-publish']` | Default order is newest publish date first. |
| `indexes = [` | Starts list of database indexes. |
| `models.Index(fields=['-publish'])` | Adds index for sorting/filtering by publish date descending. |

## Chapter 1 `__str__` and `get_absolute_url` Line by Line

Code:

```python
def __str__(self):
    return self.title

def get_absolute_url(self):
    return reverse(
        'blog:post_detail',
        args=[
            self.id,
        ],
    )
```

Line-by-line meaning:

| Line | Meaning |
|---|---|
| `def __str__(self):` | Defines how this object appears as text. |
| `return self.title` | Show the post title when object is printed. |
| `def get_absolute_url(self):` | Defines the official URL for one post. |
| `return reverse(...)` | Build URL from a URL pattern name. |
| `'blog:post_detail'` | Use the `post_detail` URL inside `blog` namespace. |
| `args=[self.id]` | Pass the post ID into the URL pattern. |

## Chapter 1 `post_list` View Line by Line

Code:

```python
def post_list(request):
    posts = Post.published.all()
    return render(
        request,
        'blog/post/list.html',
        {'posts': posts}
    )
```

Line-by-line meaning:

| Line | Meaning |
|---|---|
| `def post_list(request):` | Defines a view function. Django passes the request object. |
| `posts = Post.published.all()` | Get all published posts from the database. |
| `return render(...)` | Return an HTML response rendered from a template. |
| `request` | The original browser request. |
| `'blog/post/list.html'` | Template file to render. |
| `{'posts': posts}` | Context dictionary. Template can use `posts`. |

## Chapter 1 `post_detail` View Line by Line

Code:

```python
def post_detail(request, id):
    favourite_post = None
    post = get_object_or_404(
        Post,
        id=id,
        status=Post.Status.PUBLISHED
    )
    try:
        if request.user.is_authenticated:
            favourite_post = FavouritePost.objects.get(
                user=request.user, post=post
            )
    except FavouritePost.DoesNotExist:
        pass
    return render(
        request,
        'blog/post/detail.html',
        {
            'is_favourite': favourite_post is not None,
            'post': post
        }
    )
```

Line-by-line meaning:

| Line | Meaning |
|---|---|
| `def post_detail(request, id):` | View for one post. URL passes `id`. |
| `favourite_post = None` | Start with no favorite found. |
| `get_object_or_404(...)` | Find object or show 404 if missing. |
| `Post` | Search inside the `Post` model. |
| `id=id` | Database ID must match URL ID. |
| `status=Post.Status.PUBLISHED` | Only public published posts can be shown. |
| `try:` | Start block that may fail. |
| `if request.user.is_authenticated:` | Only check favorites for logged-in users. |
| `FavouritePost.objects.get(...)` | Try to find this user's favorite row for this post. |
| `except FavouritePost.DoesNotExist:` | If no favorite exists, handle it without crashing. |
| `pass` | Do nothing. `favourite_post` stays `None`. |
| `'is_favourite': favourite_post is not None` | Template receives True or False. |
| `'post': post` | Template receives the post object. |

Beginner note:

`try/except` prevents the page from crashing when the user has not favorited the post.

## Chapter 1 Favorite Add View Line by Line

Code:

```python
def add_favourite(request, id):
    post = get_object_or_404(Post, id=id)
    FavouritePost.objects.get_or_create(
        user=request.user, post=post
    )
    return HttpResponseRedirect(post.get_absolute_url())
```

Line-by-line meaning:

| Line | Meaning |
|---|---|
| `def add_favourite(request, id):` | View that favorites a post. URL passes post ID. |
| `post = get_object_or_404(Post, id=id)` | Get the post or show 404. |
| `FavouritePost.objects.get_or_create(...)` | Find existing favorite or create a new one. |
| `user=request.user` | Favorite belongs to the current user. |
| `post=post` | Favorite belongs to this post. |
| `HttpResponseRedirect(...)` | Tell browser to load another URL. |
| `post.get_absolute_url()` | Redirect back to the post detail page. |

Important:

This view changes data, so in a stricter version it should require login and normally use POST.
This repo uses it as a simple beginner link.

## Chapter 2 Pagination View Line by Line

Code:

```python
post_list = Post.published.all()
paginator = Paginator(post_list, 3)
page_number = request.GET.get('page', 1)
try:
    posts = paginator.page(page_number)
except PageNotAnInteger:
    posts = paginator.page(1)
except EmptyPage:
    posts = paginator.page(paginator.num_pages)
```

Line-by-line meaning:

| Line | Meaning |
|---|---|
| `post_list = Post.published.all()` | Start with all published posts. |
| `Paginator(post_list, 3)` | Split posts into pages of 3 posts each. |
| `request.GET.get('page', 1)` | Read `?page=...`; use 1 if missing. |
| `try:` | Try to load requested page. |
| `paginator.page(page_number)` | Get the selected page object. |
| `except PageNotAnInteger:` | If page is not a number, handle it. |
| `posts = paginator.page(1)` | Show first page. |
| `except EmptyPage:` | If page number is too high, handle it. |
| `posts = paginator.page(paginator.num_pages)` | Show last page. |

## Chapter 2 `EmailPostForm` Line by Line

Code:

```python
class EmailPostForm(forms.Form):
    name = forms.CharField(max_length=25)
    email = forms.EmailField()
    to = forms.EmailField()
    comments = forms.CharField(
        required=False,
        widget=forms.Textarea
    )
```

Line-by-line meaning:

| Line | Meaning |
|---|---|
| `class EmailPostForm(forms.Form):` | Creates a normal form not tied directly to a model. |
| `name = forms.CharField(max_length=25)` | User's name, max 25 characters. |
| `email = forms.EmailField()` | User's email. Django validates email format. |
| `to = forms.EmailField()` | Recipient email. |
| `comments = forms.CharField(...)` | Optional comment message. |
| `required=False` | User can leave comments empty. |
| `widget=forms.Textarea` | Render this field as a multiline text area. |

## Chapter 2 `CommentForm` Line by Line

Code:

```python
class CommentForm(forms.ModelForm):
    class Meta:
        model = Comment
        fields = ['name', 'email', 'body']
```

Line-by-line meaning:

| Line | Meaning |
|---|---|
| `class CommentForm(forms.ModelForm):` | Creates a form based on a model. |
| `class Meta:` | Configuration for this model form. |
| `model = Comment` | Use the `Comment` model. |
| `fields = [...]` | Only show these model fields in the form. |

Why `post` is not in fields:

The user should not choose the post. The view already knows which post is being commented on.

## Chapter 2 `post_share` View Line by Line

Code:

```python
def post_share(request, post_id):
    post = get_object_or_404(
        Post,
        id=post_id,
        status=Post.Status.PUBLISHED
    )
    sent = False

    if request.method == 'POST':
        form = EmailPostForm(request.POST)
        if form.is_valid():
            cd = form.cleaned_data
            post_url = request.build_absolute_uri(
                post.get_absolute_url()
            )
            subject = (
                f"{cd['name']} ({cd['email']}) "
                f"recommends you read {post.title}"
            )
            message = (
                f"Read {post.title} at {post_url}\n\n"
                f"{cd['name']}'s comments: {cd['comments']}"
            )
            send_mail(
                subject=subject,
                message=message,
                from_email=None,
                recipient_list=[cd['to']],
            )
            sent = True
    else:
        form = EmailPostForm()
```

Line-by-line meaning:

| Line | Meaning |
|---|---|
| `def post_share(request, post_id):` | View for sharing a post by email. |
| `post_id` | Comes from the URL. |
| `get_object_or_404(...)` | Get published post or show 404. |
| `sent = False` | Email has not been sent yet. |
| `if request.method == 'POST':` | User submitted the form. |
| `form = EmailPostForm(request.POST)` | Bind submitted data to the form. |
| `if form.is_valid():` | Validate form fields. |
| `cd = form.cleaned_data` | Store validated data in short variable. |
| `request.build_absolute_uri(...)` | Build full URL including domain. |
| `subject = (...)` | Create email subject text. |
| `message = (...)` | Create email body text. |
| `send_mail(...)` | Send the email. |
| `from_email=None` | Use default sender from settings. |
| `recipient_list=[cd['to']]` | Send to recipient from form. |
| `sent = True` | Tell template email was sent. |
| `else:` | Request was GET, not POST. |
| `form = EmailPostForm()` | Create empty form for first page load. |

## Chapter 2 `post_comment` View Line by Line

Code:

```python
@require_POST
def post_comment(request, post_id):
    post = get_object_or_404(
        Post,
        id=post_id,
        status=Post.Status.PUBLISHED
    )
    comment = None
    form = CommentForm(data=request.POST)
    if form.is_valid():
        comment = form.save(commit=False)
        comment.post = post
        comment.save()
    return render(
        request,
        'blog/post/comment.html',
        {
            'post': post,
            'form': form,
            'comment': comment
        },
    )
```

Line-by-line meaning:

| Line | Meaning |
|---|---|
| `@require_POST` | Only allow POST requests. |
| `def post_comment(request, post_id):` | View that creates a comment for one post. |
| `get_object_or_404(...)` | Get published post or show 404. |
| `comment = None` | No saved comment yet. |
| `CommentForm(data=request.POST)` | Bind submitted data to model form. |
| `if form.is_valid():` | Only save if data is valid. |
| `form.save(commit=False)` | Create comment object but do not save yet. |
| `comment.post = post` | Attach comment to the current post. |
| `comment.save()` | Save comment to database. |
| `return render(...)` | Show success or form errors in template. |

## Chapter 3 Tag Filtering Line by Line

Code:

```python
def post_list(request, tag_slug=None):
    post_list = Post.published.all()
    tag = None
    if tag_slug:
        tag = get_object_or_404(Tag, slug=tag_slug)
        post_list = post_list.filter(tags__in=[tag])
```

Line-by-line meaning:

| Line | Meaning |
|---|---|
| `def post_list(request, tag_slug=None):` | List view can optionally receive a tag slug. |
| `tag_slug=None` | Default is no tag filter. |
| `post_list = Post.published.all()` | Start with all published posts. |
| `tag = None` | No selected tag yet. |
| `if tag_slug:` | If URL provided a tag slug, filter by tag. |
| `get_object_or_404(Tag, slug=tag_slug)` | Find the tag or show 404. |
| `post_list.filter(tags__in=[tag])` | Keep posts that have this tag. |

## Chapter 3 Similar Posts Line by Line

Code:

```python
post_tags_ids = post.tags.values_list('id', flat=True)
similar_posts = Post.published.filter(
    tags__in=post_tags_ids
).exclude(id=post.id)
similar_posts = similar_posts.annotate(
    same_tags=Count('tags')
).order_by('-same_tags', '-publish')[:4]
```

Line-by-line meaning:

| Line | Meaning |
|---|---|
| `post.tags` | Access tags attached to current post. |
| `values_list('id', flat=True)` | Get only tag IDs as a flat list-like QuerySet. |
| `Post.published.filter(...)` | Find published posts matching condition. |
| `tags__in=post_tags_ids` | Posts that have any of these tag IDs. |
| `.exclude(id=post.id)` | Remove the current post from recommendations. |
| `.annotate(same_tags=Count('tags'))` | Add a calculated count named `same_tags`. |
| `.order_by('-same_tags', '-publish')` | Sort by most shared tags, then newest. |
| `[:4]` | Keep only first 4 results. |

## Chapter 3 Template Tags Line by Line

Code:

```python
register = template.Library()

@register.simple_tag
def total_posts():
    return Post.published.count()
```

Line-by-line meaning:

| Line | Meaning |
|---|---|
| `register = template.Library()` | Creates a registry for custom template tags/filters. |
| `@register.simple_tag` | Registers the next function as a simple template tag. |
| `def total_posts():` | Defines tag function. |
| `return Post.published.count()` | Return number of published posts. |

Use in template:

```django
{% total_posts %}
```

## Chapter 3 Inclusion Tag Line by Line

Code:

```python
@register.inclusion_tag('blog/post/latest_posts.html')
def show_latest_posts(count=5):
    latest_posts = Post.published.order_by('-publish')[:count]
    return {'latest_posts': latest_posts}
```

Line-by-line meaning:

| Line | Meaning |
|---|---|
| `@register.inclusion_tag(...)` | Register a tag that renders a small template. |
| `'blog/post/latest_posts.html'` | Template used by this tag. |
| `def show_latest_posts(count=5):` | Tag accepts optional count; default is 5. |
| `order_by('-publish')` | Newest posts first. |
| `[:count]` | Limit number of posts. |
| `return {'latest_posts': latest_posts}` | Send posts to the inclusion template. |

## Chapter 3 Markdown Filter Line by Line

Code:

```python
@register.filter(name='markdown')
def markdown_format(text):
    return mark_safe(markdown.markdown(text))
```

Line-by-line meaning:

| Line | Meaning |
|---|---|
| `@register.filter(name='markdown')` | Register this as a template filter named `markdown`. |
| `def markdown_format(text):` | Function receives the value before the pipe. |
| `markdown.markdown(text)` | Convert Markdown text to HTML. |
| `mark_safe(...)` | Tell Django not to escape the generated HTML. |

Use in template:

```django
{{ post.body|markdown }}
```

Security warning:

Only mark HTML safe when you trust or sanitize the content.

## Chapter 3 Search View Line by Line

Code:

```python
def post_search(request):
    form = SearchForm()
    query = None
    results = []

    if 'query' in request.GET:
        form = SearchForm(request.GET)
        if form.is_valid():
            query = form.cleaned_data['query']
            results = (
                Post.published.annotate(
                    similarity=TrigramSimilarity('title', query),
                )
                .filter(similarity__gt=0.1)
                .order_by('-similarity')
            )
```

Line-by-line meaning:

| Line | Meaning |
|---|---|
| `form = SearchForm()` | Create empty search form. |
| `query = None` | No search query yet. |
| `results = []` | No results yet. |
| `if 'query' in request.GET:` | Check if URL has `?query=...`. |
| `SearchForm(request.GET)` | Bind query string data to form. |
| `if form.is_valid():` | Validate search input. |
| `query = form.cleaned_data['query']` | Get safe query text. |
| `Post.published.annotate(...)` | Start with published posts and add calculated field. |
| `TrigramSimilarity('title', query)` | Compare title to query text. |
| `.filter(similarity__gt=0.1)` | Keep results above similarity threshold. |
| `.order_by('-similarity')` | Best matches first. |

## Chapter 3 Feed Line by Line

Code:

```python
class LatestPostsFeed(Feed):
    title = 'My blog'
    link = reverse_lazy('blog:post_list')
    description = 'New posts of my blog.'

    def items(self):
        return Post.published.all()[:5]
```

Line-by-line meaning:

| Line | Meaning |
|---|---|
| `class LatestPostsFeed(Feed):` | Creates an RSS feed class. |
| `title = 'My blog'` | Feed title. |
| `link = reverse_lazy(...)` | Link to main blog list. Lazy means reverse later when URLs are ready. |
| `description = ...` | Feed description. |
| `def items(self):` | Defines which objects appear in feed. |
| `Post.published.all()[:5]` | Latest 5 published posts. |

## Chapter 4 Profile Model Line by Line

Code:

```python
class Profile(models.Model):
    user = models.OneToOneField(
        settings.AUTH_USER_MODEL,
        on_delete=models.CASCADE
    )
    date_of_birth = models.DateField(blank=True, null=True)
    photo = models.ImageField(
        upload_to='users/%Y/%m/%d/',
        blank=True
    )
```

Line-by-line meaning:

| Line | Meaning |
|---|---|
| `class Profile(models.Model):` | Creates profile table. |
| `user = models.OneToOneField(...)` | One profile belongs to one user. |
| `settings.AUTH_USER_MODEL` | Use active user model. |
| `on_delete=models.CASCADE` | Delete profile if user is deleted. |
| `date_of_birth = models.DateField(...)` | Optional birth date. |
| `blank=True` | Form can leave it empty. |
| `null=True` | Database can store NULL. |
| `photo = models.ImageField(...)` | Uploaded profile image. |
| `upload_to='users/%Y/%m/%d/'` | Save uploads in date-based folders. |

## Chapter 4 Registration Form Line by Line

Code:

```python
class UserRegistrationForm(forms.ModelForm):
    password = forms.CharField(
        label='Password',
        widget=forms.PasswordInput
    )
    password2 = forms.CharField(
        label='Repeat password',
        widget=forms.PasswordInput
    )

    class Meta:
        model = get_user_model()
        fields = ['username', 'first_name', 'email']
```

Line-by-line meaning:

| Line | Meaning |
|---|---|
| `class UserRegistrationForm(forms.ModelForm):` | Model form for creating a user. |
| `password = forms.CharField(...)` | Extra password field. |
| `label='Password'` | Label shown in HTML form. |
| `widget=forms.PasswordInput` | Hide typed password characters. |
| `password2 = ...` | Repeat password field for confirmation. |
| `class Meta:` | Model form configuration. |
| `model = get_user_model()` | Use active user model. |
| `fields = [...]` | Show username, first name, and email from user model. |

Important:

The password fields are not listed in `fields` because they need special hashing with
`set_password()`.

## Chapter 4 Password Match Validation Line by Line

Code:

```python
def clean_password2(self):
    cd = self.cleaned_data
    if cd['password'] != cd['password2']:
        raise forms.ValidationError("Passwords don't match.")
    return cd['password2']
```

Line-by-line meaning:

| Line | Meaning |
|---|---|
| `def clean_password2(self):` | Custom validation method for `password2`. |
| `cd = self.cleaned_data` | Short name for validated form data. |
| `if cd['password'] != cd['password2']:` | Compare both password fields. |
| `raise forms.ValidationError(...)` | Stop validation and show error. |
| `return cd['password2']` | Return valid cleaned value. |

## Chapter 4 Register View Line by Line

Code:

```python
def register(request):
    if request.method == 'POST':
        user_form = UserRegistrationForm(request.POST)
        if user_form.is_valid():
            new_user = user_form.save(commit=False)
            new_user.set_password(user_form.cleaned_data['password'])
            new_user.save()
            Profile.objects.create(user=new_user)
            return render(
                request,
                'account/register_done.html',
                {'new_user': new_user},
            )
    else:
        user_form = UserRegistrationForm()
    return render(
        request,
        'account/register.html',
        {'user_form': user_form}
    )
```

Line-by-line meaning:

| Line | Meaning |
|---|---|
| `def register(request):` | View for creating a new account. |
| `if request.method == 'POST':` | User submitted registration form. |
| `UserRegistrationForm(request.POST)` | Bind submitted form data. |
| `if user_form.is_valid():` | Validate input. |
| `save(commit=False)` | Create user object but do not save yet. |
| `set_password(...)` | Hash the password correctly. |
| `new_user.save()` | Save user to database. |
| `Profile.objects.create(user=new_user)` | Create matching profile. |
| `return render(...register_done...)` | Show success page. |
| `else:` | Request is GET. |
| `UserRegistrationForm()` | Show empty form. |
| `return render(...register.html...)` | Render registration form page. |

## Chapter 4 Edit View Line by Line

Code:

```python
@login_required
def edit(request):
    if request.method == 'POST':
        user_form = UserEditForm(
            instance=request.user,
            data=request.POST
        )
        profile_form = ProfileEditForm(
            instance=request.user.profile,
            data=request.POST,
            files=request.FILES,
        )
        if user_form.is_valid() and profile_form.is_valid():
            user_form.save()
            profile_form.save()
    else:
        user_form = UserEditForm(instance=request.user)
        profile_form = ProfileEditForm(instance=request.user.profile)
```

Line-by-line meaning:

| Line | Meaning |
|---|---|
| `@login_required` | Only logged-in users can edit profile. |
| `def edit(request):` | View for editing account/profile. |
| `if request.method == 'POST':` | User submitted form. |
| `instance=request.user` | Edit current user, do not create new user. |
| `data=request.POST` | Use submitted text fields. |
| `instance=request.user.profile` | Edit current user's profile. |
| `files=request.FILES` | Include uploaded files, such as photo. |
| `if user_form.is_valid() and profile_form.is_valid():` | Both forms must be valid. |
| `user_form.save()` | Save user fields. |
| `profile_form.save()` | Save profile fields. |
| `else:` | Request is GET. |
| `UserEditForm(instance=request.user)` | Show form filled with current user data. |
| `ProfileEditForm(instance=request.user.profile)` | Show form filled with profile data. |

## Chapter 5 Messages Line by Line

Code:

```python
if user_form.is_valid() and profile_form.is_valid():
    user_form.save()
    profile_form.save()
    messages.success(
        request,
        'Profile updated successfully'
    )
else:
    messages.error(request, 'Error updating your profile')
```

Line-by-line meaning:

| Line | Meaning |
|---|---|
| `if user_form.is_valid() and profile_form.is_valid():` | Continue only if both forms are valid. |
| `user_form.save()` | Save user changes. |
| `profile_form.save()` | Save profile changes. |
| `messages.success(...)` | Store a success message for next rendered page. |
| `request` | Connect the message to this request/session. |
| `'Profile updated successfully'` | Text shown to user. |
| `else:` | One or both forms failed validation. |
| `messages.error(...)` | Store an error message. |

## Chapter 5 Email Auth Backend Line by Line

Code:

```python
class EmailAuthBackend:
    def authenticate(self, request, username=None, password=None):
        try:
            user = User.objects.get(email=username)
            if user.check_password(password):
                return user
            return None
        except (User.DoesNotExist, User.MultipleObjectsReturned):
            return None
```

Line-by-line meaning:

| Line | Meaning |
|---|---|
| `class EmailAuthBackend:` | Creates custom authentication backend. |
| `def authenticate(...)` | Method Django calls during login. |
| `username=None` | Django passes login username field here. This backend treats it as email. |
| `password=None` | Password from login form. |
| `try:` | Start code that may fail. |
| `User.objects.get(email=username)` | Find user with this email. |
| `user.check_password(password)` | Compare submitted password with hashed password. |
| `return user` | Authentication succeeded. |
| `return None` | Authentication failed. |
| `except (...)` | If no user or multiple users found, fail safely. |

## Chapter 5 Social Profile Helper Line by Line

Code:

```python
def create_profile(backend, user, *args, **kwargs):
    Profile.objects.get_or_create(user=user)
```

Line-by-line meaning:

| Line | Meaning |
|---|---|
| `def create_profile(...)` | Function used in the social auth pipeline. |
| `backend` | Social auth backend, such as Google. |
| `user` | User created or found by social auth. |
| `*args, **kwargs` | Accept extra values passed by pipeline. |
| `Profile.objects.get_or_create(user=user)` | Make sure this user has a profile. |

## Chapter 6 `Image` Model Line by Line

Code:

```python
class Image(models.Model):
    user = models.ForeignKey(
        settings.AUTH_USER_MODEL,
        related_name='images_created',
        on_delete=models.CASCADE,
    )
    title = models.CharField(max_length=200)
    slug = models.SlugField(max_length=200, blank=True)
    url = models.URLField(max_length=2000)
    image = models.ImageField(upload_to='images/%Y/%m/%d/')
    description = models.TextField(blank=True)
    created = models.DateTimeField(auto_now_add=True)
    users_like = models.ManyToManyField(
        settings.AUTH_USER_MODEL,
        related_name='images_liked',
        blank=True,
    )
```

Line-by-line meaning:

| Line | Meaning |
|---|---|
| `class Image(models.Model):` | Creates table for bookmarked images. |
| `user = models.ForeignKey(...)` | Image has one creator. User can create many images. |
| `settings.AUTH_USER_MODEL` | Use active user model. |
| `related_name='images_created'` | Allows `user.images_created.all()`. |
| `on_delete=models.CASCADE` | Delete user's images if user is deleted. |
| `title = models.CharField(max_length=200)` | Short image title. |
| `slug = models.SlugField(..., blank=True)` | URL slug; form can leave it empty. |
| `url = models.URLField(max_length=2000)` | Original image URL. |
| `image = models.ImageField(...)` | Actual downloaded image file. |
| `description = models.TextField(blank=True)` | Optional long description. |
| `created = models.DateTimeField(auto_now_add=True)` | Creation timestamp. |
| `users_like = models.ManyToManyField(...)` | Users who liked this image. |
| `related_name='images_liked'` | Allows `user.images_liked.all()`. |
| `blank=True` | Image can have zero likes. |

## Chapter 6 `Image.save()` Line by Line

Code:

```python
def save(self, *args, **kwargs):
    if not self.slug:
        self.slug = slugify(self.title)
    super().save(*args, **kwargs)
```

Line-by-line meaning:

| Line | Meaning |
|---|---|
| `def save(self, *args, **kwargs):` | Override Django's model save method. |
| `*args, **kwargs` | Accept normal save options and pass them forward. |
| `if not self.slug:` | If slug is empty. |
| `self.slug = slugify(self.title)` | Create slug from title. |
| `super().save(*args, **kwargs)` | Call Django's normal save behavior. |

## Chapter 6 `ImageCreateForm.clean_url()` Line by Line

Code:

```python
def clean_url(self):
    url = self.cleaned_data['url']
    valid_extensions = ['jpg', 'jpeg', 'png']
    extension = url.rsplit('.', 1)[1].lower()
    if extension not in valid_extensions:
        raise forms.ValidationError(
            'The given URL does not match valid image extensions.'
        )
    return url
```

Line-by-line meaning:

| Line | Meaning |
|---|---|
| `def clean_url(self):` | Custom validation for `url` field. |
| `url = self.cleaned_data['url']` | Get validated URL value. |
| `valid_extensions = [...]` | Allowed image file extensions. |
| `url.rsplit('.', 1)` | Split URL once from the right at the last dot. |
| `[1]` | Take the part after the final dot. |
| `.lower()` | Convert extension to lowercase. |
| `if extension not in valid_extensions:` | Check if extension is not allowed. |
| `raise forms.ValidationError(...)` | Show form error and stop valid save. |
| `return url` | Return cleaned value if valid. |

## Chapter 6 `ImageCreateForm.save()` Line by Line

Code:

```python
def save(self, force_insert=False, force_update=False, commit=True):
    image = super().save(commit=False)
    image_url = self.cleaned_data['url']
    name = slugify(image.title)
    extension = image_url.rsplit('.', 1)[1].lower()
    image_name = f'{name}.{extension}'
    response = requests.get(image_url)
    image.image.save(
        image_name,
        ContentFile(response.content),
        save=False
    )
    if commit:
        image.save()
    return image
```

Line-by-line meaning:

| Line | Meaning |
|---|---|
| `def save(..., commit=True):` | Override form save method. |
| `force_insert=False` | Optional Django save argument. Usually ignore as beginner. |
| `force_update=False` | Optional Django save argument. Usually ignore as beginner. |
| `commit=True` | Whether to write object to database. |
| `super().save(commit=False)` | Create `Image` object but do not save yet. |
| `image_url = self.cleaned_data['url']` | Get validated image URL. |
| `name = slugify(image.title)` | Make safe filename base from title. |
| `extension = ...` | Extract file extension. |
| `image_name = f'{name}.{extension}'` | Build final filename. |
| `requests.get(image_url)` | Download image from remote URL. |
| `ContentFile(response.content)` | Wrap downloaded bytes as a Django file. |
| `image.image.save(..., save=False)` | Attach file to image field but do not save model yet. |
| `if commit:` | If caller wants database save. |
| `image.save()` | Save image model to database. |
| `return image` | Return saved or unsaved image object. |

## Chapter 6 Image Create View Line by Line

Code:

```python
@login_required
def image_create(request):
    if request.method == 'POST':
        form = ImageCreateForm(data=request.POST)
        if form.is_valid():
            new_image = form.save(commit=False)
            new_image.user = request.user
            new_image.save()
            messages.success(request, 'Image added successfully')
            return redirect(new_image.get_absolute_url())
    else:
        form = ImageCreateForm(data=request.GET)
    return render(
        request,
        'images/image/create.html',
        {'section': 'images', 'form': form},
    )
```

Line-by-line meaning:

| Line | Meaning |
|---|---|
| `@login_required` | Only logged-in users can create image bookmarks. |
| `def image_create(request):` | View for image creation. |
| `if request.method == 'POST':` | User submitted create form. |
| `ImageCreateForm(data=request.POST)` | Bind submitted form data. |
| `if form.is_valid():` | Validate title, URL, description. |
| `form.save(commit=False)` | Create image object but do not save yet. |
| `new_image.user = request.user` | Set owner to current user. |
| `new_image.save()` | Save to database. |
| `messages.success(...)` | Show success message. |
| `redirect(new_image.get_absolute_url())` | Redirect to new image detail page. |
| `else:` | Request is GET. |
| `ImageCreateForm(data=request.GET)` | Pre-fill form from bookmarklet query string. |
| `render(...)` | Show create page. |
| `{'section': 'images', 'form': form}` | Template gets active menu section and form. |

## Chapter 6 Like View Line by Line

Code:

```python
@login_required
@require_POST
def image_like(request):
    image_id = request.POST.get('id')
    action = request.POST.get('action')
    if image_id and action:
        try:
            image = Image.objects.get(id=image_id)
            if action == 'like':
                image.users_like.add(request.user)
            else:
                image.users_like.remove(request.user)
            return JsonResponse({'status': 'ok'})
        except Image.DoesNotExist:
            pass
    return JsonResponse({'status': 'error'})
```

Line-by-line meaning:

| Line | Meaning |
|---|---|
| `@login_required` | User must be logged in to like. |
| `@require_POST` | Only POST requests allowed. |
| `image_id = request.POST.get('id')` | Read image ID from submitted data. |
| `action = request.POST.get('action')` | Read action, usually `like` or `unlike`. |
| `if image_id and action:` | Continue only if both values exist. |
| `try:` | Try to find and update image. |
| `Image.objects.get(id=image_id)` | Get one image by ID. |
| `if action == 'like':` | If user wants to like. |
| `image.users_like.add(request.user)` | Add current user to many-to-many likes. |
| `else:` | Otherwise treat as unlike. |
| `image.users_like.remove(request.user)` | Remove current user from likes. |
| `JsonResponse({'status': 'ok'})` | Send success JSON. |
| `except Image.DoesNotExist:` | If image ID is invalid. |
| `pass` | Do nothing, then return error. |
| `JsonResponse({'status': 'error'})` | Send error JSON. |

## Chapter 6 JavaScript Like Code Line by Line

Code:

```javascript
const url = '{% url "images:like" %}';
var options = {
  method: 'POST',
  headers: {'X-CSRFToken': csrftoken},
  mode: 'same-origin'
}
```

Line-by-line meaning:

| Line | Meaning |
|---|---|
| `const url = ...` | Build the like endpoint URL using Django template tag. |
| `var options = {` | Start fetch options object. |
| `method: 'POST'` | Send POST request. |
| `headers: {'X-CSRFToken': csrftoken}` | Include CSRF token for Django protection. |
| `mode: 'same-origin'` | Only allow request to same site origin. |

Next block:

```javascript
document.querySelector('a.like')
        .addEventListener('click', function(e){
  e.preventDefault();
  var likeButton = this;
```

Line-by-line meaning:

| Line | Meaning |
|---|---|
| `document.querySelector('a.like')` | Find the like link. |
| `.addEventListener('click', ...)` | Run function when clicked. |
| `function(e)` | Event handler receives click event. |
| `e.preventDefault()` | Stop link from jumping to `#`. |
| `var likeButton = this` | Store clicked link in a variable. |

Next block:

```javascript
var formData = new FormData();
formData.append('id', likeButton.dataset.id);
formData.append('action', likeButton.dataset.action);
options['body'] = formData;
```

Line-by-line meaning:

| Line | Meaning |
|---|---|
| `new FormData()` | Create form-like data for POST request. |
| `.append('id', ...)` | Add image ID. |
| `likeButton.dataset.id` | Read `data-id` from HTML. |
| `.append('action', ...)` | Add like/unlike action. |
| `likeButton.dataset.action` | Read `data-action` from HTML. |
| `options['body'] = formData` | Attach submitted data to fetch options. |

Next block:

```javascript
fetch(url, options)
.then(response => response.json())
.then(data => {
  if (data['status'] === 'ok')
  {
    var previousAction = likeButton.dataset.action;
    var action = previousAction === 'like' ? 'unlike' : 'like';
    likeButton.dataset.action = action;
    likeButton.innerHTML = action;
  }
})
```

Line-by-line meaning:

| Line | Meaning |
|---|---|
| `fetch(url, options)` | Send AJAX request. |
| `.then(response => response.json())` | Convert JSON response to JavaScript object. |
| `.then(data => { ... })` | Use the JSON data. |
| `if (data['status'] === 'ok')` | Continue only if server says success. |
| `previousAction = ...` | Remember old action. |
| `previousAction === 'like' ? 'unlike' : 'like'` | If old action was like, new action is unlike; otherwise like. |
| `likeButton.dataset.action = action` | Store new action on button. |
| `likeButton.innerHTML = action` | Change button text. |

## Template Line-by-Line: Base Template Pattern

Code:

```django
{% load static %}
<!DOCTYPE html>
<html>
<head>
  <title>{% block title %}{% endblock %}</title>
  <link href="{% static "css/base.css" %}" rel="stylesheet">
</head>
```

Line-by-line meaning:

| Line | Meaning |
|---|---|
| `{% load static %}` | Load Django static file template tag. |
| `<!DOCTYPE html>` | Tell browser this is HTML5. |
| `<html>` | Start HTML document. |
| `<head>` | Start metadata section. |
| `{% block title %}` | Child templates can replace this title area. |
| `{% static "css/base.css" %}` | Build URL for static CSS file. |
| `<link ...>` | Load CSS into page. |

## Template Line-by-Line: Form Pattern

Code:

```django
<form method="post">
  {{ form.as_p }}
  {% csrf_token %}
  <p><input type="submit" value="Save"></p>
</form>
```

Line-by-line meaning:

| Line | Meaning |
|---|---|
| `<form method="post">` | Browser will submit form using POST. |
| `{{ form.as_p }}` | Render all form fields as paragraphs. |
| `{% csrf_token %}` | Add CSRF protection token. |
| `<input type="submit">` | Button that submits the form. |
| `</form>` | End form. |

## Template Line-by-Line: Loop Pattern

Code:

```django
{% for post in posts %}
  <h2>{{ post.title }}</h2>
{% empty %}
  <p>No posts yet.</p>
{% endfor %}
```

Line-by-line meaning:

| Line | Meaning |
|---|---|
| `{% for post in posts %}` | Loop over every post in `posts`. |
| `{{ post.title }}` | Print current post title. |
| `{% empty %}` | If posts is empty, show this section. |
| `<p>No posts yet.</p>` | Message for empty list. |
| `{% endfor %}` | End loop. |

## Admin Class Line by Line

Code from the blog admin:

```python
@admin.register(Post)
class PostAdmin(admin.ModelAdmin):
    list_display = ['title', 'slug', 'author', 'publish', 'status']
    list_filter = ['status', 'created', 'publish', 'author']
    search_fields = ['title', 'body']
    prepopulated_fields = {'slug': ('title',)}
    raw_id_fields = ['author']
    date_hierarchy = 'publish'
    ordering = ['status', 'publish']
    show_facets = admin.ShowFacets.ALWAYS
```

Line-by-line meaning:

| Line | Meaning |
|---|---|
| `@admin.register(Post)` | Register `Post` with Django admin. |
| `class PostAdmin(admin.ModelAdmin):` | Customize how `Post` appears in admin. |
| `list_display = [...]` | Columns visible in the admin list page. |
| `list_filter = [...]` | Filter sidebar options. |
| `search_fields = [...]` | Fields searched by admin search box. |
| `prepopulated_fields = {'slug': ('title',)}` | Auto-fill slug from title while typing. |
| `raw_id_fields = ['author']` | Use ID lookup widget for author instead of huge dropdown. |
| `date_hierarchy = 'publish'` | Add date drill-down navigation by publish date. |
| `ordering = [...]` | Default ordering in admin list. |
| `show_facets = admin.ShowFacets.ALWAYS` | Always show admin filter counts/facets. |

When you add a model, ask:

```text
Do I need to manage this model in admin?
If yes, register it in admin.py.
```

## Class-Based View `ListView` Line by Line

Code:

```python
class PostListView(ListView):
    queryset = Post.published.all()
    context_object_name = 'posts'
    paginate_by = 3
    template_name = 'blog/post/list.html'
```

Line-by-line meaning:

| Line | Meaning |
|---|---|
| `class PostListView(ListView):` | Create a list page using Django's generic `ListView`. |
| `queryset = Post.published.all()` | Data source for the list. |
| `context_object_name = 'posts'` | Template variable name will be `posts`. |
| `paginate_by = 3` | Show 3 posts per page. |
| `template_name = 'blog/post/list.html'` | Template used to render the page. |

In `urls.py`, this class is used like this:

```python
path('', views.PostListView.as_view(), name='post_list')
```

Line-by-line meaning:

| Part | Meaning |
|---|---|
| `views.PostListView` | The class-based view class. |
| `.as_view()` | Converts the class into a callable view function. |
| `name='post_list'` | URL name for reversing in templates and Python. |

## Sitemap Class Line by Line

Code:

```python
class PostSitemap(Sitemap):
    changefreq = 'weekly'
    priority = 0.9

    def items(self):
        return Post.published.all()

    def lastmod(self, obj):
        return obj.updated
```

Line-by-line meaning:

| Line | Meaning |
|---|---|
| `class PostSitemap(Sitemap):` | Create sitemap entries for posts. |
| `changefreq = 'weekly'` | Hint to search engines: content changes weekly. |
| `priority = 0.9` | Hint to search engines: these pages are important. |
| `def items(self):` | Return objects that should appear in the sitemap. |
| `Post.published.all()` | Include published posts only. |
| `def lastmod(self, obj):` | Tell sitemap each object's last modification time. |
| `return obj.updated` | Use post's updated timestamp. |

Project URL line:

```python
path(
    'sitemap.xml',
    sitemap,
    {'sitemaps': sitemaps},
    name='django.contrib.sitemaps.views.sitemap',
)
```

Line-by-line meaning:

| Line | Meaning |
|---|---|
| `'sitemap.xml'` | URL path for sitemap. |
| `sitemap` | Built-in Django sitemap view function. |
| `{'sitemaps': sitemaps}` | Extra argument passed to the sitemap view. |
| `name=...` | URL name. |

## RSS Feed Methods Line by Line

Code:

```python
def item_title(self, item):
    return item.title

def item_description(self, item):
    return truncatewords_html(markdown.markdown(item.body), 30)

def item_pubdate(self, item):
    return item.publish
```

Line-by-line meaning:

| Line | Meaning |
|---|---|
| `item_title(self, item)` | Defines title for one RSS item. |
| `return item.title` | Use post title. |
| `item_description(self, item)` | Defines description/body for one RSS item. |
| `markdown.markdown(item.body)` | Convert Markdown body to HTML. |
| `truncatewords_html(..., 30)` | Keep first 30 words while respecting HTML. |
| `item_pubdate(self, item)` | Defines publication date for one RSS item. |
| `return item.publish` | Use post publish date. |

## Auth URL Include Line by Line

Code:

```python
path('', include('django.contrib.auth.urls')),
```

Line-by-line meaning:

| Part | Meaning |
|---|---|
| `path('', ...)` | Include these URLs at the account app root. |
| `include(...)` | Load URL patterns from another module. |
| `'django.contrib.auth.urls'` | Built-in Django authentication URLs. |

This adds URL names such as:

| URL name | Purpose |
|---|---|
| `login` | Login page |
| `logout` | Logout action |
| `password_change` | Change password form |
| `password_change_done` | Password change success |
| `password_reset` | Password reset request |
| `password_reset_done` | Password reset email sent |
| `password_reset_confirm` | Reset password confirmation link |
| `password_reset_complete` | Password reset complete |

Because these URL names exist, templates can write:

```django
{% url "password_reset" %}
{% url "password_change" %}
{% url "logout" %}
```

## Infinite Scroll JavaScript Line by Line

Code from `images/image/list.html`:

```javascript
var page = 1;
var emptyPage = false;
var blockRequest = false;
```

Line-by-line meaning:

| Line | Meaning |
|---|---|
| `var page = 1;` | Current loaded page is page 1. |
| `var emptyPage = false;` | We have not reached the end yet. |
| `var blockRequest = false;` | No request is currently running. |

Next block:

```javascript
window.addEventListener('scroll', function(e) {
  var margin = document.body.clientHeight - window.innerHeight - 200;
  if(window.pageYOffset > margin && !emptyPage && !blockRequest) {
    blockRequest = true;
    page += 1;
```

Line-by-line meaning:

| Line | Meaning |
|---|---|
| `window.addEventListener('scroll', ...)` | Run code whenever user scrolls. |
| `function(e)` | Function receives the scroll event. |
| `document.body.clientHeight` | Full page content height. |
| `window.innerHeight` | Visible browser window height. |
| `- 200` | Start loading before the exact bottom. |
| `window.pageYOffset > margin` | User is near bottom. |
| `!emptyPage` | Continue only if there are probably more images. |
| `!blockRequest` | Continue only if no request is already running. |
| `blockRequest = true` | Block duplicate requests. |
| `page += 1` | Move to next page number. |

Next block:

```javascript
fetch('?images_only=1&page=' + page)
.then(response => response.text())
.then(html => {
  if (html === '') {
    emptyPage = true;
  }
  else {
    var imageList = document.getElementById('image-list');
    imageList.insertAdjacentHTML('beforeEnd', html);
    blockRequest = false;
  }
})
```

Line-by-line meaning:

| Line | Meaning |
|---|---|
| `fetch(...)` | Send GET request without reloading page. |
| `?images_only=1&page=` | Ask Django for only the image-card HTML for this page. |
| `.then(response => response.text())` | Convert response to text/HTML. |
| `.then(html => { ... })` | Use returned HTML. |
| `if (html === '')` | Empty response means no more images. |
| `emptyPage = true` | Stop future loading. |
| `else` | There are more images. |
| `document.getElementById('image-list')` | Find container on page. |
| `insertAdjacentHTML('beforeEnd', html)` | Add new image HTML to end of container. |
| `blockRequest = false` | Allow another request later. |

Final block:

```javascript
const scrollEvent = new Event('scroll');
window.dispatchEvent(scrollEvent);
```

Line-by-line meaning:

| Line | Meaning |
|---|---|
| `new Event('scroll')` | Create a fake scroll event. |
| `window.dispatchEvent(scrollEvent)` | Trigger scroll logic once immediately. |

Why trigger immediately?

If the first page does not fill the screen, this can automatically load more images.

## Common Concept: CRUD

CRUD means:

```text
C = Create
R = Read
U = Update
D = Delete
```

In these chapters:

| CRUD action | Example |
|---|---|
| Create | Register user, create comment, create image bookmark |
| Read | List posts, view post detail, search posts |
| Update | Edit profile |
| Delete | Not heavily covered in chapters 1-6, but admin can delete objects |

Most web apps are built from CRUD plus authentication and permissions.

## Common Concept: Form Lifecycle

Most Django form pages follow this pattern:

```text
GET request -> show empty or pre-filled form
POST request -> bind submitted data
is_valid() -> validate
save or perform action
redirect or render result
```

Code shape:

```python
if request.method == 'POST':
    form = SomeForm(request.POST)
    if form.is_valid():
        form.save()
        return redirect(...)
else:
    form = SomeForm()
return render(request, 'template.html', {'form': form})
```

Why redirect after successful POST?

It prevents duplicate submissions if the user refreshes the browser.

This is called the POST/Redirect/GET pattern.

## Common Concept: Model and Migration Lifecycle

When you change a model:

```text
Edit models.py
Run makemigrations
Review migration file if needed
Run migrate
Update admin/forms/templates/views if needed
Test feature
```

Example:

```bash
python manage.py makemigrations
python manage.py migrate
```

Beginner mistake:

Editing `models.py` alone does not update the database. Migrations update the database.

## Common Concept: Template Inheritance

Base template:

```django
<title>{% block title %}{% endblock %}</title>
<div id="content">
  {% block content %}
  {% endblock %}
</div>
```

Child template:

```django
{% extends "base.html" %}

{% block title %}Dashboard{% endblock %}

{% block content %}
  <h1>Dashboard</h1>
{% endblock %}
```

Meaning:

- Base template defines the layout.
- Child template fills blocks.
- This avoids repeating full HTML structure on every page.

## Common Concept: URL Namespaces

In `blog/urls.py`:

```python
app_name = 'blog'
```

Then URL names are used like:

```django
{% url "blog:post_detail" post.publish.year post.publish.month post.publish.day post.slug %}
```

Meaning:

- `blog` is namespace.
- `post_detail` is URL name.

Why namespaces matter:

Two apps can both have a URL named `detail`. Namespace keeps them separate:

```text
blog:detail
images:detail
```

## Common Concept: Static vs Media

Static:

```text
Files provided by your project.
Examples: CSS, JavaScript, site images.
```

Media:

```text
Files uploaded by users.
Examples: profile photos, bookmarked images.
```

Static settings:

```python
STATIC_URL = 'static/'
```

Media settings:

```python
MEDIA_URL = 'media/'
MEDIA_ROOT = BASE_DIR / 'media'
```

Template static example:

```django
{% load static %}
<link href="{% static "css/base.css" %}" rel="stylesheet">
```

Uploaded image example:

```django
<img src="{{ image.image.url }}">
```

## Common Concept: Authentication Flow

Login flow:

```text
User submits login form
Django authenticates credentials
Django stores user ID in session
Templates can use request.user
Protected views allow access
```

Logout flow:

```text
User submits logout POST form
Django clears session auth data
User becomes anonymous
Protected views redirect to login
```

Protected view:

```python
@login_required
def dashboard(request):
    ...
```

Template check:

```django
{% if request.user.is_authenticated %}
  Hello {{ request.user.username }}
{% endif %}
```

## Common Concept: Permissions and Ownership

Chapters 1-6 mostly use login checks, not detailed permissions.

Still, ownership appears in code:

```python
new_image.user = request.user
```

Meaning:

- The created image belongs to the logged-in user.

Relationship examples:

```python
request.user.images_created.all()
request.user.images_liked.all()
request.user.profile
```

These work because of:

- `related_name='images_created'`
- `related_name='images_liked'`
- `OneToOneField` from profile to user

## Common Concept: Safe Configuration

Do not hardcode secrets in `settings.py`.

Use:

```python
from decouple import config
```

Then:

```python
EMAIL_HOST_USER = config('EMAIL_HOST_USER')
```

Safe placeholder `.env`:

```env
EMAIL_HOST_USER=your_email@example.com
```

Never put real passwords or OAuth secrets in a public repository.

## Common Concept: Debugging by File Type

When something breaks, locate the problem by symptom.

| Symptom | Check first |
|---|---|
| URL gives 404 | `urls.py` |
| Page loads but data missing | `views.py` context and template variable names |
| Database column missing | migrations |
| Form always invalid | `forms.py`, template fields, POST data |
| Login not remembered | sessions, auth middleware, cookies |
| Static CSS missing | `{% load static %}`, static path, `STATIC_URL` |
| Uploaded image missing | `MEDIA_URL`, `MEDIA_ROOT`, dev media URL route |
| AJAX POST fails 403 | CSRF token/header |
| Admin missing model | `admin.py` registration |
| Package import fails | virtualenv and `requirements.txt` |

# Final Reference Sections

## How to Update Django Code Safely

Use this checklist before changing code:

1. Identify the feature.
2. Find the app that owns the feature.
3. If data structure changes, update `models.py`.
4. If `models.py` changed database fields, run migrations.
5. If user input is involved, update or create a form.
6. If a page is involved, update view, URL, and template.
7. If admin should manage it, update `admin.py`.
8. If a package is needed, update `requirements.txt`.
9. If a setting or secret is needed, update `settings.py` and `.env`.
10. Test in browser and Django shell if needed.

## When to Run Migrations

Run migrations after changing model structure.

Run:

```bash
python manage.py makemigrations
python manage.py migrate
```

Run migrations when you:

- Add a model.
- Delete a model.
- Add a model field.
- Delete a model field.
- Change a field type.
- Change `max_length`.
- Add `unique=True`.
- Add `null=True` or remove it.
- Add an index.
- Add many-to-many field.

Usually no migration needed when you:

- Change a view.
- Change a template.
- Change a URL pattern.
- Change a form method only.
- Change a template tag.
- Change a Python method that does not change database fields.

## When to Update `settings.py`

Update settings when you:

- Add an app to `INSTALLED_APPS`.
- Add middleware.
- Add context processor.
- Configure database.
- Configure email.
- Configure media/static files.
- Configure authentication backends.
- Configure third-party package settings.

Example:

```python
INSTALLED_APPS = [
    ...
    'blog.apps.BlogConfig',
]
```

## When to Update `urls.py`

Update app `urls.py` when adding a page inside an app.

Example:

```python
path('search/', views.post_search, name='post_search')
```

Update project `urls.py` when:

- Including a new app's URLs.
- Adding admin route.
- Adding global routes like sitemap.
- Serving media in development.

Example:

```python
path('images/', include('images.urls', namespace='images'))
```

## When to Update `forms.py`

Update forms when:

- Users submit data.
- You need validation.
- You want to control displayed form fields.
- You want custom cleaning logic.

Use `forms.Form` for general input.

Use `forms.ModelForm` for model-backed input.

## When to Update Templates

Update templates when:

- You need to show new data.
- You need a new form.
- You need buttons or links.
- You need to load custom template tags.
- You need to show messages.
- You need to display media/images.

Common template tools:

```django
{{ variable }}
{% if condition %}
{% for item in items %}
{% url 'namespace:name' arg %}
{% load blog_tags %}
```

## When to Update `admin.py`

Update admin when:

- You create a model and want admin access.
- You want better list columns.
- You want filters.
- You want search.
- You want auto slug filling.

Example:

```python
@admin.register(Post)
class PostAdmin(admin.ModelAdmin):
    list_display = ['title', 'author', 'publish', 'status']
```

## When to Update `requirements.txt`

Update requirements when you add a Python package.

Examples from these chapters:

| Feature | Package |
|---|---|
| Tags | `django-taggit` |
| Markdown rendering | `Markdown` |
| PostgreSQL | `psycopg` |
| Environment config | `python-decouple` |
| Image fields | `Pillow` |
| Social login | `social-auth-app-django` |
| HTTPS dev server | `django-extensions`, `werkzeug`, `pyOpenSSL` |
| Remote image download | `requests` |
| Thumbnails | `easy-thumbnails` |

After updating requirements, install:

```bash
pip install -r requirements.txt
```

## When to Update `.env`

Update `.env` when settings need local secrets or environment-specific values.

Examples:

```env
DB_NAME=your_database_name
DB_USER=your_database_user
DB_PASSWORD=your_database_password
DB_HOST=localhost
GOOGLE_OAUTH2_KEY=your_google_client_id
GOOGLE_OAUTH2_SECRET=your_google_client_secret
EMAIL_HOST_USER=your_email@example.com
EMAIL_HOST_PASSWORD=your_email_app_password
DEFAULT_FROM_EMAIL=your_email@example.com
```

Never share real `.env` values publicly.

## Common Errors and Fixes

### Error: `ModuleNotFoundError`

Meaning:

Python package is missing.

Fix:

```bash
pip install -r requirements.txt
```

### Error: `OperationalError: no such table`

Meaning:

Database table was not created.

Fix:

```bash
python manage.py migrate
```

### Error: Model changes not appearing in database

Fix:

```bash
python manage.py makemigrations
python manage.py migrate
```

### Error: Template not found

Check:

- Template path is correct.
- App is in `INSTALLED_APPS`.
- `APP_DIRS` is `True`.
- Template folder structure matches what the view requests.

### Error: Reverse URL not found

Check:

- URL name is correct.
- Namespace is correct.
- Required URL arguments are passed.

Example:

```django
{% url 'blog:post_detail' post.publish.year post.publish.month post.publish.day post.slug %}
```

### Error: Uploaded files not showing

Check:

- `MEDIA_URL`.
- `MEDIA_ROOT`.
- Development media route in project `urls.py`.
- Template uses correct file URL.

### Error: `request.user.profile` fails

Meaning:

The user does not have a profile row.

Fix:

- Create profile during registration.
- Create profile during social login.
- Add a signal or repair existing users manually.

### Error: CSRF verification failed

Check:

- POST forms include `{% csrf_token %}`.
- AJAX POST sends CSRF token.
- You are not using GET for data-changing actions.

## About `tests.py` in These Chapters

Most apps in Chapter 1-6 contain a `tests.py` file, but these files are mostly placeholders in this
repo.

Use `tests.py` when you want to add automated checks such as:

- A post list page returns status code 200.
- Draft posts do not appear in public lists.
- Comment form rejects invalid data.
- Login-required pages redirect anonymous users.
- Image like endpoint returns JSON.

Beginner example idea:

```python
def test_post_list_page_loads(self):
    response = self.client.get('/blog/')
    self.assertEqual(response.status_code, 200)
```

Tests are not required to run the app, but they help you confirm that changes did not break old
behavior.

## Chapter 1-6 Revision Questions and Answers

### 1. What is the job of `models.py`?

It defines database tables using Python classes.

### 2. What is the job of `views.py`?

It handles requests and returns responses.

### 3. What is the job of `urls.py`?

It maps URL patterns to views.

### 4. What is a template?

An HTML file that can use Django template syntax to display dynamic data.

### 5. Why do we run migrations?

To apply model/database structure changes to the database.

### 6. What is a `ForeignKey`?

A relationship where many objects can point to one object.

Example:

```text
Many posts -> One author
```

### 7. What is a `OneToOneField`?

A relationship where one object connects to exactly one other object.

Example:

```text
One user -> One profile
```

### 8. What is a `ManyToManyField`?

A relationship where many objects on both sides can connect.

Example:

```text
Many users -> Many liked images
```

### 9. Why use `get_object_or_404`?

To return a normal 404 page when an object does not exist.

### 10. Why use `@login_required`?

To protect pages that only logged-in users should access.

### 11. What is `commit=False`?

It creates a model object from a form but does not save it to the database yet.

### 12. Why use `set_password()`?

To hash passwords safely before saving them.

### 13. What is `cleaned_data`?

Validated form data.

### 14. Why use POST for comments and likes?

Because comments and likes change data.

### 15. Why use GET for search?

Because search reads data and creates shareable URLs.

### 16. What is `reverse()`?

It builds a URL from a URL pattern name.

### 17. What is an app namespace?

It prevents URL name conflicts between apps.

Example:

```python
reverse('blog:post_detail', args=[...])
```

### 18. Why use `python-decouple`?

To read configuration from environment variables instead of hardcoding secrets.

### 19. What is an RSS feed?

A machine-readable list of recent content.

### 20. What is a sitemap?

A machine-readable list of site URLs, usually for search engines.

### 21. What is AJAX?

AJAX lets JavaScript talk to the server without reloading the whole page.

### 22. What is `JsonResponse`?

A Django response that sends JSON data.

### 23. Why use thumbnails?

To show smaller, faster-loading versions of images.

### 24. What is OAuth2?

A login method where users authenticate through another provider, such as Google.

### 25. What is the messages framework?

A Django feature for showing temporary user notifications.

## Mini Projects to Practice

### Mini Project 1: Add Reading Time to Blog Posts

Goal:

Show estimated reading time for each post.

Files likely updated:

- `blog/models.py` or template helper.
- Template detail/list pages.

Beginner approach:

- Count words in `body`.
- Divide by 200 words per minute.
- Show result in template.

### Mini Project 2: Add Comment Count to Blog List

Goal:

Show number of active comments beside each post.

Files likely updated:

- `blog/views.py` if using annotation.
- `blog/post/list.html`.

Hint:

Use `post.comments.filter(active=True).count()` for a simple beginner version.

### Mini Project 3: Add Profile Bio

Goal:

Let users write a short bio.

Files likely updated:

- `account/models.py`.
- `account/forms.py`.
- `account/templates/account/edit.html`.
- Migrations.

Commands:

```bash
python manage.py makemigrations
python manage.py migrate
```

### Mini Project 4: Add Image Categories

Goal:

Let images have a category like nature, coding, travel, or food.

Files likely updated:

- `images/models.py`.
- `images/forms.py`.
- `images/admin.py`.
- Templates.
- Migrations.

### Mini Project 5: Add Unlike Button Text Change

Goal:

When user likes an image, button text changes from "Like" to "Unlike".

Files likely updated:

- Image detail/list template.
- JavaScript.
- Like endpoint may stay the same.

### Mini Project 6: Add Tag Pages to Sitemap

Goal:

Include tag-filtered blog pages in `sitemap.xml`.

Files likely updated:

- `blog/sitemaps.py`.
- `mysite/urls.py`.

Concept:

- Create another Sitemap class for tags.
- Return tag objects.
- Use `location()` to build tag URLs.

## Final Learning Advice

When learning Django, do not try to memorize everything first.

Use this order:

1. Understand the request flow: URL -> view -> model -> template.
2. Understand models and migrations.
3. Understand forms and validation.
4. Understand authentication.
5. Understand relationships: `ForeignKey`, `OneToOneField`, `ManyToManyField`.
6. Practice by making small changes.
7. Read errors carefully and trace which file owns the problem.

Most Django bugs become easier when you ask:

```text
Is this a URL problem?
Is this a view problem?
Is this a model/database problem?
Is this a form validation problem?
Is this a template display problem?
Is this a settings/config problem?
```

That question will usually point you to the right file.
