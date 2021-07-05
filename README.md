# ToDo-WebApp-using-Django
![](media/images/output.png)

#1.install crispy-forms<br>
$pip install --upgrade django-crispy-forms

#2.Create project and app<br>
$django-admin startproject todo-site .<br>
$python manage.py runserver<br>
$ctrl-c<br>
$python manage.py startapp todo<br>



#3.todo-site/settings.py

INSTALLED_APPS = [<br>
    'todo',<br>
    'crispy_forms',<br>
    'django.contrib.admin',<br>
    'django.contrib.auth',<br>
    'django.contrib.contenttypes',<br>
    'django.contrib.sessions',<br>
    'django.contrib.messages',<br>
    'django.contrib.staticfiles',<br>
]


#4.Edit file in todo_site/urls.py,  

from django.contrib import admin<br>
from django.urls import path<br>
from todo import views<br>

urlpatterns = [<br>
    path('admin/', admin.site.urls),<br>
    #####################home_page###########################################<br>
    path('', views.index, name="todo"),<br>
    ####################give id no. item_id name or item_id=i.id ############<br>
    path('del/<item_id>', views.remove, name="del"),<br>
]


#5. todo/models.py

from django.db import models<br>
from django.utils import timezone<br>

# Create your models here.
class Todo(models.Model):<br>
    title = models.CharField(max_length= 100)<br>
    details = models.TextField()<br>
    date = models.DateTimeField(default=timezone.now)<br>

    def __str__(self):
        return self.title


6. todo/views.py 

from django.shortcuts import render, redirect<br>
from django.contrib import messages<br>

## import todo from and models
from .forms import TodoForm<br>
from .models import Todo<br>

# Create your views here.
def index(request):<br>
    item_list = Todo.objects.order_by("-date")<br>
    if request.method == "POST":<br>
        form = TodoForm(request.POST)<br>
        if form.is_valid():<br>
            form.save()<br>
            return redirect('todo')<br>
    form = TodoForm()<br>

    page = {
                "forms" : form,
                "list" : item_list,
                "title" : "TODO LIST",
    }
    return render(request, 'todo/index.html', page)


### function to remove item, it receive todo item id from url ##

def remove(request, item_id):<br>
    item = Todo.objects.get(id=item_id)<br>
    item.delete()<br>
    messages.info(request, "item removed !!!")<br>
    return redirect('todo')<br>



#7.todo/forms.py

from django import forms<br>
from django.forms import fields<br>
from .models import Todo<br>


class TodoForm(forms.ModelForm):<br>
    class Meta:<br>
        model = Todo<br>
        fields = "__all__"<br>


#8.Register models to todo/admin.py

from django.contrib import admin<br>

from .models import Todo<br>
# Register your models here.
admin.site.register(Todo)



#9.Navigate to templates/todo/index.html and edit it

{% load crispy_forms_tags %}
<!DOCTYPE html>
<html lang="en" dir="ltr">

<head>

  <meta charset="utf-8">
  <title>{{title}}</title>
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css">
  <script src="https://ajax.googleapis.com/ajax/libs/jquery/3.3.1/jquery.min.js"></script>
  <script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/js/bootstrap.min.js"></script>
  <!--style-->
  <style>
  .card {

   box-shadow: 0 4px 8px 0 rgba(0,0,0,0.5),
               0 6px 20px 0 rgba(0,0,0,0.39);
   background: rgb(166, 166, 166);
   margin-bottom : 5%;
   border-radius: 25px;
   padding : 2%;
   overflow: auto;
   resize: both;
   text-overflow: ellipsis;
  }
  .card:hover{
    background: lightblue;
  }

  .submit_form{

    text-align: center;
    padding: 3%;
    background: rgb(0, 153, 153);
    border-radius: 25px;
    box-shadow: 0 4px 8px 0 rgba(0,0,0,0.4),
                0 6px 20px 0 rgba(0,0,0,0.36);
  }
  </style>

</head>

<body  class="container-fluid">

  {% if messages %}
  {% for message in messages %}
  <div class="alert alert-info">
    <strong>{{message}}</strong>
  </div>
  {% endfor %}
  {% endif %}

  <center class="row">
    <h1><i>__TODO LIST__</i></h1>
    <hr />
  </center>

  <div class="row">

    <div class="col-md-8">

      {% for i in list %}
      <div class="card">
        <center><b>{{i.title}}</b></center>
        <hr/>
        {{i.date}}
        <hr/>
        {{i.details}}
        <br />
        <br />
        <form action="/del/{{i.id}}" method="POST" style=" padding-right: 4%; padding-bottom: 3%;">
          {% csrf_token %}
          <button value="remove" type="submit"  class="btn btn-primary" style="float: right;"><span class="glyphicon glyphicon-trash"></span> &nbsp; remove</button>
        </form>
      </div>
      {% endfor%}
    </div>
    <div class="col-md-1"> </div>
    <div class="col-md-3" >
      <div  class="submit_form">
      <form  method="POST">
        {% csrf_token %}
        {{forms|crispy}}
        <center>
        <input type="submit" class="btn btn-default" value="submit" />
      </center>
      </form>
    </div>
  </div>
</div>
</body>

</html>



#10.Make migrations and migrate it 

$python manage.py makemigrations<br>
$python manage.py migrate


11.run the server to see your todo app 

$python manage.py runserver

and go http://127.0.0.1:8000/
