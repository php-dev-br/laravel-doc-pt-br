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
para iniciantes aprimorados do Breeze, melhorias no cliente HTTP e até mesmo
novos tipos de relacionamento do Eloquent, como “tem um de muitos”.

Portanto, esse compromisso de lançar novos recursos excelentes durante a versão
atual provavelmente fará com que futuras versões “principais” sejam usadas
principalmente para tarefas de “manutenção”, como atualização das dependências
originais, que podem ser vistas nestas notas de versão.

O Laravel 9 continua as melhorias feitas no Laravel 8.x, introduzindo suporte
para componentes do Symfony 6.0, Symfony Mailer, Flysystem 3.0, saída aprimorada
do comando `route:list`, um _driver_ de banco de dados do Laravel Scout, nova
sintaxe de acessadores/modificadores do Eloquent, vinculações de rota implícitas
via _enums_, e uma variedade de outras correções de falhas e melhorias de
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

### Acessadores e Modificadores Aprimorados do Eloquent

_Os acessadores e modificadores aprimorados do Eloquent foram contribuídos por
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
usando um método único não prefixado, declarando um tipo de retorno
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

O Eloquent agora permite que você converta os valores de seus atributos em
[_enums_ “apoiadas”](/docs/php/doc/8/language.enumerations.backed.html) do PHP.
Para fazer isso, você pode especificar o atributo e a _enum_ que deseja
converter no _array_ de propriedades `$casts` do seu modelo:

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

Você pode definir uma rota que só será invocada se o segmento de rota
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

### Grupos de Rotas do Controlador

_As melhorias no grupo de rotas foram contribuídas por
[Luke Downing](https://github.com/lukeraymonddowning)_.

Agora você pode usar o método `controller` para definir o controlador comum para
todas as rotas dentro do grupo.
Então, ao definir as rotas, você só precisa fornecer o método do controlador que
elas invocam:

```php
use App\Http\Controllers\OrderController;

Route::controller(OrderController::class)->group(function () {
    Route::get('/orders/{id}', 'show');
    Route::post('/orders', 'store');
});
```

### Índices de Texto Completo e Cláusulas `WHERE`

_Os índices de texto completo e cláusulas `WHERE` foram contribuídos por
[Taylor Otwell](https://github.com/taylorotwell) e
[Dries Vints](https://github.com/driesvints)_.

Ao usar o MySQL ou PostgreSQL, o método `fullText` agora pode ser adicionado às
definições de coluna para gerar índices de texto completo:

```php
$table->text('bio')->fullText();
```

Além disso, os métodos `whereFullText` e `orWhereFullText` podem ser usados para
adicionar cláusulas `WHERE` de texto completo a uma consulta para colunas que
possuem [índices de texto completo](../migrations.md#available-index-types).
Esses métodos serão transformados no SQL apropriado para o sistema de banco de
dados subjacente pelo Laravel.
Por exemplo, uma cláusula `MATCH AGAINST` será gerada para aplicações que
utilizam o MySQL:

```php
$users = DB::table('users')
    ->whereFullText('bio', 'web developer')
    ->get();
```

### Motor de Banco de Dados do Laravel Scout

_O motor de banco de dados do Laravel Scout foi contribuído por
[Taylor Otwell](https://github.com/taylorotwell) e
[Dries Vints](https://github.com/driesvints)_.

Se a sua aplicação interage com bancos de dados de pequeno a médio porte ou tem
uma carga de trabalho leve, agora você pode usar o motor de “banco de dados” do
Scout em vez de um serviço de pesquisa dedicado, como Algolia ou MeiliSearch.
O motor de banco de dados usará cláusulas `WHERE LIKE` e índices de texto
completo ao filtrar os resultados do seu banco de dados existente para
determinar os resultados de pesquisa aplicáveis à sua consulta.

Para saber mais sobre o motor de banco de dados do Scout, consulte a
[documentação do Scout](../scout.md).

### Renderização de Modelos Blade em Linha

_A renderização de modelos Blade em linha foi contribuída por
[Jason Beggs](https://github.com/jasonlbeggs).
A renderização de componentes Blade em linha foi contribuída por
[Toby Zerner](https://github.com/tobyzerner)_.

Às vezes, você pode precisar transformar uma _string_ bruta de modelo Blade em
HTML válido.
Você pode fazer isso usando o método `render` fornecido pela fachada `Blade`.
O método `render` aceita a _string_ do modelo Blade e um _array_ opcional de
dados para fornecer ao modelo:

```php
use Illuminate\Support\Facades\Blade;

return Blade::render('Olá, {{ $name }}', ['name' => 'Julian Bashir']);
```

Da mesma forma, o método `renderComponent` pode ser usado para renderizar um
determinado componente de classe, passando a instância do componente para o
método:

```php
use App\View\Components\HelloComponent;

return Blade::renderComponent(new HelloComponent('Julian Bashir'));
```

### Atalho para o Nome do _Slot_

_O atalho para o nome do slot foi contribuído por
[Caleb Porzio](https://github.com/calebporzio)._

Nas versões anteriores do Laravel, os nomes dos _slots_ eram fornecidos usando
um atributo `name` na _tag_ `x-slot`:

```blade
<x-alert>
    <x-slot name="title">
        Erro no Servidor
    </x-slot>

    <strong>Opa!</strong> Algo deu errado!
</x-alert>
```

No entanto, a partir do Laravel 9.x, você pode especificar o nome do _slot_
usando uma sintaxe mais curta e conveniente:

```xml

<x-slot:title>
    Erro no Servidor
</x-slot>
```

### Diretivas Blade `@checked` e `@selected`

_As diretivas Blade `@checked` e `@selected` foram contribuídas por
[Ash Allen](https://github.com/ash-jc-allen) e
[Taylor Otwell](https://github.com/taylorotwell)_.

Por conveniência, agora você pode usar a diretiva `@checked` para indicar
facilmente se um determinado elemento HTML `checkbox` está “marcado”.
Esta diretiva irá exibir `checked` se a condição fornecida for avaliada como
`true`:

```blade
<input type="checkbox"
    name="active"
    value="active"
    @checked(old('active', $user->active)) />
```

Da mesma forma, a diretiva `@selected` pode ser usada para indicar se uma
determinada opção do elemento HTML `select` deve ser “selecionada”:

```blade
<select name="version">
    @foreach ($product->versions as $version)
        <option value="{{ $version }}" @selected(old('version') == $version)>
            {{ $version }}
        </option>
    @endforeach
</select>
```

### Visualizações de Paginação do Bootstrap 5

_As visualizações de paginação do Bootstrap 5 foram contribuídas por
[Jared Lewis](https://github.com/jrd-lewis)_.

O Laravel agora inclui visualizações de paginação construídas usando o
[Bootstrap 5](https://getbootstrap.com/).
Para usar essas visualizações em vez das visualizações padrão do Tailwind, você
pode chamar o método `useBootstrapFive` do paginador dentro do método `boot` da
sua classe `App\Providers\AppServiceProvider`:

```php
use Illuminate\Pagination\Paginator;

/**
 * Inicializa quaisquer serviços da aplicação.
 *
 * @return void
 */
public function boot()
{
    Paginator::useBootstrapFive();
}
```

### Validação Aprimorada dos Dados de _Arrays_ Aninhados

_A validação aprimorada das entradas de _arrays_ aninhados foi contribuída por
[Steve Bauman](https://github.com/stevebauman)_.

Às vezes, você pode precisar acessar o valor de um determinado elemento de
_array_ aninhado ao atribuir regras de validação ao atributo.
Agora você pode fazer isso usando o método `Rule::forEach`.
O método `forEach` aceita uma _clojure_ que será invocada para cada iteração do
atributo do _array_ sob validação e receberá o valor do atributo e o nome do
atributo explícito e totalmente expandido.
A _clojure_ deve retornar um _array_ de regras para atribuir ao elemento do
_array_:

```php
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
```

### API do Laravel Breeze e Next.js

_A geração automática de código da API do Laravel Breeze e o kit para iniciantes
do Next.js foram contribuídos por
[Taylor Otwell](https://github.com/taylorotwell) e
[Miguel Piedrafita](https://twitter.com/m1guelpf)_.

O _kit_ para iniciantes do [Laravel Breeze](../starter-kits.md#breeze-and-next)
recebeu um modo de geração automática de código da API e uma
[implementação de _front-end_](https://github.com/laravel/breeze-next)
[Next.js](https://nextjs.org) gratuita.
Esta geração automática de código do _kit_ para iniciantes pode ser usada para
iniciar suas aplicações Laravel que estão servindo como _back-end_ de API
autenticada do Laravel Sanctum para um _front-end_ JavaScript.

### Página de Exceção Aprimorada da Ignition

_A Ignition é desenvolvida por [Spatie](https://spatie.be/)._

Ignition, a página de depuração de exceções de código aberto criada por Spatie,
foi redesenhada do zero.
A nova e aprimorada Ignition vem com o Laravel 9.x e inclui temas claro e
escuro, funcionalidade personalizável de “abrir no editor” e muito mais.

<img
alt="Captura de tela do novo tema da Ignition"
src="https://user-images.githubusercontent.com/483853/149235404-f7caba56-ebdf-499e-9883-cac5d5610369.png" />

### Saída Aprimorada de `route:list` na CLI

_A saída aprimorada de `route:list` na CLI foi contribuída por
[Nuno Maduro](https://github.com/nunomaduro)_.

A saída de `route:list` na CLI foi significativamente melhorada para o
lançamento do Laravel 9.x, oferecendo uma bela nova experiência ao explorar suas
definições de rota.

<img
alt="Captura de tela da saída do comando 'route:list' na CLI"
src="https://user-images.githubusercontent.com/5457236/148321982-38c8b869-f188-4f42-a3cc-a03451d5216c.png"/>

### Cobertura de Testes Usando o Comando `test` do Artisan

_A cobertura de testes ao usar o comando `test` do Artisan foi contribuída por
[Nuno Maduro](https://github.com/nunomaduro)_.

O comando `test` do Artisan recebeu uma nova opção `--coverage` que você pode
usar para explorar a quantidade de cobertura de código que seus testes estão
fornecendo à sua aplicação:

```shell
php artisan test --coverage
```

Os resultados da cobertura do teste serão exibidos diretamente na saída da CLI.

<img
alt="Captura de tela da cobertura de teste na CLI"
src="https://user-images.githubusercontent.com/5457236/150133237-440290c2-3538-4d8e-8eac-4fdd5ec7bd9e.png" />

Além disso, se desejar especificar um limite mínimo que sua porcentagem de
cobertura de teste deve atingir, você poderá usar a opção `--min`.
O conjunto de testes falhará se o limite mínimo fornecido não for atingido:

```shell
php artisan test --coverage --min=80.3
```

<img
alt="Captura de tela da cobertura de teste na CLI com limite mínimo"
src="https://user-images.githubusercontent.com/5457236/149989853-a29a7629-2bfa-4bf3-bbf7-cdba339ec157.png" />

### Servidor Echo Soketi

_O servidor Echo Soketi foi desenvolvido por
[Alex Renoki](https://github.com/rennokki)_.

Embora não seja exclusivo do Laravel 9.x, o Laravel recentemente ajudou na
documentação do Soketi, um servidor WebSocket compatível com
[Laravel Echo](../broadcasting.md) escrito para Node.js.
O Soketi fornece uma excelente alternativa de código aberto ao Pusher e Ably
para aquelas aplicações que preferem gerenciar seu próprio servidor WebSocket.

Para obter mais informações sobre o uso do Soketi, consulte a
[documentação de transmissão](../broadcasting.md) e a
[documentação do Soketi](https://docs.soketi.app/).

### Suporte Aprimorado a Coleções no IDE

_O suporte aprimorado a coleções no IDE foi contribuído por
[Nuno Maduro](https://github.com/nunomaduro)_.

O Laravel 9.x adiciona definições aprimoradas de tipo no estilo “genérico” ao
componente de coleções, melhorando o suporte ao IDE e à análise estática.
IDEs como
[PHPStorm](https://blog.jetbrains.com/phpstorm/2021/12/phpstorm-2021-3-release/#support_for_future_laravel_collections)
ou ferramentas de análise estática como [PHPStan](https://phpstan.org) agora
entenderão melhor as coleções do Laravel nativamente.

<img
alt="Imagem animada exibindo o suporte aprimorado a coleções no IDE em
funcionamento"
src="https://user-images.githubusercontent.com/5457236/151783350-ed301660-1e09-44c1-b549-85c6db3f078d.gif" />

### Novas Funções Auxiliares

O Laravel 9.x introduz duas novas funções auxiliares convenientes que você pode
usar na sua aplicação.

#### `str`

A função `str` retorna uma nova instância `Illuminate\Support\Stringable` para
a _string_ fornecida.
Esta função é equivalente ao método `Str::of`:

```php
$string = str('Taylor')->append(' Otwell');

// 'Taylor Otwell'
```

Se nenhum argumento for fornecido para a função `str`, a função retornará uma
instância de `Illuminate\Support\Str`:

```php
$snake = str()->snake('LaravelFramework');

// 'laravel_framework'
```

#### `to_route`

A função `to_route` gera uma resposta HTTP de redirecionamento para uma
determinada rota nomeada, fornecendo uma maneira expressiva de redirecionar para
rotas nomeadas a partir de suas rotas e controladores:

```php
return to_route('users.show', ['user' => 1]);
```

Se necessário, você pode passar o código de _status_ HTTP que deve ser atribuído
ao redirecionamento e quaisquer cabeçalhos de resposta adicionais como o
terceiro e quarto argumentos para o método `to_route`:

```php
return to_route('users.show', ['user' => 1], 302, ['X-Framework' => 'Laravel']);
```
