## Steps for creating a blog site using django:
Credits: https://tutorial.djangogirls.org/

### Set up django
Navigate to the djangogirls dir and create a virutal environment:
```
$ mkdir djangogirls
$ cd djangogirls
$ python3 -m venv myvenv
```
Start the virtual environment 
*(you will know that you have virtualenv started when you see that the prompt in your console is prefixed with **(myvenv)**)*:
```
$ myvenv\Scripts\activate
```
Install django by first creating a **requirements.txt** file that would list all libraries to be installed/used, *i.e. enter the following into the text file*:
```
Django~=2.0.6
```
```
(myvenv) ~$ python -m pip install --upgrade pip
(myvenv) ~$ pip install -r requirements.txt
```
Start the project by running the following, which would create the site dir structure:
```
(myvenv) C:\Users\Name\djangogirls> django-admin.exe startproject mysite .
```
Afterwards make some changes in **mysite/settings.py**:
```
TIME_ZONE = 'Europe/Berlin'
STATIC_URL = '/static/'
STATIC_ROOT = os.path.join(BASE_DIR, 'static')
ALLOWED_HOSTS = ['127.0.0.1', '.pythonanywhere.com']
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.sqlite3',
        'NAME': os.path.join(BASE_DIR, 'db.sqlite3'),
    }
}
```
### Web server
Start the web server by running the following and then go to check it out at http://127.0.0.1:8000/:
```
(myvenv) ~/djangogirls $ python manage.py runserver
```
Note that a command window can only run one thing at a time, and the command window you opened earlier is running the web server. 

To type additional commands while the web server is running, open a new terminal window and activate your virtualenv. To stop the web server, switch back to the window in which it's running and press CTRL+C - Control and C keys together (on Windows, you might have to press Ctrl+Break).

### Model
Begin building the model of the site by first creating an application called blog:

*(note this would also create a new folder for it)*
```
(myvenv) C:\Users\Name\djangogirls> python manage.py startapp blog
```
Next you need to go to the **mysite/settings.py** file and update it to include the new application:
```
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'blog',
]
```
Next open the **blog/models.py** file and add the following code which would include the class of the models:
```
from django.conf import settings
from django.db import models
from django.utils import timezone

class Post(models.Model):
    author = models.ForeignKey(settings.AUTH_USER_MODEL, on_delete=models.CASCADE)
    title = models.CharField(max_length=200)
    text = models.TextField()
    created_date = models.DateTimeField(default=timezone.now)
    published_date = models.DateTimeField(blank=True, null=True)

    def publish(self):
        self.published_date = timezone.now()
        self.save()

    def __str__(self):
        return self.title
```       
The last step is to create tables for the models in our database:
```
(myvenv) ~/djangogirls$ python manage.py makemigrations blog
(myvenv) ~/djangogirls$ python manage.py migrate blog
```
Once done, open the **blog/admin.py** file and register the newly created Post model by entering the following:
```
from django.contrib import admin
from .models import Post

admin.site.register(Post)
```
### Admin
Check out the output by going to http://127.0.0.1:8000/admin/. To log in, you need to create a *superuser* - a user account that has control over everything on the site. Go back to the command line:
```
(myvenv) C:\Users\Name\djangogirls> python manage.py createsuperuser
```

### Deploy in Git
Go to the djangogirls directory and set up the git repo:
```
$ git init
$ git config --global user.name "Your Name"
$ git config --global user.email you@example.com
```
Create a .gitignore file in the base dir containing the following:
```
*.pyc
*~
__pycache__
myvenv
db.sqlite3
/static
.DS_Store
```
Lastly, commit all the changes:
```
$ git status
$ git add --all .
$ git commit -m "My Django Girls app, first commit"
```
Next, go to github, create a new repository, giving it the name "my-first-blog" (*or in my case "django-test-blog"*). Leave the "initialize with a README" checkbox unchecked, leave the .gitignore option blank (we've done that manually) and leave the License as None. Copy the name of the repo site into the following:
```
$ git remote add origin https://github.com/<your-github-username>/<repo-name>.git
$ git push -u origin master
```
### Deploy in PythonAnywhere
Once you go to your PythonAnywhere dashboard, create a new API token and only then navigate to Bash and enter the following:
```
$ pip3.6 install --user pythonanywhere
$ pa_autoconfigure_django.py https://github.com/<your-github-username>/<repo-name>.git
$ python manage.py createsuperuser
$ ls
```
### URLs
Open the **mysite/urls.py** file and enter the following:
```
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', include('blog.urls')),
]
```
Next, create a new empty file called **blog/urls.py** and enter the following:
```
from django.urls import path
from . import views

urlpatterns = [
    path('', views.post_list, name='post_list'),
]
```
### Views
Open the **blog/views.py** file, add the following and then save it. Go to http://127.0.0.1:8000/ and see what we've got:
```
def post_list(request):
    return render(request, 'blog/post_list.html', {})
```
