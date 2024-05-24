<!-- source_url: https://github.com/laravel/docs/blob/11.x/releases.md -->
<!-- revision: 2099c290d28e19aac91eed068a986231fa2447f7 -->
<!-- status: ready -->

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
de uma classe separada do manipulador de exceções, reduzindo o número total de
arquivos incluídos em uma nova aplicação Laravel:

```php
->withExceptions(function (Exceptions $exceptions) {
    $exceptions->dontReport(MissedFlightException::class);

    $exceptions->report(function (InvalidOrderException $e) {
        // ...
    });
})
```

#### Classe `Controller` Base

O controlador base incluído nas novas aplicações Laravel foi simplificado.
Ele não estende mais a classe `Controller` interna do Laravel, e as traits
`AuthorizesRequests` e `ValidatesRequests` foram removidas, pois podem ser
incluídas nos controladores individuais da sua aplicação, se desejado:

```php
<?php

namespace App\Http\Controllers;

abstract class Controller
{
    //
}
```

#### Padrões da Aplicação

Por padrão, as novas aplicações Laravel usam SQLite para armazenamento de banco
de dados, bem como o driver `database` para sessão, cache e fila do Laravel.
Isso permite que você comece a construir sua aplicação imediatamente após criar
uma nova aplicação Laravel, sem ser necessário instalar software adicional ou
criar migrações adicionais de banco de dados.

Além disso, com o tempo, os drivers `database` para esses serviços do Laravel
tornaram-se robustos o suficiente para uso em produção em muitos contextos de
aplicação; portanto, eles oferecem uma escolha sensata e unificada para
aplicações locais e de produção.

### Laravel Reverb

_O Laravel Reverb foi desenvolvido por
[Joe Dixon](https://github.com/joedixon)_.

O [Laravel Reverb](https://reverb.laravel.com) traz comunicação WebSocket em
tempo real extremamente rápida e escalável diretamente para sua aplicação
Laravel e fornece integração perfeita com o conjunto existente de ferramentas de
transmissão de eventos do Laravel, como o Laravel Echo.

```shell
php artisan reverb:start
```

Além disso, o Reverb oferece suporte ao dimensionamento horizontal por meio dos
recursos de publicação/assinatura do Redis, permitindo distribuir o tráfego
WebSocket por vários servidores Reverb de back-end, todos suportando uma única
aplicação de alta demanda.

Para mais informações sobre o Laravel Reverb, consulte a [documentação completa
do Reverb](../reverb.md).

### Limitação de Taxa por Segundo

_A limitação de taxa por segundo foi contribuída por
[Tim MacDonald](https://github.com/timacdonald)_.

O Laravel agora suporta limitação de taxa "por segundo" para todos os
limitadores de taxa, incluindo aqueles para requisições HTTP e trabalhos em
fila.
Anteriormente, os limitadores de taxa do Laravel eram limitados à granularidade
"por minuto":

```php
RateLimiter::for('invoices', function (Request $request) {
    return Limit::perSecond(1);
});
```

Para obter mais informações sobre limitação de taxa no Laravel, verifique a
[documentação sobre limitação de taxa](../routing.md#rate-limiting).

### Roteamento de Integridade

_O roteamento de integridade foi contribuído por
[Taylor Otwell](https://github.com/taylorotwell)_.

As novas aplicações Laravel 11 incluem uma diretiva de roteamento `health`, que
instrui o Laravel a definir um endpoint de verificação de integridade simples
que pode ser invocado por serviços de monitoramento de integridade de aplicações
de terceiros ou sistemas de orquestração como o Kubernetes.
Por padrão, esta rota é servida em `/up`:

```php
->withRouting(
    web: __DIR__.'/../routes/web.php',
    commands: __DIR__.'/../routes/console.php',
    health: '/up',
)
```

Quando requisições HTTP forem feitas a esta rota, o Laravel também despachará um
evento `DiagnosingHealth`, permitindo realizar verificações de integridade
adicionais relevantes para sua aplicação.

### Rotação Elegante das Chaves de Criptografia

_A rotação elegante das chaves de criptografia foi contribuída por
[Taylor Otwell](https://github.com/taylorotwell)_.

Como o Laravel criptografa todos os cookies, incluindo o cookie de sessão da sua
aplicação, essencialmente toda requisição para uma aplicação Laravel depende de
criptografia.
No entanto, por causa disso, a rotação da chave de criptografia da sua aplicação
desconectaria todos os usuários da sua aplicação.
Além disso, torna-se impossível descriptografar os dados criptografados pela
chave de criptografia anterior.

O Laravel 11 permite que você defina as chaves de criptografia anteriores da sua
aplicação como uma lista delimitada por vírgula através da variável de ambiente
`APP_PREVIOUS_KEYS`.

Ao criptografar valores, o Laravel sempre usará a chave de criptografia "atual",
que está na variável de ambiente `APP_KEY`.
Ao descriptografar valores, o Laravel tentará primeiro a chave atual.
Se a descriptografia falhar usando a chave atual, o Laravel tentará todas as
chaves anteriores até que uma das chaves seja capaz de descriptografar o valor.

Essa abordagem de descriptografia elegante permite que os usuários continuem
usando sua aplicação ininterruptamente, mesmo que sua chave de criptografia
seja alternada.

Para mais informações sobre criptografia no Laravel, confira
a [documentação sobre criptografia](../encryption.md).

### Rehashing Automático de Senha

_O rehashing automático de senha foi contribuído por
[Stephen Rees-Carter](https://github.com/valorin)_.

O algoritmo de hash de senha padrão do Laravel é o bcrypt.
O "fator de trabalho" para hashes bcrypt pode ser ajustado através do arquivo de
configuração `config/hashing.php` ou da variável de ambiente `BCRYPT_ROUNDS`.

Normalmente, o fator de trabalho do bcrypt deve ser aumentado ao longo do tempo
à medida que o poder de processamento das CPUs/GPUs aumenta.
Se você aumentar o fator de trabalho do bcrypt para sua aplicação, o Laravel irá
refazer as senhas dos usuários de maneira elegante e automática à medida que os
usuários se autenticam na sua aplicação.

### Validação de Prompt

_A integração com o validador do Prompt foi contribuída por
[Andrea Marco Sartori](https://github.com/cerbero90)_.

[Laravel Prompts](../prompts.md) é um pacote PHP para adicionar formulários
bonitos e fáceis de usar às suas aplicações de linha de comando, com recursos
semelhantes aos de navegador, incluindo texto de espaço reservado e validação.

O Laravel Prompts suporta validação de entrada por meio de closures:

```php
$name = text(
    label: 'Qual o seu nome?',
    validate: fn (string $value) => match (true) {
        strlen($value) < 3 => 'O nome deve ter pelo menos 3 caracteres.',
        strlen($value) > 255 => 'O nome não deve exceder 255 caracteres.',
        default => null
    }
);
```

No entanto, isso pode se tornar trabalhoso ao lidar com muitas entradas ou
cenários de validação complicados.
Portanto, no Laravel 11, você pode usar todo o poder do
[validador](../validation.md) do Laravel ao validar entradas de prompt:

```php
$name = text('Qual o seu nome?', validate: [
    'name' => 'required|min:3|max:255',
]);
```

### Testes de Interação de Fila

_Os testes de interação de fila foram contribuídos por
[Taylor Otwell](https://github.com/taylorotwell)_.

Anteriormente, tentar testar se um trabalho na fila foi liberado, excluído ou
falhou manualmente era complicado e exigia a definição de dados de teste e stubs
de fila personalizados.
No entanto, no Laravel 11, você pode testar facilmente essas interações de fila
usando o método `withFakeQueueInteractions`:

```php
use App\Jobs\ProcessPodcast;

$job = (new ProcessPodcast)->withFakeQueueInteractions();

$job->handle();

$job->assertReleased(delay: 30);
```

Para obter mais informações sobre como testar trabalhos em fila, confira a
[documentação sobre filas](../queues.md#testing).

### Novos Comandos do Artisan

_Os comandos de criação de classes do Artisan foram contribuídos por
[Taylor Otwell](https://github.com/taylorotwell)_.

Novos comandos do Artisan foram adicionados para permitir a criação rápida de
classes, enums, interfaces e traits:

```shell
php artisan make:class
php artisan make:enum
php artisan make:interface
php artisan make:trait
```

### Melhorias nas Conversões de Modelos

_As melhorias nas conversões de modelos foram contribuídas por
[Nuno Maduro](https://github.com/nunomaduro)_.

O Laravel 11 suporta a definição das conversões do seu modelo usando um método
em vez de uma propriedade.
Isso permite definições de conversão simplificadas e fluentes, especialmente ao
usar conversões com argumentos:

```php
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
```

Para obter mais informações sobre conversão de atributos, revise a
[documentação do Eloquent](../eloquent-mutators.md#attribute-casting).

### A Função `once`

_A função auxiliar `once` foi contribuída por
[Taylor Otwell](https://github.com/taylorotwell)_ e
_[Nuno Maduro](https://github.com/nunomaduro)_.

A função auxiliar `once` executa a função de retorno de chamada fornecida e
armazena o resultado em cache na memória durante a duração da requisição.
Qualquer chamada subsequentes para a função `once` com a mesma função de retorno
de chamada retornará o resultado armazenado em cache anteriormente:

```php
function random(): int
{
    return once(function () {
        return random_int(1, 1000);
    });
}

random(); // 123
random(); // 123 (resultado armazenado em cache)
random(); // 123 (resultado armazenado em cache)
```

Para obter mais informações sobre a função auxiliar `once`, verifique a
documentação das [funções auxiliares](../helpers.md#method-once).

### Desempenho Aprimorado dos Testes com Banco de Dados em Memória

_O desempenho aprimorado dos testes com banco de dados em memória foi
contribuído por [Anders Jenbo](https://github.com/AJenbo)_.

O Laravel 11 oferece um aumento significativo de velocidade ao usar o banco de
dados SQLite `:memory:` durante os testes.
Para conseguir isso, o Laravel agora mantém uma referência ao objeto PDO do PHP
e o reutiliza entre as conexões, muitas vezes reduzindo pela metade o tempo
total de execução do teste.

### Suporte Aprimorado ao MariaDB

_O suporte aprimorado ao MariaDB foi contribuído por
[Jonas Staudenmeir](https://github.com/staudenmeir) e
[Julius Kiekbusch](https://github.com/Jubeki)_.

O Laravel 11 inclui suporte aprimorado ao MariaDB.
Nas versões anteriores do Laravel, você poderia usar o MariaDB através do driver
MySQL do Laravel.
No entanto, o Laravel 11 agora inclui um driver MariaDB dedicado que fornece
melhores padrões para este sistema de banco de dados.

Para mais informações sobre os drivers de banco de dados do Laravel, confira a
[documentação sobre banco de dados](../database.md).

### Operação e Inspeção Aprimoradas de Esquemas de Bancos de Dados

_Operação e inspeção aprimoradas de esquemas de bancos de dados foram
contribuídas por [Hafez Divandari](https://github.com/hafezdivandari)_.

O Laravel 11 fornece métodos adicionais de operação e inspeção de esquema de
banco de dados, incluindo modificação, renomeação e remoção nativa de colunas.
Além disso, são fornecidos tipos espaciais avançados, nomes de esquema não
padrão e métodos de esquema nativos para manipulação de tabelas, visualizações,
colunas, índices e chaves estrangeiras:

```php
use Illuminate\Support\Facades\Schema;

$tables = Schema::getTables();
$views = Schema::getViews();
$columns = Schema::getColumns('users');
$indexes = Schema::getIndexes('users');
$foreignKeys = Schema::getForeignKeys('users');
```
