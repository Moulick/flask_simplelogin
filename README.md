# Login Extension for Flask

There are good and recommended options to deal with web authentication
in Flask.

**I recommend you use:**

- [Flask-Login](https://flask-login.readthedocs.io)
- [Flask-Security](https://pythonhosted.org/Flask-Security/)

Those extensions are really complete and **production ready**!

> So why **Flask Simple Login**?

However sometimes you need something **simple** for that small project or for
prototyping.

## Flask Simple Login

What it provides:

- Login and Logout forms and pages
- Function to check if user is logged-in
- Decorator for views
- Easy and customizable `login_checker`

What it does not provide:
(but of course you can easily implement by your own)

- ~~Database Integration~~
- ~~Password management~~
- ~~API authentication~~
- ~~Role or user based access control~~

## Hot it works

> `pip install flask_simplelogin`

```python
from flask import Flask
from flask_simplelogin import SimpleLogin

app = Flask(__name__)
SimpleLogin(app)
```

That's it! now you have `/login` and `/logout` routes in your application.

The username defaults to `admin` and the password defaults to `secret` (yeah that's not clever, let's see how to change it)

## Configuring

```python
from flask import Flask
from flask_simplelogin import SimpleLogin

app = Flask(__name__)
app.config['SECRET_KEY'] = 'something-secret'
# TIP: use `dynaconf` to configure your Flask app and those values will be taken from environment variables.


def auth_my_user(user):
    "user = {'username': 'foo', 'password': 'bar'}"
    # do the authentication here, it is up to you!
    # query your database, check your user/passwd file
    # connect to external service.. anything.
    return True or False  # True = authenticated


SimpleLogin(app, login_checker=auth_my_user)
```

## Checking if user is logged in

```python

from flask_simplelogin import is_logged_in

if is_logged_in():
    # do things if anyone is logged in

if is_logged_in('admin'):
    # do things only if admin is logged in
```


## Decorating your views

```python
from flask_simplelogin import login_required

@app.route('/it_is_protected')
@login_required   # < --- simple decorator
def foo():
    return 'secret'
```

### Protecting Flask Admin views

```python

from flask_admin.contrib.foo import ModelView
from flask_simplelogin import is_logged_in


class AdminView(ModelView)
    def is_accessible(self):
        return is_logged_in('admin')
```

## Customizing templates

There are only one template to customize and it is called `login.html`

Example is:

```html
{% extends 'base.html' %}
{% block title %}Login{% endblock %}
{% block messages %}
   {{super()}}
   {%if form.errors %}
     <ul class="alert alert-danger">
       {% for field, errors in form.errors.items() %}
         <li>{{field}} {% for error in errors %}{{ error }}{% endfor %}</li>
       {% endfor %}
     </ul>
   {% endif %}
{% endblock %}

{% block page_body %}
       <form action="{{ url_for('simplelogin.login', next=request.args.get('next', '/')) }}" method="post">
            <div class="form-group">
            {{ form.csrf_token }}
            {{form.username.label}}<div class="form-control">{{ form.username }}</div><br>
            {{form.password.label}}<div class="form-control"> {{ form.password }}</div><br>
            </form>
           <input type="submit" value="Send">
       </form>
{% endblock %}
```

> Take a look at the examples app in this repository.

And you can customize it in anyway you want and need, it receives a `form` in context and it is a `WTF form` the submit should be done to `request.path` which is the same `/login` view.

You can also use `{% if is_logged_in %}` in your template if needed.


## Requirements

- Flask-WTF and WTForms
- having a `SECRET_KEY` set in your `app.config`
