<!-- source_url: https://github.com/laravel/docs/blob/11.x/upgrade.md -->
<!-- revision: b8bf6ca8a64bd05c27e4ef2b6eddbd961d02bf2b -->
<!-- status: ready -->

# Guia de Atualização

- [Atualizando da versão 10.x para a 11.0](#atualizando-da-versao-10.x-para-a-11.0)

## Mudanças de Alto Impacto

- [Atualizando Dependências](#atualizando-dependencias)
- [Estrutura da Aplicação](#estrutura-da-aplicacao)
- [Tipos de Ponto Flutuante](#tipos-de-ponto-flutuante)
- [Modificando Colunas](#modificando-colunas)
- [Versão Mínima do SQLite](#versao-minima-do-sqlite)
- [Atualizando o Sanctum](#atualizando-o-sanctum)

## Mudanças de Médio Impacto

- [Carbon 3](#carbon-3)
- [Atualização do _Hash_ de Senha](#atualizacao-do-hash-de-senha)
- [Limitação de Taxa por Segundo](#limitacao-de-taxa-por-segundo)

## Mudanças de Baixo Impacto

- [Remoção do Doctrine DBAL](#remocao-do-doctrine-dbal)
- [Método `casts` do Modelo do Eloquent](#metodo-casts-do-modelo-do-eloquent)
- [Tipos Espaciais](#tipos-espaciais)
- [Pacote Spatie Once](#pacote-spatie-once)
- [O Contrato `Enumerable`](#o-contrato-enumerable)
- [O Contrato `UserProvider`](#o-contrato-userprovider)
- [O Contrato `Authenticatable`](#o-contrato-authenticatable)

## Atualizando da versão 10.x para a 11.0 {: #atualizando-da-versao-10.x-para-a-11.0 }

#### Tempo estimado de atualização: 15 minutos

> **Nota:**
> Tentamos documentar todas as alterações significativas possíveis.
> Como algumas dessas alterações importantes estão em partes obscuras do
> _framework_, apenas uma parte dessas alterações pode realmente afetar sua
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
migrações automaticamente do diretório de migrações**.
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
instalador usando o Composer:

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
_middlewares_ e arquivos de configuração.

No entanto, **não recomendamos** que as aplicações do Laravel 10 sendo
atualizadas para o Laravel 11 tentem migrar sua estrutura de aplicação, pois o
Laravel 11 foi cuidadosamente ajustado para também suportar a estrutura de
aplicação do Laravel 10.

### Autenticação

#### Atualização do _Hash_ de Senha

O Laravel 11 irá atualizar automaticamente o _hash_ das senhas das pessoas
usuárias durante a autenticação se o fator de trabalho do algoritmo de _hash_
tiver sido atualizado desde que o último _hash_ de senha foi feito.

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
Este método é responsável por refazer o _hash_ e armazenar a senha da pessoa
usuária no armazenamento quando o fator de trabalho do algoritmo de _hash_ da
aplicação for alterado.

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
uma vez que o método está incluído na _trait_ `Illuminate\Auth\Authenticatable`.

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

### _Cache_

#### Prefixos de Chave de _Cache_

**Probabilidade de Impacto: Muito Baixa**

Anteriormente, se um prefixo de chave de _cache_ fosse definido para os
armazenamentos de _cache_ DynamoDB, Memcached ou Redis, o Laravel acrescentaria
um `:` ao prefixo.
No Laravel 11, o prefixo da chave de _cache_ não recebe o sufixo `:`.
Se desejar manter o comportamento de prefixo anterior, você pode adicionar
manualmente o sufixo `:` ao prefixo da chave de _cache_.

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

Se a sua aplicação estiver usando um banco de dados SQLite, será necessário o
SQLite 3.35.0 ou superior.

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
Qualquer atributo ausente será descartado.
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

Os tipos de coluna de migração `double` e `float` foram reescritos para serem
consistentes em todos os bancos de dados.

O tipo de coluna `double` agora cria uma coluna `DOUBLE` equivalente sem o total
de dígitos e casas decimais (dígitos após o ponto decimal), que é a sintaxe SQL
padrão.
Portanto, você pode remover os argumentos `$total` e `$places`:

```php
$table->double('valor');
```

O tipo de coluna `float` agora cria uma coluna equivalente `FLOAT` sem o total
de dígitos e casas decimais (dígitos após o ponto decimal), mas com uma
especificação `$precision` opcional para determinar o tamanho do armazenamento
como uma coluna de precisão simples de 4 _bytes_ ou uma coluna de precisão dupla
de 8 _bytes_.
Portanto, você pode remover os argumentos `$total` e `$places` e especificar o
argumento opcional `$precision` para o valor desejado e conforme a documentação
do seu banco de dados:

```php
$table->float('valor', precision: 53);
```

Os métodos `unsignedDecimal`, `unsignedDouble` e `unsignedFloat` foram
removidos, pois o modificador `unsigned` para esses tipos de coluna foi
descontinuado pelo MySQL e nunca foi padronizado em outros sistemas de banco de
dados.
No entanto, se você deseja continuar usando o atributo obsoleto `unsigned` para
esses tipos de coluna, você pode encadear o método `unsigned` na definição da
coluna:

```php
$table->decimal('valor', total: 8, places: 2)->unsigned();
$table->double('valor')->unsigned();
$table->float('valor', precision: 53)->unsigned();
```

#### _Driver_ Dedicado MariaDB

**Probabilidade de Impacto: Muito Baixa**

Em vez de sempre usar o _driver_ MySQL ao conectar-se a bancos de dados MariaDB,
o Laravel 11 adiciona um _driver_ de banco de dados dedicado para o MariaDB.

Se a sua aplicação se conecta a um banco de dados MariaDB, você poderá atualizar
a configuração da conexão para o novo _driver_ `mariadb` para se beneficiar dos
recursos específicos do MariaDB no futuro:

```php
'driver' => 'mariadb',
'url' => env('DB_URL'),
'host' => env('DB_HOST', '127.0.0.1'),
'port' => env('DB_PORT', '3306'),
// ...
```

Atualmente, o novo _driver_ MariaDB se comporta como o _driver_ MySQL atual com
uma exceção: o método `uuid` do construtor de esquema cria colunas UUID nativas
em vez de colunas `char(36)`.

Se suas migrações existentes usam o método `uuid` do construtor de esquema e
você optar por usar o novo _driver_ de banco de dados `mariadb`, você deverá
atualizar as invocações do método `uuid` da sua migração para `char` para evitar
alterações significativas ou comportamento inesperado:

```php
Schema::table('usuarios', function (Blueprint $table) {
    $table->char('uuid', 36);

    // ...
});
```

#### Tipos Espaciais

**Probabilidade de Impacto: Baixa**

Os tipos de colunas espaciais de migrações de banco de dados foram reescritos
para serem consistentes em todos os bancos de dados.
Portanto, você pode remover os métodos `point`, `lineString`, `polygon`,
`geometryCollection`, `multiPoint`, `multiLineString`, `multiPolygon` e
`multiPolygonZ` das suas migrações e usar os métodos `geometry` ou `geography`:

```php
$table->geometry('formas');
$table->geography('coordenadas');
```

Para restringir explicitamente o tipo ou o identificador do sistema de
referência espacial para valores armazenados na coluna no MySQL, MariaDB e
PostgreSQL, você pode passar o `subtype` e o `srid` para o método:

```php
$table->geometry('dimensao', subtype: 'polygon', srid: 0);
$table->geography('latitude', subtype: 'point', srid: 4326);
```

Os modificadores de coluna `isGeometry` e `projection` da gramática do
PostgreSQL foram removidos de acordo.

#### Remoção do Doctrine DBAL

**Probabilidade de Impacto: Baixa**

A lista de classes e métodos relacionados ao Doctrine DBAL a seguir foi
removida.
O Laravel não depende mais deste pacote e registrar tipos personalizados do
Doctrine não é mais necessário para a criação e alteração adequada de vários
tipos de colunas que anteriormente exigiam tipos personalizados:

- Propriedade de classe
  `Illuminate\Database\Schema\Builder::$alwaysUsesNativeSchemaOperationsIfPossible`
- Método
  `Illuminate\Database\Schema\Builder::useNativeSchemaOperationsIfPossible()`
- Método `Illuminate\Database\Connection::usingNativeSchemaOperations()`
- Método `Illuminate\Database\Connection::isDoctrineAvailable()`
- Método `Illuminate\Database\Connection::getDoctrineConnection()`
- Método `Illuminate\Database\Connection::getDoctrineSchemaManager()`
- Método `Illuminate\Database\Connection::getDoctrineColumn()`
- Método `Illuminate\Database\Connection::registerDoctrineType()`
- Método `Illuminate\Database\DatabaseManager::registerDoctrineType()`
- Diretório `Illuminate\Database\PDO`
- Classe `Illuminate\Database\DBAL\TimestampType`
- Classe `Illuminate\Database\Schema\Grammars\ChangeColumn`
- Classe `Illuminate\Database\Schema\Grammars\RenameColumn`
- Método `Illuminate\Database\Schema\Grammars\Grammar::getDoctrineTableDiff()`

Além disso, não é mais necessário registrar os tipos personalizados do Doctrine
via `dbal.types` no arquivo de configuração `database` da sua aplicação.

Se você estava usando o Doctrine DBAL anteriormente para inspecionar seu banco
de dados e suas tabelas associativas, em vez disso, você pode usar os novos
métodos de esquema nativos do Laravel (`Schema::getTables()`,
`Schema::getColumns()`, `Schema::getIndexes()`, `Schema::getForeignKeys()`,
etc.).

#### Métodos de Esquema Obsoletos

**Probabilidade de Impacto: Muito Baixa**

Os métodos obsoletos `Schema::getAllTables()`, `Schema::getAllViews()`
e `Schema::getAllTypes()` baseados no Doctrine foram removidos em favor dos
novos métodos nativos do Laravel `Schema::getTables()`, `Schema::getViews()` e
`Schema::getTypes()`.

Ao usar o PostgreSQL e o SQL Server, nenhum dos novos métodos de esquema
aceitará uma referência de três partes (por exemplo,
`banco-de-dados.esquema.tabela`).
Portanto, você deve usar `connection()` para declarar o banco de dados:

```php
Schema::connection('banco-de-dados')->hasTable('esquema.tabela');
```

#### Método `getColumnType()` do Construtor de Esquema

**Probabilidade de Impacto: Muito Baixa**

O método `Schema::getColumnType()` agora sempre retorna o tipo real da coluna
fornecida, não o tipo equivalente do Doctrine DBAL.

#### Interface de Conexão com Banco de Dados

**Probabilidade de Impacto: Muito Baixa**

A interface `Illuminate\Database\ConnectionInterface` recebeu um novo método
`scalar`.
Se você estiver definindo sua implementação desta interface, deverá adicionar o
método `scalar` à sua implementação:

```php
public function scalar($query, $bindings = [], $useReadPdo = true);
```

### Datas

#### Carbon 3

**Probabilidade de Impacto: Média**

O Laravel 11 suporta Carbon 2 e Carbon 3.
Carbon é uma biblioteca de manipulação de datas usada amplamente pelo Laravel e
por pacotes em todo o ecossistema.
Se você instalar o Carbon 3, deverá revisar a
[lista de alterações](https://github.com/briannesbitt/Carbon/releases/tag/3.0.0)
do Carbon.

### Correio

#### O Contrato `Mailer`

**Probabilidade de Impacto: Muito Baixa**

O contrato `Illuminate\Contracts\Mail\Mailer` recebeu um novo método `sendNow`.
Se sua aplicação ou pacote estiver implementando manualmente este contrato, você
deverá adicionar o novo método `sendNow` à sua implementação:

```php
public function sendNow($mailable, array $data = [], $callback = null);
```

### Pacotes

#### Publicando Provedores de Serviços na Aplicação

**Probabilidade de Impacto: Muito Baixa**

Se você escreveu um pacote Laravel que publica manualmente um provedor de
serviços no diretório `app/Providers` da aplicação e modifica manualmente o
arquivo de configuração `config/app.php` da aplicação para registrar o provedor
de serviços, você deve atualizar seu pacote para usar o novo método
`ServiceProvider::addProviderToBootstrapFile`.

O método `addProviderToBootstrapFile` adicionará automaticamente o provedor de
serviços que você publicou ao arquivo `bootstrap/providers.php` da aplicação,
uma vez que o _array_ `providers` não existe no arquivo de configuração
`config/app.php` das novas aplicações Laravel 11.

```php
use Illuminate\Support\ServiceProvider;

ServiceProvider::addProviderToBootstrapFile(Provider::class);
```

### Filas

#### A Interface `BatchRepository`

**Probabilidade de Impacto: Muito Baixa**

A interface `Illuminate\Bus\BatchRepository` recebeu um novo método `rollBack`.
Se você estiver implementando essa interface no seu pacote ou aplicação, deverá
adicionar este método à sua implementação:

```php
public function rollBack();
```

#### Trabalhos Síncronos em Transações de Banco de Dados

**Probabilidade de Impacto: Muito Baixa**

Anteriormente, os trabalhos síncronos (trabalhos que usavam o _driver_ de
fila `sync`) seriam executados imediatamente, independentemente da opção de
configuração `after_commit` da conexão da fila ter sido definida como `true` ou
o método `afterCommit` ter sido invocado no trabalho.

No Laravel 11, os trabalhos de fila síncronos agora respeitarão a configuração
"after commit" da conexão da fila ou do trabalho.

### Limitação de Taxa

#### Limitação de Taxa por Segundo

**Probabilidade de Impacto: Média**

O Laravel 11 suporta limitação de taxa por segundo em vez de ser limitado à
granularidade por minuto.
Há uma variedade de possíveis alterações significativas das quais você deve
estar ciente em relação a essa alteração.

O construtor da classe `GlobalLimit` agora aceita segundos em vez de minutos.
Esta classe não está documentada e normalmente não seria usada pela sua
aplicação:

```php
new GlobalLimit($attempts, 2 * 60);
```

O construtor da classe `Limit` agora aceita segundos em vez de minutos.
Todos os usos documentados desta classe são limitados a construtores estáticos
como `Limit::perMinute` e `Limit::perSecond`.
No entanto, se você estiver instanciando esta classe manualmente, deverá
atualizar sua aplicação para fornecer segundos ao construtor da classe:

```php
new Limit($key, $attempts, 2 * 60);
```

A propriedade `decayMinutes` da classe `Limit` foi renomeada para `decaySeconds`
e agora contém segundos em vez de minutos.

Os construtores das classes `Illuminate\Queue\Middleware\ThrottlesExceptions` e
`Illuminate\Queue\Middleware\ThrottlesExceptionsWithRedis` agora aceitam
segundos em vez de minutos:

```php
new ThrottlesExceptions($attempts, 2 * 60);
new ThrottlesExceptionsWithRedis($attempts, 2 * 60);
```

### Cashier Stripe

#### Atualizando o Cashier Stripe

**Probabilidade de Impacto: Alta**

O Laravel 11 não suporta mais o Cashier Stripe 14.x.
Portanto, você deve atualizar a dependência do Laravel Cashier Stripe da sua
aplicação para `^15.0` no arquivo `composer.json`.

O Cashier Stripe 15.0 não carrega mais as migrações automaticamente do seu
diretório de migrações.
Em vez disso, você deve executar o seguinte comando para publicar as migrações
do Cashier Stripe na sua aplicação:

```shell
php artisan vendor:publish --tag=cashier-migrations
```

Revise o guia completo de
[atualização do Cashier Stripe](https://github.com/laravel/cashier-stripe/blob/15.x/UPGRADE.md)
para saber sobre alterações adicionais.

### Spark (Stripe)

#### Atualizando o Spark Stripe

**Probabilidade de Impacto: Alta**

O Laravel 11 não suporta mais o Laravel Spark Stripe 4.x.
Portanto, você deve atualizar a dependência do Laravel Spark Stripe da sua
aplicação para `^5.0` no arquivo `composer.json`.

O Spark Stripe 5.0 não carrega mais as migrações automaticamente do seu
diretório de migrações.
Em vez disso, você deve executar o seguinte comando para publicar as migrações
do Spark Stripe na sua aplicação:

```shell
php artisan vendor:publish --tag=spark-migrations
```

Revise o guia completo de
[atualização do Spark Stripe](https://spark.laravel.com/docs/spark-stripe/upgrade.html)
para saber sobre alterações adicionais.

### Passport

#### Atualizando o Passport

**Probabilidade de Impacto: Alta**

O Laravel 11 não suporta mais o Laravel Passport 11.x.
Portanto, você deve atualizar a dependência do Laravel Passport da sua aplicação
para `^12.0` no arquivo `composer.json`.

O Passport 12.0 não carrega mais as migrações automaticamente do diretório de
migrações.
Em vez disso, você deve executar o seguinte comando para publicar as migrações
do Passport na sua aplicação:

```shell
php artisan vendor:publish --tag=passport-migrations
```

Além disso, o tipo de concessão com senha está desabilitado por padrão.
Você pode habilitá-lo invocando o método `enablePasswordGrant` no método `boot`
do `AppServiceProvider` da sua aplicação:

```php
public function boot(): void
{
    Passport::enablePasswordGrant();
}
```

### Sanctum

#### Atualizando o Sanctum

**Probabilidade de Impacto: Alta**

O Laravel 11 não suporta mais o Laravel Sanctum 3.x.
Portanto, você deve atualizar a dependência do Laravel Sanctum da sua aplicação
para `^4.0` no arquivo `composer.json`.

O Sanctum 4.0 não carrega mais as migrações automaticamente do diretório de
migrações.
Em vez disso, você deve executar o seguinte comando para publicar as migrações
do Sanctum na sua aplicação:

```shell
php artisan vendor:publish --tag=sanctum-migrations
```

Em seguida, no arquivo de configuração `config/sanctum.php` da sua aplicação,
você deve atualizar as referências aos _middlewares_ `authenticate_session`,
`encrypt_cookies` e `validate_csrf_token` para o seguinte:

```php
'middleware' => [
    'authenticate_session' => Laravel\Sanctum\Http\Middleware\AuthenticateSession::class,
    'encrypt_cookies' => Illuminate\Cookie\Middleware\EncryptCookies::class,
    'validate_csrf_token' => Illuminate\Foundation\Http\Middleware\ValidateCsrfToken::class,
],
```

### Telescope

#### Atualizando o Telescope

**Probabilidade de Impacto: Alta**

O Laravel 11 não suporta mais o Laravel Telescope 4.x.
Portanto, você deve atualizar a dependência do Telescópio Laravel da sua
aplicação para `^5.0` no arquivo `composer.json`.

O Telescope 5.0 não carrega mais as migrações automaticamente do diretório de
migrações.
Em vez disso, você deve executar o seguinte comando para publicar as migrações
do Telescope na sua aplicação:

```shell
php artisan vendor:publish --tag=telescope-migrations
```

### Pacote Spatie Once

**Probabilidade de Impacto: Média**

O Laravel 11 agora fornece sua própria
[função `once`](../helpers.md#method-once) para garantir que uma determinada
_closure_ seja executada apenas uma vez.
Portanto, se sua aplicação depende do pacote `spatie/once`, você deve removê-lo
do arquivo `composer.json` da sua aplicação para evitar conflitos.

### Diversos

Também encorajamos você a visualizar as mudanças no repositório
`laravel/laravel` no [GitHub](https://github.com/laravel/laravel).
Embora muitas dessas alterações não sejam necessárias, você pode querer manter
esses arquivos sincronizados com sua aplicação.
Algumas dessas alterações serão abordadas neste guia de atualização, mas outras,
como alterações nos arquivos de configuração ou comentários, não serão.
Você pode visualizar facilmente as alterações com a
[ferramenta de comparação do GitHub](https://github.com/laravel/laravel/compare/10.x...11.x)
e escolher quais atualizações são importantes para você.
