<!-- source_url: https://github.com/laravel/docs/blob/11.x/releases.md -->
<!-- revision: 2099c290d28e19aac91eed068a986231fa2447f7 -->
<!-- status: wip -->

# Notas de Versão

- [Esquema de Versionamento](#esquema-de-versionamento)
- [Política de Suporte](#politica-de-suporte)
- [Laravel 11](#laravel-11)

## Esquema de Versionamento

O Laravel e seus pacotes seguem o
[Versionamento Semântico](https://semver.org/lang/pt-BR/).
As versões maiores do framework são lançadas todos os anos (por volta do 1º
trimestre), enquanto as versões menores e de correções podem ser lançadas
semanalmente.
Versões menores e de correções **nunca** devem conter alterações significativas.

Ao referenciar o framework Laravel e seus componentes a partir de sua aplicação
ou pacote, você deve sempre usar uma restrição de versão como `^11.0`, uma vez
que as principais versões do Laravel incluem alterações significativas.
No entanto, nos esforçamos para sempre garantir que você possa atualizar para
uma nova versão maior em um dia ou menos.

### Argumentos Nomeados

[Argumentos nomeados](/docs/php/doc/8.3/functions.arguments.html#functions.named-arguments)
não são cobertos pelas diretrizes de compatibilidade com versões anteriores do
Laravel.
Podemos optar por renomear os argumentos de funções quando necessário para
melhorar a base de código do Laravel.
Portanto, o uso de argumentos nomeados ao chamar métodos do Laravel deve ser
feito com cautela e com o entendimento de que os nomes dos parâmetros podem
mudar no futuro.

## Política de Suporte

Para todas as versões do Laravel, as correções de falhas são fornecidas por 18
meses e as correções de segurança são fornecidas por 2 anos.
Para todas as bibliotecas adicionais, incluindo Lumen, apenas a versão maior
mais recente recebe correções de falhas.
Além disso, revise as versões do banco de dados
[suportadas pelo Laravel](../database.md#introduction).

|              Versão               |  PHP (*)  |       Lançamento        | Correções de falhas até | Correções de segurança até |
|:---------------------------------:|:---------:|:-----------------------:|:-----------------------:|:--------------------------:|
| <span class="bg-red-500">9</span> | 8.0 - 8.2 | 8 de fevereiro de 2022  |   8 de agosto de 2023   |   6 de fevereiro de 2024   |
|                10                 | 8.1 - 8.3 | 14 de fevereiro de 2023 |   6 de agosto de 2024   |   4 de fevereiro de 2025   |
|                11                 | 8.2 - 8.3 |   12 de março de 2024   |  3 de setembro de 2025  |    12 de março de 2026     |
|                12                 | 8.2 - 8.3 |  1º trimestre de 2025   |  3º trimestre de 2026   |    1º trimestre de 2027    |

<div class="version-colors">
  <div class="end-of-life">
    <div class="color-box"></div>
    <div>Fim da vida</div>
  </div>
  <div class="security-fixes">
    <div class="color-box"></div>
    <div>Apenas correções de segurança</div>
  </div>
</div>

(*) Versões suportadas do PHP

## Laravel 11

O Laravel 11 continua as melhorias feitas no Laravel 10.x, introduzindo uma
estrutura de aplicação simplificada, limitação de taxa por segundo, roteamento
de integridade, rotação elegante de chaves de criptografia, melhorias nos testes
de fila, transporte de e-mail [Resend](https://resend.com), integração do
validador de Prompt, novos comandos do Artisan e muito mais.
Além disso, o Laravel Reverb, um servidor WebSocket próprio escalonável, foi
introduzido para fornecer recursos robustos em tempo real para suas aplicações.

### PHP 8.2 {: #php-8-2 }

O Laravel 11.x requer no mínimo a versão 8.2 do PHP.

### Estrutura Simplificada de Aplicação

_A estrutura simplificada da aplicação Laravel foi desenvolvida por
[Taylor Otwell](https://github.com/taylorotwell) e
[Nuno Maduro](https://github.com/nunomaduro)_.

O Laravel 11 introduz uma estrutura simplificada de aplicação para **novas**
aplicações Laravel, sem exigir nenhuma alteração nas aplicações existentes.
A nova estrutura de aplicação pretende fornecer uma experiência mais enxuta e
moderna, mantendo muitos dos conceitos com os quais as pessoas desenvolvedoras
Laravel já estão familiarizadas.
A seguir discutiremos os destaques da nova estrutura de aplicação do Laravel.

#### O Arquivo de Inicialização da Aplicação

O arquivo `bootstrap/app.php` foi revitalizado como um arquivo de configuração
da aplicação com foco em código.
A partir deste arquivo, agora você pode personalizar o roteamento, o middleware,
os provedores de serviços, o tratamento de exceções da sua aplicação e muito
mais.
Este arquivo unifica uma variedade de configurações de comportamento de alto
nível das aplicações que antes estavam espalhadas pela estrutura de arquivos da
sua aplicação:

```php
return Application::configure(basePath: dirname(__DIR__))
    ->withRouting(
        web: __DIR__.'/../routes/web.php',
        commands: __DIR__.'/../routes/console.php',
        health: '/up',
    )
    ->withMiddleware(function (Middleware $middleware) {
        //
    })
    ->withExceptions(function (Exceptions $exceptions) {
        //
    })->create();
```

#### Provedores de Serviço

Em vez da estrutura padrão da aplicação Laravel contendo cinco provedores de
serviços, o Laravel 11 inclui apenas um único `AppServiceProvider`.
A funcionalidade dos provedores de serviços anteriores foi incorporada ao
`bootstrap/app.php`, é tratada automaticamente pelo framework ou pode ser
colocada no `AppServiceProvider` da sua aplicação.

Por exemplo, a descoberta de eventos agora está habilitada por padrão,
eliminando grande parte da necessidade do registro manual de eventos e seus
ouvintes.
No entanto, se precisar registrar eventos manualmente, você pode simplesmente
fazê-lo no `AppServiceProvider`.
Da mesma forma, os vínculos de modelo de rota ou portas de autorização que você
pode ter registrado anteriormente no `AuthServiceProvider` também podem ser
registradas no `AppServiceProvider`.

#### Desabilitação do Roteamento de Broadcast e da API

Os arquivos de rotas `api.php` e `channels.php` não estão mais presentes por
padrão, pois muitas aplicações não requerem esses arquivos.
Em vez disso, eles podem ser criados usando comandos simples do Artisan:

```shell
php artisan install:api

php artisan install:broadcasting
```

#### Middleware

Anteriormente, as novas aplicações Laravel incluíam nove middlewares.
Esses middlewares executavam uma variedade de tarefas, como autenticação de
requisições, filtragem de strings de entrada e validação de tokens CSRF.

No Laravel 11, esses middlewares foram movidos para o próprio framework, para
não adicionarem volume à estrutura da sua aplicação.
Novos métodos para personalizar o comportamento desses middlewares foram
adicionados ao framework e podem ser invocados a partir do arquivo
`bootstrap/app.php` da sua aplicação:

```php
->withMiddleware(function (Middleware $middleware) {
    $middleware->validateCsrfTokens(
        except: ['stripe/*']
    );

    $middleware->web(append: [
        EnsureUserIsSubscribed::class,
    ])
})
```

Como todo middleware pode ser facilmente personalizado por meio do arquivo
`bootstrap/app.php` da sua aplicação, a necessidade de uma classe de "kernel"
HTTP separada foi eliminada.

#### Agendamento

Usando uma nova fachada `Schedule`, as tarefas agendadas agora podem ser
definidas diretamente no arquivo `routes/console.php` da sua aplicação,
eliminando a necessidade de uma classe "kernel" de console separada:

```php
use Illuminate\Support\Facades\Schedule;

Schedule::command('emails:send')->daily();
```

#### Tratamento de Exceções

Assim como o roteamento e o middleware, o tratamento de exceções agora pode ser
personalizado a partir do arquivo `bootstrap/app.php` da sua aplicação, em vez
de uma classe separada de manipulador de exceções, reduzindo o número total de
arquivos incluídos em uma nova aplicação Laravel:

```php
->withExceptions(function (Exceptions $exceptions) {
    $exceptions->dontReport(MissedFlightException::class);

    $exceptions->report(function (InvalidOrderException $e) {
        // ...
    });
})
```

#### Base `Controller` Class

The base controller included in new Laravel applications has been simplified.
It no longer extends Laravel's internal `Controller` class, and
the `AuthorizesRequests` and `ValidatesRequests` traits have been removed, as
they may be included in your application's individual controllers if desired:

    <?php

    namespace App\Http\Controllers;

    abstract class Controller
    {
        //
    }

<a name="application-defaults"></a>

#### Application Defaults

By default, new Laravel applications use SQLite for database storage, as well as
the `database` driver for Laravel's session, cache, and queue.
This allows you to begin building your application immediately after creating a
new Laravel application, without being required to install additional software
or create additional database migrations.

In addition, over time, the `database` drivers for these Laravel services have
become robust enough for production usage in many application contexts;
therefore, they provide a sensible, unified choice for both local and production
applications.

<a name="reverb"></a>

### Laravel Reverb

_Laravel Reverb was developed by [Joe Dixon](https://github.com/joedixon)_.

[Laravel Reverb](https://reverb.laravel.com) brings blazing-fast and scalable
real-time WebSocket communication directly to your Laravel application, and
provides seamless integration with Laravel’s existing suite of event
broadcasting tools, such as Laravel Echo.

```shell
php artisan reverb:start
```

In addition, Reverb supports horizontal scaling via Redis's publish / subscribe
capabilities, allowing you to distribute your WebSocket traffic across multiple
backend Reverb servers all supporting a single, high-demand application.

For more information on Laravel Reverb, please consult the
complete [Reverb documentation](reverb.md).

<a name="rate-limiting"></a>

### Per-Second Rate Limiting

_Per-second rate limiting was contributed
by [Tim MacDonald](https://github.com/timacdonald)_.

Laravel now supports "per-second" rate limiting for all rate limiters, including
those for HTTP requests and queued jobs.
Previously, Laravel's rate limiters were limited to "per-minute" granularity:

```php
RateLimiter::for('invoices', function (Request $request) {
    return Limit::perSecond(1);
});
```

For more information on rate limiting in Laravel, check out
the [rate limiting documentation](routing.md#rate-limiting).

<a name="health"></a>

### Health Routing

_Health routing was contributed
by [Taylor Otwell](https://github.com/taylorotwell)_.

New Laravel 11 applications include a `health` routing directive, which
instructs Laravel to define a simple health-check endpoint that may be invoked
by third-party application health monitoring services or orchestration systems
like Kubernetes.
By default, this route is served at `/up`:

```php
->withRouting(
    web: __DIR__.'/../routes/web.php',
    commands: __DIR__.'/../routes/console.php',
    health: '/up',
)
```

When HTTP requests are made to this route, Laravel will also dispatch
a `DiagnosingHealth` event, allowing you to perform additional health checks
that are relevant to your application.

<a name="encryption"></a>

### Graceful Encryption Key Rotation

_Graceful encryption key rotation was contributed
by [Taylor Otwell](https://github.com/taylorotwell)_.

Since Laravel encrypts all cookies, including your application's session cookie,
essentially every request to a Laravel application relies on encryption.
However, because of this, rotating your application's encryption key would log
all users out of your application.
In addition, decrypting data that was encrypted by the previous encryption key
becomes impossible.

Laravel 11 allows you to define your application's previous encryption keys as a
comma-delimited list via the `APP_PREVIOUS_KEYS` environment variable.

When encrypting values, Laravel will always use the "current" encryption key,
which is within the `APP_KEY` environment variable.
When decrypting values, Laravel will first try the current key.
If decryption fails using the current key, Laravel will try all previous keys
until one of the keys is able to decrypt the value.

This approach to graceful decryption allows users to keep using your application
uninterrupted even if your encryption key is rotated.

For more information on encryption in Laravel, check out
the [encryption documentation](encryption.md).

<a name="automatic-password-rehashing"></a>

### Automatic Password Rehashing

_Automatic password rehashing was contributed
by [Stephen Rees-Carter](https://github.com/valorin)_.

Laravel's default password hashing algorithm is bcrypt.
The "work factor" for bcrypt hashes can be adjusted via the `config/hashing.php`
configuration file or the `BCRYPT_ROUNDS` environment variable.

Typically, the bcrypt work factor should be increased over time as CPU / GPU
processing power increases.
If you increase the bcrypt work factor for your application, Laravel will now
gracefully and automatically rehash user passwords as users authenticate with
your application.

<a name="prompt-validation"></a>

### Prompt Validation

_Prompt validator integration was contributed
by [Andrea Marco Sartori](https://github.com/cerbero90)_.

[Laravel Prompts](prompts.md) is a PHP package for adding beautiful and
user-friendly forms to your command-line applications, with browser-like
features including placeholder text and validation.

Laravel Prompts supports input validation via closures:

```php
$name = text(
    label: 'What is your name?',
    validate: fn (string $value) => match (true) {
        strlen($value) < 3 => 'The name must be at least 3 characters.',
        strlen($value) > 255 => 'The name must not exceed 255 characters.',
        default => null
    }
);
```

However, this can become cumbersome when dealing with many inputs or complicated
validation scenarios.
Therefore, in Laravel 11, you may utilize the full power of
Laravel's [validator](validation.md) when validating prompt inputs:

```php
$name = text('What is your name?', validate: [
    'name' => 'required|min:3|max:255',
]);
```

<a name="queue-interaction-testing"></a>

### Queue Interaction Testing

_Queue interaction testing was contributed
by [Taylor Otwell](https://github.com/taylorotwell)_.

Previously, attempting to test that a queued job was released, deleted, or
manually failed was cumbersome and required the definition of custom queue fakes
and stubs.
However, in Laravel 11, you may easily test for these queue interactions using
the `withFakeQueueInteractions` method:

```php
use App\Jobs\ProcessPodcast;

$job = (new ProcessPodcast)->withFakeQueueInteractions();

$job->handle();

$job->assertReleased(delay: 30);
```

For more information on testing queued jobs, check out
the [queue documentation](queues.md#testing).

<a name="new-artisan-commands"></a>

### New Artisan Commands

_Class creation Artisan commands were contributed
by [Taylor Otwell](https://github.com/taylorotwell)_.

New Artisan commands have been added to allow the quick creation of classes,
enums, interfaces, and traits:

```shell
php artisan make:class
php artisan make:enum
php artisan make:interface
php artisan make:trait
```

<a name="model-cast-improvements"></a>

### Model Casts Improvements

_Model casts improvements were contributed
by [Nuno Maduro](https://github.com/nunomaduro)_.

Laravel 11 supports defining your model's casts using a method instead of a
property.
This allows for streamlined, fluent cast definitions, especially when using
casts with arguments:

    /**
     * Get the attributes that should be cast.
     *
     * @return array<string, string>
     */
    protected function casts(): array
    {
        return [
            'options' => AsCollection::using(OptionCollection::class),
                      // AsEncryptedCollection::using(OptionCollection::class),
                      // AsEnumArrayObject::using(OptionEnum::class),
                      // AsEnumCollection::using(OptionEnum::class),
        ];
    }

For more information on attribute casting, review
the [Eloquent documentation](eloquent-mutators.md#attribute-casting).

<a name="the-once-function"></a>

### The `once` Function

_The `once` helper was contributed
by [Taylor Otwell](https://github.com/taylorotwell)_ and
_[Nuno Maduro](https://github.com/nunomaduro)_.

The `once` helper function executes the given callback and caches the result in
memory for the duration of the request.
Any subsequent calls to the `once` function with the same callback will return
the previously cached result:

    function random(): int
    {
        return once(function () {
            return random_int(1, 1000);
        });
    }

    random(); // 123
    random(); // 123 (cached result)
    random(); // 123 (cached result)

For more information on the `once` helper, check out
the [helpers documentation](helpers.md#method-once).

<a name="database-performance"></a>

### Improved Performance When Testing With In-Memory Databases

_Improved in-memory database testing performance was contributed
by [Anders Jenbo](https://github.com/AJenbo)_

Laravel 11 offers a significant speed boost when using the `:memory:` SQLite
database during testing.
To accomplish this, Laravel now maintains a reference to PHP's PDO object and
reuses it across connections, often cutting total test run time in half.

<a name="mariadb"></a>

### Improved Support for MariaDB

_Improved support for MariaDB was contributed
by [Jonas Staudenmeir](https://github.com/staudenmeir)
and [Julius Kiekbusch](https://github.com/Jubeki)_

Laravel 11 includes improved support for MariaDB.
In previous Laravel releases, you could use MariaDB via Laravel's MySQL driver.
However, Laravel 11 now includes a dedicated MariaDB driver which provides
better defaults for this database system.

For more information on Laravel's database drivers, check out
the [database documentation](database.md).

<a name="inspecting-database"></a>

### Inspecting Databases and Improved Schema Operations

_Improved schema operations and database inspection was contributed
by [Hafez Divandari](https://github.com/hafezdivandari)_

Laravel 11 provides additional database schema operation and inspection methods,
including the native modifying, renaming, and dropping of columns.
Furthermore, advanced spatial types, non-default schema names, and native schema
methods for manipulating tables, views, columns, indexes, and foreign keys are
provided:

    use Illuminate\Support\Facades\Schema;

    $tables = Schema::getTables();
    $views = Schema::getViews();
    $columns = Schema::getColumns('users');
    $indexes = Schema::getIndexes('users');
    $foreignKeys = Schema::getForeignKeys('users');
