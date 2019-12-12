## Primeiro atualizar todos os pacotes e recursos da máquina
```
sudo apt-get update && sudo apt-get upgrade
sudo apt-get install python3-venv
```

* Criar uma virtual env

`python3 -m venv .venv`

* Clonar o repositorio

`git clone your-url.git`

* Instalar as dependências

`pip install -r requirements.txt`

* Separar os arquivos estáticos 

`python manage.py collectstatic`

* Criar o Banco de Dados

`python manage.py migrate`

* Criar usuários adm

`python manage.py createsuperuser`

## Instalar e configurar

* Instalar o uwsgi no ambiente

```
sudo apt-get install python3.6-dev
sudo apt-get install build-essential libssl-dev libffi-dev python-dev
pip install wheel
pip install uwsgi
```

* Instalar e iniciar o Ngninx
```
sudo apt-get install nginx
sudo /etc/init.d/nginx start
```

## Configurar Nginx

* Criar um arquivo chamdo uwsgi_params com o conteúdo
```
uwsgi_param  QUERY_STRING       $query_string;
uwsgi_param  REQUEST_METHOD     $request_method;
uwsgi_param  CONTENT_TYPE       $content_type;
uwsgi_param  CONTENT_LENGTH     $content_length;

uwsgi_param  REQUEST_URI        $request_uri;
uwsgi_param  PATH_INFO          $document_uri;
uwsgi_param  DOCUMENT_ROOT      $document_root;
uwsgi_param  SERVER_PROTOCOL    $server_protocol;
uwsgi_param  REQUEST_SCHEME     $scheme;
uwsgi_param  HTTPS              $https if_not_empty;

uwsgi_param  REMOTE_ADDR        $remote_addr;
uwsgi_param  REMOTE_PORT        $remote_port;
uwsgi_param  SERVER_PORT        $server_port;
uwsgi_param  SERVER_NAME        $server_name;
```

