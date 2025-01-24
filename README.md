## Django MarkdownX - Documentação oficinal 

https://neutronx.github.io/django-markdownx/

_____

#### No settings.py

Adicione 'video/mp4' a opção MARKDOWNX_UPLOAD_CONTENT_TYPES

```python
MARKDOWNX_UPLOAD_CONTENT_TYPES = ["image/jpeg", "image/png", "image/svg+xml", "video/mp4"]
```

Configure o MARKDOWNX_UPLOAD_URLS_PATH um novo caminho ou sobrescreva o valor padrão

```python
MARKDOWNX_UPLOAD_URLS_PATH = "/markdownx/upload/"
```

Altere a opção MARKDOWNX_MEDIA_PATH para o caminho aonde deseja salvar os arquivos, o padrão é markdownx/

```python
MARKDOWNX_MEDIA_PATH = "media/"
```

#### Views.py

Adicione ao arquivo views ou crie um novo e adicione o código

```python
from markdownx.views import ImageUploadView
from django.http import JsonResponse
from django.core.files.storage import default_storage
from django.core.files.base import ContentFile
from django.conf import settings
import os

class CustomMarkdownxMediaUploadView(ImageUploadView):
    def post(self, request, *args, **kwargs):
        file = request.FILES.get('image')
        content_type = file.content_type
        if not file:
            return JsonResponse({'error': 'Arquivo não foi enviado'}, status=400)

        if file.content_type not in settings.MARKDOWNX_UPLOAD_CONTENT_TYPES:
            return JsonResponse({'error': 'Tipo de arquivo não suportado'}, status=400)

        file_path = default_storage.save(os.path.join(settings.MARKDOWNX_MEDIA_PATH, file.name), ContentFile(file.read()))
        file_url = default_storage.url(file_path)
        return JsonResponse({'image_path': file_url, 'type': content_type}, status=200)
```

#### Urls.py

Importe o arquivo views dentro de urls.py 

```python
from caminho.views import CustomMarkdownxMediaUploadView
```

Adicione a urlpatterns com o valor informado em MARKDOWNX_UPLOAD_URLS_PATH, nesse exemplo, 'markdownx/upload/'

```python
urlspatterns = [
   ...
   path("markdownx/upload/", CustomMarkdownxMediaUploadView.as_view(), name="markdownx_upload"),
]
```




