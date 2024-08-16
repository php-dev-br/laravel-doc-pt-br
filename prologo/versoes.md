---
source_url: https://github.com/laravel/docs/blob/11.x/releases.md
revision: 1b69438a67473dc3a9c7f351b3fa742460d11054
status: ready
---

# Notas de Versão

- [Esquema de Versionamento](#esquema-de-versionamento)
- [Política de Suporte](#politica-de-suporte)
- [Laravel 11](#laravel-11)

## Esquema de Versionamento

O Laravel e seus outros pacotes originais seguem o
[Versionamento Semântico](https://semver.org/lang/pt-BR/).
As versões maiores do _framework_ são lançadas todos os anos (por volta do 1º
trimestre), enquanto as versões menores e de correções podem ser lançadas
semanalmente.
Versões menores e de correções **nunca** devem conter alterações significativas.

Ao referenciar o _framework_ Laravel e seus componentes a partir da sua
aplicação ou pacote, você deve sempre usar uma restrição de versão como `^11.0`,
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

|              Versão               |  PHP (*)  |       Lançamento        | Correções de erros até | Correções de segurança até |
|:---------------------------------:|:---------:|:-----------------------:|:----------------------:|:--------------------------:|
| <span class="bg-red-500">9</span> | 8.0 - 8.2 | 8 de fevereiro de 2022  |  8 de agosto de 2023   |   6 de fevereiro de 2024   |
|                10                 | 8.1 - 8.3 | 14 de fevereiro de 2023 |  6 de agosto de 2024   |   4 de fevereiro de 2025   |
|                11                 | 8.2 - 8.3 |   12 de março de 2024   | 3 de setembro de 2025  |    12 de março de 2026     |
|                12                 | 8.2 - 8.3 |  1º trimestre de 2025   |  3º trimestre de 2026  |    1º trimestre de 2027    |

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

## Laravel 11

O Laravel 11 continua as melhorias feitas no Laravel 10.x, introduzindo uma
estrutura de aplicação simplificada, limitação de taxa por segundo, roteamento
de integridade, rotação elegante de chaves de encriptação, melhorias nos testes
de fila, transporte de e-mail [Resend](https://resend.com), integração do
validador do Prompts, novos comandos do Artisan e muito mais.
Além disso, o Laravel Reverb, um servidor WebSocket original escalonável, foi
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
A partir deste arquivo, agora você pode personalizar o roteamento, os
_middlewares_, os provedores de serviços, o tratamento de exceções da sua
aplicação e muito mais.
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

#### Provedores de Serviços

Em vez da estrutura padrão da aplicação Laravel contendo cinco provedores de
serviços, o Laravel 11 inclui apenas um único `AppServiceProvider`.
A funcionalidade dos provedores de serviços anteriores foi incorporada ao
`bootstrap/app.php`, é tratada automaticamente pelo _framework_ ou pode ser
colocada no `AppServiceProvider` da sua aplicação.

Por exemplo, a descoberta de eventos agora está habilitada por padrão,
eliminando grande parte da necessidade do registro manual de eventos e seus
ouvintes.
No entanto, se precisar registrar eventos manualmente, você pode simplesmente
fazê-lo no `AppServiceProvider`.
Da mesma forma, os vínculos de modelo de rota ou portas de autorização que você
pode ter registrado anteriormente no `AuthServiceProvider` também podem ser
registradas no `AppServiceProvider`.

#### Desabilitação das Rotas de _Broadcast_ e da API

Os arquivos de rotas `api.php` e `channels.php` não estão mais presentes por
padrão, pois muitas aplicações não requerem esses arquivos.
Em vez disso, eles podem ser criados usando comandos simples do Artisan:

```shell
php artisan install:api

php artisan install:broadcasting
```

#### _Middlewares_

Anteriormente, as novas aplicações Laravel incluíam nove _middlewares_.
Esses _middlewares_ executavam uma variedade de tarefas, como autenticação de
requisições, filtragem de _strings_ de entrada e validação de _tokens_ CSRF.

No Laravel 11, esses _middlewares_ foram movidos para o próprio _framework_,
para não adicionarem volume à estrutura da sua aplicação.
Novos métodos para personalizar o comportamento desses _middlewares_ foram
adicionados ao _framework_ e podem ser invocados a partir do arquivo
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

Como todo _middleware_ pode ser facilmente personalizado por meio do arquivo
`bootstrap/app.php` da sua aplicação, a necessidade de uma classe “_kernel_”
HTTP separada foi eliminada.

#### Agendamento

Usando uma nova fachada `Schedule`, as tarefas agendadas agora podem ser
definidas diretamente no arquivo `routes/console.php` da sua aplicação,
eliminando a necessidade de uma classe “_kernel_” de console separada:

```php
use Illuminate\Support\Facades\Schedule;

Schedule::command('emails:send')->daily();
```

#### Tratamento de Exceções

Assim como o roteamento e os _middlewares_, o tratamento de exceções agora pode
ser personalizado a partir do arquivo `bootstrap/app.php` da sua aplicação, em
vez de uma classe separada do manipulador de exceções, reduzindo o número total
de arquivos incluídos em uma nova aplicação Laravel:

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
Ele não estende mais a classe `Controller` interna do Laravel, e as _traits_
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

Por padrão, as novas aplicações Laravel usam SQLite para armazenamento do banco
de dados, bem como o _driver_ `database` para sessão, _cache_ e fila do Laravel.
Isso permite que você comece a construir sua aplicação imediatamente após criar
uma nova aplicação Laravel, sem ser necessário instalar software adicional ou
criar migrações adicionais de banco de dados.

Além disso, com o tempo, os _drivers_ `database` para esses serviços do Laravel
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
WebSocket por vários servidores Reverb de _back-end_, todos suportando uma única
aplicação de alta demanda.

Para mais informações sobre o Laravel Reverb, consulte a
[documentação completa do Reverb](../reverb.md).

### Limitação de Taxa por Segundo

_A limitação de taxa por segundo foi contribuída por
[Tim MacDonald](https://github.com/timacdonald)_.

O Laravel agora suporta limitação de taxa “por segundo” para todos os
limitadores de taxa, incluindo aqueles para requisições HTTP e trabalhos em
fila.
Anteriormente, os limitadores de taxa do Laravel eram limitados à granularidade
“por minuto”:

```php
RateLimiter::for('faturas', function (Request $request) {
    return Limit::perSecond(1);
});
```

Para obter mais informações sobre limitação de taxa no Laravel, verifique a
[documentação sobre limitação de taxa](../routing.md#rate-limiting).

### Rota de Integridade

_A rota de integridade foi contribuído por
[Taylor Otwell](https://github.com/taylorotwell)_.

As novas aplicações Laravel 11 incluem uma diretiva de rota `health`, que
instrui o Laravel a definir um _endpoint_ de verificação de integridade simples
que pode ser invocado por serviços de monitoramento de integridade de outras
aplicações ou sistemas de orquestração como o Kubernetes.
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

### Rotação Elegante das Chaves de Encriptação

_A rotação elegante das chaves de encriptação foi contribuída por
[Taylor Otwell](https://github.com/taylorotwell)_.

Como o Laravel encripta todos os _cookies_, incluindo o _cookie_ de sessão da
sua aplicação, essencialmente toda requisição para uma aplicação Laravel depende
de encriptação.
No entanto, por causa disso, a rotação da chave de encriptação da sua aplicação
desconectaria todas as pessoas usuárias da sua aplicação.
Além disso, torna-se impossível desencriptar os dados encriptados pela chave de
encriptação anterior.

O Laravel 11 permite que você defina as chaves de encriptação anteriores da sua
aplicação como uma lista delimitada por vírgula através da variável de ambiente
`APP_PREVIOUS_KEYS`.

Ao encriptar valores, o Laravel sempre usará a chave de encriptação “atual”, que
está na variável de ambiente `APP_KEY`.
Ao desencriptar valores, o Laravel tentará primeiro a chave atual.
Se a desencriptação falhar usando a chave atual, o Laravel tentará todas as
chaves anteriores até que uma das chaves seja capaz de desencriptar o valor.

Essa abordagem de desencriptação elegante permite que as pessoas usuárias
continuem usando sua aplicação ininterruptamente, mesmo que sua chave de
encriptação seja rotacionada.

Para mais informações sobre encriptação no Laravel, confira a
[documentação sobre encriptação](../encryption.md).

### Atualização Automática do _Hash_ de Senha

_A atualização automática do hash de senha foi contribuída por
[Stephen Rees-Carter](https://github.com/valorin)_.

O algoritmo de _hash_ de senha padrão do Laravel é o bcrypt.
O “fator de trabalho” para _hashes_ bcrypt pode ser ajustado através do arquivo
de configuração `config/hashing.php` ou da variável de ambiente `BCRYPT_ROUNDS`.

Normalmente, o fator de trabalho do bcrypt deve ser aumentado ao longo do tempo
à medida que o poder de processamento das CPUs/GPUs aumenta.
Se você aumentar o fator de trabalho do bcrypt para sua aplicação, o Laravel irá
refazer as senhas das pessoas usuárias de maneira elegante e automática à medida
que elas se autenticam na sua aplicação.

### Validação do _Prompt_

_A integração com o validador do prompt foi contribuída por
[Andrea Marco Sartori](https://github.com/cerbero90)_.

[Laravel Prompts](../prompts.md) é um pacote PHP para adicionar formulários
bonitos e fáceis de usar às suas aplicações de linha de comando, com recursos
semelhantes aos de navegador, incluindo texto de sugestão e validação.

O Laravel Prompts suporta validação de entrada por meio de _closures_:

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
[validador](../validation.md) do Laravel ao validar entradas de _prompt_:

```php
$name = text('Qual o seu nome?', validate: [
    'name' => 'required|min:3|max:255',
]);
```

### Testes de Interação de Fila

_Os testes de interação de fila foram contribuídos por
[Taylor Otwell](https://github.com/taylorotwell)_.

Anteriormente, tentar testar manualmente se um trabalho na fila foi liberado,
excluído ou falhou era complicado e exigia a definição de dados de teste e
_stubs_ de fila personalizados.
No entanto, no Laravel 11, você pode testar facilmente essas interações de fila
usando o método `withFakeQueueInteractions`:

```php
use App\Jobs\ProcessPodcast;

$job = (new ProcessPodcast)->withFakeQueueInteractions();

$job->handle();

$job->assertReleased(delay: 30);
```

Para obter mais informações sobre como testar trabalhos de fila, confira a
[documentação sobre filas](../queues.md#testing).

### Novos Comandos do Artisan

_Os comandos de criação de classes do Artisan foram contribuídos por
[Taylor Otwell](https://github.com/taylorotwell)_.

Novos comandos do Artisan foram adicionados para permitir a criação rápida de
classes, _enums_, interfaces e _traits_:

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
 * Obtém os atributos que devem ser convertidos.
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
armazena o resultado em _cache_ na memória durante a duração da requisição.
Qualquer chamada subsequente para a função `once` com a mesma função de retorno
de chamada retornará o resultado armazenado em _cache_ anteriormente:

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
Nas versões anteriores do Laravel, você poderia usar o MariaDB através do
_driver_ MySQL do Laravel.
No entanto, o Laravel 11 agora inclui um _driver_ MariaDB dedicado que fornece
melhores padrões para este sistema de banco de dados.

Para mais informações sobre os _drivers_ de banco de dados do Laravel, confira a
[documentação sobre banco de dados](../database.md).

### Operação e Inspeção Aprimoradas de Esquemas de Bancos de Dados

_A operação e a inspeção aprimoradas de esquemas de bancos de dados foram
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
$columns = Schema::getColumns('usuarios');
$indexes = Schema::getIndexes('usuarios');
$foreignKeys = Schema::getForeignKeys('usuarios');
```
