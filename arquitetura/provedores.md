<!-- source_url: https://github.com/laravel/docs/blob/11.x/providers.md -->
<!-- revision: 910898f77947c560ed9e1017b15314229a4bd1b6 -->
<!-- status: ready -->

# Provedores de Serviços

- [Introdução](#introducao)
- [Escrevendo Provedores de Serviços](#escrevendo-provedores-de-servicos)
    - [O Método `register`](#o-metodo-register)
    - [O Método `boot`](o-metodo-boot)
- [Registrando Provedores](#registrando-provedores)
- [Provedores Adiados](#provedores-adiados)

## Introdução

Os provedores de serviços são o local central de toda a inicialização da
aplicação Laravel.
Sua aplicação, bem como todos os principais serviços do Laravel, são
inicializados por meio de provedores de serviços.

Mas, o que queremos dizer com “inicializados”? Em geral, queremos dizer **ao
registrar** coisas, incluindo registrar ligações de contêineres de serviços,
ouvintes de eventos, _middleware_ e até rotas.
Os provedores de serviços são o local central para configurar sua aplicação.

O Laravel usa dezenas de provedores de serviços internamente para inicializar
seus principais serviços, como `mailer`, `queue`, `cache` e outros.
Muitos desses provedores são provedores “adiados”, o que significa que não serão
carregados em todas as requisições, mas apenas quando os serviços que fornecem
forem realmente necessários.

Todos os provedores de serviços definidos pela pessoa usuária são registrados no
arquivo `bootstrap/providers.php`.
Na documentação a seguir, você aprenderá como escrever seus próprios provedores
de serviços e registrá-los em sua aplicação Laravel.

> **Nota:**
> Se você quiser saber mais sobre como o Laravel lida com requisições e como
> funciona internamente, verifique nossa documentação sobre o
> [ciclo de vida da requisição](ciclo-de-vida.md) do Laravel.

## Escrevendo Provedores de Serviços

Todos os provedores de serviços estendem a classe
`Illuminate\Support\ServiceProvider`.
A maioria dos provedores de serviços contém um método `register` e um método
`boot`.
No método `register`, você **só deve vincular coisas ao
[contêiner de serviços](conteiner.md)**.
Você nunca deve tentar registrar quaisquer ouvintes de eventos, rotas ou
qualquer outra funcionalidade no método `register`.

O Artisan CLI pode gerar um novo provedor por meio do comando `make:provider`.
O Laravel registrará automaticamente seu novo provedor no arquivo
`bootstrap/providers.php` da sua aplicação:

```shell
php artisan make:provider RiakServiceProvider
```

### O Método `register`

Conforme mencionado anteriormente, no método `register`, você só deve vincular
coisas ao [contêiner de serviços](conteiner.md).
Você nunca deve tentar registrar quaisquer ouvintes de eventos, rotas ou
qualquer outra funcionalidade no método `register`.
Caso contrário, você poderá usar acidentalmente um serviço fornecido por um
provedor de serviços que ainda não foi carregado.

Vamos dar uma olhada em um provedor de serviços básico.
Dentro de qualquer um dos métodos do seu provedor de serviços, você sempre tem
acesso à propriedade `$app` que fornece acesso ao contêiner de serviços:

```php
<?php

namespace App\Providers;

use App\Services\Riak\Connection;
use Illuminate\Contracts\Foundation\Application;
use Illuminate\Support\ServiceProvider;

class RiakServiceProvider extends ServiceProvider
{
    /**
     * Registra quaisquer serviços da aplicação.
     */
    public function register(): void
    {
        $this->app->singleton(Connection::class, function (Application $app) {
            return new Connection(config('riak'));
        });
    }
}
```

Este provedor de serviços define apenas um método `register` e usa esse método
para definir uma implementação de `App\Services\Riak\Connection` no contêiner de
serviços.
Se você ainda não se familiarizou com o contêiner de serviços do Laravel,
confira [sua documentação](conteiner.md).

#### As Propriedades `bindings` e `singletons`

Se o seu provedor de serviços registrar muitas ligações simples, você poderá usar
as propriedades `bindings` e `singletons` em vez de registrar manualmente cada
ligação de contêiner.
Quando o provedor de serviços for carregado pelo _framework_, ele verificará
automaticamente essas propriedades e registrará suas ligações:

```php
<?php

namespace App\Providers;

use App\Contracts\DowntimeNotifier;
use App\Contracts\ServerProvider;
use App\Services\DigitalOceanServerProvider;
use App\Services\PingdomDowntimeNotifier;
use App\Services\ServerToolsProvider;
use Illuminate\Support\ServiceProvider;

class AppServiceProvider extends ServiceProvider
{
    /**
     * Todas as ligações de contêiner que devem ser registradas.
     *
     * @var array
     */
    public $bindings = [
        ServerProvider::class => DigitalOceanServerProvider::class,
    ];

    /**
     * Todos os singletons de contêiner que devem ser registrados.
     *
     * @var array
     */
    public $singletons = [
        DowntimeNotifier::class => PingdomDowntimeNotifier::class,
        ServerProvider::class => ServerToolsProvider::class,
    ];
}
```

### O Método `boot`

Então, e se precisarmos registrar um
[compositor de visualização](../views.md#view-composers) em nosso provedor de
serviço?
Isso deve ser feito no método `boot`.
**Este método é chamado após todos os outros provedores de serviços terem sido
registrados**, ou seja, você tem acesso a todos os outros serviços registrados
pelo _framework_:

```php
<?php

namespace App\Providers;

use Illuminate\Support\Facades\View;
use Illuminate\Support\ServiceProvider;

class ComposerServiceProvider extends ServiceProvider
{
    /**
     * Inicializa quaisquer serviços da aplicação.
     */
    public function boot(): void
    {
        View::composer('view', function () {
            // ...
        });
    }
}
```

#### Injeção de Dependência no Método `boot`

Você pode declarar o tipo das dependências no método `boot` do seu provedor de
serviços.
O [contêiner de serviços](conteiner.md) injetará automaticamente todas as
dependências necessárias:

```php
use Illuminate\Contracts\Routing\ResponseFactory;

/**
 * Inicializa quaisquer serviços da aplicação.
 */
public function boot(ResponseFactory $response): void
{
    $response->macro('serialized', function (mixed $value) {
        // ...
    });
}
```

## Registrando Provedores de Serviços

Todos os provedores de serviços são registrados no arquivo de configuração
`bootstrap/providers.php`.
Este arquivo retorna um _array_ que contém os nomes das classes dos provedores
de serviços da sua aplicação:

```php
<?php

return [
    App\Providers\AppServiceProvider::class,
];
```

Quando você invoca o comando `make:provider` do Artisan, o Laravel adicionará
automaticamente o provedor gerado ao arquivo `bootstrap/providers.php`.
No entanto, se você criou manualmente a classe do provedor, deverá adicioná-la
manualmente ao _array_:

```php
<?php

return [
    App\Providers\AppServiceProvider::class,
    App\Providers\ComposerServiceProvider::class,
];
```

## Provedores Adiados

Se o seu provedor estiver registrando **apenas** ligações no
[contêiner de serviços](conteiner.md), você poderá optar por adiar seu registro
até que uma das ligações registradas seja realmente necessária.
Adiar o carregamento de tal provedor melhorará o desempenho da sua aplicação,
uma vez que ele não é carregado do sistema de arquivos a cada requisição.

O Laravel compila e armazena uma lista de todos os serviços fornecidos por
provedores de serviços adiados, juntamente com o nome da classe dos provedores
de serviços.
Assim, somente quando você tenta resolver um desses serviços o Laravel carrega o
provedor de serviços.

Para adiar o carregamento de um provedor, implemente a interface
`\Illuminate\Contracts\Support\DeferrableProvider` e defina um método
`provides`.
O método `provides` deve retornar as ligações do contêiner de serviços
registradas pelo provedor:

```php
<?php

namespace App\Providers;

use App\Services\Riak\Connection;
use Illuminate\Contracts\Foundation\Application;
use Illuminate\Contracts\Support\DeferrableProvider;
use Illuminate\Support\ServiceProvider;

class RiakServiceProvider extends ServiceProvider implements DeferrableProvider
{
    /**
     * Registra quaisquer serviços da aplicação.
     */
    public function register(): void
    {
        $this->app->singleton(Connection::class, function (Application $app) {
            return new Connection($app['config']['riak']);
        });
    }

    /**
     * Obtém os serviços fornecidos pelo provedor.
     *
     * @return array<int, string>
     */
    public function provides(): array
    {
        return [Connection::class];
    }
}
```
