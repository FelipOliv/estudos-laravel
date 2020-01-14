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

# Rotas

````
<?php

Route::get ('/', function ()
{
    return view('welcome') ;
});

Route::get ("/test", function ()
{
    echo "Welcome" ;
}) ;

// rota com parametros

Route::get("welcome/{user_name}/{last_name}", function ($user_name, $last_name)
{
    echo "Welcome {$user_name} {$last_name}" ;
});

// rota com parametros opcionais

Route::get ("/optional/{param?}", function ($param = null)
{
    if ( empty ( $param ) )
    {
        echo "Optional: ... ";
    }
    else
    {
        echo "Optional: {$param}" ;
    }

    var_dump ($param) ;
}) ;

// rotas com regras

Route::get( "/regra/{id}", function ( $id )
{
    echo "user id: {$id}" ;

})
->where ("id", '[1-9]+')
# ->where ("outroParam", '[A-Za-z]+')
;

// rotas agrupadas e pre-fixadas

Route::prefix ( "/app")->group (function ()
{
    Route::get ("/", function ()
    {
        return "rota <b>raiz</b> do group app" ;

        # return view ("app") ;
    }) ;

    Route::get ("/user", function ()
    {
        return "rota <b>user</b> do group app" ;

        # return view ("user") ;
    }) ;

    Route::get ("/profile", function ()
    {
        return "rota <b>profile</b> do group app" ;

        # return view ("profile") ;
    }) ;

}) ;


// Rotas nomeadas

Route::prefix("named")->group (function ()
{
    /**
     * muito útil no template blade, facilita a chamada de rotas por nomes
     * 
     * <a href="route("name.subname")"> Ir para algum lugar </a>
     * 
     * Ou em qualquer lugar para aprontar para uma rota :)
     */

    Route::get("/", function ()
    {
        echo "named, raiz" ;

    })->name ("named.raiz");

    Route::get("/sub", function ()
    {
        echo "/named/sub" ;
    })->name("named.sub");

}) ;


// rota redirect

Route::redirect ("/redirect", "/") ;

# com http code: Route::redirect ("/redirect", "/", 301) ;


Route::get ("/new-redirect", function ()
{
    return redirect()->route ("named.raiz") ;
}) ;


// outros verbos http

Route::post ("post-request", function ()
{
    return json_encode (
        [
            "code_response" => 200,
            "response_message" => "hello world",
            "response_users" => [
                [
                    "name" => "Feipe",
                    "age" => 22
                ],
                [
                    "name" => "Example",
                    "age" => 0
                ]
            ]
        ]
    ) ;

    /** 
     * Request feito via insomnia
     * 
     * Para que um request POST funcione sem o Laravel reclamar da
     * falta do token csrf, editar o arquivo VerifyCsrfToken.php e no atributo $except = [] adicionar dentro do array
     * a rota que você não quer que o Laravel exija a presença do csrf
     * 
     * exemplo:
     * 
     * protected $except = [ "minha-rota" ]
    */
}) ;

/*

    Route::delete ("/verb", function ()
    {
        echo "deleted" ;
    }) ;

    Route::put ("/verb", function ()
    {
        echo "added" ;
    }) ;

    Route::patch ("/verb", function ()
    {
        echo "added" ;
    }) ;

    Route::options ("/verb", function ()
    {
        echo "options" ;
    }) ;

*/
````