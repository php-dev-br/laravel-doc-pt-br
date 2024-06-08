<!-- source_url: https://github.com/laravel/docs/blob/11.x/container.md -->
<!-- revision: 46c2634ef5a4f15427c94a3157b626cf5bd3937f -->
<!-- status: wip -->

# Contêiner de Serviço

- [Introdução](#introducao)
    - [Resolução sem Configuração](#resolucao-sem-configuracao)
    - [Quando Usar o Contêiner](#quando-usar-o-conteiner)
- [Vinculação](#vinculacao)
    - [Noções Básicas de Vinculação](#nocoes-basicas-de-vinculacao)
    - [Vinculando Interfaces a Implementações](#vinculando-interfaces-a-implementacoes)
    - [Vinculação Contextual](#vinculacao-contextual)
    - [Vinculando Primitivos](#vinculando-primitivos)
    - [Vinculando Variádicas Tipadas](#vinculando-variadicas-tipadas)
    - [_Tagging_](#tagging)
    - [Estendendo Vinculações](#estendendo-vinculacoes)
- [Resolvendo](#resolvendo)
    - [O Método `make`](#o-metodo-make)
    - [Injeção Automática](#injecao-automatica)
- [Invocação e Injeção de Método](#invocacao-e-injecao-de-metodo)
- [Eventos de Contêiner](#eventos-de-conteiner)
- [PSR-11](#psr-11)

## Introdução

O contêiner de serviço Laravel é uma ferramenta poderosa para gerenciar
dependências de classe e realizar injeção de dependência.
Injeção de dependência é uma frase sofisticada que significa essencialmente o
seguinte: dependências de classe são injetadas na classe por meio do construtor
ou, em alguns casos, métodos _setters_.

Vejamos um exemplo simples:

```php
<?php

namespace App\Http\Controllers;

use App\Http\Controllers\Controller;
use App\Repositories\UserRepository;
use App\Models\User;
use Illuminate\View\View;

class UserController extends Controller
{
    /**
     * Cria uma nova instância do controlador.
     */
    public function __construct(
        protected UserRepository $users,
    ) {}

    /**
     * Exibe o perfil do usuário fornecido.
     */
    public function show(string $id): View
    {
        $user = $this->users->find($id);

        return view('user.profile', ['user' => $user]);
    }
}
```

Neste exemplo, o `UserController` precisa recuperar usuários de uma fonte de
dados.
Então, vamos **injetar** um serviço capaz de recuperar usuários.
Nesse contexto, nosso `UserRepository` provavelmente usa o
[Eloquent](../eloquent.md) para recuperar informações do usuário do banco de
dados.
No entanto, como o repositório é injetado, podemos trocá-lo facilmente por outra
implementação.
Também podemos simular facilmente ou criar uma implementação fictícia do
`UserRepository` ao testar nossa aplicação.

Um conhecimento profundo do contêiner de serviço do Laravel é essencial para
construir uma aplicação grande e poderosa, bem como para contribuir com o
próprio núcleo do Laravel.

### Resolução sem Configuração

Se uma classe não tiver dependências ou depender apenas de outras classes
concretas (não interfaces), o contêiner não precisará ser instruído sobre
como resolver essa classe.
Por exemplo, você pode colocar o seguinte código no arquivo `routes/web.php`:

```php
<?php

class Service
{
    // ...
}

Route::get('/', function (Service $service) {
    die($service::class);
});
```

Neste exemplo, acessar a rota `/` da sua aplicação resolverá automaticamente a
classe `Service` e a injetará no manipulador da sua rota.
Isto faz toda a diferença.
Isso significa que você pode desenvolver sua aplicação e aproveitar as
vantagens da injeção de dependência sem se preocupar com arquivos de
configuração gigantescos.

Felizmente, muitas das classes que você escreverá ao construir uma aplicação
Laravel recebem automaticamente suas dependências por meio do contêiner,
incluindo [controladores](../controllers.md),
[ouvintes de eventos](../events.md), [middlewares](../middleware.md) e muito
mais.
Além disso, você pode declarar o tipo das dependências no método `handle` dos
[trabalhos em fila](../queues.md).
Após experimentar o poder da injeção de dependência automática e sem
configuração, parece impossível desenvolver sem ela.

### Quando Usar o Contêiner

Graças à resolução sem configuração, você frequentemente declarará o tipo das
dependências em rotas, controladores, ouvintes de eventos e em outros lugares,
sem nunca interagir manualmente com o contêiner.
Por exemplo, você pode declarar o tipo do objeto `Illuminate\Http\Request` na
sua definição de rota para poder acessar facilmente a requisição atual.
Mesmo que nunca tenhamos que interagir com o contêiner para escrever esse
código, ele gerencia a injeção dessas dependências nos bastidores:

```php
use Illuminate\Http\Request;

Route::get('/', function (Request $request) {
    // ...
});
```

Em muitos casos, graças à injeção automática de dependências e às
[fachadas](../facades.md), você pode construir aplicações Laravel sem **nunca**
vincular manualmente ou resolver nada do contêiner.
**Então, quando você interagiria manualmente com o contêiner?**
Vamos examinar duas situações.

Primeiro, se você escrever uma classe que implementa uma interface e desejar
declarar o tipo desta interface em uma rota ou construtor de classe, você deve
[informar ao contêiner como resolver essa interface](#vinculando-interfaces-a-implementacoes).
Em segundo lugar, se você estiver [escrevendo um pacote Laravel](../packages.md)
que planeja compartilhar com outras pessoas desenvolvedoras Laravel, pode ser
necessário vincular os serviços do seu pacote ao contêiner.

## Vinculação

### Noções Básicas de Vinculação

#### Vinculações Simples

Quase todas as suas vinculações de contêiner de serviço serão registradas nos
[provedores de serviços](../providers.md), portanto, a maioria desses exemplos
demonstrará o uso do contêiner nesse contexto.

Em um provedor de serviço, você sempre tem acesso ao contêiner por meio da
propriedade `$this->app`.
Podemos registrar uma vinculação usando o método `bind`, passando o nome da
classe ou interface que desejamos registrar com uma _clojure_ que retorna uma
instância da classe:

```php
use App\Services\Transistor;
use App\Services\PodcastParser;
use Illuminate\Contracts\Foundation\Application;

$this->app->bind(Transistor::class, function (Application $app) {
    return new Transistor($app->make(PodcastParser::class));
});
```

Observe que recebemos o próprio contêiner como argumento para o resolvedor.
Podemos então usar o contêiner para resolver subdependências do objeto que
estamos construindo.

Conforme mencionado, você normalmente interagirá com o contêiner nos provedores
de serviços; no entanto, se desejar interagir com o contêiner fora de um
provedor de serviços, você poderá fazê-lo por meio da [fachada](../facades.md)
`App`:

```php
use App\Services\Transistor;
use Illuminate\Contracts\Foundation\Application;
use Illuminate\Support\Facades\App;

App::bind(Transistor::class, function (Application $app) {
    // ...
});
```

Você pode usar o método `bindIf` para registrar uma vinculação de contêiner
somente se uma vinculação ainda não tiver sido registrada para o tipo
determinado:

```php
$this->app->bindIf(Transistor::class, function (Application $app) {
    return new Transistor($app->make(PodcastParser::class));
});
```

> **Nota:**
> Não há necessidade de vincular classes ao contêiner se elas não dependerem de
> nenhuma interface.
> O contêiner não precisa ser instruído sobre como construir esses objetos, pois
> ele pode resolvê-los automaticamente usando reflexão.

<a name="binding-a-singleton"></a>

#### Vinculando um Singleton

O método `singleton` vincula uma classe ou interface ao contêiner que deve ser
resolvida apenas uma vez.
Depois que uma vinculação _singleton_ for resolvida, a mesma instância do objeto
será retornada nas chamadas subsequentes ao contêiner:

```php
use App\Services\Transistor;
use App\Services\PodcastParser;
use Illuminate\Contracts\Foundation\Application;

$this->app->singleton(Transistor::class, function (Application $app) {
    return new Transistor($app->make(PodcastParser::class));
});
```

Você pode usar o método `singletonIf` para registrar uma vinculação de contêiner
_singleton_ somente se uma vinculação ainda não tiver sido registrada para o
tipo especificado:

```php
$this->app->singletonIf(Transistor::class, function (Application $app) {
    return new Transistor($app->make(PodcastParser::class));
});
```

<a name="binding-scoped"></a>

#### Vinculação de Singletons com Escopo

O método `scoped` vincula uma classe ou interface ao contêiner que deve ser
resolvida apenas uma vez em um determinado ciclo de vida da requisição/trabalho
do Laravel.
Embora este método seja semelhante ao método `singleton`, as instâncias
registradas usando o método `scoped` serão liberadas sempre que a aplicação
Laravel iniciar um novo ciclo de vida, como quando um _worker_
[Laravel Octane](../octane.md) processa uma nova requisição ou quando um
[_worker_ de fila](../queues.md) do Laravel processa um novo trabalho:

```php
use App\Services\Transistor;
use App\Services\PodcastParser;
use Illuminate\Contracts\Foundation\Application;

$this->app->scoped(Transistor::class, function (Application $app) {
    return new Transistor($app->make(PodcastParser::class));
});
```

#### Vinculando Instâncias

Você também pode vincular uma instância de objeto existente ao contêiner usando
o método `instance`.
A instância fornecida sempre será retornada nas chamadas subsequentes ao
contêiner:

```php
use App\Services\Transistor;
use App\Services\PodcastParser;

$service = new Transistor(new PodcastParser);

$this->app->instance(Transistor::class, $service);
```

### Vinculando Interfaces a Implementações

Um recurso muito poderoso do contêiner de serviço é a capacidade de vincular uma
interface a uma determinada implementação.
Por exemplo, vamos supor que temos uma interface `EventPusher` e uma
implementação `RedisEventPusher`.
Após codificarmos nossa implementação `RedisEventPusher` desta interface,
podemos registrá-la no contêiner de serviço da seguinte forma:

```php
use App\Contracts\EventPusher;
use App\Services\RedisEventPusher;

$this->app->bind(EventPusher::class, RedisEventPusher::class);
```

Esta instrução informa ao contêiner que ele deve injetar `RedisEventPusher`
quando uma classe precisar de uma implementação de `EventPusher`.
Agora podemos declarar o tipo da interface `EventPusher` no construtor de uma
classe resolvida pelo contêiner.
Lembre-se, controladores, ouvintes de eventos, _middlewares_ e vários outros
tipos de classes dentro de aplicações Laravel são sempre resolvidos usando o
contêiner:

```php
use App\Contracts\EventPusher;

/**
 * Cria uma nova instância da classe.
 */
public function __construct(
    protected EventPusher $pusher
) {}
```

### Vinculação Contextual

Às vezes você pode ter duas classes que utilizam a mesma interface, mas deseja
injetar implementações diferentes em cada classe.
Por exemplo, dois controladores podem depender de implementações diferentes do
[contrato](../contracts.md). `Illuminate\Contracts\Filesystem\Filesystem`.
O Laravel fornece uma interface simples e fluente para definir este
comportamento:

```php
use App\Http\Controllers\PhotoController;
use App\Http\Controllers\UploadController;
use App\Http\Controllers\VideoController;
use Illuminate\Contracts\Filesystem\Filesystem;
use Illuminate\Support\Facades\Storage;

$this->app->when(PhotoController::class)
          ->needs(Filesystem::class)
          ->give(function () {
              return Storage::disk('local');
          });

$this->app->when([VideoController::class, UploadController::class])
          ->needs(Filesystem::class)
          ->give(function () {
              return Storage::disk('s3');
          });
```

### Vinculando Primitivos

Às vezes você pode ter uma classe que recebe algumas classes injetadas, mas
também precisa de um valor primitivo injetado, como um número inteiro.
Você pode facilmente usar vinculação contextual para injetar qualquer valor que
sua classe possa precisar:

```php
use App\Http\Controllers\UserController;

$this->app->when(UserController::class)
          ->needs('$variableName')
          ->give($value);
```

Às vezes, uma classe pode depender de um _array_ de instâncias
[com uma _tag_](#tagging).
Usando o método `giveTagged`, você pode injetar facilmente todas as vinculações
do contêiner com essa _tag_:

```php
$this->app->when(ReportAggregator::class)
    ->needs('$reports')
    ->giveTagged('reports');
```

Se precisar injetar um valor de um dos arquivos de configuração da sua
aplicação, você pode usar o método `giveConfig`:

```php
$this->app->when(ReportAggregator::class)
    ->needs('$timezone')
    ->giveConfig('app.timezone');
```

### Vinculando Variádicas Tipadas

Ocasionalmente, você pode ter uma classe que recebe um _array_ de objetos
tipados usando um argumento variádico no construtor:

```php
<?php

use App\Models\Filter;
use App\Services\Logger;

class Firewall
{
    /**
     * As instâncias de filtro.
     *
     * @var array
     */
    protected $filters;

    /**
     * Cria uma nova instância da classe.
     */
    public function __construct(
        protected Logger $logger,
        Filter ...$filters,
    ) {
        $this->filters = $filters;
    }
}
```

Usando vinculação contextual, você pode resolver essa dependência fornecendo ao
método `give` uma _clojure_ que retorna um _array_ de instâncias de `Filter`
resolvidas:

```php
$this->app->when(Firewall::class)
          ->needs(Filter::class)
          ->give(function (Application $app) {
                return [
                    $app->make(NullFilter::class),
                    $app->make(ProfanityFilter::class),
                    $app->make(TooLongFilter::class),
                ];
          });
```

Por conveniência, você também pode fornecer um _array_ de nomes de classes a
serem resolvidas pelo contêiner sempre que o `Firewall` precisar de instâncias
de `Filter`:

```php
$this->app->when(Firewall::class)
          ->needs(Filter::class)
          ->give([
              NullFilter::class,
              ProfanityFilter::class,
              TooLongFilter::class,
          ]);
```

#### Dependências de _Tags_ Variádicas

Às vezes, uma classe pode ter uma dependência variádica declarada com o tipo de
uma determinada classe (`Report ...$reports`).
Usando os métodos `need` e `giveTagged`, você pode facilmente injetar todas as
vinculações de contêiner com essa [_tag_](#tagging) para a dependência
fornecida:

```php
$this->app->when(ReportAggregator::class)
          ->needs(Report::class)
          ->giveTagged('reports');
```

### _Tagging_

Ocasionalmente, pode ser necessário resolver toda uma determinada categoria de
vinculação.
Por exemplo, talvez você esteja construindo um analisador de relatórios que
receba um _array_ de muitas implementações diferentes da interface `Report`.
Após registrar as implementações de `Report`, você pode atribuir uma _tag_ a
elas usando o método `tag`:

```php
$this->app->bind(CpuReport::class, function () {
    // ...
});

$this->app->bind(MemoryReport::class, function () {
    // ...
});

$this->app->tag([CpuReport::class, MemoryReport::class], 'reports');
```

Após os serviços receberem _tags_, você poderá resolvê-los facilmente por meio
do método `tagged` do contêiner:

```php
$this->app->bind(ReportAnalyzer::class, function (Application $app) {
    return new ReportAnalyzer($app->tagged('reports'));
});
```

### Estendendo Vinculações

O método `extend` permite a modificação de serviços resolvidos.
Por exemplo, quando um serviço é resolvido, você pode executar código adicional
para decorar ou configurar o serviço.
O método `extend` aceita dois argumentos, a classe de serviço que você está
estendendo e uma _clojure_ que deve retornar o serviço modificado.
A _clojure_ recebe o serviço que está sendo resolvido e a instância do
contêiner:

```php
$this->app->extend(Service::class, function (Service $service, Application $app) {
    return new DecoratedService($service);
});
```

## Resolvendo

### O Método `make`

Você pode usar o método `make` para resolver uma instância de classe do
contêiner.
O método `make` aceita o nome da classe ou interface que você deseja resolver:

```php
use App\Services\Transistor;

$transistor = $this->app->make(Transistor::class);
```

Se algumas das dependências da sua classe não puderem ser resolvidas por meio do
contêiner, você poderá injetá-las passando-as como um _array_ associativo para o
método `makeWith`.
Por exemplo, podemos passar manualmente o argumento do construtor `$id` exigido
pelo serviço `Transistor`:

```php
use App\Services\Transistor;

$transistor = $this->app->makeWith(Transistor::class, ['id' => 1]);
```

O método `bound` pode ser usado para determinar se uma classe ou interface foi
explicitamente vinculada no contêiner:

```php
if ($this->app->bound(Transistor::class)) {
    // ...
}
```

Se você estiver fora de um provedor de serviços em um local do seu código que
não tenha acesso à variável `$app`, poderá usar a [fachada](../facades.md) `App`
ou a função [auxiliar](../helpers.md#method-app) `app` para resolver uma
instância de classe do contêiner:

```php
use App\Services\Transistor;
use Illuminate\Support\Facades\App;

$transistor = App::make(Transistor::class);

$transistor = app(Transistor::class);
```

Se você quiser que a própria instância do contêiner Laravel seja injetada em uma
classe que está sendo resolvida pelo contêiner, você pode digitar a classe
`Illuminate\Container\Container` no construtor da sua classe:

```php
use Illuminate\Container\Container;

/**
 * Crie uma nova instância da classe.
 */
public function __construct(
    protected Container $container
) {}
```

### Injeção Automática

Alternativamente, e mais importante, você pode declarar o tipo da dependência no
construtor de uma classe resolvida pelo contêiner, incluindo
[controladores](../controllers.md), [ouvintes de eventos](../events.md),
[_middlewares_](../middleware.md) e muito mais.
Além disso, você pode declarar o tipo das dependências no método `handle`
dos [trabalhos em fila](../queues.md).
Na prática, é assim que a maioria dos seus objetos devem ser resolvidos pelo
container.

Por exemplo, você pode declarar o tipo um repositório definido pela sua
aplicação no construtor de um controlador.
O repositório será automaticamente resolvido e injetado na classe:

```php
<?php

namespace App\Http\Controllers;

use App\Repositories\UserRepository;
use App\Models\User;

class UserController extends Controller
{
    /**
     * Crie uma nova instância do controlador.
     */
    public function __construct(
        protected UserRepository $users,
    ) {}

    /**
     * Exibe o usuário com o ID fornecido.
     */
    public function show(string $id): User
    {
        $user = $this->users->findOrFail($id);

        return $user;
    }
}
```

## Invocação e Injeção de Método

Às vezes você pode desejar invocar um método em uma instância de objeto enquanto
permite que o contêiner injete automaticamente as dependências desse método.
Por exemplo, dada a seguinte classe:

```php
<?php

namespace App;

use App\Repositories\UserRepository;

class UserReport
{
    /**
     * Gera um novo relatório de usuário.
     */
    public function generate(UserRepository $repository): array
    {
        return [
            // ...
        ];
    }
}
```

Você pode invocar o método `generate` através do contêiner assim:

```php
use App\UserReport;
use Illuminate\Support\Facades\App;

$report = App::call([new UserReport, 'generate']);
```

O método `call` aceita qualquer _callable_ PHP.
O método `call` do contêiner pode até ser usado para invocar uma _conjure_
enquanto injeta automaticamente suas dependências:

```php
use App\Repositories\UserRepository;
use Illuminate\Support\Facades\App;

$result = App::call(function (UserRepository $repository) {
    // ...
});
```

## Eventos de Contêiner

O contêiner de serviço dispara um evento sempre que resolve um objeto.
Você pode ouvir este evento usando o método `resolving`:

```php
use App\Services\Transistor;
use Illuminate\Contracts\Foundation\Application;

$this->app->resolving(Transistor::class, function (Transistor $transistor, Application $app) {
    // Chamado quando o contêiner resolve objetos do tipo "Transistor"...
});

$this->app->resolving(function (mixed $object, Application $app) {
    // Chamado quando o contêiner resolve objetos de qualquer tipo...
});
```

Como você pode ver, o objeto que está sendo resolvido será passado para o
retorno de chamada, permitindo definir quaisquer propriedades adicionais no
objeto antes que ele seja fornecido ao seu consumidor.

## PSR-11

O contêiner de serviço do Laravel implementa a interface
[PSR-11](https://github.com/php-fig/fig-standards/blob/master/accepted/PSR-11-container.md).
Portanto, você pode declarar o tipo da interface de contêiner PSR-11 para obter
uma instância do contêiner Laravel:

```php
use App\Services\Transistor;
use Psr\Container\ContainerInterface;

Route::get('/', function (ContainerInterface $container) {
    $service = $container->get(Transistor::class);

    // ...
});
```

Uma exceção será lançada se o identificador fornecido não puder ser resolvido.
A exceção será uma instância de `Psr\Container\NotFoundExceptionInterface` se o
identificador nunca tiver sido vinculado.
Se o identificador foi vinculado, mas não pôde ser resolvido, uma instância de
`Psr\Container\ContainerExceptionInterface` será lançada.
