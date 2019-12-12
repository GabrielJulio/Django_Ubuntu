##Primeiro atualizar todos os pacotes e recursos da máquina
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