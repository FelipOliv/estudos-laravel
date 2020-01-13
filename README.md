<img src="./logo.png"  width="100" />

# Meus estudos sobre Laravel

Retomando meus estudos em Laravel, decidi anotar e colocar tudo que estou revisando aqui, eu poderia colocar no meu Google Docs, mas caso eu quisesse fazer uma consulta seria um processo longo até eu logar na minha conta e entrar e procurar o arquivo respectivo.

Claro que existe a documentação, mas eu quero escrever para deixar claro na minha mente algo que estou codando no momento.

# Criando um VirtualHost para projetos dentro do htdocs do xampp (windows)

Basicamente:

Editar o arquivo <b>httpd-vhosts.conf</b> localizado em <b>C:\xampp\apache\conf\extra</b>

Colocar o seguinte trecho:

````
<VirtualHost *:80>
    DocumentRoot "C:\xampp\htdocs\pasta-do-projeto-laravel\public"
    ServerName nomedoserver.com
    DirectoryIndex index.php
    <Directory "C:\xampp\htdocs\pasta-do-projeto-laravel\public">
        Options All
        AllowOverride All
        Order Allow,Deny
        Allow from all
    </Directory>
</VirtualHost>
````

Feito isso, editar o arquivo <b>hosts</b> localizado em <b>C:\Windows\System32\drivers\etc</b>, adicionar o seguinte:

````
127.0.0.1 servername.com
````