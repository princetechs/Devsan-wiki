

⸻

🐍 Django for Rails & Laravel Developers (Quick Guide)

⸻

📁 Project Structure

Concept	Django	Rails	Laravel
Create app	startapp app_name	whole project	inside app/
Settings	settings.py	*.rb files	.env + config/


⸻

⚙️ Common Commands

Task	Django	Rails	Laravel
Create project	django-admin startproject	rails new	laravel new
Create app/module	python manage.py startapp	N/A	php artisan make:model
Create migration	makemigrations (auto)	manual	manual
Apply migrations	migrate	db:migrate	migrate
Run server	runserver	rails s	serve


⸻

🧱 Models & Migrations
	•	Django auto-generates migration from model.
	•	Example:

# models.py
class Book(models.Model):
    title = models.CharField(max_length=100)

python manage.py makemigrations
python manage.py migrate


⸻

🔁 Relationships

class Author(models.Model):
    name = models.CharField(max_length=100)

class Book(models.Model):
    title = models.CharField(max_length=255)
    author = models.ForeignKey(Author, on_delete=models.CASCADE)

Like belongsTo and hasMany in Laravel/Rails.


# How to return response 

If we are returning a view it should be import render with response

import render
just say the template name and 
like :-  ``
`from django.shortcuts import render

from django.http import JsonResponse

from django.views.decorators.csrf import csrf_exempt

from relay.core import Relay

import json

  

def relay_chat(request):

if request.method == 'GET':

response = {

'status': 'success',

'message': 'Welcome to Relay Chat',

'chat_available': True

}

return JsonResponse(response)

return JsonResponse({'error': 'Method not allowed'}, status=405)

  

@csrf_exempt`