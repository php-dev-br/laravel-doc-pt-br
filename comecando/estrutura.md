<!-- source_url: https://github.com/laravel/docs/blob/11.x/structure.md -->
<!-- revision: 2cda2495b7737431b295d64dc4e0e38e35ce5e48 -->
<!-- status: ready -->

# Estrutura de Diretório

- [Introdução](#introducao)
- [O Diretório Raiz](#o-diretorio-raiz)
  - [O Diretório `app`](#o-diretorio-app)
  - [O Diretório `bootstrap`](#o-diretorio-bootstrap)
  - [O Diretório `config`](#o-diretorio-config)
  - [O Diretório `database`](#o-diretorio-database)
  - [O Diretório `public`](#o-diretorio-public)
  - [O Diretório `resources`](#o-diretorio-resources)
  - [O Diretório `routes`](#o-diretorio-routes)
  - [O Diretório `storage`](#o-diretorio-storage)
  - [O Diretório `tests`](#o-diretorio-tests)
  - [O Diretório `vendor`](#o-diretorio-vendor)
- [O Namespace `App`](#o-namespace-app)
  - [O Diretório `Broadcasting`](#o-diretorio-broadcasting)
  - [O Diretório `Console`](#o-diretorio-console)
  - [O Diretório `Events`](#o-diretorio-events)
  - [O Diretório `Exceptions`](#o-diretorio-exceptions)
  - [O Diretório `Http`](#o-diretorio-http)
  - [O Diretório `Jobs`](#o-diretorio-jobs)
  - [O Diretório `Listeners`](#o-diretorio-listeners)
  - [O Diretório `Mail`](#o-diretorio-mail)
  - [O Diretório `Models`](#o-diretorio-models)
  - [O Diretório `Notifications`](#o-diretorio-notifications)
  - [O Diretório `Policies`](#o-diretorio-policies)
  - [O Diretório `Providers`](#o-diretorio-providers)
  - [O Diretório `Rules`](#o-diretorio-rules)

## Introdução

A estrutura padrão da aplicação Laravel visa fornecer um excelente ponto de
partida para aplicações grandes e pequenas.
Mas você é livre para organizar sua aplicação como quiser.
O Laravel não impõe quase nenhuma restrição sobre a localização de qualquer
classe - desde que o Composer possa carregar automaticamente a classe.

> **Nota:**
> Começando agora com o Laravel?
> Confira o [Laravel Bootcamp](https://bootcamp.laravel.com) para uma jornada
> prática pelo _framework_ enquanto orientamos você na construção da sua
> primeira aplicação Laravel.

## O Diretório Raiz

#### O Diretório `app`

O diretório `app` contém o código principal da sua aplicação.
Exploraremos esse diretório com mais detalhes em breve; entretanto, quase todas
as classes da sua aplicação estarão neste diretório.

#### O Diretório `bootstrap`

O diretório `bootstrap` contém o arquivo `app.php` que inicializa o _framework_.
Este diretório também abriga um diretório `cache` que contém arquivos gerados
pelo _framework_ para otimização de desempenho, como arquivos de _cache_ de
rotas e serviços.

#### O Diretório `config`

O diretório `config`, como o nome indica, contém todos os arquivos de
configuração da sua aplicação.
É uma ótima ideia ler todos esses arquivos e se familiarizar com todas as opções
disponíveis.

#### O Diretório `database`

O diretório `database` contém migrações de banco de dados, fábricas de modelos e
sementes.
Se desejar, você também pode usar este diretório para armazenar um banco de
dados SQLite.

#### O Diretório `public`

O diretório `public` contém o arquivo `index.php`, que é o ponto de entrada para
todas as requisições que entram na sua aplicação e configura o carregamento
automático.
Este diretório também abriga seus ativos como imagens, JavaScript e CSS.

#### O Diretório `resources`

O diretório `resources` contém suas [visualizações](../views.md), bem como seus
ativos brutos e não compilados, como CSS ou JavaScript.

#### O Diretório `routes`

O diretório `routes` contém todas as definições de rota da sua aplicação.
Por padrão, dois arquivos de rota estão incluídos no Laravel: `web.php` e
`console.php`.

O arquivo `web.php` contém rotas que o Laravel coloca no grupo de _middlewares_
`web`, que fornecem estado de sessão, proteção contra CSRF e encriptação de
_cookies_.
Se a sua aplicação não oferecer uma API _RESTful_ sem estado, todas as suas
rotas provavelmente serão definidas no arquivo `web.php`.

O arquivo `console.php` é onde você pode definir todos os comandos de console
baseados em _closure_.
Cada _closure_ está vinculada a uma instância de comando, permitindo uma
abordagem simples para interagir com os métodos de entrada e saída de cada
comando.
Mesmo que este arquivo não defina rotas HTTP, ele define pontos de entrada
(rotas) baseados em console da sua aplicação.
Você também pode [agendar](../scheduling.md) tarefas no arquivo `console.php`.

Opcionalmente, você pode instalar arquivos de rota adicionais para rotas de API
(`api.php`) e canais de transmissão (`channels.php`), através dos comandos
`install:api` e `install:broadcasting` do Artisan.

O arquivo `api.php` contém rotas que não devem possuir estado, portanto, as
requisições que entram na aplicação por meio dessas rotas devem ser autenticadas
por meio de [_tokens_](../sanctum.md) e não terão acesso ao estado da sessão.

O arquivo `channels.php` é onde você pode registrar todos os canais de
[transmissão de eventos](../broadcasting.md) que sua aplicação suporta.

#### O Diretório `storage`

O diretório `storage` contém seus _logs_, _templates_ Blade compilados, sessões
baseadas em arquivos, _caches_ de arquivos e outros arquivos gerados pelo
_framework_.
Este diretório é dividido nos diretórios `app`, `framework` e `logs`.
O diretório `app` pode ser usado para armazenar qualquer arquivo gerado pela sua
aplicação.
O diretório `framework` é usado para armazenar arquivos e _caches_ gerados pelo
_framework_.
Finalmente, o diretório `logs` contém os arquivos de _log_ da sua aplicação.

O diretório `storage/app/public` pode ser usado para armazenar arquivos gerados
pela pessoa usuária, como avatares de perfil, que devem ser acessíveis
publicamente.
Você deve criar um link simbólico em `public/storage` que aponte para este
diretório.
Você pode criar o link usando o comando `php artisan storage:link` do Artisan.

#### O Diretório `tests`

O diretório `tests` contém seus testes automatizados.
Exemplos de testes unitários do [Pest](https://pestphp.com) ou
[PHPUnit](https://phpunit.de/) e testes de funcionalidades são fornecidos
prontos para uso.
Cada classe de teste deve ter como sufixo a palavra `Test`.
Você pode executar seus testes usando os comandos `/vendor/bin/pest` ou
`/vendor/bin/phpunit`.
Ou, se desejar uma representação mais detalhada e bonita dos resultados dos seus
testes, você pode executá-los usando o comando `php artisan test` do Artisan.

#### O Diretório `vendor`

O diretório `vendor` contém suas dependências do
[Composer](https://getcomposer.org).

## O Namespace `App`

Grande parte da sua aplicação está hospedada no diretório `app`.
Por padrão, este diretório tem o namespace `App` e é carregado automaticamente
pelo Composer usando o
[padrão de carregamento automático PSR-4](https://www.php-fig.org/psr/psr-4/).

Por padrão, o diretório `app` contém os diretórios `Http`, `Models` e
`Providers`.
No entanto, com o tempo, vários outros diretórios serão gerados no diretório
`app` conforme você usa os comandos `make` do Artisan para gerar classes.
Por exemplo, o diretório `app/Console` não existirá até você executar o comando
`make:command` do Artisan para gerar uma classe de comando.

Os diretórios `Console` e `Http` são explicados com mais detalhes em suas
respectivas seções abaixo, mas pense nos diretórios `Console` e `Http` como
fornecendo uma API para o núcleo da sua aplicação.
O protocolo HTTP e a CLI são mecanismos para interagir com a sua aplicação, mas,
na verdade, não contêm lógica de aplicação.
Em outras palavras, são duas formas de emitir comandos para sua aplicação.
O diretório `Console` contém todos os seus comandos do Artisan, enquanto o
diretório `Http` contém seus controladores, _middlewares_ e requisições.

> **Nota:**
> Muitas das classes no diretório `app` podem ser geradas pelo Artisan por meio
> de comandos.
> Para revisar os comandos disponíveis, execute o comando
> `php artisan list make` no seu terminal.

#### O Diretório `Broadcasting`

O diretório `Broadcasting` contém todas as classes de canais de transmissão da
sua aplicação.
Essas classes são geradas usando o comando `make:channel`.
Este diretório não existe por padrão, mas será criado para você quando você
criar seu primeiro canal.
Para saber mais sobre canais, confira a documentação sobre
[transmissão de eventos](../broadcasting.md).

#### O Diretório `Console`

O diretório `Console` contém todos os comandos do Artisan personalizados da sua
aplicação.
Esses comandos podem ser gerados usando o comando `make:command`.

#### O Diretório `Events`

Este diretório não existe por padrão, mas será criado para você pelos comandos
`event:generate` e `make:event` do Artisan.
O diretório `Events` abriga [classes de eventos](../events.md).
Os eventos podem ser usados para alertar outras partes da sua aplicação sobre a
ocorrência de uma determinada ação, proporcionando muita flexibilidade e
desacoplamento.

#### O Diretório `Exceptions`

O diretório `Exceptions` contém todas as exceções personalizadas da sua
aplicação.
Essas exceções podem ser geradas usando o comando `make:exception`.

#### O Diretório `Http`

O diretório `Http` contém seus controladores, _middlewares_ e requisições de
formulário.
Quase toda a lógica para tratar as requisições que entram na sua aplicação será
colocada neste diretório.

#### O Diretório `Jobs`

Este diretório não existe por padrão, mas será criado para você se você executar
o comando `make:job` do Artisan.
O diretório `Jobs` abriga os [trabalhos enfileiráveis](../queues.md) da sua
aplicação.
Os trabalhos podem ser enfileirados pela sua aplicação ou executados de forma
síncrona durante do ciclo de vida da requisição atual.
Os trabalhos executados de forma síncrona durante a requisição atual são às
vezes chamados de comandos, pois são uma implementação do
[padrão Command](https://pt.wikipedia.org/wiki/Command).

#### O Diretório `Listeners`

Este diretório não existe por padrão, mas será criado para você se você executar
os comandos `event:generate` ou `make:listener` do Artisan.
O diretório `Listeners` contém as classes que tratam seus
[eventos](../events.md).
Os ouvintes de eventos recebem uma instância de evento e executam a lógica em
resposta ao evento que está sendo disparado.
Por exemplo, um evento `UserRegistered` pode ser tratado por um ouvinte
`SendWelcomeEmail`.

#### O Diretório `Mail`

Este diretório não existe por padrão, mas será criado para você se você executar
o comando `make:mail` do Artisan.
O diretório `Mail` contém todas as suas
[classes que representam _e-mails_](../mail.md) enviados pela sua aplicação.
Os objetos de _e-mail_ permitem encapsular toda a lógica de construção de um
_e-mail_ em uma única classe simples que pode ser enviada usando o método
`Mail::send`.

#### O Diretório `Models`

O diretório `Models` contém todas as suas
[classes de modelo do Eloquent](../eloquent.md).
O ORM Eloquent incluído no Laravel fornece uma implementação simples e bonita do
ActiveRecord para trabalhar com seu banco de dados.
Cada tabela do banco de dados possui um modelo correspondente usado para
interagir com essa tabela.
Os modelos permitem consultar dados em suas tabelas, bem como inserir novos
registros na tabela.

#### O Diretório `Notifications`

Este diretório não existe por padrão, mas será criado para você se você executar
o comando `make:notification` do Artisan.
O diretório `Notifications` contém todas as notificações transacionais enviadas
pela sua aplicação, como notificações simples sobre eventos que acontecem na sua
aplicação.
O recurso de notificação do Laravel abstrai o envio de notificações por meio de
uma variedade de _drivers_, como _e-mail_, Slack, SMS ou armazenadas em um banco
de dados.

#### O Diretório `Policies`

Este diretório não existe por padrão, mas será criado para você se você executar
o comando `make:policy` do Artisan.
O diretório `Policies` contém as
[classes de política de autorização](../authorization.md) da sua aplicação.
As políticas são usadas para determinar se uma pessoa usuária pode executar uma
determinada ação em um recurso.

#### O Diretório `Providers`

O diretório `Providers` contém todos os
[provedores de serviços](../providers.md) da sua aplicação.
Os provedores de serviços inicializam sua aplicação vinculando serviços no
contêiner de serviço, registrando eventos ou executando qualquer outra tarefa
para preparar sua aplicação para as requisições recebidas.

Em uma nova aplicação Laravel, este diretório já conterá o `AppServiceProvider`.
Você pode adicionar seus próprios provedores a este diretório conforme
necessário.

#### O Diretório `Rules`

Este diretório não existe por padrão, mas será criado para você se você executar
o comando `make:rule` do Artisan.
O diretório `Rules` contém os objetos de regras de validação customizadas da sua
aplicação.
As regras são usadas para encapsular lógica de validação complicada em um objeto
simples.
Para obter mais informações, verifique a
[documentação de validação](../validation.md).
