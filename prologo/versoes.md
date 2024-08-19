---
source_url: https://github.com/laravel/docs/blob/5.7/releases.md
revision: b1b37c94754d45a6c70acb955b387f305ec8927e
status: ready
---

# Notas de Versão

- [Esquema de Versionamento](#esquema-de-versionamento)
- [Política de Suporte](#politica-de-suporte)
- [Laravel 5.7](#laravel-5-7)

## Esquema de Versionamento

O esquema de versionamento do Laravel mantém a seguinte convenção:
`paradigm.major.minor`.
As versões maiores do _framework_ são lançadas a cada seis meses (em fevereiro e
agosto), enquanto as versões menores podem ser lançadas semanalmente.
Versões menores **nunca** devem conter alterações significativas.

Ao referenciar o _framework_ Laravel ou seus componentes a partir da sua
aplicação ou pacote, você deve sempre usar uma restrição de versão como
`^5.7.*`, uma vez que as principais versões do Laravel incluem alterações
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

## Laravel 5.7 {: #laravel-5-7 }

O Laravel 5.7 continua as melhorias feitas no Laravel 5.6, introduzindo o
[Laravel Nova](https://nova.laravel.com), verificação de _e-mail_ opcional para
a geração automática de código de autenticação, suporte para pessoas usuárias
convidadas em portões e políticas de autorização, melhorias nos testes do
console, integração do `dump-server` do Symfony, notificações localizáveis e
uma variedade de outras correções de falhas e melhorias de usabilidade.

### Laravel Nova

O [Laravel Nova](https://nova.laravel.com) é um lindo painel de administração
de primeira classe para aplicações Laravel.
O principal recurso do Nova é a capacidade de administrar os registros do banco
de dados subjacente usando o Eloquent.
Além disso, o Nova oferece suporte para filtros, lentes, ações, ações em fila,
métricas, autorização, ferramentas personalizadas, cartões personalizados,
campos personalizados e muito mais.

Para saber mais sobre o Laravel Nova, confira o
[site do Nova](https://nova.laravel.com).

### Verificação de _E-mail_

O Laravel 5.7 introduz a verificação de _e-mail_ opcional à geração automática
de código de autenticação incluída no _framework_.
Para acomodar esse recurso, uma coluna de _timestamp_ `email_verified_at` foi
adicionada à migração da tabela `users` padrão incluída no _framework_.

Para solicitar que pessoas usuárias recém-registradas verifiquem seus e-mails, o
modelo `User` deve ser marcado com a interface `MustVerifyEmail`:

```php
<?php

namespace App;

use Illuminate\Notifications\Notifiable;
use Illuminate\Contracts\Auth\MustVerifyEmail;
use Illuminate\Foundation\Auth\User as Authenticatable;

class User extends Authenticatable implements MustVerifyEmail
{
    // ...
}
```

Assim que o modelo `User` for marcado com a interface `MustVerifyEmail`, as
pessoas usuárias recém-registradas receberão um _e-mail_ contendo um _link_ de
verificação assinado.
Assim que este _link_ for clicado, o Laravel registrará automaticamente o tempo
de verificação no banco de dados e redirecionará as pessoas usuárias para um
local de sua escolha.

Um _middleware_ `verified` foi adicionado ao _kernel_ HTTP da aplicação padrão.
Este _middleware_ pode ser anexado a rotas que devem permitir apenas pessoas
usuárias verificadas:

```php
'verified' => \Illuminate\Auth\Middleware\EnsureEmailIsVerified::class,
```

> **Dica:**
> Para saber mais sobre a verificação de _e-mail_, confira a
> [documentação completa](../verification.md).

### Políticas e Portões de Pessoas Usuárias Convidadas

Nas versões anteriores do Laravel, os portões e políticas de autorização
retornavam automaticamente `false` para pessoas visitantes não autenticadas da
sua aplicação.
No entanto, agora você pode permitir que pessoas convidadas passem por
verificações de autorização declarando um tipo "opcional" ou fornecendo um valor
padrão `null` para a definição do argumento do modelo de pessoa usuária:

```php
Gate::define('update-post', function (?User $user, Post $post) {
    // ...
});
```

### Servidor de _Dump_ do Symfony

O Laravel 5.7 oferece integração com o comando `dump-server` do Symfony através
de
[um pacote de Marcel Pociot](https://github.com/beyondcode/laravel-dump-server).
Para começar, execute o comando `dump-server` do Artisan:

```shell
php artisan dump-server
```

Depois que o servidor for iniciado, todas as chamadas para `dump` serão exibidas
na janela do console do `dump-server` em vez de no seu navegador, permitindo que
você inspecione os valores sem alterar a saída da resposta HTTP.

### Localização de Notificações

O Laravel agora permite que você envie notificações em uma localidade diferente
do idioma atual, e até lembrará dessa localidade se a notificação estiver na
fila.

Para conseguir isso, a classe `Illuminate\Notifications\Notification` agora
oferece um método `locale` para definir o idioma desejado.
A aplicação mudará para esta localidade quando a notificação estiver sendo
formatada e, em seguida, reverterá para a localidade anterior quando a
formatação for concluída:

```php
$user->notify((new InvoicePaid($invoice))->locale('es'));
```

A localização de múltiplas entradas notificáveis também pode ser obtida através
da fachada `Notification`:

```php
Notification::locale('es')->send($users, new InvoicePaid($invoice));
```

### Testes de Console

O Laravel 5.7 permite que você "simule" facilmente a entrada da pessoa usuária
para os comandos do console usando o método `expectsQuestion`.
Além disso, você pode especificar o código de saída e o texto que espera que
seja gerado pelo comando do console usando os métodos `assertExitCode` e
`expectsOutput`.
Por exemplo, considere o seguinte comando do console:

```php
Artisan::command('question', function () {
    $name = $this->ask('Qual o seu nome?');

    $language = $this->choice('Em qual linguagem você programa?', [
        'PHP',
        'Ruby',
        'Python',
    ]);

    $this->line('Seu nome é ' . $name . ' e você programa em ' . $language . '.');
});
```

Você pode testar este comando com o seguinte teste que utiliza os métodos
`expectsQuestion`, `expectsOutput` e `assertExitCode`:

```php
/**
 * Testa um comando do console.
 *
 * @return void
 */
public function test_console_command()
{
    $this->artisan('question')
        ->expectsQuestion('Qual o seu nome?', 'Taylor Otwell')
        ->expectsQuestion('Em qual linguagem você programa?', 'PHP')
        ->expectsOutput(' Seu nome é Taylor Otwell e você programa em PHP.')
        ->assertExitCode(0);
}
```

### Gerador de URL e Sintaxe _Callable_

Em vez de aceitar apenas _strings_, o gerador de URL do Laravel agora aceita
sintaxe "callable" ao gerar URLs para ações do controlador:`

```php
action([UserController::class, 'index']);
```

### Links do Paginador

O Laravel 5.7 permite controlar quantos _links_ adicionais são exibidos em cada
lado da "janela" de URLs do paginador.
Por padrão, três _links_ são exibidos em cada lado dos _links_ do paginador
principal.
No entanto, você pode controlar esse número usando o método `onEachSide`:

```bladehtml
{{ $paginator->onEachSide(5)->links() }}
```

### Fluxos de Leitura e Escrita do Sistema de Arquivos

A integração do Laravel com o Flysystem agora oferece métodos `readStream` e
`writeStream`:

```php
Storage::disk('s3')->writeStream(
    'remote-file.zip',
    Storage::disk('local')->readStream('local-file.zip')
);
```
