---
source_url: https://github.com/laravel/docs/blob/9.x/releases.md
revision: 525c23bd8a5b43addcef2edf983e2e1e2f74a3e5
status: ready
---

# Notas de Versão

- [Esquema de Versionamento](#esquema-de-versionamento)
- [Política de Suporte](#politica-de-suporte)
- [Laravel 9](#laravel-9)

## Esquema de Versionamento

O Laravel e seus outros pacotes originais seguem o
[Versionamento Semântico](https://semver.org/lang/pt-BR/).
As versões maiores do _framework_ são lançadas todos os anos (por volta de
fevereiro), enquanto as versões menores e de correções podem ser lançadas
semanalmente.
Versões menores e de correções **nunca** devem conter alterações significativas.

Ao referenciar o _framework_ Laravel e seus componentes a partir da sua
aplicação ou pacote, você deve sempre usar uma restrição de versão como `^9.0`,
uma vez que as principais versões do Laravel incluem alterações significativas.
No entanto, nos esforçamos para sempre garantir que você possa atualizar para
uma nova versão maior em um dia ou menos.

### Argumentos Nomeados

[Argumentos nomeados](/docs/php/doc/8/functions.arguments.html#functions.named-arguments)
não são cobertos pelas diretrizes de compatibilidade com versões anteriores do
Laravel.
Podemos optar por renomear os argumentos de funções quando necessário para
melhorar a base de código do Laravel.
Portanto, o uso de argumentos nomeados ao chamar métodos do Laravel deve ser
feito com cautela e com o entendimento de que os nomes dos parâmetros podem
mudar no futuro.

## Política de Suporte

Para todas as versões do Laravel, as correções de erros são fornecidas por 18
meses e as correções de segurança são fornecidas por 2 anos.
Para todas as bibliotecas adicionais, incluindo Lumen, apenas a versão maior
mais recente recebe correções de erros.
Além disso, revise as versões do banco de dados
[suportadas pelo Laravel](../database.md#introduction).

| Versão  |  PHP (*)  |       Lançamento        | Correções de erros até | Correções de segurança até |
|:-------:|:---------:|:-----------------------:|:----------------------:|:--------------------------:|
| 6 (LTS) | 7.2 - 8.0 |  3 de setembro de 2019  | 25 de janeiro de 2022  |   6 de setembro de 2022    |
|    7    | 7.2 - 8.0 |   3 de março de 2020    |  6 de outubro de 2020  |     3 de março de 2021     |
|    8    | 7.3 - 8.1 |  8 de setembro de 2020  |  26 de julho de 2022   |   24 de janeiro de 2023    |
|    9    | 8.0 - 8.2 | 8 de fevereiro de 2022  |  8 de agosto de 2023   |   6 de fevereiro de 2024   |
|   10    | 8.1 - 8.3 | 14 de fevereiro de 2023 |  6 de agosto de 2024   |   4 de fevereiro de 2025   |

<div class="version-colors">
  <div class="end-of-life">
    <div class="color-box"></div>
    <div>Fim de vida</div>
  </div>
  <div class="security-fixes">
    <div class="color-box"></div>
    <div>Apenas correções de segurança</div>
  </div>
</div>

(*) Versões suportadas do PHP

## Laravel 9

Como você deve saber, o Laravel fez a transição para versões anuais com o
lançamento do Laravel 8.
Anteriormente, as versões principais eram lançadas a cada 6 meses.
Essa transição visa aliviar a carga de manutenção da comunidade e desafiar nosso
time de desenvolvimento a lançar novos recursos incríveis e poderosos sem
introduzir alterações significativas.
Portanto, lançamos uma variedade de recursos robustos no Laravel 8 sem quebrar a
compatibilidade com versões anteriores, como suporte a testes paralelos, _kits_
aprimorados do Breeze para iniciantes, melhorias no cliente HTTP e até mesmo
novos tipos de relacionamento do Eloquent, como “tem um de muitos”.

Portanto, esse compromisso de lançar novos recursos excelentes durante a versão
atual provavelmente fará com que futuras versões “principais” sejam usadas
principalmente para tarefas de “manutenção”, como atualização das dependências
originais, que podem ser vistas nestas notas de versão.

O Laravel 9 continua as melhorias feitas no Laravel 8.x, introduzindo suporte
para componentes do Symfony 6.0, Symfony Mailer, Flysystem 3.0, saída aprimorada
do comando `route:list`, um _driver_ de banco de dados do Laravel Scout, nova
sintaxe de acessadores/modificadores do Eloquent, vinculações de rota implícitas
via Enums, e uma variedade de outras correções de falhas e melhorias de
usabilidade.

### PHP 8.0 {: #php-8-0 }

O Laravel 9.x requer no mínimo a versão 8.0 do PHP.

### Symfony Mailer

_O suporte ao Symfony Mailer foi contribuído por
[Dries Vints](https://github.com/driesvints),
[James Brooks](https://github.com/jbrooksuk) e
[Julius Kiekbusch](https://github.com/Jubeki)_.

Versões anteriores do Laravel utilizavam a biblioteca
[Swift Mailer](https://swiftmailer.symfony.com/docs/introduction.html)
para enviar emails.
No entanto, essa biblioteca não é mais mantida, sendo sucedida pelo Symfony
Mailer.

Revise o [guia de atualização](../upgrade.md#symfony-mailer) para saber mais
sobre como garantir que sua aplicação seja compatível com o Symfony Mailer.

### Flysystem 3.x {: #flysystem-3-x }

_O suporte ao Flysystem 3.x foi contribuído por
[Dries Vints](https://github.com/driesvints)_.

O Laravel 9.x atualiza nossa dependência original do Flysystem para Flysystem
3.x.
O Flysystem dá suporte a todas as interações do sistema de arquivos oferecidas
pela fachada `Storage`.

Revise o [guia de atualização](../upgrade.md#flysystem-3) para saber mais sobre
como garantir que sua aplicação seja compatível com Flysystem 3.x.

### Acessadores / Modificadores Aprimorados do Eloquent

_Os acessadores/modificadores aprimorados do Eloquent foram contribuídos por
[Taylor Otwell](https://github.com/taylorotwell)_.

O Laravel 9.x oferece uma nova maneira de definir
[acessadores e modificadores](../eloquent-mutators.md#accessors-and-mutators) do
Eloquent.
Nas versões anteriores do Laravel, a única maneira de definir acessadores e
modificadores era definindo métodos prefixados no seu modelo da seguinte forma:

```php
public function getNameAttribute($value)
{
    return strtoupper($value);
}

public function setNameAttribute($value)
{
    $this->attributes['name'] = $value;
}
```

No entanto, no Laravel 9.x você pode definir um acessador e um modificador
usando um método único e não prefixado, declarando um tipo de retorno
`Illuminate\Database\Eloquent\Casts\Attribute`:

```php
use Illuminate\Database\Eloquent\Casts\Attribute;

public function name(): Attribute
{
    return new Attribute(
        get: fn ($value) => strtoupper($value),
        set: fn ($value) => $value,
    );
}
```

Além disso, esta nova abordagem para definir acessadores armazenará em _cache_
os valores dos objetos retornados pelo atributo, assim como as
[classes de conversão personalizadas](../eloquent-mutators.md#custom-casts):

```php
use App\Support\Address;
use Illuminate\Database\Eloquent\Casts\Attribute;

public function address(): Attribute
{
    return new Attribute(
        get: fn ($value, $attributes) => new Address(
            $attributes['address_line_one'],
            $attributes['address_line_two'],
        ),
        set: fn (Address $value) => [
            'address_line_one' => $value->lineOne,
            'address_line_two' => $value->lineTwo,
        ],
    );
}
```

### Conversão de Atributos do Eloquent em _Enums_

> **Aviso**: a conversão em _enums_ está disponível apenas a partir do PHP 8.1.

_A conversão em enums foi contribuída por
[Mohamed Said](https://github.com/themsaid)_.

O Eloquent agora permite que você converta seus valores de atributos em
[_enums_ “apoiadas”](/docs/php/doc/8/language.enumerations.backed.html) do PHP.
Para fazer isso, você pode especificar o atributo e a _enum_ que deseja
converter no array de propriedades `$casts` do seu modelo:

```php
use App\Enums\ServerStatus;

/**
 * Os atributos que devem ser convertidos.
 *
 * @var array
 */
protected $casts = [
    'status' => ServerStatus::class,
];
```

Após definir a conversão no seu modelo, o atributo especificado será convertido
automaticamente de e para uma _enum_ quando você interagir com o atributo:

```php
if ($server->status == ServerStatus::Provisioned) {
    $server->status = ServerStatus::Ready;

    $server->save();
}
```

### Vinculações Implícitas de Rotas com _Enums_

_As vinculações implícitas com enums foram contribuídas por
[Nuno Maduro](https://github.com/nunomaduro)_.

O PHP 8.1 introduziu o suporte a
[_enums_](/docs/php/doc/8/language.enumerations.backed.html).
O Laravel 9.x introduz a capacidade de declarar o tipo `enum` na sua definição
de rota e o Laravel só invocará a rota se esse segmento de rota for um valor
de _enum_ válido no URI.
Caso contrário, uma resposta HTTP 404 será retornada automaticamente.
Por exemplo, dada a seguinte _enum_:

```php
enum Category: string
{
    case Fruits = 'frutas';
    case People = 'pessoas';
}
```

Você pode definir uma rota que só será invocada se o segmento da rota
`{category}` for `frutas` ou `pessoas`.
Caso contrário, uma resposta HTTP 404 será retornada:

```php
Route::get('/categories/{category}', function (Category $category) {
    return $category->value;
});
```

### Vinculações de Rota com Escopo Forçado

_As vinculações com escopo forçado foram contribuídas por
[Claudio Dekker](https://github.com/claudiodekker)_.

Em versões anteriores do Laravel, você poderia querer definir o escopo do
segundo modelo do Eloquent em uma definição de rota de forma que ele seja filho
do modelo anterior do Eloquent.
Por exemplo, considere esta definição de rota que recupera uma postagem de
_blog_ por _slug_ para uma pessoa usuária específica:

```php
use App\Models\Post;
use App\Models\User;

Route::get('/users/{user}/posts/{post:slug}', function (User $user, Post $post) {
    return $post;
});
```

Ao usar uma vinculação implícita com chave personalizada como parâmetro aninhado
de rota, o Laravel irá automaticamente definir o escopo da consulta para
recuperar o modelo aninhado por seu pai usando convenções para adivinhar o nome
do relacionamento no pai.
No entanto, este comportamento só era suportado anteriormente pelo Laravel
quando uma chave personalizada era usada para a vinculação da rota filha.

No entanto, no Laravel 9.x, agora você pode instruir o Laravel a definir o
escopo das vinculações “filhas” mesmo quando uma chave personalizada não é
fornecida.
Para fazer isso, você pode invocar o método `scopeBindings` ao definir sua rota:

```php
use App\Models\Post;
use App\Models\User;

Route::get('/users/{user}/posts/{post}', function (User $user, Post $post) {
    return $post;
})->scopeBindings();
```

Ou você pode instruir um grupo inteiro de definições de rota para usar
vinculações com escopo definido:

```php
Route::scopeBindings()->group(function () {
    Route::get('/users/{user}/posts/{post}', function (User $user, Post $post) {
        return $post;
    });
});
```

### Controller Route Groups

_Route group improvements were contributed
by [Luke Downing](https://github.com/lukeraymonddowning)_.

You may now use the `controller` method to define the common controller for all
of the routes within the group. Then, when defining the routes, you only need to
provide the controller method that they invoke:

    use App\Http\Controllers\OrderController;

    Route::controller(OrderController::class)->group(function () {
        Route::get('/orders/{id}', 'show');
        Route::post('/orders', 'store');
    });

<a name="full-text"></a>

### Full Text Indexes / Where Clauses

_Full text indexes and "where" clauses were contributed
by [Taylor Otwell](https://github.com/taylorotwell)
and [Dries Vints](https://github.com/driesvints)_.

When using MySQL or PostgreSQL, the `fullText` method may now be added to column
definitions to generate full text indexes:

    $table->text('bio')->fullText();

In addition, the `whereFullText` and `orWhereFullText` methods may be used to
add full text "where" clauses to a query for columns that
have [full text indexes](/docs/{{version}}/migrations#available-index-types).
These methods will be transformed into the appropriate SQL for the underlying
database system by Laravel. For example, a `MATCH AGAINST` clause will be
generated for applications utilizing MySQL:

    $users = DB::table('users')
               ->whereFullText('bio', 'web developer')
               ->get();

<a name="laravel-scout-database-engine"></a>

### Laravel Scout Database Engine

_The Laravel Scout database engine was contributed
by [Taylor Otwell](https://github.com/taylorotwell)
and [Dries Vints](https://github.com/driesvints)_.

If your application interacts with small to medium sized databases or has a
light workload, you may now use Scout's "database" engine instead of a dedicated
search service such as Algolia or MeiliSearch. The database engine will use "
where like" clauses and full text indexes when filtering results from your
existing database to determine the applicable search results for your query.

To learn more about the Scout database engine, consult
the [Scout documentation](/docs/{{version}}/scout).

<a name="rendering-inline-blade-templates"></a>

### Rendering Inline Blade Templates

_Rendering inline Blade templates was contributed
by [Jason Beggs](https://github.com/jasonlbeggs). Rendering inline Blade
components was contributed by [Toby Zerner](https://github.com/tobyzerner)_.

Sometimes you may need to transform a raw Blade template string into valid HTML.
You may accomplish this using the `render` method provided by the `Blade`
facade. The `render` method accepts the Blade template string and an optional
array of data to provide to the template:

```php
use Illuminate\Support\Facades\Blade;

return Blade::render('Hello, {{ $name }}', ['name' => 'Julian Bashir']);
```

Similarly, the `renderComponent` method may be used to render a given class
component by passing the component instance to the method:

```php
use App\View\Components\HelloComponent;

return Blade::renderComponent(new HelloComponent('Julian Bashir'));
```

<a name="slot-name-shortcut"></a>

### Slot Name Shortcut

_Slot name shortcuts were contributed
by [Caleb Porzio](https://github.com/calebporzio)._

In previous releases of Laravel, slot names were provided using a `name`
attribute on the `x-slot` tag:

```blade
<x-alert>
    <x-slot name="title">
        Server Error
    </x-slot>

    <strong>Whoops!</strong> Something went wrong!
</x-alert>
```

However, beginning in Laravel 9.x, you may specify the slot's name using a
convenient, shorter syntax:

```xml

<x-slot:title>
    Server Error
</x-slot>
```

<a name="checked-selected-blade-directives"></a>

### Checked / Selected Blade Directives

_Checked and selected Blade directives were contributed
by [Ash Allen](https://github.com/ash-jc-allen)
and [Taylor Otwell](https://github.com/taylorotwell)_.

For convenience, you may now use the `@checked` directive to easily indicate if
a given HTML checkbox input is "checked". This directive will echo `checked` if
the provided condition evaluates to `true`:

```blade
<input type="checkbox"
        name="active"
        value="active"
        @checked(old('active', $user->active)) />
```

Likewise, the `@selected` directive may be used to indicate if a given select
option should be "selected":

```blade
<select name="version">
    @foreach ($product->versions as $version)
        <option value="{{ $version }}" @selected(old('version') == $version)>
            {{ $version }}
        </option>
    @endforeach
</select>
```

<a name="bootstrap-5-pagination-views"></a>

### Bootstrap 5 Pagination Views

_Bootstrap 5 pagination views were contributed
by [Jared Lewis](https://github.com/jrd-lewis)_.

Laravel now includes pagination views built
using [Bootstrap 5](https://getbootstrap.com/). To use these views instead of
the default Tailwind views, you may call the paginator's `useBootstrapFive`
method within the `boot` method of your `App\Providers\AppServiceProvider`
class:

    use Illuminate\Pagination\Paginator;

    /**
     * Bootstrap any application services.
     *
     * @return void
     */
    public function boot()
    {
        Paginator::useBootstrapFive();
    }

<a name="improved-validation-of-nested-array-data"></a>

### Improved Validation Of Nested Array Data

_Improved validation of nested array inputs was contributed
by [Steve Bauman](https://github.com/stevebauman)_.

Sometimes you may need to access the value for a given nested array element when
assigning validation rules to the attribute. You may now accomplish this using
the `Rule::forEach` method. The `forEach` method accepts a closure that will be
invoked for each iteration of the array attribute under validation and will
receive the attribute's value and explicit, fully-expanded attribute name. The
closure should return an array of rules to assign to the array element:

    use App\Rules\HasPermission;
    use Illuminate\Support\Facades\Validator;
    use Illuminate\Validation\Rule;

    $validator = Validator::make($request->all(), [
        'companies.*.id' => Rule::forEach(function ($value, $attribute) {
            return [
                Rule::exists(Company::class, 'id'),
                new HasPermission('manage-company', $value),
            ];
        }),
    ]);

<a name="laravel-breeze-api"></a>

### Laravel Breeze API & Next.js

_The Laravel Breeze API scaffolding and Next.js starter kit was contributed
by [Taylor Otwell](https://github.com/taylorotwell)
and [Miguel Piedrafita](https://twitter.com/m1guelpf)_.

The [Laravel Breeze](/docs/{{version}}/starter-kits#breeze-and-next) starter kit
has received an "API" scaffolding mode and
complimentary [Next.js](https://nextjs.org) [frontend implementation](https://github.com/laravel/breeze-next).
This starter kit scaffolding may be used to jump start your Laravel applications
that are serving as a backend, Laravel Sanctum authenticated API for a
JavaScript frontend.

<a name="exception-page"></a>

### Improved Ignition Exception Page

_Ignition is developed by [Spatie](https://spatie.be/)._

Ignition, the open source exception debug page created by Spatie, has been
redesigned from the ground up. The new, improved Ignition ships with Laravel 9.x
and includes light / dark themes, customizable "open in editor" functionality,
and more.

<p align="center">
<img width="100%" src="https://user-images.githubusercontent.com/483853/149235404-f7caba56-ebdf-499e-9883-cac5d5610369.png"/>
</p>

<a name="improved-route-list"></a>

### Improved `route:list` CLI Output

_Improved `route:list` CLI output was contributed
by [Nuno Maduro](https://github.com/nunomaduro)_.

The `route:list` CLI output has been significantly improved for the Laravel 9.x
release, offering a beautiful new experience when exploring your route
definitions.

<p align="center">
<img src="https://user-images.githubusercontent.com/5457236/148321982-38c8b869-f188-4f42-a3cc-a03451d5216c.png"/>
</p>

<a name="test-coverage-support-on-artisan-test-Command"></a>

### Test Coverage Using Artisan `test` Command

_Test coverage when using the Artisan `test` command was contributed
by [Nuno Maduro](https://github.com/nunomaduro)_.

The Artisan `test` command has received a new `--coverage` option that you may
use to explore the amount of code coverage your tests are providing to your
application:

```shell
php artisan test --coverage
```

The test coverage results will be displayed directly within the CLI output.

<p align="center">
<img width="100%" src="https://user-images.githubusercontent.com/5457236/150133237-440290c2-3538-4d8e-8eac-4fdd5ec7bd9e.png"/>
</p>

In addition, if you would like to specify a minimum threshold that your test
coverage percentage must meet, you may use the `--min` option. The test suite
will fail if the given minimum threshold is not met:

```shell
php artisan test --coverage --min=80.3
```

<p align="center">
<img width="100%" src="https://user-images.githubusercontent.com/5457236/149989853-a29a7629-2bfa-4bf3-bbf7-cdba339ec157.png"/>
</p>

<a name="soketi-echo-server"></a>

### Soketi Echo Server

_The Soketi Echo server was developed
by [Alex Renoki](https://github.com/rennokki)_.

Although not exclusive to Laravel 9.x, Laravel has recently assisted with the
documentation of Soketi, a [Laravel Echo](/docs/{{version}}/broadcasting)
compatible Web Socket server written for Node.js. Soketi provides a great, open
source alternative to Pusher and Ably for those applications that prefer to
manage their own Web Socket server.

For more information on using Soketi, please consult
the [broadcasting documentation](/docs/{{version}}/broadcasting)
and [Soketi documentation](https://docs.soketi.app/).

<a name="improved-collections-ide-support"></a>

### Improved Collections IDE Support

_Improved collections IDE support was contributed
by [Nuno Maduro](https://github.com/nunomaduro)_.

Laravel 9.x adds improved, "generic" style type definitions to the collections
component, improving IDE and static analysis support. IDEs such
as [PHPStorm](https://blog.jetbrains.com/phpstorm/2021/12/phpstorm-2021-3-release/#support_for_future_laravel_collections)
or static analysis tools such as [PHPStan](https://phpstan.org) will now better
understand Laravel collections natively.

<p align="center">
<img width="100%" src="https://user-images.githubusercontent.com/5457236/151783350-ed301660-1e09-44c1-b549-85c6db3f078d.gif"/>
</p>

<a name="new-helpers"></a>

### New Helpers

Laravel 9.x introduces two new, convenient helper functions that you may use in
your own application.

<a name="new-helpers-str"></a>

#### `str`

The `str` function returns a new `Illuminate\Support\Stringable` instance for
the given string. This function is equivalent to the `Str::of` method:

    $string = str('Taylor')->append(' Otwell');

    // 'Taylor Otwell'

If no argument is provided to the `str` function, the function returns an
instance of `Illuminate\Support\Str`:

    $snake = str()->snake('LaravelFramework');

    // 'laravel_framework'

<a name="new-helpers-to-route"></a>

#### `to_route`

The `to_route` function generates a redirect HTTP response for a given named
route, providing an expressive way to redirect to named routes from your routes
and controllers:

    return to_route('users.show', ['user' => 1]);

If necessary, you may pass the HTTP status code that should be assigned to the
redirect and any additional response headers as the third and fourth arguments
to the to_route method:

    return to_route('users.show', ['user' => 1], 302, ['X-Framework' => 'Laravel']);
