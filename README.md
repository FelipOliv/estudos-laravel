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

# Rotas que apontam para um controlador

````
Route::get ("/", "FirstController@index") ;

Route::get ("/show-your-name", "FirstController@showName") ;

Route::get ("/multiplica/{a}/{b}", "FirstController@multiplica") ;

class FirstController extends Controller
{
    public function index ()
    {
        echo "Welcome" ;
    }

    public function showName ()
    {
        return "Felipe Oliveira" ;
    }

    public function multiplica ($a, $b)
    {
        echo "{$a} x {$b} = " . ($a * $b) ;
    }
}


/**
 * É possível criar um controlador com todos os métodos de manipulação de recurso já prontos
 * atravéz do comando artisan: php artisan make:controller NameController --resource
 * 
 * Passando o parâmetro --resource, o controlador é criando com os métodos:
 * 
 * -> index : para mostrar uma listagem de recursos
 * -> create : para mostrar um formulário de contrução de recurso
 * -> store : para persistir um recurso
 * -> show : para mostrar um recurso específico
 * -> edit : para mostrar um formulário de edição do recurso
 * -> update : para persistir a atualização do recurso
 * -> destroy : para apagar um recurso
*/

Route::resource("cliente", "ClienteController") ;

/**
 * Com a rota resource, o Laravel ira associar automaticamente cada endpoint
 * para seu método específico no controlador criado a partir do comando resource.
 * 
 * Na prática isso significa que se não tivesse esse Route::resource, teria-mos que
 * direcionar na mão cada endpoint para cada método específico da rota e do verbo http
*/

namespace App\Http\Controllers;

use Illuminate\Http\Request;

class ClienteController extends Controller
{
    protected $clientes = [
        0 => [
            "id" => 1,
            "name" => "Felipe"
        ],
        1 => [
            "id" => 2,
            "name" => "Gustavo"
        ],
        2 => [
            "id" => 3,
            "name" => "Cleiton"
        ],
        3 => [
            "id" => 4,
            "name" => "Bruna"
        ],
        4 => [
            "id" => 5,
            "name" => "Maria"
        ],
    ] ;

    public function __construct ()
    {
        // var_dump (session("clientes")) ;

        if ( ! session ("clientes") )
        {
            session (["clientes" => $this->clientes]) ;
        }
    }


    public function user ( int $userID )
    {
        return session("clientes") [ $userID - 1 ] ;
    }

    /**
     * Display a listing of the resource.
     *
     * @return \Illuminate\Http\Response
     */
    public function index()
    {
        return view ("clients.index")
        ->with("clients", session("clientes"))
        ->with("title", "my title") ;

        /**
         *  Maneiras diferentes de passar dados para uma view
         * 
         * $varName = "content" ;
         * 
         * compact
         * return view("name", compact("varName", $varName))
         * 
         * assoc array
         * return view ("viewName", ["varName" => $varName])
         * 
         * with function
         * return view ("viewName")->with("varName", $varName)
        */
    }

    /**
     * Show the form for creating a new resource.
     *
     * @return \Illuminate\Http\Response
     */
    public function create()
    {
        $nextClient_id = (count ( session("clientes") ) + 1) ;

        return view ("clients.create", ["next" => $nextClient_id]) ;
    }

    /**
     * Store a newly created resource in storage.
     *
     * @param  \Illuminate\Http\Request  $request
     * @return \Illuminate\Http\Response
     */
    public function store(Request $request)
    {
        $sessionClientes = session ("clientes") ;

        $newClientData = [
            "id" => (int) $request->all() ["id"],
            "name" => $request->all() ["name"]
        ] ;

         $sessionClientes [] = $newClientData ;

         session(["clientes" => $sessionClientes ]) ;

         return redirect ()->route ("cliente.index") ;
    }

    /**
     * Display the specified resource.
     *
     * @param  int  $id
     * @return \Illuminate\Http\Response
     */
    public function show($id)
    {
        return view (
            "clients.show",
            [
                "client" => $this->user ( $id )
            ]
        ) ;
    }

    /**
     * Show the form for editing the specified resource.
     *
     * @param  int  $id
     * @return \Illuminate\Http\Response
     */
    public function edit ($id)
    {
        return view ("clients.edit", ["client" => $this->user ( $id )]) ;
    }

    /**
     * Update the specified resource in storage.
     *
     * @param  \Illuminate\Http\Request  $request
     * @param  int  $id
     * @return \Illuminate\Http\Response
     */
    public function update(Request $request, $id)
    {

        // dd (

        //     $request->all() [ "name" ],
        //     (int) $request->all() [ "id" ],
        //     session ("clientes") [ (int) $request->all() [ "id" ] ],

        // ) ;

        $sessionClientes = session("clientes") ;

        // $sessionClientes [ (int) $request->all() [ "id" ] ] [ "name" ] = $request->all() [ "name" ] ;

        $sessionClientes [ (int) $request->id - 1 ][ "name" ] = $request->all() [ "name" ] ;

        session(["clientes" => $sessionClientes]) ;

        return redirect()->route ("cliente.index") ;
    }

    /**
     * Remove the specified resource from storage.
     *
     * @param  int  $id
     * @return \Illuminate\Http\Response
     */
    public function destroy($id)
    {
        $sessionClientes = session("clientes") ;

        $columns =  array_column ($sessionClientes, "id") ;
        
        $arraySearch = array_search ($id, $columns) ;

        array_splice ($sessionClientes, $arraySearch) ;

        session(["clientes" => $sessionClientes]) ;

        return redirect()->route("cliente.index") ;
    }
}


/*

+--------+-----------+------------------------+-----------------+-------------------------------------------------+--------------+
| Domain | Method    | URI                    | Name            | Action                                          | Middleware   |
+--------+-----------+------------------------+-----------------+-------------------------------------------------+--------------+
|        | GET|HEAD  | /                                                     |                 | App\Http\Controllers\FirstController@index      | web          |

|        | GET|HEAD  | api/user                                      |                 | Closure                                         | api,auth:api |

|        | GET|HEAD  | cliente                                         | cliente.index   | App\Http\Controllers\ClienteController@index    | web          |

|        | POST      | cliente                                             | cliente.store   | App\Http\Controllers\ClienteController@store    | web          |

|        | GET|HEAD  | cliente/create                       | cliente.create  | App\Http\Controllers\ClienteController@create   | web          |

|        | GET|HEAD  | cliente/{cliente}                  | cliente.show    | App\Http\Controllers\ClienteController@show     | web          |

|        | PUT|PATCH | cliente/{cliente}               | cliente.update  | App\Http\Controllers\ClienteController@update   | web          |

|        | DELETE    | cliente/{cliente}                  | cliente.destroy | App\Http\Controllers\ClienteController@destroy  | web          |

|        | GET|HEAD  | cliente/{cliente}/edit    | cliente.edit    | App\Http\Controllers\ClienteController@edit     | web          |

|        | GET|HEAD  | multiplica/{a}/{b}          |                 | App\Http\Controllers\FirstController@multiplica | web          |

|        | GET|HEAD  | show-your-name         |                 | App\Http\Controllers\FirstController@showName   | web          |
+--------+-----------+------------------------+-----------------+-------------------------------------------------+--------------+

*/
````