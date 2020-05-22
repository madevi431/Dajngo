# User Registration
Creating Django user sign up/registration, Implement it with Model Form

## Model Creation
Model is a blue print for how we gonna store data (Creating the structure of tables).In other words, a model is a class which is used to contain essential fields and methods. Each model class maps to a single table in the database. Let’s Create a Register model in models.py file

### models.py

from django.db import models


'''class Register(models.Model):
    gender_vals = [('Male', 'Male'), ('FeMale', 'FeMale')]
    firstName = models.CharField(max_length=100)
    lastName = models.CharField(max_length=100)
    emailId = models.EmailField(null=True)
    phoneNo = models.CharField(max_length=10)
    age = models.IntegerField(null=True)
    gender = models.CharField(max_length=10, choices=gender_vals)
    date_of_birth = models.DateField(null=True)'''
Every model inherits from django.db.models.Model. Each of our model fields has a related Django field type and field options. The Register model uses four different field types—CharField, DateTimeField, TextField and EmailField.

NOTE: Don't forget to mention app name in INSTALLED_APPS list of settings.py file, if you created a new app

Sync With database
Now that we’ve created the model, it’s time to add it to the database. To do this we need to open Command Promnt and run these two commands:

python manage.py makemigrations
python manage.py migrate Your terminal output should look something like this:


NOTE: If you made any changes in the model you need to follow these two steps every time.

Model Form Creation
A Form class describes a form and determines how it works and appears.In a similar way that a model class’s fields map to database fields, a form class’s fields map to HTML form <input> elements.

For the above Registraion model, which we could use to implement “Registraion” functionality on our website: First we need to create froms.py file in our app location and import Register model class from models.py

forms.py

from django import forms
from .models import Register


class RegisterForm(forms.ModelForm):
    class Meta:
        model = Register
        fields = "__all__"
We need to import Django forms first (from django import forms) and our Register model (from .models import Register). Next, we have class Meta, where we tell Django which model should be used to create this form (model = Register). Finally, we can say which field(s) should end up in our form. In this scenario if we want only few fields then metion them in a list formate.

Here’s how the form data could be processed in the view that handles this form:

Define the Views Function
views.py

from django.shortcuts import render
from django.http import HttpResponse
from .forms import RegisterForm


def register(request):
    form = RegisterForm()
    return render(request,'register.html',{'form':form})
Initially we are using get method, To check how from is working, Here we are using BootstrapCDN

NOTE: Add the {% csrf_token %} to every Django template you create that uses POST to submit data. This will reduce the chance of forms being hijacked by malicious users.

template
Create a register.html file in the templates folder and code up like below

register.html

<html>
<head>
    <title>Registration</title>
	<meta charset="utf-8">
	<meta name="viewport" content="width=device-width, initial-scale=1">
	<link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/4.4.1/css/bootstrap.min.css">
	<script src="https://maxcdn.bootstrapcdn.com/bootstrap/4.4.1/js/bootstrap.min.js"></script>
</head>
<body>
	<div class="container">
		<div class="row justify-content-center">
			<div class="card">
			<div class="card-header bg-info">Registration Page</div>
			<div class="card-body">
				<form method="POST">
					{% csrf_token %}
					<table>
						{{ form.as_table }}
					</table>
					<div class="card-footer">
					<input class="btn btn-info" type="submit" name="submit" value="Submit">
					</div>
				</form>
			</div>
		</div>
		</div>
	</div>
</body>
</html>
NOTE: Don’t forget that a form’s output does not include the surrounding <form> tags, or the form’s submit control. You will have to provide these yourself.

Here we got the output like this



Form validation
Till now we haven't used post method from register.htmt, a visitor will hit the submit button after filling up the details, that means the form method is changed to "POST".

Now our task is to validate the form and save details, for that we need to change register function in views.py

views.py

def register(request):
    if request.method == "POST":
        form = RegisterForm(request.POST)
        print(form.is_valid())
        if form.is_valid():
            form.save()
            return HttpResponse("Registration successfull")
    form = RegisterForm()
    return render(request,'userApp/register.html',{'form':form})
is_valid() validates the form details given by visitor and save the user details in database.

NOTE: Fill the Data of birth must be in formate of YYYY-MM-DD (registration page )

Display users
Displaying users is nothing but retrieving information from database, Here we will display all users information in a table, for this fallow below steps

In views
Define a function for displaying all user information

def display(request):
    data = Register.objects.all()
    return render(request,'userApp/display.html',{'data':data})
Here data has all users imformation in the form of query set

The Template
In templates create a new html file with the name display.html

<!DOCTYPE html>
<html>
<head>
    <title>User details</title>
	<meta charset="utf-8">
	<meta name="viewport" content="width=device-width, initial-scale=1">
	<link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/4.4.1/css/bootstrap.min.css">
	<script src="https://maxcdn.bootstrapcdn.com/bootstrap/4.4.1/js/bootstrap.min.js"></script>
</head>
<body>
	<div class="container">
    <div class="row justify-content-center">
        <div class="card">
            <div class="card-body">
                <table class="table table-bordered table-striped"">
                    <thead class="bg-info ">
                        <tr>
                            <th>First Name</th>
                            <th>Last Name</th>
                            <th>Mail Id</th>
                            <th>Phone number</th>
                            <th>Age</th>
                            <th>Gender</th>
                            <th>Date of Birth</th>
                        </tr>

                    </thead>
                    <tbody>
                    {% for info in data %}
                        <tr>
                            <td>{{ info.firstName }}</td>
                            <td>{{ info.lastName }}</td>
                            <td>{{ info.emailId }}</td>
                            <td>{{ info.phoneNo }}</td>
                            <td>{{ info.age }}</td>
                            <td>{{ info.gender }}</td>
                            <td>{{ info.date_of_birth }}</td>
                        </tr>
                    {% endfor %}
                    </tbody>
                </table>
        </div>
        </div>
    </div>
</div>
</body>
</html>
output 
