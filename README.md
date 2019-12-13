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

* Criar arquivo de configurações do Nginx em /etc/nginx/sites-available/ exemplo: mysite.conf

```
upstream django {
    server unix:///home/ubuntu/VPS/mysite.sock; 
}

server {
    listen      80;
    server_name example.com;
    charset     utf-8;

    client_max_body_size 75M; 

    location /media  {
        alias /home/ubuntu/VPS/media; 
    }

    location /static {
        alias /home/ubuntu/VPS/static;
    }

    location / {
        uwsgi_pass  django;
        include     /home/ubuntu/VPS/uwsgi_params; 
    }
}
```

* Criar um symlink em /etc/nginx/sites-enabled
/ exemplo: mysite.conf

`sudo ln -s /etc/nginx/sites-available/djangovps.conf`

* Reinicie o Nginx

`sudo /etc/init.d/nginx restart`


* Baixe uma imagem para a pasta de media e teste

`uwsgi --socket mysite.sock --module django_vps.wsgi --chmod-socket=666`

* Criar o arquivo de inicialização

```
[uwsgi]
chdir           = /home/ubuntu/VPS
module          = django_vps.wsgi
home            = /home/ubuntu/.venv
master          = true
processes       = 10
socket          = /home/ubuntu/VPS/mysite.sock
vacuum          = true
chmod-socket    = 666
```

* Testar o arquivo de inicialização

`uwsgi --ini mysite_uwsgi.ini`

## Configurando o uWSGI em modo Emperor
```
sudo mkdir /etc/uwsgi
sudo mkdir /etc/uwsgi/vassals
sudo ln -s /home/ubuntu/VPS/mysite_uwsgi.ini /etc/uwsgi/vassals/
uwsgi --emperor /etc/uwsgi/vassals --uid www-data --gid www-data
```
## Configurar o systemctl para iniciar no boot

```
https://uwsgi-docs.readthedocs.io/en/latest/Systemd.html

cd /etc/systemd/system/

sudo vim djangovps.service


======
[Unit]
Description=Django VPS uWSGI Emperor
After=syslog.target

[Service]
ExecStart=/home/ubuntu/venv/bin/uwsgi --emperor /etc/uwsgi/vassals --uid www-data --gid www-data
RuntimeDirectory=uwsgi
Restart=always
KillSignal=SIGQUIT
Type=notify
StandardError=syslog
NotifyAccess=all
User=ubuntu

[Install]
WantedBy=multi-user.target
======

sudo chmod 664 /etc/systemd/system/djangovps.service

sudo systemctl daemon-reload

sudo systemctl enable djangovps.service

 sudo systemctl start djangovps.service

 sudo systemctl status djangovps.service

journalctl -u djangovps.service

```
