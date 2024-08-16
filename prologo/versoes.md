---
source_url: https://github.com/laravel/docs/blob/8.x/releases.md
revision: ead5ead2ec97266ec049736cf0a93271327efdc7
status: ready
---

# Notas de Versão

- [Esquema de Versionamento](#esquema-de-versionamento)
    - [Exceções](#excecoes)
- [Política de Suporte](#politica-de-suporte)
- [Laravel 8](#laravel-8)

## Esquema de Versionamento

O Laravel e seus outros pacotes originais seguem o
[Versionamento Semântico](https://semver.org/lang/pt-BR/).
As versões maiores do _framework_ são lançadas todos os anos (por volta de
fevereiro), enquanto as versões menores e de correções podem ser lançadas
semanalmente.
Versões menores e de correções **nunca** devem conter alterações significativas.

Ao referenciar o _framework_ Laravel e seus componentes a partir da sua
aplicação ou pacote, você deve sempre usar uma restrição de versão como `^8.0`,
uma vez que as principais versões do Laravel incluem alterações significativas.
No entanto, nos esforçamos para sempre garantir que você possa atualizar para
uma nova versão maior em um dia ou menos.

### Exceções

### Argumentos Nomeados

No momento, a funcionalidade
[argumentos nomeados](/docs/php/doc/8/functions.arguments.html#functions.named-arguments)
não é coberta pelas diretrizes de compatibilidade com versões anteriores do
Laravel.
Podemos optar por renomear os parâmetros de funções quando necessário para
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

| Versão  |  PHP (*)  |       Lançamento        | Correções de falhas até | Correções de segurança até |
|:-------:|:---------:|:-----------------------:|:-----------------------:|:--------------------------:|
| 6 (LTS) | 7.2 - 8.0 |  3 de setembro de 2019  |  25 de janeiro de 2022  |   6 de setembro de 2022    |
|    7    | 7.2 - 8.0 |   3 de março de 2020    |  6 de outubro de 2020   |     3 de março de 2021     |
|    8    | 7.3 - 8.1 |  8 de setembro de 2020  |   26 de julho de 2022   |   24 de janeiro de 2023    |
|    9    | 8.0 - 8.1 | 8 de fevereiro de 2022  |   8 de agosto de 2023   |   6 de fevereiro de 2024   |
|   10    | 8.1 - 8.3 | 14 de fevereiro de 2023 |   6 de agosto de 2024   |   4 de fevereiro de 2025   |

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

## Laravel 8

O Laravel 8 continua as melhorias feitas no Laravel 7.x introduzindo o Laravel
Jetstream, classes de fábricas de modelos, compressão de migrações, trabalhos em
lote, limitação de taxa aprimorada, melhorias nas filas, componentes dinâmicos
do Blade, visualizações de paginação do Tailwind, auxiliares de testes de tempo,
melhorias no `artisan serve`, melhorias nos ouvintes de eventos e uma variedade
de outras correções de falhas e melhorias de usabilidade.

### Laravel Jetstream

_O Laravel Jetstream foi escrito
por [Taylor Otwell](https://github.com/taylorotwell)_.

O [Laravel Jetstream](https://jetstream.laravel.com) é um gerador automático de
código de aplicações Laravel que foi lindamente projetado.
O Jetstream fornece o ponto de partida perfeito para seu próximo projeto e
inclui _login_, registro, verificação de e-mail, autenticação de dois fatores,
gerenciamento de sessão, suporte a API via Laravel Sanctum e gerenciamento
opcional de time.
O Laravel Jetstream substitui e aprimora a geração automática de código da UI de
autenticação legada disponível para versões anteriores do Laravel.

O Jetstream foi projetado usando [Tailwind CSS](https://tailwindcss.com) e
oferece as opções de geração automática de código com
[Livewire](https://laravel-livewire.com) ou
[Inertia](https://inertiajs.com).

### Diretório `Models`

Atendendo à grande demanda da comunidade, o esqueleto da aplicação Laravel
padrão agora contém um diretório `app/Models`.
Esperamos que você aproveite esta nova casa para seus modelos do Eloquent!
Todos os comandos relevantes do gerador foram atualizados para assumir que
existem modelos no diretório `app/Models`, se ele existir.
Se o diretório não existir, o _framework_ assumirá que seus modelos devem ser
colocados no diretório `app`.

### Classes de Fábricas de Modelos

_As classes de fábricas de modelos foram contribuídas por
[Taylor Otwell](https://github.com/taylorotwell)_.

As [fábricas de modelos](../database-testing.md#defining-model-factories) do
Eloquent foram totalmente reescritas como fábricas baseadas em classes e
aprimoradas para ter suporte de primeira classe a relacionamentos.
Por exemplo, a `UserFactory` incluída no Laravel é escrita assim:

```php
<?php

namespace Database\Factories;

use App\Models\User;
use Illuminate\Database\Eloquent\Factories\Factory;
use Illuminate\Support\Str;

class UserFactory extends Factory
{
    /**
     * O nome do modelo correspondente da fábrica.
     *
     * @var string
     */
    protected $model = User::class;

    /**
     * Define o estado padrão do modelo.
     *
     * @return array
     */
    public function definition()
    {
        return [
            'name' => $this->faker->name(),
            'email' => $this->faker->unique()->safeEmail(),
            'email_verified_at' => now(),
            'password' => '$2y$10$92IXUNpkjO0rOQ5byMi.Ye4oKoEa3Ro9llC/.og/at2.uheWG/igi', // senha
            'remember_token' => Str::random(10),
        ];
    }
}
```

Graças à nova _trait_ `HasFactory` disponível nos modelos gerados, a fábrica de
modelos pode ser usada da seguinte forma:

```php
use App\Models\User;

User::factory()->count(50)->create();
```

Como as fábricas de modelos agora são classes PHP simples, as transformações de
estado podem ser escritas como métodos de classe.
Além disso, você pode adicionar quaisquer outras classes auxiliares à sua
fábrica de modelos do Eloquent, conforme necessário.

Por exemplo, seu modelo `User` pode ter um estado `suspended` que modifica um de
seus valores de atributo padrão.
Você pode definir suas transformações de estado usando o método `state` da
fábrica base.
Você pode nomear seu método de estado como quiser.
Afinal, é apenas um método PHP comum:

```php
/**
 * Indica que a pessoa usuária está suspensa.
 *
 * @return \Illuminate\Database\Eloquent\Factories\Factory
 */
public function suspended()
{
    return $this->state([
        'account_status' => 'suspended',
    ]);
}
```

Após definir o método de transformação de estado, podemos usá-lo assim:

```php
use App\Models\User;

User::factory()->count(5)->suspended()->create();
```

Conforme mencionado, as fábricas de modelos do Laravel 8 contêm suporte de
primeira classe a relacionamentos.
Portanto, supondo que nosso modelo `User` tenha um método de relacionamento
`posts`, podemos simplesmente executar o seguinte código para gerar uma pessoa
usuária com três _posts_:

```php
$users = User::factory()
    ->hasPosts(3, [
        'published' => false,
    ])
    ->create();
```

Para facilitar o processo de atualização, o pacote
[laravel/legacy-factories](https://github.com/laravel/legacy-factories) foi
lançado para fornecer suporte à iteração anterior de fábricas de modelos no
Laravel 8.x.

As fábricas reescritas do Laravel contêm muito mais recursos que achamos que
você irá adorar.
Para saber mais sobre fábricas de modelos, consulte a
[documentação de testes de banco de dados](../database-testing.md#defining-model-factories).

### Compressão de Migrações

_A compressão de migrações foi contribuída por
[Taylor Otwell](https://github.com/taylorotwell)_.

À medida que você cria sua aplicação, você poderá acumular cada vez mais
migrações ao longo do tempo.
Isso pode fazer com que seu diretório de migrações fique inchado com
potencialmente centenas de migrações.
Se estiver usando o MySQL ou PostgreSQL, agora você pode “comprimir” suas
migrações em um único arquivo SQL.
Para começar, execute o comando `schema:dump`:

```shell
php artisan schema:dump

# Faz o dump do esquema do banco de dados atual e remove todas as migrações
# existentes...
php artisan schema:dump --prune
```

Quando você executa este comando, o Laravel irá gravar um arquivo de “esquema”
no seu diretório `database/schema`.
Agora, quando você tentar migrar seu banco de dados e nenhuma outra migração
tiver sido executada, o Laravel executará primeiro o SQL do arquivo de esquema.
Após executar os comandos do arquivo de esquema, o Laravel executará quaisquer
migrações restantes que não fizeram parte do _dump_ do esquema.

### Trabalhos em Lote

_Os trabalhos em lote foram contribuídos por
[Taylor Otwell](https://github.com/taylorotwell) e
[Mohamed Said](https://github.com/themsaid)_.

O recurso de trabalhos em lote do Laravel permite que você execute facilmente um
lote de trabalhos e, em seguida, execute alguma ação quando a execução do lote
de trabalhos for concluída.

O novo método `batch` da fachada `Bus` pode ser usado para despachar um lote de
trabalhos.
É claro que os trabalhos em lote são úteis principalmente quando combinados com
retornos de chamada de conclusão.
Portanto, você pode usar os métodos `then`, `catch` e `finally` para definir
retornos de chamada de conclusão para o lote.
Cada um desses retornos de chamada receberá uma instância `Illuminate\Bus\Batch`
quando forem invocados:

```php
use App\Jobs\ProcessPodcast;
use App\Models\Podcast;
use Illuminate\Bus\Batch;
use Illuminate\Support\Facades\Bus;
use Throwable;

$batch = Bus::batch([
    new ProcessPodcast(Podcast::find(1)),
    new ProcessPodcast(Podcast::find(2)),
    new ProcessPodcast(Podcast::find(3)),
    new ProcessPodcast(Podcast::find(4)),
    new ProcessPodcast(Podcast::find(5)),
])->then(function (Batch $batch) {
    // Todos os trabalhos foram concluídos com sucesso...
})->catch(function (Batch $batch, Throwable $e) {
    // Primeira falha detectada nos trabalhos em lote...
})->finally(function (Batch $batch) {
    // O lote terminou de ser executado...
})->dispatch();

return $batch->id;
```

Para saber mais sobre processamento em lote de trabalhos, consulte a
[documentação de filas](../queues.md#job-batching).

### Limitação de Taxa Aprimorada

_As melhorias na limitação de taxa foram contribuídas por
[Taylor Otwell](https://github.com/taylorotwell)_.

O recurso limitador de taxa de requisição do Laravel foi ampliado com mais
flexibilidade e poder, enquanto ainda mantém compatibilidade retroativa com a
API de _middleware_ `throttle` da versão anterior.

Os limitadores de taxa são definidos usando o método `for` da fachada
`RateLimiter`.
O método `for` aceita um nome de limitador de taxa e uma _closure_ que retorna a
configuração de limite que deve ser aplicada às rotas às quais esse limitador de
taxa é atribuído:

```php
use Illuminate\Cache\RateLimiting\Limit;
use Illuminate\Support\Facades\RateLimiter;

RateLimiter::for('global', function (Request $request) {
    return Limit::perMinute(1000);
});
```

Como os retornos de chamada do limitador de taxa recebem a instância da
requisição HTTP recebida, você pode criar o limite de taxa apropriado
dinamicamente com base na requisição recebida ou na pessoa usuária autenticada:

```php
RateLimiter::for('uploads', function (Request $request) {
    return $request->user()->vipCustomer()
        ? Limit::none()
        : Limit::perMinute(100);
});
```

Às vezes, você pode querer segmentar os limites de taxa por algum valor
arbitrário.
Por exemplo, você pode permitir que as pessoas usuárias acessem uma
determinada rota 100 vezes por minuto por endereço IP.
Para fazer isso, você pode usar o método `by` ao construir seu limite de taxa:

```php
RateLimiter::for('uploads', function (Request $request) {
    return $request->user()->vipCustomer()
        ? Limit::none()
        : Limit::perMinute(100)->by($request->ip());
});
```

Limitadores de taxa podem ser anexados a rotas ou grupos de rotas usando o
[_middleware_](../middleware.md) `throttle`.
O _middleware_ `throttle` aceita o nome do limitador de taxa que você deseja
atribuir à rota:

```php
Route::middleware(['throttle:uploads'])->group(function () {
    Route::post('/audio', function () {
        //
    });

    Route::post('/video', function () {
        //
    });
});
```

Para saber mais sobre limitação de taxa, consulte a
[documentação de roteamento](../routing.md#rate-limiting).

### Modo de Manutenção Aprimorado

_As melhorias no modo de manutenção foram contribuídas por
[Taylor Otwell](https://github.com/taylorotwell) com inspiração na
[Spatie](https://spatie.be)_.

Nas versões anteriores do Laravel, o recurso do modo de manutenção
`php artisan down` pode ser ignorado usando uma “lista de permissões” de
endereços IP autorizados a acessar a aplicação.
Este recurso foi removido em favor de uma solução de “segredo” ou _token_ mais
simples.

Enquanto estiver no modo de manutenção, você pode usar a opção `secret` para
especificar um _token_ de desvio do modo de manutenção:

```shell
php artisan down --secret="1630542a-246b-4b66-afa1-dd72a4c43515"
```

Após colocar a aplicação em modo de manutenção, você pode navegar até a URL da
aplicação correspondente a este _token_ e o Laravel emitirá um _cookie_ de
desvio do modo de manutenção para o seu navegador:

```text
https://example.com/1630542a-246b-4b66-afa1-dd72a4c43515
```

Ao acessar esta rota oculta, você será então redirecionado para a rota `/` da
aplicação.
Uma vez emitido o _cookie_ para o seu navegador, você poderá navegar na
aplicação normalmente como se ela não estivesse em modo de manutenção.

#### Pré-renderizando a Visualização do Modo de Manutenção

Se você utilizar o comando `php artisan down` durante a implantação, suas
pessoas usuárias ainda poderão ocasionalmente encontrar erros se acessarem a
aplicação enquanto as dependências do Composer ou outros componentes de
infraestrutura estiverem sendo atualizados.
Isso ocorre porque uma parte significativa do _framework_ Laravel deve ser
inicializada para determinar se sua aplicação está em modo de manutenção e
renderizar a visualização do modo de manutenção usando o mecanismo de modelos.

Por esse motivo, o Laravel agora permite pré-renderizar uma visualização em modo
de manutenção que será retornada logo no início do ciclo de requisição.
Essa visualização é renderizada antes do carregamento de qualquer dependência da
sua aplicação.
Você pode pré-renderizar um modelo de sua escolha usando a opção `render` do
comando `down`:

```shell
php artisan down --render="errors::503"
```

### _Closure_ de Despacho e Encadeamento no `catch`

_As melhorias no `catch` foram contribuídas por
[Mohamed Said](https://github.com/themsaid)_.

Usando o novo método `catch`, agora você pode fornecer uma _closure_ que deve
ser executada se uma _closure_ na fila não for concluída com sucesso após
esgotar todas as tentativas de repetição configuradas na sua fila:

```php
use Throwable;

dispatch(function () use ($podcast) {
    $podcast->publish();
})->catch(function (Throwable $e) {
    // Este trabalho falhou...
});
```

### Componentes Dinâmicos do Blade

_Os componentes dinâmicos do Blade foram contribuídos por
[Taylor Otwell](https://github.com/taylorotwell)_.

Às vezes você pode precisar renderizar um componente, mas não saber qual
componente deve ser renderizado até o tempo de execução.
Nesta situação, agora você pode usar o componente `dynamic-component` nativo do
Laravel para renderizar o componente com base em um valor ou variável de tempo
de execução:

```bladehtml
<x-dynamic-component :component="$componentName" class="mt-4"/>
```

Para saber mais sobre os componentes do Blade, consulte a
[documentação do Blade](../blade.md#components).

### Melhorias nos Ouvintes de Eventos

_As melhorias nos ouvintes de eventos foram contribuídas por
[Taylor Otwell](https://github.com/taylorotwell)_.

Os ouvintes de eventos baseados em _closure_ agora podem ser registrados
simplesmente passando a _closure_ para o método `Event::listen`.
O Laravel irá inspecionar a _closure_ para determinar qual tipo de evento o
ouvinte trata:

```php
use App\Events\PodcastProcessed;
use Illuminate\Support\Facades\Event;

Event::listen(function (PodcastProcessed $event) {
    //
});
```

Além disso, ouvintes de eventos baseados em _closure_ agora podem ser marcados
como usáveis em fila usando a função `Illuminate\Events\queueable`:

```php
use App\Events\PodcastProcessed;
use function Illuminate\Events\queueable;
use Illuminate\Support\Facades\Event;

Event::listen(queueable(function (PodcastProcessed $event) {
    //
}));
```

Assim como os trabalhos usáveis em fila, você pode usar os métodos
`onConnection`, `onQueue` e `delay` para personalizar a execução do ouvinte
em fila:

```php
Event::listen(queueable(function (PodcastProcessed $event) {
    //
})->onConnection('redis')->onQueue('podcasts')->delay(now()->addSeconds(10)));
```

Se quiser lidar com falhas anônimas de ouvintes em fila, você pode fornecer uma
_closure_ para o método `catch` ao definir o ouvinte `queueable`:

```php
use App\Events\PodcastProcessed;
use function Illuminate\Events\queueable;
use Illuminate\Support\Facades\Event;
use Throwable;

Event::listen(queueable(function (PodcastProcessed $event) {
    //
})->catch(function (PodcastProcessed $event, Throwable $e) {
    // O ouvinte na fila falhou...
}));
```

### Auxiliares de Testes de Tempo

_Os auxiliares de testes de tempo foram contribuídos por
[Taylor Otwell](https://github.com/taylorotwell) com inspiração no Ruby on
Rails_.

Ao testar, ocasionalmente você pode precisar modificar o tempo retornado por
auxiliares como `now` ou `Illuminate\Support\Carbon::now()`.
A classe base de teste de recursos do Laravel agora inclui auxiliares que
permitem manipular a hora atual:

```php
public function testTimeCanBeManipulated()
{
    // Viaja para o futuro...
    $this->travel(5)->milliseconds();
    $this->travel(5)->seconds();
    $this->travel(5)->minutes();
    $this->travel(5)->hours();
    $this->travel(5)->days();
    $this->travel(5)->weeks();
    $this->travel(5)->years();

    // Viaja para o passado...
    $this->travel(-5)->hours();

    // Viaja para um tempo específico...
    $this->travelTo(now()->subHours(6));

    // Retorna ao tempo presente...
    $this->travelBack();
}
```

### Melhorias no Comando `serve` do Artisan

_As melhorias no comando `serve` do Artisan foram contribuídas por
[Taylor Otwell](https://github.com/taylorotwell)_.

O comando `serve` do Artisan foi aprimorado com recarregamento automático quando
alterações nas variáveis de ambiente são detectadas no seu arquivo `.env` local.
Anteriormente, o comando precisava ser interrompido e reiniciado manualmente.

### Visualizações de Paginação do Tailwind

O paginador do Laravel foi atualizado para usar o _framework_
[Tailwind CSS](https://tailwindcss.com) por padrão.
O Tailwind CSS é um _framework_ CSS de baixo nível altamente personalizável
que fornece todos os blocos de construção necessários para criar _designs_
personalizados, sem quaisquer estilos opinativos irritantes que você tenha que
lutar para substituir.
Claro, as visualizações Bootstrap 3 e 4 também permanecem disponíveis.

### Atualizações do _Namespace_ de Roteamento

Nas versões anteriores do Laravel, o `RouteServiceProvider` continha uma
propriedade `$namespace`.
O valor desta propriedade seria automaticamente prefixo nas definições de rota
do controlador e nas chamadas para a função auxiliar `action` e o método
`URL::action`.
No Laravel 8.x, esta propriedade é `null` por padrão.
Isso significa que nenhuma prefixação automática de _namespace_ será feita
pelo Laravel.
Portanto, nas novas aplicações Laravel 8.x, as definições de rota do controlador
devem ser definidas usando a sintaxe padrão de _callables_ do PHP:

```php
use App\Http\Controllers\UserController;

Route::get('/users', [UserController::class, 'index']);
```

As chamadas para os métodos relacionados a `action` devem usar a mesma sintaxe
de _callables_:

```php
action([UserController::class, 'index']);

return Redirect::action([UserController::class, 'index']);
```

Se você preferir o prefixo de rota do controlador no estilo Laravel 7.x, você
pode simplesmente adicionar a propriedade `$namespace` no `RouteServiceProvider`
da sua aplicação.

> **Nota:**
> Esta mudança afeta apenas novas aplicações Laravel 8.x.
> As aplicações atualizadas do Laravel 7.x ainda terão a propriedade
> `$namespace` em seu `RouteServiceProvider`.
