---
source_url: https://github.com/laravel/docs/blob/5.6/releases.md
revision: 93237f3c2eb77adc1e2d9bf3baba05036d5c8036
status: ready
---

# Notas de Versão

- [Esquema de Versionamento](#esquema-de-versionamento)
- [Política de Suporte](#politica-de-suporte)
- [Laravel 5.6](#laravel-5-6)

## Esquema de Versionamento

O esquema de versionamento do Laravel mantém a seguinte convenção:
`paradigm.major.minor`.
As versões maiores do _framework_ são lançadas a cada seis meses (em fevereiro e
agosto), enquanto as versões menores podem ser lançadas semanalmente.
Versões menores **nunca** devem conter alterações significativas.

Ao referenciar o _framework_ Laravel ou seus componentes a partir da sua
aplicação ou pacote, você deve sempre usar uma restrição de versão como
`^5.6.*`, uma vez que as principais versões do Laravel incluem alterações
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

|  Versão   |       Lançamento       | Correções de falhas até | Correções de segurança até |
|:---------:|:----------------------:|:-----------------------:|:--------------------------:|
|    5.0    | 4 de fevereiro de 2015 |   4 de agosto de 2015   |   4 de fevereiro de 2016   |
| 5.1 (LTS) |   9 de junho de 2015   |   9 de junho de 2017    |     9 de junho de 2018     |
|    5.2    | 21 de dezembro de 2015 |   21 de junho de 2016   |   21 de dezembro de 2016   |
|    5.3    |  23 de agosto de 2016  | 23 de fevereiro de 2017 |    23 de agosto de 2017    |
|    5.4    | 24 de janeiro de 2017  |   24 de julho de 2017   |   24 de janeiro de 2018    |
| 5.5 (LTS) |  30 de agosto de 2017  |  30 de agosto de 2019   |    30 de agosto de 2020    |
|    5.6    | 7 de fevereiro de 2018 |   7 de agosto de 2018   |   7 de fevereiro de 2019   |

## Laravel 5.6 {: #laravel-5-6 }

O Laravel 5.6 continua as melhorias feitas no Laravel 5.5, adicionando um
sistema de _logging_ aprimorado, agendamento de tarefas de servidor único,
melhorias na serialização de modelos, limitação de taxa dinâmica, classes de
canais de transmissão, geração de controladores de recursos de API, melhorias na
formatação de data do Eloquent, apelidos de componentes do Blade, suporte para
criação de _hash_ de senhas do Argon2, inclusão do pacote Collision e muito
mais.
Além disso, todas as gerações de código automático do _front-end_ foram
atualizadas para o Bootstrap 4.

Todos os componentes do Symfony usados pelo Laravel foram atualizados para a
série de lançamentos Symfony `~4.0`.

O lançamento do Laravel 5.6 coincide com o lançamento do
[Spark 6.0](https://spark.laravel.com), a primeira grande atualização do Laravel
Spark desde o seu lançamento.
O Spark 6.0 apresenta preços por pessoa usuária para o Stripe e Braintree,
localização, Bootstrap 4, uma interface de pessoa usuária aprimorada e suporte a
Stripe Elements.

> **Dica:**
> Esta documentação resume as melhorias mais notáveis do _framework_; no
> entanto, registros de alterações mais completos estão sempre disponíveis
> [no GitHub](https://github.com/laravel/framework/blob/5.6/CHANGELOG-5.6.md).

### Melhorias no _Logging_

O Laravel 5.6 traz grandes melhorias ao sistema de _logging_ do Laravel.
Todas as configurações de _logging_ estão armazenadas no novo arquivo de
configuração `config/logging.php`.
Agora você pode criar facilmente "pilhas" de _logging_ que enviam mensagens de
_log_ para vários manipuladores.
Por exemplo, você pode enviar todas as mensagens de nível `debug` para o _log_
do sistema enquanto envia mensagens de nível `error` para o Slack para que seu
time possa reagir rapidamente aos erros:

```php
'channels' => [
    'stack' => [
        'driver' => 'stack',
        'channels' => ['syslog', 'slack'],
    ],
],
```

Além disso, agora é mais fácil personalizar canais de _log_ existentes usando a
nova funcionalidade "tap" do sistema de _logging_.
Para obter mais informações, verifique a
[documentação completa sobre _logging_](../logging.md).

### Agendamento de Tarefas de Servidor Único

> **Alerta:**
> Para utilizar esse recurso, sua aplicação deve usar o _driver_ de _cache_
> `memcached` ou `redis` como _driver_ de _cache_ padrão da sua aplicação.
> Além disso, todos os servidores devem comunicar-se com o mesmo servidor de
> _cache_ central.

Se a sua aplicação estiver sendo executada em vários servidores, agora você
poderá limitar um trabalho agendado para ser executado apenas em um único
servidor.
Por exemplo, suponha que você tenha uma tarefa agendada que gera um novo
relatório toda sexta-feira à noite.
Se o agendador de tarefas estiver em execução em três servidores de trabalho, a
tarefa agendada será executada em todos os três servidores e gerará o relatório
três vezes. Isso não é bom!

Para indicar que a tarefa deve ser executada em apenas um servidor, você pode
usar o método `onOneServer` ao definir a tarefa agendada.
O primeiro servidor a obter a tarefa criará um bloqueio atômico na tarefa para
evitar que outros servidores executem a mesma tarefa no mesmo ciclo do Cron:

```php
$schedule->command('report:generate')
     ->fridays()
     ->at('17:00')
     ->onOneServer();
```

### Limitação de Taxa Dinâmica

Ao especificar um [limite de taxa](../routing.md#rate-limiting) em um grupo de
rotas em versões anteriores do Laravel, você precisava fornecer um número máximo
de requisições diretamente no código:

```php
Route::middleware('auth:api', 'throttle:60,1')->group(function () {
    Route::get('/user', function () {
        //
    });
});
```

No Laravel 5.6, você pode especificar um número máximo dinâmico de requisições
baseado em um atributo do modelo `User` autenticado.
Por exemplo, se o seu modelo `User` contém um atributo `rate_limit`, você pode
passar o nome do atributo para o _middleware_ `throttle` para que ele seja usado
para calcular a contagem máxima de requisições:

```php
Route::middleware('auth:api', 'throttle:rate_limit,1')->group(function () {
    Route::get('/user', function () {
        //
    });
});
```

### Classes de Canais de Transmissão

Se sua aplicação estiver consumindo muitos canais diferentes, seu arquivo
`routes/channels.php` poderá se tornar volumoso.
Portanto, em vez de usar _closures_ para autorizar canais, agora você pode usar
classes de canais.
Para gerar uma classe de canal, use o comando `make:channel` do Artisan.
Este comando colocará uma nova classe de canal no diretório `App/Broadcasting`.

```shell
php artisan make:channel OrderChannel
```

A seguir, registre seu canal no seu arquivo `routes/channels.php`:

```php
use App\Broadcasting\OrderChannel;

Broadcast::channel('order.{order}', OrderChannel::class);
```

Finalmente, você pode colocar a lógica de autorização do seu canal no método
`join` da classe do canal.
Este método `join` abrigará a mesma lógica que você normalmente colocaria na
closure de autorização de canal.
Claro, você também pode aproveitar a vinculação do modelo de canal:

```php
<?php

namespace App\Broadcasting;

use App\User;
use App\Order;

class OrderChannel
{
    /**
     * Cria uma nova instância de canal.
     *
     * @return void
     */
    public function __construct()
    {
        //
    }

    /**
     * Autentica o acesso da pessoa usuária ao canal.
     *
     * @param  \App\User  $user
     * @param  \App\Order  $order
     * @return array|bool
     */
    public function join(User $user, Order $order)
    {
        return $user->id === $order->user_id;
    }
}
```

### Geração do Controlador de API

Ao declarar rotas de recursos que serão consumidas por APIs, normalmente você
desejará excluir rotas que apresentem modelos HTML, como `create` e `edit`.
Para gerar um controlador de recurso que não inclua esses métodos, agora você
pode usar a opção `--api` ao executar o comando `make:controller`:

```shell
php artisan make:controller API/PhotoController --api
```

### Melhorias na Serialização dos Modelos

Nas versões anteriores do Laravel, os modelos em fila não seriam restaurados com
seus relacionamentos carregados intactos.
No Laravel 5.6, os relacionamentos carregados no modelo quando ele foi
enfileirado são automaticamente recarregados quando o trabalho é processado pela
fila.

### Conversão de Data do Eloquent

Agora você pode personalizar individualmente o formato das colunas de data do
Eloquent.
Para começar, especifique o formato de data desejado na declaração de conversão.
Uma vez especificado, este formato será usado ao serializar o modelo para um
_array_ ou JSON:

```php
protected $casts = [
    'birthday' => 'date:Y-m-d',
    'joined_at' => 'datetime:Y-m-d H:00',
];
```

### Apelidos de Componentes do Blade

Se os componentes do Blade estiverem armazenados em um subdiretório, agora você
poderá apelidá-los para facilitar o acesso.
Por exemplo, imagine um componente do Blade armazenado em
`resources/views/components/alert.blade.php`.
Você pode usar o método `component` para criar um apelido `alert` para o
componente `componentes.alert`:

```php
Blade::component('components.alert', 'alert');
```

Depois que o componente tiver o apelido, você poderá renderizá-lo usando uma
diretiva:

```bladehtml
@alert('alert', ['type' => 'danger'])
    Você não tem permissão para acessar este recurso!
@endalert
```

Você pode omitir os parâmetros do componente se ele não tiver _slots_
adicionais:

```bladehtml
@alert
    Você não tem permissão para acessar este recurso!
@endalert
```

### Criação do _Hash_ de Senha do Argon2

Se você estiver construindo uma aplicação em PHP 7.2.0 ou superior, o Laravel
agora suporta a criação do _hash_ de senha por meio do algoritmo Argon2.
O _driver_ de _hash_ padrão para a sua aplicação é controlado por um novo
arquivo de configuração `config/hashing.php`.

### Métodos UUID

O Laravel 5.6 introduz dois novos métodos para gerar UUIDs: `Str::uuid` e
`Str::orderedUuid`.
O método `orderedUuid` gerará um UUID iniciado por _timestamp_ que é indexado de
maneira mais fácil e eficiente por bancos de dados como MySQL.
Cada um desses métodos retorna um objeto `Ramsey\Uuid\Uuid`:

```php
use Illuminate\Support\Str;

return (string) Str::uuid();

return (string) Str::orderedUuid();
```

### Collision

A aplicação `laravel/laravel` padrão agora contém uma dependência `dev` do
Composer para o pacote [Collision](https://github.com/nunomaduro/collision)
mantido por Nuno Maduro.
Este pacote fornece belos relatórios de erros ao interagir com sua aplicação
Laravel na linha de comando:

<img
alt="Captura de tela do Collision"
src="https://raw.githubusercontent.com/nunomaduro/collision/v2.1.1/docs/example.png" />

### Bootstrap 4

Toda a geração automática de código de _front-end_, como o código padrão de
autenticação e o componente Vue de exemplo, foram atualizados para o
Bootstrap 4.
