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
Versões menores **nunca** devem conter alterações significativas.

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
agendador, suporte a atribuição de múltiplas proteções de autenticação para
canais de transmissão, conformidade do _driver_ de _cache_ PSR-16, melhorias no
comando `artisan serve`, suporte ao PHPUnit 8.0, suporte ao Carbon 2.0, suporte
ao Pheanstalk 4.0 e uma variedade de outras correções de falhas e melhorias de
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

### Descoberta Automática de Políticas de Modelo

Ao usar o Laravel 5.7, a
[política de autorização](../authorization.md#creating-policies) de cada modelo
precisava ser explicitamente registrada no `AuthServiceProvider` da sua
aplicação:

```php
/**
 * Os mapeamentos de políticas de autorização da aplicação.
 *
 * @var array
 */
protected $policies = [
    'App\User' => 'App\Policies\UserPolicy',
];
```

O Laravel 5.8 introduz a descoberta automática de políticas de modelo, desde que
o modelo e a política sigam as convenções de nomenclatura padrão do Laravel.
Especificamente, as políticas devem estar em um diretório `Policies` abaixo do
diretório que contém os modelos.
Assim, por exemplo, os modelos podem ser colocados no diretório `app` enquanto
as políticas podem ser colocadas no diretório `app/Policies`.
Além disso, o nome da política deve corresponder ao nome do modelo e ter um
sufixo `Policy`.
Portanto, um modelo `User` corresponderia a uma classe `UserPolicy`.

Se desejar fornecer sua própria lógica de descoberta de política, você pode
registrar um retorno de chamada personalizado usando o método
`Gate::guessPolicyNamesUsing`.
Normalmente, esse método deve ser chamado do `AuthServiceProvider` da sua
aplicação:

```php
    use Illuminate\Support\Facades\Gate;

    Gate::guessPolicyNamesUsing(function ($modelClass) {
        // Retorna o nome da classe da política de autorização...
    });
```

> **Nota:**
> Quaisquer políticas mapeadas explicitamente em seu `AuthServiceProvider` terão
> precedência sobre quaisquer potenciais políticas descobertas automaticamente.

### Conformidade com o _Cache_ PSR-16

Para permitir um tempo de expiração mais granular ao armazenar itens e fornecer
conformidade com o padrão de _cache_ PSR-16, o tempo de vida do item de _cache_
mudou de minutos para segundos.
Os métodos `put`, `putMany`, `add`, `remember` e `setDefaultCacheTime` da classe
`Illuminate\Cache\Repository` e suas classes estendidas, bem como o método `put`
de cada armazenamento de _cache_ foram atualizados com esta mudança de
comportamento.
Consulte o [PR relacionado](https://github.com/laravel/framework/pull/27276)
para obter mais informações.

Se você estiver passando um número inteiro para qualquer um desses métodos,
deverá atualizar seu código para garantir que agora está passando o número de
segundos que deseja que o item permaneça no _cache_.
Alternativamente, você pode passar uma instância de `DateTime` indicando quando
o item deve expirar:

```php
//  Laravel 5.7 - Armazena item por 30 minutos...
Cache::put('foo', 'bar', 30);

// Laravel 5.8 - Armazena item por 30 segundos...
Cache::put('foo', 'bar', 30);

// Laravel 5.7 / 5.8 - Armazena item por 30 segundos...
Cache::put('foo', 'bar', now()->addSeconds(30));
```

### Múltiplas Proteções de Autenticação de Transmissão

Nas versões anteriores do Laravel, os canais de transmissão privados e de
presença autenticavam a pessoa usuária através da proteção de autenticação
padrão da sua aplicação.
A partir do Laravel 5.8, agora você pode atribuir múltiplas proteções que devem
autenticar a solicitação recebida:

```php
Broadcast::channel('channel', function() {
    // ...
}, ['guards' => ['web', 'admin']])
```

### Criação do _Hash_ do _Token_ da Proteção `token`

A proteção `token` do Laravel, que fornece autenticação básica de API, agora
suporta o armazenamento de _tokens_ de API como _hashes_ SHA-256.
Isso fornece segurança aprimorada em relação ao armazenamento de _tokens_ como
texto simples.
Para saber mais sobre _tokens_ com _hash_, revise a
[documentação completa da autenticação de API](../api-authentication.md).

> **Nota:**
> Embora o Laravel venha com uma proteção de autenticação simples baseada em
> _token_, recomendamos fortemente que você considere usar o
> [Laravel Passport](../passport.md) para aplicações de produção robustas que
> oferecem autenticação de API.

### Validação de _E-mail_ Aprimorada

O Laravel 5.8 introduz melhorias na lógica de validação de _e-mail_ subjacente
do validador, adotando o pacote `egulias/email-validator` utilizado pelo
SwiftMailer.
A lógica de validação de _e-mail_ anterior do Laravel ocasionalmente considerava
_e-mails_ válidos, como `example@bär.se`, como inválidos.

### Fuso Horário Padrão do Agendador

O Laravel permite que você personalize o fuso horário de uma tarefa agendada
usando o método `timezone`:

```php
$schedule->command('inspire')
     ->hourly()
     ->timezone('America/Chicago');
```

No entanto, isso pode se tornar complicado e repetitivo se você especificar o
mesmo fuso horário para todas as tarefas agendadas.
Por esse motivo, agora você pode definir um método `scheduleTimezone` no seu
arquivo `app/Console/Kernel.php`.
Este método deve retornar o fuso horário padrão que deve ser atribuído a todas
as tarefas agendadas:

```php
/**
 * Obtém o fuso horário que deve ser usado por padrão para eventos agendados.
 *
 * @return \DateTimeZone|string|null
 */
protected function scheduleTimezone()
{
    return 'America/Chicago';
}
```

### Eventos de Modelo de Tabela Intermediária / Pivô

Nas versões anteriores do Laravel, os
[eventos dos modelos do Eloquent](../eloquent.md#events) não eram despachados ao
anexar, desanexar ou sincronizar modelos de tabelas intermediárias / "pivôs"
personalizadas de um relacionamento muitos para muitos.
Ao usar
[modelos de tabela intermediária customizados](../eloquent-relationships.md#defining-custom-intermediate-table-models)
no Laravel 5.8, os eventos de modelo aplicáveis agora serão despachados.

### Melhorias no Método `Artisan::call`

O Laravel permite que você invoque o Artisan através do método `Artisan::call`.
Nas versões anteriores do Laravel, as opções do comando são passadas usando um
_array_ como segundo argumento do método:

```php
use Illuminate\Support\Facades\Artisan;

Artisan::call('migrate:install', ['database' => 'foo']);
```

No entanto, o Laravel 5.8 permite que você passe o comando inteiro, incluindo
as opções, como uma _string_ no primeiro argumento do método:

```php
Artisan::call('migrate:install --database=foo');
```

### Métodos Auxiliares de Testes `mock` e `spy`

Para tornar os objetos de simulação mais convenientes, novos métodos `mock` e
`spy` foram adicionados à classe base do caso de teste do Laravel.
Esses métodos vinculam automaticamente a classe simulada ao contêiner.
Por exemplo:

```php
// Laravel 5.7
$this->instance(Service::class, Mockery::mock(Service::class, function ($mock) {
    $mock->shouldReceive('process')->once();
}));

// Laravel 5.8
$this->mock(Service::class, function ($mock) {
    $mock->shouldReceive('process')->once();
});
```

### Preservação da Chave de Recurso do Eloquent

Ao retornar uma [coleção de recursos do Eloquent](../eloquent-resources.md) de
uma rota, o Laravel redefine as chaves da coleção para ficarem em ordem numérica
simples:

```php
use App\User;
use App\Http\Resources\User as UserResource;

Route::get('/user', function () {
    return UserResource::collection(User::all());
});
```

Ao usar o Laravel 5.8, agora você pode adicionar uma propriedade `preserveKeys`
à sua classe de recurso indicando se as chaves da coleção devem ser preservadas.
Por padrão, e para manter a consistência com versões anteriores do Laravel, as
chaves serão redefinidas por padrão:

```php
<?php

namespace App\Http\Resources;

use Illuminate\Http\Resources\Json\JsonResource;

class User extends JsonResource
{
    /**
     * Indica se as chaves da coleção do recurso devem ser preservadas.
     *
     * @var bool
     */
    public $preserveKeys = true;
}
```

Quando a propriedade `preserveKeys` for definida como `true`, as chaves da
coleção serão preservadas:

```php
use App\User;
use App\Http\Resources\User as UserResource;

Route::get('/user', function () {
    return UserResource::collection(User::all()->keyBy->id);
});
```

### Método `orWhere` do Eloquent de Ordem Superior

Nas versões anteriores do Laravel, a combinação de vários escopos de modelo do
Eloquent por meio de um operador de consulta `or` exigia o uso de retornos de
chamada de _closure_:

```php
// Métodos scopePopular e scopeActive definidos no modelo User...
$users = App\User::popular()->orWhere(function (Builder $query) {
    $query->active();
})->get();
```

O Laravel 5.8 introduz um método `orWhere` de "ordem superior" que permite
encadear fluentemente esses escopos sem o uso de _closures_:

```php
$users = App\User::popular()->orWhere->active()->get();
```

### Melhorias no Comando `serve` do Artisan

Nas versões anteriores do Laravel, o comando `serve` do Artisan serviria sua
aplicação na porta `8000`.
Se outro processo do comando `serve` já estivesse escutando nesta porta, uma
tentativa de servir uma segunda aplicação via `serve` falharia.
A partir do Laravel 5.8, o `serve` agora irá procurar portas disponíveis até a
porta `8009`, permitindo que você atenda várias aplicações simultaneamente.

### Mapeamento de Arquivos do Blade

Ao compilar modelos do Blade, o Laravel agora adiciona um comentário no topo do
arquivo compilado que contém o caminho para o modelo original do Blade.

### _Drivers_ de _Cache_ e Sessão do DynamoDB

O Laravel 5.8 introduz os _drivers_ de _cache_ e sessão do
[DynamoDB](https://aws.amazon.com/dynamodb/).
O DynamoDB é um banco de dados NoSQL sem servidor fornecido pela Amazon Web
Services.
A configuração padrão para o _driver_ de _cache_ `dynamodb` pode ser encontrada
no
[arquivo de configuração de
_cache_](https://github.com/laravel/laravel/blob/5.8/config/cache.php)
do Laravel 5.8.

### Suporte para a Carbon 2.0

O Laravel 5.8 fornece suporte para a versão `~2.0` da biblioteca de manipulação
de datas Carbon.

### Suporte para a Pheanstalk 4.0

O Laravel 5.8 fornece suporte para a versão `~4.0` da biblioteca de filas
Pheanstalk.
Se você estiver usando a biblioteca Pheanstalk na sua aplicação, atualize sua
biblioteca para a versão `~4.0` via Composer.
