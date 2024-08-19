---
source_url: https://github.com/laravel/docs/blob/7.x/releases.md
revision: e54f854d0b5cf318b3b994f6367e66ecad09cac9
status: ready
---

# Notas de Versão

- [Esquema de Versionamento](#esquema-de-versionamento)
- [Política de Suporte](#politica-de-suporte)
- [Laravel 7](#laravel-7)

## Esquema de Versionamento

O Laravel e seus outros pacotes originais seguem o
[Versionamento Semântico](https://semver.org/lang/pt-BR/).
As versões maiores do _framework_ são lançadas a cada seis meses (por volta de
fevereiro e agosto), enquanto as versões menores e de correções podem ser
lançadas semanalmente.
Versões menores e de correções **nunca** devem conter alterações significativas.

Ao referenciar o _framework_ Laravel e seus componentes a partir da sua
aplicação ou pacote, você deve sempre usar uma restrição de versão como `^7.0`,
uma vez que as principais versões do Laravel incluem alterações significativas.
No entanto, nos esforçamos para sempre garantir que você possa atualizar para
uma nova versão maior em um dia ou menos.

## Política de Suporte

Para versões LTS, como o Laravel 6, as correções de falhas são fornecidas por 2
anos e as correções de segurança são fornecidas por 3 anos.
Essas versões oferecem a janela mais longa de suporte e manutenção.
Para versões gerais, as correções de falhas são fornecidas por 6 meses e as
correções de segurança são fornecidas por 1 ano.
Para todas as bibliotecas adicionais, incluindo Lumen, apenas a versão maior
mais recente recebe correções de falhas.
Além disso, revise as versões do banco de dados
[suportadas pelo Laravel](../database.md#introduction).

| Versão  |      Lançamento       | Correções de falhas até | Correções de segurança até |
|:-------:|:---------------------:|:-----------------------:|:--------------------------:|
| 6 (LTS) | 3 de setembro de 2019 |  3 de setembro de 2021  |   3 de setembro de 2022    |
|    7    |  3 de março de 2020   | 10 de setembro de 2020  |     3 de março de 2021     |
|    8    | 8 de setembro de 2020 |   8 de março de 2021    |   8 de setembro de 2021    |

## Laravel 7

O Laravel 7 continua as melhorias feitas no Laravel 6.x introduzindo o Laravel
Sanctum, melhorias na velocidade de roteamento, conversões personalizadas do
Eloquent, _tags_ de componentes do Blade, operações de _strings_ fluentes, um
cliente HTTP focado na pessoa desenvolvedora, suporte CORS original, escopo
aprimorado para vinculação de modelos de rota, personalização de arquivos de
exemplo, melhorias na fila do banco de dados, múltiplos _drivers_ de correio,
conversões em tempo de consulta, um novo comando `artisan test` e uma variedade
de outras correções de falhas e melhorias de usabilidade.

### Laravel Sanctum

_O Laravel Sanctum foi construído por
[Taylor Otwell](https://github.com/taylorotwell)_.

O Laravel Sanctum fornece um sistema de autenticação leve para SPAs (aplicações
de página única), aplicativos móveis e APIs simples baseadas em _tokens_.
O Sanctum permite que cada pessoa usuária da sua aplicação gere vários _tokens_
de API para sua conta.
Esses _tokens_ podem receber habilidades e escopos que especificam quais ações
os _tokens_ podem realizar.

Para mais informações sobre o Laravel Sanctum, consulte a
[documentação do Sanctum](../sanctum.md).

### Conversões Personalizadas do Eloquent

_As conversões personalizadas do Eloquent foram contribuídas por
[Taylor Otwell](https://github.com/taylorotwell)_.

O Laravel possui uma variedade de tipos de conversões nativos úteis; no entanto,
ocasionalmente você pode precisar definir seus próprios tipos de conversões.
Agora você pode fazer isso definindo uma classe que implemente a interface
`CastsAttributes`.

As classes que implementam esta interface devem definir métodos `get` e `set`.
O método `get` é responsável por transformar um valor bruto do banco de dados em
um valor convertido, enquanto o método `set` deve transformar um valor
convertido em um valor bruto que pode ser armazenado no banco de dados.
Como exemplo, reimplementaremos o tipo de conversão nativo `json` como um tipo
de conversão personalizado:

```php
<?php

namespace App\Casts;

use Illuminate\Contracts\Database\Eloquent\CastsAttributes;

class Json implements CastsAttributes
{
    /**
     * Converte o valor fornecido.
     *
     * @param  \Illuminate\Database\Eloquent\Model  $model
     * @param  string  $key
     * @param  mixed  $value
     * @param  array  $attributes
     * @return array
     */
    public function get($model, $key, $value, $attributes)
    {
        return json_decode($value, true);
    }

    /**
     * Prepara o valor fornecido para armazenamento.
     *
     * @param  \Illuminate\Database\Eloquent\Model  $model
     * @param  string  $key
     * @param  array  $value
     * @param  array  $attributes
     * @return string
     */
    public function set($model, $key, $value, $attributes)
    {
        return json_encode($value);
    }
}
```

Após definir um tipo de conversão personalizado, você pode anexá-lo a um
atributo de modelo usando seu nome de classe:

```php
<?php

namespace App;

use App\Casts\Json;
use Illuminate\Database\Eloquent\Model;

class User extends Model
{
    /**
     * Os atributos que devem ser convertidos em tipos nativos.
     *
     * @var array
     */
    protected $casts = [
        'options' => Json::class,
    ];
}
```

Para aprender como escrever conversões personalizadas do Eloquent, incluindo
conversões personalizadas que convertem para objetos de valor, consulte a
[documentação do Eloquent](../eloquent-mutators.md#custom-casts).

### Tags e Melhorias nos Componentes do Blade

_As tags dos componentes do Blade foram contribuídas por
[Spatie](https://spatie.be/), [Marcel Pociot](https://twitter.com/marcelpociot),
[Caleb Porzio](https://twitter.com/calebporzio),
[Dries Vints](https://twitter.com/driesvints) e
[Taylor Otwell](https://github.com/taylorotwell)_.

> **Dica:**
> Os componentes do Blade foram revisados para permitir a renderização baseada
> em _tags_, o gerenciamento de atributos, as classes de componentes, os
> componentes de visualização em linha e muito mais.
> Como a revisão dos componentes do Blade é muito extensa, consulte a
> [documentação completa de componentes do Blade](../blade.md#components) para
> saber mais sobre esse recurso.

Em resumo, um componente agora pode ter uma classe associada que especifica os
dados que ele aceita.
Todas as propriedades e métodos públicos definidos na classe do componente serão
automaticamente disponibilizados para a visualização do componente.
Quaisquer atributos HTML adicionais especificados no componente podem ser
gerenciados usando a variável `$attributes` incluída automaticamente, que é uma
instância de `Illuminate\View\ComponentAttributeBag`.

Neste exemplo, assumiremos que um componente `App\View\Components\Alert` foi
definido assim:

```php
<?php

namespace App\View\Components;

use Illuminate\View\Component;

class Alert extends Component
{
    /**
     * O tipo de alerta.
     *
     * @var string
     */
    public $type;

    /**
     * Cria a instância do componente.
     *
     * @param  string  $type
     * @return void
     */
    public function __construct($type)
    {
        $this->type = $type;
    }

    /**
     * Obtém a classe para o tipo de alerta fornecido.
     *
     * @return string
     */
    public function classForType()
    {
        return $this->type == 'danger' ? 'alert-danger' : 'alert-warning';
    }

    /**
     * Obtém a visualização ou conteúdo que representa o componente.
     *
     * @return \Illuminate\View\View|string
     */
    public function render()
    {
        return view('components.alert');
    }
}
```

E, supondo que o modelo do componente do Blade tenha sido definido assim:

```bladehtml
<!-- /resources/views/components/alert.blade.php -->

<div class="alert {{ $classForType }}" {{ $attributes }}>
    {{ $heading }}

    {{ $slot }}
</div>
```

O componente pode ser renderizado em outra visualização do Blade usando a _tag_
do componente:

```bladehtml

<x-alert type="error" class="mb-4">
    <x-slot name="heading">
        Conteúdo do alerta...
    </x-slot>

    Conteúdo padrão do slot...
</x-alert>
```

Conforme mencionado, esta é apenas uma pequena amostra da funcionalidade da
revisão dos componentes do Blade no Laravel 7 e não demonstra componentes
anônimos, componentes de visualização em linha e uma variedade de outros
recursos.
Consulte
a [documentação completa dos componentes do Blade](../blade.md#components)
para saber mais sobre esse recurso.

> **Nota:**
> A sintaxe `@component` anterior para componentes do Blade não foi e não será
> removida.

### Cliente HTTP

_O cliente HTTP é um wrapper do Guzzle e foi contribuído por
[Adam Wathan](https://twitter.com/adamwathan),
[Jason McCreary](https://twitter.com/gonedark) e
[Taylor Otwell](https://github.com/taylorotwell)_.

O Laravel agora fornece uma API mínima e expressiva em torno do
[cliente HTTP Guzzle](http://docs.guzzlephp.org/en/stable/), permitindo que você
faça rapidamente requisições HTTP de saída para se comunicar com outras
aplicações _web_.
O _wrapper_ do Laravel em torno do Guzzle é focado em seus casos de uso mais
comuns e em uma experiência maravilhosa para a pessoa desenvolvedora.
Por exemplo, o cliente facilita requisições `POST` e a interface com dados JSON:

```php
use Illuminate\Support\Facades\Http;

$response = Http::withHeaders([
    'X-Primeiro' => 'foo',
    'X-Segundo' => 'bar'
])->post('http://test.com/users', [
    'nome' => 'Taylor',
]);

return $response['id'];
```

Além disso, o cliente HTTP oferece funcionalidades de testes fantásticas e
ergonômicas:

```php
Http::fake([
    // Simula uma resposta JSON para endpoints do GitHub...
    'github.com/*' => Http::response(['foo' => 'bar'], 200, ['Headers']),

    // Simula uma resposta de string para endpoints do Google...
    'google.com/*' => Http::response('Olá, mundo', 200, ['Headers']),

    // Simula uma série de respostas para endpoints do Facebook...
    'facebook.com/*' => Http::sequence()
        ->push('Olá, mundo', 200)
        ->push(['foo' => 'bar'], 200)
        ->pushStatus(404),
]);
```

Para saber mais sobre todos os recursos do cliente HTTP, consulte a
[documentação do cliente HTTP](../http-client.md).

### Operações de _Strings_ Fluentes

_As operações de _strings_ fluentes foram contribuídas por
[Taylor Otwell](https://twitter.com/taylorotwell)_.

Você está provavelmente familiarizado com a classe `Illuminate\Support\Str`
existente do Laravel, que fornece uma variedade de funções úteis de manipulação
de _strings_.
O Laravel 7 agora oferece uma biblioteca de manipulação de _strings_
mais fluente e orientada a objetos, construída sobre essas funções.
Você pode criar um objeto `Illuminate\Support\Stringable` fluente usando o
método `Str::of`.
Uma variedade de métodos pode então ser encadeada ao objeto para manipular a
_string_:

```php
return (string) Str::of('  Laravel Framework 6.x ')
    ->trim()
    ->replace('6.x', '7.x')
    ->slug();
```

Para obter mais informações sobre os métodos disponíveis por meio da manipulação
de _strings_ fluentes, consulte a
[documentação completa](../helpers.md#fluent-strings).

### Melhorias na Vinculação dos Modelos de Rotas

_As melhorias na vinculação dos modelos de rotas foram contribuídas por
[Taylor Otwell](https://twitter.com/taylorotwell)_.

#### Personalização de Chave

Às vezes você pode querer resolver modelos do Eloquent usando uma coluna
diferente de `id`.
Para fazer isso, o Laravel 7 permite que você especifique a coluna na definição
do parâmetro da rota:

```php
Route::get('api/posts/{post:slug}', function (App\Post $post) {
    return $post;
});
```

#### Escopo Automático

Às vezes, ao vincular implicitamente vários modelos do Eloquent em uma única
definição de rota, você pode desejar definir o escopo do segundo modelo do
Eloquent de forma que ele seja filho do primeiro modelo do Eloquent.
Por exemplo, considere esta situação que recupera uma postagem de _blog_ por
_slug_ para uma pessoa usuária específica:

```php
use App\Post;
use App\User;

Route::get('api/users/{user}/posts/{post:slug}', function (User $user, Post $post) {
    return $post;
});
```

Ao usar uma vinculação implícita com chave personalizada como parâmetro de rota
aninhada, o Laravel 7 irá automaticamente definir o escopo da consulta para
recuperar o modelo aninhado por seu pai usando convenções para adivinhar o nome
do relacionamento no pai.
Neste caso, será assumido que o modelo `User` possui um relacionamento chamado
`posts` (o plural do nome do parâmetro de rota) que pode ser usado para
recuperar o modelo `Post`.

Para obter mais informações sobre a vinculação do modelo de rota, consulte a
[documentação de roteamento](../routing.md#route-model-binding).

### Múltiplos _Drivers_ de Correio

_O suporte a múltiplos drivers de correio foi contribuído por
[Taylor Otwell](https://twitter.com/taylorotwell)_.

O Laravel 7 permite a configuração de múltiplos “_mailers_” para uma única
aplicação.
Cada _mailer_ configurado no arquivo de configuração `mail` pode ter suas
próprias opções e até mesmo seu próprio “transporte” exclusivo, permitindo que
sua aplicação use diferentes serviços de _e-mail_ para enviar determinadas
mensagens de _e-mail_.
Por exemplo, sua aplicação pode usar Postmark para enviar mensagens
transacionais enquanto usa o Amazon SES para enviar mensagens em massa.

Por padrão, o Laravel usará o _mailer_ configurado como _mailer_ `default` no
seu arquivo de configuração `mail`.
No entanto, você pode usar o método `mailer` para enviar uma mensagem usando uma
configuração específica de _mailer_:

```php
Mail::mailer('postmark')
    ->to($request->user())
    ->send(new OrderShipped($order));
```

### Melhorias na Velocidade do _Cache_ de Rota

_As melhorias na velocidade do cache de rota foram contribuídas pelos
contribuidores originais do [Symfony](https://symfony.com) e
[Dries Vints](https://twitter.com/driesvints)_.

O Laravel 7 inclui um novo método de correspondência de rotas compiladas e
armazenadas em _cache_ usando o comando `route:cache` do Artisan.
Em aplicações grandes (por exemplo, aplicações com 800 ou mais rotas),
essas melhorias podem resultar em uma **melhoria de velocidade de 2x** nas
requisições por segundo em um simples _benchmark_ com "Olá, mundo".
Nenhuma alteração na sua aplicação é necessária.

### Suporte ao CORS

_O suporte ao CORS foi contribuído por
[Barry vd. Heuvel](https://twitter.com/barryvdh)_.

O Laravel 7 inclui suporte original para configurar respostas de requisições
`OPTIONS` de Compartilhamento de Recursos de Origem Cruzada (CORS), integrando o
popular pacote Laravel CORS escrito por Barry vd. Heuvel.
Uma nova configuração `cors` está incluída no
[esqueleto da aplicação Laravel padrão](https://github.com/laravel/laravel/blob/develop/config/cors.php).

Para obter mais informações sobre o suporte ao CORS no Laravel 7.x, consulte a
[documentação do CORS](../routing.md#cors).

### Conversão em Tempo de Consulta

_A conversão em tempo de consulta foi contribuída por
[Matt Barlow](https://github.com/mpbarlow)_.

Às vezes, pode ser necessário aplicar conversões durante a execução de uma
consulta, como ao selecionar um valor bruto de uma tabela.
Por exemplo, considere a seguinte consulta:

```php
use App\Post;
use App\User;

$users = User::select([
    'users.*',
    'last_posted_at' => Post::selectRaw('MAX(created_at)')
        ->whereColumn('user_id', 'users.id')
])->get();
```

O atributo `last_posted_at` nos resultados desta consulta será uma _string_
bruta.
Seria conveniente se pudéssemos aplicar uma conversão `date` a este atributo ao
executar a consulta.
Para fazer isso, podemos usar o método `withCasts` fornecido pelo Laravel 7:

```php
$users = User::select([
    'users.*',
    'last_posted_at' => Post::selectRaw('MAX(created_at)')
        ->whereColumn('user_id', 'users.id')
])->withCasts([
    'last_posted_at' => 'date'
])->get();
```

### Melhorias na Fila do Banco de Dados MySQL 8+

_As melhorias na fila do banco de dados MySQL foram contribuídas por
[Mohamed Said](https://github.com/themsaid)_.

Nas versões anteriores do Laravel, a fila `database` não era considerada robusta
o suficiente para uso em produção, devido a _deadlocks_.
No entanto, o Laravel 7 fornece melhorias para aplicações que usam MySQL 8+ como
banco de dados para suporte de filas.
Usando a cláusula `FOR UPDATE SKIP LOCKED` e outros aprimoramentos SQL, o
_driver_ `database` agora pode ser usado com segurança em aplicações de produção
de maior volume.

### O Comando `test` do Artisan

_O comando `test` foi contribuído por
[Nuno Maduro](https://twitter.com/enunomaduro)_.

Além do comando `phpunit`, agora você pode usar o comando `test` do Artisan para
executar seus testes.
O executor de testes do Artisan fornece uma bela experiência de pessoa usuária
no console e mais informações sobre o teste que está sendo executado no momento.
Além disso, o executor irá parar automaticamente na primeira falha de teste:

```shell
php artisan test
```

<img
alt="Captura de tela do comando `test` do Artisan"
src="/content/img/docs/laravel/laravel/doc/7.x/release-notes-artisan-test-preview.png" />

Quaisquer argumentos que possam ser passados para o comando `phpunit` também
podem ser passados para o comando `test` do Artisan:

```shell
php artisan test --group=feature
```

### Melhorias no Modelo de Correio Markdown

_As melhorias no modelo de correio Markdown foram contribuídas por
[Taylor Otwell](https://twitter.com/taylorotwell)_.

O modelo de correio Markdown padrão recebeu um design novo e mais moderno
baseado na paleta de cores do Tailwind CSS.
Claro, este modelo pode ser publicado e customizado conforme as necessidades da
sua aplicação:

<img
alt="Captura de tela do novo modelo de correio Markdown"
src="/content/img/docs/laravel/laravel/doc/7.x/release-notes-notification-preview.png" />

Para obter mais informações sobre o correio Markdown, consulte a
[documentação do correio](../mail.md#markdown-mailables).

### Personalização dos Arquivos de Exemplo

_A personalização dos arquivos de exemplo foi contribuída por
[Taylor Otwell](https://twitter.com/taylorotwell)_.

Os comandos `make` do console do Artisan são usados para criar uma variedade de
classes, como controladores, trabalhos, migrações e testes.
Essas classes são geradas usando arquivos “de exemplo” preenchidos com valores
baseados em sua entrada.
No entanto, às vezes você pode querer fazer pequenas alterações nos arquivos
gerados pelo Artisan.
Para fazer isso, o Laravel 7 fornece o comando `stub:publish` para publicar os
arquivos de exemplo mais comuns para personalização:

```shell
php artisan stub:publish
```

Os arquivos de exemplos publicados estarão localizados em um diretório `stubs`
na raiz da sua aplicação.
Quaisquer alterações feitas nesses arquivos de exemplo serão refletidas quando
você gerar suas classes correspondentes usando os comandos `make` do Artisan.

### Configuração `maxExceptions` da Fila

_A propriedade `maxExceptions` foi contribuída por
[Mohamed Said](https://twitter.com/themsaid)_.

Às vezes, você pode querer especificar que um trabalho pode ser tentado várias
vezes, mas deverá falhar se as novas tentativas forem acionadas por um
determinado número de exceções.
No Laravel 7, você pode definir uma propriedade `maxExceptions` na sua classe de
trabalho:

```php
<?php

namespace App\Jobs;

class ProcessPodcast implements ShouldQueue
{
    /**
     * O número de vezes que o trabalho pode ser tentado.
     *
     * @var int
     */
    public $tries = 25;

    /**
     * O número máximo de exceções a serem permitidas antes da falha.
     *
     * @var int
     */
    public $maxExceptions = 3;

    /**
     * Executa o trabalho.
     *
     * @return void
     */
    public function handle()
    {
        Redis::throttle('key')->allow(10)->every(60)->then(function () {
            // Bloqueio obtido, processa o podcast...
        }, function () {
            // Não foi possível obter o bloqueio...
            return $this->release(10);
        });
    }
}
```

Neste exemplo, o trabalho será liberado por dez segundos se a aplicação não
conseguir obter um bloqueio do Redis e continuará a ser repetido até 25 vezes.
No entanto, o trabalho falhará se três exceções não tratadas forem lançadas pelo
trabalho.
