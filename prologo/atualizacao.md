<!-- source_url: https://github.com/laravel/docs/blob/11.x/upgrade.md -->
<!-- revision: b8bf6ca8a64bd05c27e4ef2b6eddbd961d02bf2b -->
<!-- status: wip -->

# Guia de Atualização

- [Atualizando da versão 10.x para a 11.0](#atualizando-da-versao-10.x-para-a-11.0)

## Mudanças de Alto Impacto

- [Atualizando Dependências](#atualizando-dependencias)
- [Estrutura da Aplicação](#estrutura-da-aplicacao)
- [Tipos de Ponto Flutuante](#tipos-de-ponto-flutuante)
- [Modificando Colunas](#modificando-colunas)
- [Versão Mínima do SQLite](#versao-minima-do-sqlite)
- [Updating Sanctum](#updating-sanctum)

## Mudanças de Médio Impacto

- [Carbon 3](#carbon-3)
- [Atualização do Hash da Senha](#atualizacao-do-hash-da-senha)
- [Per-Second Rate Limiting](#per-second-rate-limiting)

## Mudanças de Baixo Impacto

- [Doctrine DBAL Removal](#doctrine-dbal-removal)
- [Método `casts` do Modelo do Eloquent](#metodo-casts-do-modelo-do-eloquent)
- [Spatial Types](#spatial-types)
- [Spatie Once Package](#spatie-once-package)
- [O Contrato `Enumerable`](#o-contrato-enumerable)
- [O Contrato `UserProvider`](#o-contrato-userprovider)
- [O Contrato `Authenticatable`](#o-contrato-authenticatable)

## Atualizando da versão 10.x para a 11.0 {: #atualizando-da-versao-10.x-para-a-11.0 }

#### Tempo estimado de atualização: 15 minutos

> **Nota:** Tentamos documentar todas as alterações significativas possíveis.
> Como algumas dessas alterações importantes estão em partes obscuras do
> framework, apenas uma parte dessas alterações pode realmente afetar sua
> aplicação.
> Quer economizar tempo? Você pode usar o
> [Laravel Shift](https://laravelshift.com/) para automatizar as atualizações da
> sua aplicação.

### Atualizando Dependências

**Probabilidade de Impacto: Alta**

#### PHP 8.2.0 Requerido {: #php-8.2.0-requerido }

O Laravel agora requer o PHP 8.2.0 ou superior.

#### curl 7.34.0 Requerida {: #curl-7.34.0-requerida }

O cliente HTTP do Laravel agora requer a ferramenta curl 7.34.0 ou superior.

#### Dependências do Composer

Você deve atualizar as seguintes dependências no arquivo `composer.json` da sua
aplicação:

- `laravel/framework` para versão `^11.0`
- `nunomaduro/collision` para versão `^8.1`
- `laravel/breeze` para versão `^2.0` (se instalado)
- `laravel/cashier` para versão `^15.0` (se instalado)
- `laravel/dusk` para versão `^8.0` (se instalado)
- `laravel/jetstream` para versão `^5.0` (se instalado)
- `laravel/octane` para versão `^2.3` (se instalado)
- `laravel/passport` para versão `^12.0` (se instalado)
- `laravel/sanctum` para versão `^4.0` (se instalado)
- `laravel/spark-stripe` para versão `^5.0` (se instalado)
- `laravel/telescope` para versão `^5.0` (se instalado)
- `inertiajs/inertia-laravel` para versão `^1.0` (se instalado)

Se sua aplicação estiver usando Laravel Cashier Stripe, Passport, Sanctum, Spark
Stripe ou Telescope, você precisará publicar suas migrações na sua aplicação.
Cashier Stripe, Passport, Sanctum, Spark Stripe e Telescope **não carregam mais
migrações automaticamente de seu próprio diretório de migrações**.
Portanto, você deve executar o seguinte comando para publicar suas migrações na
sua aplicação:

```bash
php artisan vendor:publish --tag=cashier-migrations
php artisan vendor:publish --tag=passport-migrations
php artisan vendor:publish --tag=sanctum-migrations
php artisan vendor:publish --tag=spark-migrations
php artisan vendor:publish --tag=telescope-migrations
```

Além disso, você deve revisar os guias de atualização de cada um destes pacotes
para garantir que está ciente de quaisquer alterações significativas adicionais:

- [Laravel Cashier Stripe](#cashier-stripe)
- [Laravel Passport](#passport)
- [Laravel Sanctum](#sanctum)
- [Laravel Spark Stripe](#spark-stripe)
- [Laravel Telescope](#telescope)

Se você instalou manualmente o instalador do Laravel, você deve atualizar o
instalador via Composer:

```bash
composer global require laravel/installer:^5.6
```

Finalmente, você pode remover a dependência `doctrine/dbal` do Composer se você
a adicionou anteriormente à sua aplicação, já que o Laravel não depende mais
deste pacote.

### Estrutura da Aplicação

O Laravel 11 introduz uma nova estrutura de aplicação padrão com menos arquivos
padrão.
Ou seja, as novas aplicações Laravel contêm menos provedores de serviços,
middlewares e arquivos de configuração.

No entanto, **não recomendamos** que as aplicações do Laravel 10 sendo
atualizadas para o Laravel 11 tentem migrar sua estrutura de aplicação, pois o
Laravel 11 foi cuidadosamente ajustado para também suportar a estrutura de
aplicação do Laravel 10.

### Autenticação

#### Atualização do Hash da Senha

O Laravel 11 irá atualizar automaticamente o hash das senhas dos seus usuários
durante a autenticação se o "fator de trabalho" do seu algoritmo de hash tiver
sido atualizado desde o último hash da senha.

Normalmente, isso não deve quebrar sua aplicação; entretanto, você pode
desabilitar este comportamento adicionando a opção `rehash_on_login` ao arquivo
de configuração `config/hashing.php` da sua aplicação:

```php
'rehash_on_login' => false,
```

#### O Contrato `UserProvider`

**Probabilidade de Impacto: Baixa**

O contrato `Illuminate\Contracts\Auth\UserProvider` recebeu um novo método
`rehashPasswordIfRequired`.
Este método é responsável por refazer o hash e armazenar a senha do usuário no
armazenamento quando o fator de trabalho do algoritmo de hash da aplicação for
alterado.

Se sua aplicação ou pacote definir uma classe que implemente essa interface,
você deverá adicionar o novo método `rehashPasswordIfRequired` à sua
implementação.
Uma implementação de referência pode ser encontrada na classe
`Illuminate\Auth\EloquentUserProvider`:

```php
public function rehashPasswordIfRequired(Authenticatable $user, array $credentials, bool $force = false);
```

#### O Contrato `Authenticatable`

**Probabilidade de Impacto: Baixa**

O contrato `Illuminate\Contracts\Auth\Authenticatable` recebeu um novo método
`getAuthPasswordName`.
Este método é responsável por retornar o nome da coluna de senha da sua entidade
autenticável.

Se sua aplicação ou pacote definir uma classe que implemente essa interface,
você deverá adicionar o novo método `getAuthPasswordName` à sua implementação:

```php
public function getAuthPasswordName()
{
    return 'password';
}
```

O modelo `User` padrão incluído no Laravel recebe este método automaticamente,
uma vez que o método está incluído na trait `Illuminate\Auth\Authenticatable`.

#### A Classe `AuthenticationException`

**Probabilidade de Impacto: Muito Baixa**

O método `redirectTo` da classe `Illuminate\Auth\AuthenticationException` agora
requer uma instância `Illuminate\Http\Request` como seu primeiro argumento.
Se você estiver capturando manualmente esta exceção e chamando o método
`redirectTo`, deverá atualizar seu código de acordo:

```php
if ($e instanceof AuthenticationException) {
    $path = $e->redirectTo($request);
}
```

### Cache

#### Prefixos de Chave de Cache

**Probabilidade de Impacto: Muito Baixa**

Anteriormente, se um prefixo de chave de cache fosse definido para os
armazenamentos de cache DynamoDB, Memcached ou Redis, o Laravel acrescentaria
um `:` ao prefixo.
No Laravel 11, o prefixo da chave de cache não recebe o sufixo `:`.
Se desejar manter o comportamento de prefixo anterior, você pode adicionar
manualmente o sufixo `:` ao prefixo da chave de cache.

### Coleções

#### O Contrato `Enumerable`

**Probabilidade de Impacto: Baixa**

O método `dump` do contrato `Illuminate\Support\Enumerable` foi atualizado para
aceitar um argumento variável `...$args`.
Se você estiver implementando esta interface, deverá atualizar sua implementação
de acordo:

```php
public function dump(...$args);
```

### Banco de Dados

#### SQLite 3.35.0+ { #versao-minima-do-sqlite }

**Probabilidade de Impacto: Alta**

Se a sua aplicação estiver utilizando um banco de dados SQLite, será necessário
o SQLite 3.35.0 ou superior.

#### Método `casts` do Modelo do Eloquent

**Probabilidade de Impacto: Baixa**

A classe base do modelo do Eloquent agora define um método `casts` para suportar
a definição de conversões de atributos.
Se um dos modelos da sua aplicação estiver definindo um relacionamento `casts`,
ele poderá entrar em conflito com o método `casts` agora presente na classe base
de modelo do Eloquent.

#### Modificando Colunas

**Probabilidade de Impacto: Alta**

Ao modificar uma coluna, agora você deve incluir explicitamente todos os
modificadores que deseja manter na definição da coluna após ela ser alterada.
Quaisquer atributos ausentes serão descartados.
Por exemplo, para manter os atributos `unsigned`, `default` e `comment`, você
deve chamar cada modificador explicitamente ao alterar a coluna, mesmo que esses
atributos tenham sido atribuídos à coluna por uma migração anterior.

Por exemplo, imagine que você tem uma migração que cria uma coluna `votos` com
os atributos `unsigned`, `default` e `comment`:

```php
Schema::create('usuarios', function (Blueprint $table) {
    $table->integer('votos')->unsigned()->default(1)->comment('A contagem dos votos');
});
```

Mais tarde, você escreve uma migração que altera a coluna para ser `nullable`
também:

```php
Schema::table('usuarios', function (Blueprint $table) {
    $table->integer('votos')->nullable()->change();
});
```

No Laravel 10, esta migração manteria os atributos `unsigned`, `default` e
`comment` na coluna.
Porém, no Laravel 11, a migração agora também deve incluir todos os atributos
que foram previamente definidos na coluna.
Caso contrário, eles serão descartados:

```php
Schema::table('usuarios', function (Blueprint $table) {
    $table->integer('votos')
        ->unsigned()
        ->default(1)
        ->comment('A contagem dos votos')
        ->nullable()
        ->change();
});
```

O método `change` não altera os índices da coluna.
Portanto, você pode usar modificadores de índice para adicionar ou eliminar
explicitamente um índice ao modificar a coluna:

```php
// Adicionar um índice...
$table->bigIncrements('id')->primary()->change();

// Descartar um índice...
$table->char('codigo_postal', 10)->unique(false)->change();
```

Se você não quiser atualizar todas as migrações de "alteração" existentes na sua
aplicação para manter os atributos existentes da coluna, você pode simplesmente
[comprimir suas migrações](../migrations.md#squashing-migrations):

```bash
php artisan schema:dump
```

Depois que suas migrações forem comprimidas, o Laravel irá "migrar" o banco de
dados usando o arquivo de esquema da sua aplicação antes de executar qualquer
migração pendente.

#### Tipos de Ponto Flutuante

**Probabilidade de Impacto: Alta**

The `double` and `float` migration column types have been rewritten to be
consistent across all databases.

The `double` column type now creates a `DOUBLE` equivalent column without total
digits and places (digits after decimal point), which is the standard SQL
syntax.
Therefore, you may remove the arguments for `$total` and `$places`:

```php
$table->double('amount');
```

The `float` column type now creates a `FLOAT` equivalent column without total
digits and places (digits after decimal point), but with an
optional `$precision` specification to determine storage size as a 4-byte
single-precision column or an 8-byte double-precision column.
Therefore, you may remove the arguments for `$total` and `$places` and specify
the optional `$precision` to your desired value and according to your database's
documentation:

```php
$table->float('amount', precision: 53);
```

The `unsignedDecimal`, `unsignedDouble`, and `unsignedFloat` methods have been
removed, as the unsigned modifier for these column types has been deprecated by
MySQL, and was never standardized on other database systems.
However, if you wish to continue using the deprecated unsigned attribute for
these column types, you may chain the `unsigned` method onto the column's
definition:

```php
$table->decimal('amount', total: 8, places: 2)->unsigned();
$table->double('amount')->unsigned();
$table->float('amount', precision: 53)->unsigned();
```

<a name="dedicated-mariadb-driver"></a>

#### Dedicated MariaDB Driver

**Probabilidade de Impacto: Muito Baixa**

Instead of always utilizing the MySQL driver when connecting to MariaDB
databases, Laravel 11 adds a dedicated database driver for MariaDB.

If your application connects to a MariaDB database, you may update the
connection configuration to the new `mariadb` driver to benefit from MariaDB
specific features in the future:

    'driver' => 'mariadb',
    'url' => env('DB_URL'),
    'host' => env('DB_HOST', '127.0.0.1'),
    'port' => env('DB_PORT', '3306'),
    // ...

Currently, the new MariaDB driver behaves like the current MySQL driver with one
exception: the `uuid` schema builder method creates native UUID columns instead
of `char(36)` columns.

If your existing migrations utilize the `uuid` schema builder method and you
choose to use the new `mariadb` database driver, you should update your
migration's invocations of the `uuid` method to `char` to avoid breaking changes
or unexpected behavior:

```php
Schema::table('users', function (Blueprint $table) {
    $table->char('uuid', 36);

    // ...
});
```

<a name="spatial-types"></a>

#### Spatial Types

**Probabilidade de Impacto: Baixa**

The spatial column types of database migrations have been rewritten to be
consistent across all databases.
Therefore, you may
remove `point`, `lineString`, `polygon`, `geometryCollection`, `multiPoint`, `multiLineString`, `multiPolygon`,
and `multiPolygonZ` methods from your migrations and use `geometry`
or `geography` methods instead:

```php
$table->geometry('shapes');
$table->geography('coordinates');
```

To explicitly restrict the type or the spatial reference system identifier for
values stored in the column on MySQL, MariaDB, and PostgreSQL, you may pass
the `subtype` and `srid` to the method:

```php
$table->geometry('dimension', subtype: 'polygon', srid: 0);
$table->geography('latitude', subtype: 'point', srid: 4326);
```

The `isGeometry` and `projection` column modifiers of the PostgreSQL grammar
have been removed accordingly.

<a name="doctrine-dbal-removal"></a>

#### Doctrine DBAL Removal

**Probabilidade de Impacto: Baixa**

The following list of Doctrine DBAL related classes and methods have been
removed.
Laravel is no longer dependent on this package and registering custom Doctrines
types is no longer necessary for the proper creation and alteration of various
column types that previously required custom types:

<div class="content-list" markdown="1">

- `Illuminate\Database\Schema\Builder::$alwaysUsesNativeSchemaOperationsIfPossible`
  class property
- `Illuminate\Database\Schema\Builder::useNativeSchemaOperationsIfPossible()`
  method
- `Illuminate\Database\Connection::usingNativeSchemaOperations()` method
- `Illuminate\Database\Connection::isDoctrineAvailable()` method
- `Illuminate\Database\Connection::getDoctrineConnection()` method
- `Illuminate\Database\Connection::getDoctrineSchemaManager()` method
- `Illuminate\Database\Connection::getDoctrineColumn()` method
- `Illuminate\Database\Connection::registerDoctrineType()` method
- `Illuminate\Database\DatabaseManager::registerDoctrineType()` method
- `Illuminate\Database\PDO` directory
- `Illuminate\Database\DBAL\TimestampType` class
- `Illuminate\Database\Schema\Grammars\ChangeColumn` class
- `Illuminate\Database\Schema\Grammars\RenameColumn` class
- `Illuminate\Database\Schema\Grammars\Grammar::getDoctrineTableDiff()` method

</div>

In addition, registering custom Doctrine types via `dbal.types` in your
application's `database` configuration file is no longer required.

If you were previously using Doctrine DBAL to inspect your database and its
associated tables, you may use Laravel's new native schema
methods (`Schema::getTables()`, `Schema::getColumns()`, `Schema::getIndexes()`, `Schema::getForeignKeys()`,
etc.) instead.

<a name="deprecated-schema-methods"></a>

#### Deprecated Schema Methods

**Probabilidade de Impacto: Muito Baixa**

The deprecated, Doctrine
based `Schema::getAllTables()`, `Schema::getAllViews()`,
and `Schema::getAllTypes()` methods have been removed in favor of new Laravel
native `Schema::getTables()`, `Schema::getViews()`, and `Schema::getTypes()`
methods.

When using PostgreSQL and SQL Server, none of the new schema methods will accept
a three-part reference (e.g. `database.schema.table`).
Therefore, you should use `connection()` to declare the database instead:

```php
Schema::connection('database')->hasTable('schema.table');
```

<a name="get-column-types"></a>

#### Schema Builder `getColumnType()` Method

**Probabilidade de Impacto: Muito Baixa**

The `Schema::getColumnType()` method now always returns actual type of the given
column, not the Doctrine DBAL equivalent type.

<a name="database-connection-interface"></a>

#### Database Connection Interface

**Probabilidade de Impacto: Muito Baixa**

The `Illuminate\Database\ConnectionInterface` interface has received a
new `scalar` method.
If you are defining your own implementation of this interface, you should add
the `scalar` method to your implementation:

```php
public function scalar($query, $bindings = [], $useReadPdo = true);
```

<a name="dates"></a>

### Dates

<a name="carbon-3"></a>

#### Carbon 3

**Probabilidade de Impacto: Média**

Laravel 11 supports both Carbon 2 and Carbon 3.
Carbon is a date manipulation library utilized extensively by Laravel and
packages throughout the ecosystem.
If you install Carbon 3, you should review
Carbon's [change log](https://github.com/briannesbitt/Carbon/releases/tag/3.0.0).

<a name="mail"></a>

### Mail

<a name="the-mailer-contract"></a>

#### The `Mailer` Contract

**Probabilidade de Impacto: Muito Baixa**

The `Illuminate\Contracts\Mail\Mailer` contract has received a new `sendNow`
method.
If your application or package is manually implementing this contract, you
should add the new `sendNow` method to your implementation:

```php
public function sendNow($mailable, array $data = [], $callback = null);
```

<a name="packages"></a>

### Packages

<a name="publishing-service-providers"></a>

#### Publishing Service Providers to the Application

**Probabilidade de Impacto: Muito Baixa**

If you have written a Laravel package that manually publishes a service provider
to the application's `app/Providers` directory and manually modifies the
application's `config/app.php` configuration file to register the service
provider, you should update your package to utilize the
new `ServiceProvider::addProviderToBootstrapFile` method.

The `addProviderToBootstrapFile` method will automatically add the service
provider you have published to the application's `bootstrap/providers.php` file,
since the `providers` array does not exist within the `config/app.php`
configuration file in new Laravel 11 applications.

```php
use Illuminate\Support\ServiceProvider;

ServiceProvider::addProviderToBootstrapFile(Provider::class);
```

<a name="queues"></a>

### Queues

<a name="the-batch-repository-interface"></a>

#### The `BatchRepository` Interface

**Probabilidade de Impacto: Muito Baixa**

The `Illuminate\Bus\BatchRepository` interface has received a new `rollBack`
method.
If you are implementing this interface within your own package or application,
you should add this method to your implementation:

```php
public function rollBack();
```

<a name="synchronous-jobs-in-database-transactions"></a>

#### Synchronous Jobs in Database Transactions

**Probabilidade de Impacto: Muito Baixa**

Previously, synchronous jobs (jobs using the `sync` queue driver) would execute
immediately, regardless of whether the `after_commit` configuration option of
the queue connection was set to `true` or the `afterCommit` method was invoked
on the job.

In Laravel 11, synchronous queue jobs will now respect the "after commit"
configuration of the queue connection or job.

<a name="rate-limiting"></a>

### Rate Limiting

<a name="per-second-rate-limiting"></a>

#### Per-Second Rate Limiting

**Probabilidade de Impacto: Média**

Laravel 11 supports per-second rate limiting instead of being limited to
per-minute granularity.
There are a variety of potential breaking changes you should be aware of related
to this change.

The `GlobalLimit` class constructor now accepts seconds instead of minutes.
This class is not documented and would not typically be used by your
application:

```php
new GlobalLimit($attempts, 2 * 60);
```

The `Limit` class constructor now accepts seconds instead of minutes.
All documented usages of this class are limited to static constructors such
as `Limit::perMinute` and `Limit::perSecond`.
However, if you are instantiating this class manually, you should update your
application to provide seconds to the class's constructor:

```php
new Limit($key, $attempts, 2 * 60);
```

The `Limit` class's `decayMinutes` property has been renamed to `decaySeconds`
and now contains seconds instead of minutes.

The `Illuminate\Queue\Middleware\ThrottlesExceptions`
and `Illuminate\Queue\Middleware\ThrottlesExceptionsWithRedis` class
constructors now accept seconds instead of minutes:

```php
new ThrottlesExceptions($attempts, 2 * 60);
new ThrottlesExceptionsWithRedis($attempts, 2 * 60);
```

<a name="cashier-stripe"></a>

### Cashier Stripe

<a name="updating-cashier-stripe"></a>

#### Updating Cashier Stripe

**Probabilidade de Impacto: Alta**

Laravel 11 no longer supports Cashier Stripe 14.x.
Therefore, you should update your application's Laravel Cashier Stripe
dependency to `^15.0` in your `composer.json` file.

Cashier Stripe 15.0 no longer automatically loads migrations from its own
migrations directory.
Instead, you should run the following command to publish Cashier Stripe's
migrations to your application:

```shell
php artisan vendor:publish --tag=cashier-migrations
```

Please review the
complete [Cashier Stripe upgrade guide](https://github.com/laravel/cashier-stripe/blob/15.x/UPGRADE.md)
for additional breaking changes.

<a name="spark-stripe"></a>

### Spark (Stripe)

<a name="updating-spark-stripe"></a>

#### Updating Spark Stripe

**Probabilidade de Impacto: Alta**

Laravel 11 no longer supports Laravel Spark Stripe 4.x.
Therefore, you should update your application's Laravel Spark Stripe dependency
to `^5.0` in your `composer.json` file.

Spark Stripe 5.0 no longer automatically loads migrations from its own
migrations directory.
Instead, you should run the following command to publish Spark Stripe's
migrations to your application:

```shell
php artisan vendor:publish --tag=spark-migrations
```

Please review the
complete [Spark Stripe upgrade guide](https://spark.laravel.com/docs/spark-stripe/upgrade.html)
for additional breaking changes.

<a name="passport"></a>

### Passport

<a name="updating-telescope"></a>

#### Updating Passport

**Probabilidade de Impacto: Alta**

Laravel 11 no longer supports Laravel Passport 11.x.
Therefore, you should update your application's Laravel Passport dependency
to `^12.0` in your `composer.json` file.

Passport 12.0 no longer automatically loads migrations from its own migrations
directory.
Instead, you should run the following command to publish Passport's migrations
to your application:

```shell
php artisan vendor:publish --tag=passport-migrations
```

In addition, the password grant type is disabled by default.
You may enable it by invoking the `enablePasswordGrant` method in the `boot`
method of your application's `AppServiceProvider`:

    public function boot(): void
    {
        Passport::enablePasswordGrant();
    }

<a name="sanctum"></a>

### Sanctum

<a name="updating-sanctum"></a>

#### Updating Sanctum

**Probabilidade de Impacto: Alta**

Laravel 11 no longer supports Laravel Sanctum 3.x.
Therefore, you should update your application's Laravel Sanctum dependency
to `^4.0` in your `composer.json` file.

Sanctum 4.0 no longer automatically loads migrations from its own migrations
directory.
Instead, you should run the following command to publish Sanctum's migrations to
your application:

```shell
php artisan vendor:publish --tag=sanctum-migrations
```

Then, in your application's `config/sanctum.php` configuration file, you should
update the references to the `authenticate_session`, `encrypt_cookies`,
and `validate_csrf_token` middleware to the following:

    'middleware' => [
        'authenticate_session' => Laravel\Sanctum\Http\Middleware\AuthenticateSession::class,
        'encrypt_cookies' => Illuminate\Cookie\Middleware\EncryptCookies::class,
        'validate_csrf_token' => Illuminate\Foundation\Http\Middleware\ValidateCsrfToken::class,
    ],

<a name="telescope"></a>

### Telescope

<a name="updating-telescope"></a>

#### Updating Telescope

**Probabilidade de Impacto: Alta**

Laravel 11 no longer supports Laravel Telescope 4.x.
Therefore, you should update your application's Laravel Telescope dependency
to `^5.0` in your `composer.json` file.

Telescope 5.0 no longer automatically loads migrations from its own migrations
directory.
Instead, you should run the following command to publish Telescope's migrations
to your application:

```shell
php artisan vendor:publish --tag=telescope-migrations
```

<a name="spatie-once-package"></a>

### Spatie Once Package

**Probabilidade de Impacto: Média**

Laravel 11 now provides its own [`once` function](../helpers.md#method-once) to
ensure that a given closure is only executed once.
Therefore, if your application has a dependency on the `spatie/once` package,
you should remove it from your application's `composer.json` file to avoid
conflicts.

<a name="miscellaneous"></a>

### Miscellaneous

We also encourage you to view the changes in
the `laravel/laravel` [GitHub repository](https://github.com/laravel/laravel).
While many of these changes are not required, you may wish to keep these files
in sync with your application.
Some of these changes will be covered in this upgrade guide, but others, such as
changes to configuration files or comments, will not be.
You can easily view the changes with
the [GitHub comparison tool](https://github.com/laravel/laravel/compare/10.x...11.x)
and choose which updates are important to you.
