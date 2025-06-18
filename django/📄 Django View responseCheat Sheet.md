

### **@api_view([...])**

  

**Use when:**

You are building an **API view** that returns **JSON**, typically used by frontend apps (React, mobile, etc.).

  

**Provided by:** Django REST Framework (DRF)

  

**Features:**

- Handles request.data (parsed JSON or form data)
    
- Returns Response() with JSON
    
- Supports CORS, authentication, and more
    

  

**Example:**

```
from rest_framework.decorators import api_view
from rest_framework.response import Response

@api_view(['GET', 'POST'])
def api_view_example(request):
    return Response({'message': 'This is an API'})
```

---

### **🔹** 

### **@require_http_methods([...])**

  

**Use when:**

You are building a **regular Django view** that renders **HTML templates** and uses forms.

  

**Provided by:** Django core

  

**Features:**

- Prevents invalid HTTP methods (returns 405)
    
- Works with render() and redirect()
    
- No JSON handling or request parsing
    

  

**Example:**

```
from django.views.decorators.http import require_http_methods
from django.shortcuts import render

@require_http_methods(["GET", "POST"])
def template_view_example(request):
    return render(request, 'template.html')
```

---

### **✅ Quick Decision Guide:**

|**Need**|**Use**|
|---|---|
|JSON request/response|@api_view|
|HTML form and template|@require_http_methods|
|Using Django REST Framework|@api_view|
|Using only Django core|@require_http_methods|

---

Let me know if you want this in Markdown, PDF, or ready for your team wiki!