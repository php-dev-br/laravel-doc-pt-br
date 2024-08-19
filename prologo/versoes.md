---
source_url: https://github.com/laravel/docs/blob/5.8/releases.md
revision: 886eb797d67bca559298e269b05afcabe08ac503
status: ready
---

# Notas de Versão

- [Esquema de Versionamento](#esquema-de-versionamento)
- [Política de Suporte](#politica-de-suporte)
- [Laravel 5.8](#laravel-5-8)

## Esquema de Versionamento

O esquema de versionamento do Laravel mantém a seguinte convenção:
`paradigm.major.minor`.
As versões maiores do _framework_ são lançadas a cada seis meses (em fevereiro e
agosto), enquanto as versões menores podem ser lançadas semanalmente.
Versões menores e de correções **nunca** devem conter alterações significativas.

Ao referenciar o _framework_ Laravel e seus componentes a partir da sua
aplicação ou pacote, você deve sempre usar uma restrição de versão como
`^5.8.*`, uma vez que as principais versões do Laravel incluem alterações
significativas.
No entanto, nos esforçamos para sempre garantir que você possa atualizar para
uma nova versão maior em um dia ou menos.

Os lançamentos que mudam o paradigma são separados por muitos anos e representam
mudanças fundamentais na arquitetura e nas convenções do _framework_.
Atualmente, não há nenhuma versão de mudança de paradigma em desenvolvimento.

## Política de Suporte

Para versões LTS, como o Laravel 5.5, as correções de falhas são fornecidas por
2
anos e as correções de segurança são fornecidas por 3 anos.
Essas versões oferecem a janela mais longa de suporte e manutenção.
Para versões gerais, as correções de falhas são fornecidas por 6 meses e as
correções de segurança são fornecidas por 1 ano.
Para todas as bibliotecas adicionais, incluindo Lumen, apenas a versão maior
mais recente recebe correções de falhas.

|  Versão   |       Lançamento        | Correções de falhas até | Correções de segurança até |
|:---------:|:-----------------------:|:-----------------------:|:--------------------------:|
|    5.0    | 4 de fevereiro de 2015  |   4 de agosto de 2015   |   4 de fevereiro de 2016   |
| 5.1 (LTS) |   9 de junho de 2015    |   9 de junho de 2017    |     9 de junho de 2018     |
|    5.2    | 21 de dezembro de 2015  |   21 de junho de 2016   |   21 de dezembro de 2016   |
|    5.3    |  23 de agosto de 2016   | 23 de fevereiro de 2017 |    23 de agosto de 2017    |
|    5.4    |  24 de janeiro de 2017  |   24 de julho de 2017   |   24 de janeiro de 2018    |
| 5.5 (LTS) |  30 de agosto de 2017   |  30 de agosto de 2019   |    30 de agosto de 2020    |
|    5.6    | 7 de fevereiro de 2018  |   7 de agosto de 2018   |   7 de fevereiro de 2019   |
|    5.7    |  4 de setembro de 2018  |   4 de março de 2019    |   4 de setembro de 2019    |
|    5.8    | 26 de fevereiro de 2019 |  26 de agosto de 2019   |  26 de fevereiro de 2020   |

## Laravel 5.8

O Laravel 5.8 continua as melhorias feitas no Laravel 5.7, introduzindo
relacionamentos `hasOneThrough` do Eloquent, validação de _e-mail_ aprimorada,
registro automático de políticas de autorização baseado em convenção, _cache_ e
_drivers_ de sessão do DynamoDB, configuração aprimorada de fuso horário do
agendador, suporte a atribuição de vários guardas de autenticação para canais de
transmissão, conformidade do _driver_ de _cache_ com a PSR-16, melhorias no
comando `artisan serve`, suporte ao PHPUnit, suporte ao Carbon 2.0, suporte ao
Pheanstalk 4.0 e uma variedade de outras correções de falhas e melhorias de
usabilidade.

### Relacionamento `hasOneThrough` do Eloquent

O Eloquent agora fornece suporte para o tipo de relacionamento `hasOneThrough`.
Por exemplo, imagine que um modelo `Supplier` tenha um modelo `Account` e um
modelo `Account` tenha um modelo `AccountHistory`.
Você pode usar um relacionamento `hasOneThrough` para acessar o `AccountHistory`
de um `Supplier` por meio do modelo `Account`:

```php
/**
 * Obtém o histórico da conta do fornecedor.
 */
public function accountHistory()
{
    return $this->hasOneThrough(AccountHistory::class, Account::class);
}
```

### Auto-Discovery Of Model Policies

When using Laravel 5.7, each model's
corresponding [authorization policy](authorization.md#creating-policies) needed
to be explicitly registered in your application's `AuthServiceProvider`:

    /**
     * The policy mappings for the application.
     *
     * @var array
     */
    protected $policies = [
        'App\User' => 'App\Policies\UserPolicy',
    ];

Laravel 5.8 introduces auto-discovery of model policies as long as the model and
policy follow standard Laravel naming conventions. Specifically, the policies
must be in a `Policies` directory below the directory that contains the models.
So, for example, the models may be placed in the `app` directory while the
policies may be placed in the `app/Policies` directory. In addition, the policy
name must match the model name and have a `Policy` suffix. So, a `User` model
would correspond to a `UserPolicy` class.

If you would like to provide your own policy discovery logic, you may register a
custom callback using the `Gate::guessPolicyNamesUsing` method. Typically, this
method should be called from your application's `AuthServiceProvider`:

    use Illuminate\Support\Facades\Gate;

    Gate::guessPolicyNamesUsing(function ($modelClass) {
        // return policy class name...
    });

> {note} Any policies that are explicitly mapped in your `AuthServiceProvider`
> will take precedence over any potential auto-discovered policies.

### PSR-16 Cache Compliance

In order to allow a more granular expiration time when storing items and provide
compliance with the PSR-16 caching standard, the cache item time-to-live has
changed from minutes to seconds. The `put`, `putMany`, `add`, `remember`
and `setDefaultCacheTime` methods of the `Illuminate\Cache\Repository` class and
its extended classes, as well as the `put` method of each cache store were
updated with this changed behavior.
See [the related PR](https://github.com/laravel/framework/pull/27276) for more
info.

If you are passing an integer to any of these methods, you should update your
code to ensure you are now passing the number of seconds you wish the item to
remain in the cache. Alternatively, you may pass a `DateTime` instance
indicating when the item should expire:

    // Laravel 5.7 - Store item for 30 minutes...
    Cache::put('foo', 'bar', 30);

    // Laravel 5.8 - Store item for 30 seconds...
    Cache::put('foo', 'bar', 30);

    // Laravel 5.7 / 5.8 - Store item for 30 seconds...
    Cache::put('foo', 'bar', now()->addSeconds(30));

### Multiple Broadcast Authentication Guards

In previous releases of Laravel, private and presence broadcast channels
authenticated the user via your application's default authentication guard.
Beginning in Laravel 5.8, you may now assign multiple guards that should
authenticate the incoming request:

    Broadcast::channel('channel', function() {
        // ...
    }, ['guards' => ['web', 'admin']])

### Token Guard Token Hashing

Laravel's `token` guard, which provides basic API authentication, now supports
storing API tokens as SHA-256 hashes. This provides improved security over
storing plain-text tokens. To learn more about hashed tokens, please review the
full [API authentication documentation](api-authentication.md).

> **Note:** While Laravel ships with a simple, token based authentication guard,
> we strongly recommend you consider using [Laravel Passport](passport.md) for
> robust, production applications that offer API authentication.

### Improved Email Validation

Laravel 5.8 introduces improvements to the validator's underlying email
validation logic by adopting the `egulias/email-validator` package utilized by
SwiftMailer. Laravel's previous email validation logic occasionally considered
valid emails, such as `example@bär.se`, to be invalid.

### Default Scheduler Timezone

Laravel allows you to customize the timezone of a scheduled task using
the `timezone` method:

    $schedule->command('inspire')
             ->hourly()
             ->timezone('America/Chicago');

However, this can become cumbersome and repetitive if you are specifying the
same timezone for all of your scheduled tasks. For that reason, you may now
define a `scheduleTimezone` method in your `app/Console/Kernel.php` file. This
method should return the default timezone that should be assigned to all
scheduled tasks:

    /**
     * Get the timezone that should be used by default for scheduled events.
     *
     * @return \DateTimeZone|string|null
     */
    protected function scheduleTimezone()
    {
        return 'America/Chicago';
    }

### Intermediate Table / Pivot Model Events

In previous versions of Laravel, [Eloquent model events](eloquent.md#events)
were not dispatched when attaching, detaching, or syncing custom intermediate
table / "pivot" models of a many-to-many relationship. When
using [custom intermediate table models](eloquent-relationships.md#defining-custom-intermediate-table-models)
in Laravel 5.8, the applicable model events will now be dispatched.

### Artisan Call Improvements

Laravel allows you to invoke Artisan via the `Artisan::call` method. In previous
releases of Laravel, the command's options are passed via an array as the second
argument to the method:

    use Illuminate\Support\Facades\Artisan;

    Artisan::call('migrate:install', ['database' => 'foo']);

However, Laravel 5.8 allows you to pass the entire command, including options,
as the first string argument to the method:

    Artisan::call('migrate:install --database=foo');

### Mock / Spy Testing Helper Methods

In order to make mocking objects more convenient, new `mock` and `spy` methods
have been added to the base Laravel test case class. These methods automatically
bind the mocked class into the container. For example:

    // Laravel 5.7
    $this->instance(Service::class, Mockery::mock(Service::class, function ($mock) {
        $mock->shouldReceive('process')->once();
    }));

    // Laravel 5.8
    $this->mock(Service::class, function ($mock) {
        $mock->shouldReceive('process')->once();
    });

### Eloquent Resource Key Preservation

When returning an [Eloquent resource collection](eloquent-resources.md) from a
route, Laravel resets the collection's keys so that they are in simple numerical
order:

    use App\User;
    use App\Http\Resources\User as UserResource;

    Route::get('/user', function () {
        return UserResource::collection(User::all());
    });

When using Laravel 5.8, you may now add a `preserveKeys` property to your
resource class indicating if collection keys should be preserved. By default,
and to maintain consistency with previous Laravel releases, the keys will be
reset by default:

    <?php

    namespace App\Http\Resources;

    use Illuminate\Http\Resources\Json\JsonResource;

    class User extends JsonResource
    {
        /**
         * Indicates if the resource's collection keys should be preserved.
         *
         * @var bool
         */
        public $preserveKeys = true;
    }

When the `preserveKeys` property is set to `true`, collection keys will be
preserved:

    use App\User;
    use App\Http\Resources\User as UserResource;

    Route::get('/user', function () {
        return UserResource::collection(User::all()->keyBy->id);
    });

### Higher Order `orWhere` Eloquent Method

In previous releases of Laravel, combining multiple Eloquent model scopes via
an `or` query operator required the use of Closure callbacks:

    // scopePopular and scopeActive methods defined on the User model...
    $users = App\User::popular()->orWhere(function (Builder $query) {
        $query->active();
    })->get();

Laravel 5.8 introduces a "higher order" `orWhere` method that allows you to
fluently chain these scopes together without the use of Closures:

    $users = App\User::popular()->orWhere->active()->get();

### Artisan Serve Improvements

In previous releases of Laravel, Artisan's `serve` command would serve your
application on port `8000`. If another `serve` command process was already
listening on this port, an attempt to serve a second application via `serve`
would fail. Beginning in Laravel 5.8, `serve` will now scan for available ports
up to port `8009`, allowing you to serve multiple applications at once.

### Blade File Mapping

When compiling Blade templates, Laravel now adds a comment to the top of the
compiled file which contains the path to the original Blade template.

### DynamoDB Cache / Session Drivers

Laravel 5.8 introduces [DynamoDB](https://aws.amazon.com/dynamodb/) cache and
session drivers. DynamoDB is a serverless NoSQL database provided by Amazon Web
Services. The default configuration for the `dynamodb` cache driver can be found
in the Laravel
5.8 [cache configuration file](https://github.com/laravel/laravel/blob/master/config/cache.php).

### Carbon 2.0 Support

Laravel 5.8 provides support for the `~2.0` release of the Carbon date
manipulation library.

### Pheanstalk 4.0 Support

Laravel 5.8 provides support for the `~4.0` release of the Pheanstalk queue
library. If you are using Pheanstalk library in your application, please upgrade
your library to the `~4.0` release via Composer.
