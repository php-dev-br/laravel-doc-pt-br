---
source_url: https://github.com/laravel/docs/blob/6.x/releases.md
revision: 31944de4e5e7f59d50d5de0f5d064f49bffae723
status: ready
---

# Notas de Versão

- [Esquema de Versionamento](#esquema-de-versionamento)
- [Política de Suporte](#politica-de-suporte)
- [Laravel 6](#laravel-6)

## Esquema de Versionamento

O Laravel e seus outros pacotes originais seguem o
[Versionamento Semântico](https://semver.org/lang/pt-BR/).
As versões maiores do _framework_ são lançadas a cada seis meses (em fevereiro e
agosto), enquanto as versões menores e de correções podem ser lançadas
semanalmente.
Versões menores e de correções **nunca** devem conter alterações significativas.

Ao referenciar o _framework_ Laravel ou seus componentes a partir da sua
aplicação ou pacote, você deve sempre usar uma restrição de versão como `^6.0`,
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

|  Versão   |       Lançamento        | Correções de falhas até | Correções de segurança até |
|:---------:|:-----------------------:|:-----------------------:|:--------------------------:|
| 5.5 (LTS) |  30 de agosto de 2017   |  30 de agosto de 2019   |    30 de agosto de 2020    |
|    5.6    | 7 de fevereiro de 2018  |   7 de agosto de 2018   |   7 de fevereiro de 2019   |
|    5.7    |  4 de setembro de 2018  |   4 de março de 2019    |   4 de setembro de 2019    |
|    5.8    | 26 de fevereiro de 2019 |  26 de agosto de 2019   |  26 de fevereiro de 2020   |
|  6 (LTS)  |  3 de setembro de 2019  |  3 de setembro de 2021  |   3 de setembro de 2022    |

## Laravel 6

O Laravel 6 (LTS) continua as melhorias feitas no Laravel 5.8, introduzindo
versionamento semântico, compatibilidade com o
[Laravel Vapor](https://vapor.laravel.com), respostas de autorização
aprimoradas, _middlewares_ de trabalho, coleções preguiçosas, melhorias das
subconsultas, extração da geração automática de código do _front-end_ para o
pacote `laravel/ui` do Composer e uma variedade de outras correções de falhas e
melhorias de usabilidade.

### Versionamento Semântico

O pacote do _framework_ Laravel (`laravel/framework`) agora segue o padrão de
[versionamento semântico](https://semver.org/).
Isso torna o _framework_ consistente com outros pacotes originais do Laravel que
já seguiam esse padrão de versionamento.
O ciclo de lançamento do Laravel permanecerá inalterado.

### Compatibilidade com o Laravel Vapor

_O Laravel Vapor foi construído por
[Taylor Otwell](https://github.com/taylorotwell)_.

O Laravel 6 oferece compatibilidade com o
[Laravel Vapor](https://vapor.laravel.com), uma plataforma de implantação
_serverless_ com escalonamento automático para o Laravel.
O Vapor abstrai a complexidade de gerenciar aplicações Laravel no AWS Lambda,
bem como fazer a interface dessas aplicações com filas SQS, bancos de dados,
_clusters_ Redis, redes, CloudFront CDN e muito mais.

### Exceções Aprimoradas Via Ignition

O Laravel 6 vem com o [Ignition](https://github.com/facade/ignition), uma nova
página de detalhes de exceção de código aberto criada por Freek Van der Herten e
Marcel Pociot.
O Ignition oferece muitos benefícios em relação às versões anteriores, como
arquivo de erro do Blade e tratamento do número de linha aprimorados, soluções
executáveis para problemas comuns, edição de código, compartilhamento de
exceções e uma experiência da pessoa usuária aprimorada.

### Respostas de Autorização Aprimoradas

_As respostas de autorização aprimoradas foram implementadas por
[Gary Green](https://github.com/garygreen)_.

Nas versões anteriores do Laravel, era difícil recuperar e expor mensagens de
autorização personalizadas às pessoas usuárias finais.
Isso tornava difícil explicar às pessoas usuárias finais exatamente por que uma
requisição em particular havia sido negada.
No Laravel 6, isso agora é muito mais fácil usando mensagens de resposta de
autorização e o novo método `Gate::inspect`.
Por exemplo, dado o seguinte método de política de autorização:

```php
/**
 * Determina se a pessoa usuária pode visualizar o voo determinado.
 *
 * @param  \App\User  $user
 * @param  \App\Flight  $flight
 * @return mixed
 */
public function view(User $user, Flight $flight)
{
    return $this->deny('Explicação da negação.');
}
```

A resposta e a mensagem da política de autorização podem ser facilmente
recuperadas usando o método `Gate::inspect`:

```php
$response = Gate::inspect('view', $flight);

if ($response->allowed()) {
    // A pessoa usuária está autorizada a visualizar o voo...
}

if ($response->denied()) {
    echo $response->message();
}
```

Além disso, essas mensagens personalizadas serão retornadas automaticamente ao
seu _front-end_ ao usar métodos auxiliares como `$this->authorize` ou
`Gate::authorize` a partir de suas rotas ou controladores.

### _Middlewares_ de Trabalho

_Os middlewares de trabalho foi implementado por
[Taylor Otwell](https://github.com/taylorotwell)_.

Os _middlewares_ de trabalho permitem que você envolva uma lógica personalizada
em torno da execução de trabalhos em fila, reduzindo o código padrão nos
próprios trabalhos.
Por exemplo, em versões anteriores do Laravel, você pode ter agrupado a lógica
do método `handle` de um trabalho em um retorno de chamada com taxa limitada:

```php
/**
 * Executa o trabalho.
 *
 * @return void
 */
public function handle()
{
    Redis::throttle('key')->block(0)->allow(1)->every(5)->then(function () {
        info('Bloqueio obtido...');

        // Trata o trabalho...
    }, function () {
        // Não foi possível obter o bloqueio...

        return $this->release(5);
    });
}
```

No Laravel 6, essa lógica pode ser extraída para um _middleware_ de trabalho,
permitindo que você mantenha o método `handle` do seu trabalho livre de
quaisquer responsabilidades de limitação de taxa:

```php
<?php

namespace App\Jobs\Middleware;

use Illuminate\Support\Facades\Redis;

class RateLimited
{
    /**
     * Processa o trabalho em fila.
     *
     * @param  mixed  $job
     * @param  callable  $next
     * @return mixed
     */
    public function handle($job, $next)
    {
        Redis::throttle('key')
            ->block(0)->allow(1)->every(5)
            ->then(function () use ($job, $next) {
                // Bloqueio obtido...

                $next($job);
            }, function () use ($job) {
                // Não foi possível obter o bloqueio...

                $job->release(5);
            });
    }
}
```

Após criar os _middlewares_, eles podem ser anexados a um trabalho retornando-os
do método `middleware` do trabalho:

```php
use App\Jobs\Middleware\RateLimited;

/**
 * Obtém o middleware pelo qual o trabalho deve passar.
 *
 * @return array
 */
public function middleware()
{
    return [new RateLimited];
}
```

### Coleções Preguiçosas

_As coleções preguiçosas foram implementadas por
[Joseph Silber](https://github.com/JosephSilber)_.

Muitas pessoas desenvolvedoras já desfrutam dos poderosos
[métodos da classe Collection](../collections.md) do Laravel.
Para complementar a já poderosa classe `Collection`, o Laravel 6 introduz uma
`LazyCollection`, que aproveita os
[geradores](/docs/php/doc/8/language.generators.overview.html) do PHP para
permitir que você trabalhe com conjuntos de dados muito grandes enquanto mantém
baixo o consumo de memória.

Por exemplo, imagine que sua aplicação precise processar um arquivo de _log_ de
vários _gigabytes_ enquanto aproveita os métodos de coleções do Laravel para
analisar os _logs_.
Em vez de ler o arquivo inteiro na memória de uma só vez, as coleções
preguiçosas podem ser usadas para manter apenas uma pequena parte do arquivo na
memória em um determinado momento:

```php
use App\LogEntry;
use Illuminate\Support\LazyCollection;

LazyCollection::make(function () {
    $handle = fopen('log.txt', 'r');

    while (($line = fgets($handle)) !== false) {
        yield $line;
    }
})
->chunk(4)
->map(function ($lines) {
    return LogEntry::fromLines($lines);
})
->each(function (LogEntry $logEntry) {
    // Processa a entrada de log...
});
```

Ou, imagine que você precisa iterar 10.000 modelos do Eloquent.
Ao usar coleções tradicionais do Laravel, todos os 10.000 modelos do Eloquent
devem ser carregados na memória simultaneamente:

```php
$users = App\User::all()->filter(function ($user) {
    return $user->id > 500;
});
```

No entanto, a partir do Laravel 6, o método `cursor` do construtor de consultas
foi atualizado para retornar uma instância de `LazyCollection`.
Isso permite que você execute apenas uma única consulta no banco de dados, mas
também mantenha apenas um modelo do Eloquent carregado na memória por vez.
Neste exemplo, o retorno de chamada de `filter` não é executado até que
realmente iteremos sobre cada modelo de pessoa usuária individualmente,
permitindo uma redução drástica no consumo de memória:

```php
$users = App\User::cursor()->filter(function ($user) {
    return $user->id > 500;
});

foreach ($users as $user) {
    echo $user->id;
}
```

### Melhorias das Subconsultas do Eloquent

_As melhorias das subconsultas do Eloquent foram implementados por
[Jonathan Reinink](https://github.com/reinink)_.

O Laravel 6 introduz vários novos aprimoramentos e melhorias no suporte a
subconsultas de banco de dados.
Por exemplo, vamos imaginar que temos uma tabela `destinations` de destinos de
voos e uma tabela `flights` de voos para destinos.
A tabela `flights` contém uma coluna `arrived_at` que indica quando o voo chegou
ao destino.

Usando a nova funcionalidade de seleção de subconsulta no Laravel 6, podemos
selecionar todos os destinos em `destinations` e o nome do voo que chegou mais
recentemente a esse destino usando uma única consulta:

```php
return Destination::addSelect(['last_flight' => Flight::select('name')
    ->whereColumn('destination_id', 'destinations.id')
    ->orderBy('arrived_at', 'desc')
    ->limit(1)
])->get();
```

Além disso, podemos usar novos recursos de subconsulta adicionados à função
`orderBy` do construtor de consultas para ordenar todos os destinos com base
em quando o último voo chegou a esse destino.
Novamente, isso pode ser feito durante a execução de uma única consulta no banco
de dados:

```php
return Destination::orderByDesc(
    Flight::select('arrived_at')
        ->whereColumn('destination_id', 'destinations.id')
        ->orderBy('arrived_at', 'desc')
        ->limit(1)
)->get();
```

### Laravel UI

A geração automática de código do _front-end_ normalmente fornecida com versões
anteriores do Laravel foi extraído em um pacote `laravel/ui` do Composer.
Isso permite que a geração automática de código da interface da pessoa usuária
original seja desenvolvida e versionada separadamente do _framework_ principal.
Como resultado dessa mudança, nenhum código do Bootstrap ou Vue está presente na
geração automática de código padrão do _framework_, e o comando `make:auth`
também foi extraído do _framework_.

Para restaurar a geração automática de código Vue ou Bootstrap tradicional
presente nas versões anteriores do Laravel, você pode instalar o pacote
`laravel/ui` e usar o comando `ui` do Artisan para instalar a geração automática
de código do _front-end_:

```shell
composer require laravel/ui "^1.0" --dev

php artisan ui vue --auth
```
