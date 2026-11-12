# Painel API

Aplicação Django para consulta de cotação de moeda e histórico de consultas.

## Visão geral

Este projeto expõe uma interface web para consultar múltiplas cotações em uma mesma requisição, como USD-BRL, EUR-BRL e BTC-BRL, e mantém um histórico das consultas em banco de dados SQLite.

Funcionalidades:
- consulta de várias cotações na mesma requisição via `requests`;
- renderização da página de consulta em template Django;
- registro de consultas em `RegistroCotacao`;
- API REST para listar o histórico em ordem decrescente.

## Estrutura do projeto

```text
painelapi/
├── manage.py
├── README.md
├── db.sqlite3
├── painel/
│   ├── migrations/
│   ├── templates/
│   ├── admin.py
│   ├── apps.py
│   ├── models.py
│   ├── serializers.py
│   ├── tests.py
│   ├── urls.py
│   └── views.py
├── painelapi/
│   ├── __init__.py
│   ├── asgi.py
│   ├── settings.py
│   ├── urls.py
│   └── wsgi.py
└── venv/
```

## Requisitos

- Python 3.10+
- Django
- Django REST Framework
- requests

## Configuração do ambiente

Crie e ative um ambiente virtual:

```bash
python -m venv venv
```

No Windows (PowerShell):

```powershell
Set-ExecutionPolicy -Scope Process -ExecutionPolicy RemoteSigned
.\venv\Scripts\Activate.ps1
```

No Linux/macOS:

```bash
source venv/bin/activate
```

Instale as dependências:

```bash
pip install django djangorestframework requests
```

## Executando o projeto

Na raiz do projeto:

```bash
python manage.py migrate
python manage.py runserver
```

Acesse:
- http://127.0.0.1:8000/
- http://127.0.0.1:8000/consultar/
- http://127.0.0.1:8000/api/historico/

## Rotas

### Página de consulta

```text
/consultar/
```

Página principal da aplicação, responsável por:
- tentar consultar a cotação externa;
- registrar o valor em banco quando a resposta for válida;
- exibir mensagem de erro em caso de falha ou resposta inesperada.

### Histórico da API

```text
/api/historico/
```

Endpoint de leitura do histórico de cotações. Retorna os registros ordenados do mais recente para o mais antigo.

## Modelo principal

```python
class RegistroCotacao(models.Model):
    moeda_origem = models.CharField(max_length=5, default='USD')
    moeda_destino = models.CharField(max_length=5, default='BRL')
    valor = models.DecimalField(max_digits=20, decimal_places=4)
    data_hora_consulta = models.DateTimeField(auto_now_add=True)
```

## Testes

Execute a suíte:

```bash
python manage.py test
```

Os testes cobrem:
- sucesso de consulta;
- timeout da API externa;
- erro HTTP da API externa;
- formato inesperado da resposta;
- presença de `fetch` e `catch` no template;
- ordenação do histórico;
- método de leitura somente.

## Troubleshooting

### Erro: `NoReverseMatch` ao usar `reverse('consultar')`

Esse erro acontece quando a rota é resolvida com um nome que não corresponde ao `urlpatterns` da aplicação.

Verifique se a configuração de URLs está assim:

```python
# painel/urls.py
from django.urls import path
from . import views

urlpatterns = [
    path("", views.consultar_cotacao, name="index"),
    path("consultar/", views.consultar_cotacao, name="consultar"),
    path("api/historico/", views.HistoricoCotacaoView.as_view(), name="api-historico"),
]
```

No template, use:

```html
<a href="{% url 'consultar' %}">Consultar novamente</a>
```

No projeto principal:

```python
from django.contrib import admin
from django.urls import include, path

urlpatterns = [
    path("admin/", admin.site.urls),
    path("", include("painel.urls")),
]
```

## Observações

- A aplicação usa SQLite como banco de dados padrão do Django.
- A consulta externa possui timeout para evitar travamento da interface.
- Em caso de falha ou resposta inválida, a interface mostra uma mensagem amigável ao usuário sem quebrar a página.

## Contribuição

Para contribuir com melhorias:
1. crie uma branch;
2. ajuste o código e os testes;
3. rode `python manage.py test`;
4. valide o comportamento em execução local.

# Painel de Cotações — Projeto 4
Painel Django que consulta a cotação USD/BRL na AwesomeAPI,
guarda o histórico no banco e o exibe com fetch().
## Funcionalidades
- /consultar/ busca a cotação e salva um registro
- Tratamento de falhas de rede no servidor e no navegador
- /api/historico/ (JSON, mais recente primeiro)
## Como rodar
1. python -m venv venv e ative o ambiente
2. pip install -r requirements.txt
3. python manage.py migrate
4. python manage.py runserver
## Variáveis de ambiente (opcional)
- COTACAO_URL: troca a URL da API externa
## Prints
(inclua prints do painel e do Admin)
## Autor
taynna thaylla — IESB 2026
