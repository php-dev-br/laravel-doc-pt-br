---
source_url: https://github.com/laravel/docs/blob/11.x/configuration.md
revision: 5208fb2976c6ee1b7a3c2e69de3ebf72e28e3658
status: ready
---

# Configuração

- [Introdução](#introducao)
- [Configuração do Ambiente](#configuracao-do-ambiente)
    - [Tipos de Variáveis de Ambiente](#tipos-de-variaveis-de-ambiente)
    - [Recuperando a Configuração de Ambiente](#recuperando-a-configuracao-de-ambiente)
    - [Determinando o Ambiente Atual](#determinando-o-ambiente-atual)
    - [Encriptando Arquivos de Ambiente](#encriptando-arquivos-de-ambiente)
- [Acessando Valores de Configuração](#acessando-valores-de-configuracao)
- [_Cache_ de Configuração](#cache-de-configuracao)
- [Publicação de Configuração](#publicacao-de-configuracao)
- [Modo de Depuração](#modo-de-depuracao)
- [Modo de Manutenção](#modo-de-manutencao)

## Introdução

Todos os arquivos de configuração do _framework_ Laravel são armazenados no
diretório `config`.
Cada opção está documentada, então fique à vontade para examinar os arquivos e
se familiarizar com as opções disponíveis.

Esses arquivos de configuração permitem que você configure coisas como
informações de conexão do banco de dados, informações do servidor de e-mail, bem
como várias outras configurações importantes, como fuso horário da aplicação e
chave de encriptação.

#### O Comando `about`

O Laravel pode exibir uma visão geral da configuração, _drivers_ e ambiente da
sua aplicação através do comando `about` do Artisan.

```shell
php artisan about
```

Se você tiver interesse apenas em uma seção específica da saída da visão geral
da aplicação, poderá filtrar essa seção usando a opção `--only`:

```shell
php artisan about --only=environment
```

Ou, para explorar detalhadamente os valores de um arquivo de configuração
específico, você pode usar o comando `config:show` do Artisan:

```shell
php artisan config:show database
```

## Configuração do Ambiente

Muitas vezes é útil ter valores de configuração diferentes com base no ambiente
onde a aplicação está sendo executada.
Por exemplo, você pode querer usar localmente um _driver_ de cache diferente
daquele usado em seu servidor de produção.

Para tornar isso mais fácil, o Laravel usa a biblioteca PHP
[DotEnv](https://github.com/vlucas/phpdotenv).
Em uma nova instalação do Laravel, o diretório raiz da sua aplicação conterá um
arquivo `.env.example` que define muitas variáveis de ambiente comuns.
Durante o processo de instalação do Laravel, este arquivo será copiado
automaticamente para `.env`.

O arquivo `.env` padrão do Laravel contém alguns valores de configuração comuns
que podem ser diferentes dependendo se sua aplicação está sendo executada
localmente ou em um servidor _web_ de produção.
Esses valores são então lidos pelos arquivos de configuração dentro do diretório
`config` usando a função `env` do Laravel.

Se você estiver desenvolvendo com uma equipe, talvez queira continuar incluindo
e atualizando o arquivo `.env.example` com sua aplicação.
Ao colocar valores de sugestão no arquivo de configuração de exemplo, outras
pessoas da sua equipe podem ver claramente quais variáveis de ambiente são
necessárias para executar sua aplicação.

> **Nota:**
> Qualquer variável no arquivo `.env` pode ser substituída por variáveis de
> ambiente externas, como variáveis de ambiente de nível de servidor ou de
> sistema.

#### Segurança dos Arquivos de Ambiente

O arquivo `.env` não deve ser enviado para o controle de versão da sua
aplicação, pois cada pessoa desenvolvedora ou servidor que usa sua aplicação
pode exigir uma configuração de ambiente diferente.
Além disso, isso seria um risco de segurança caso alguém sem autorização
obtivesse acesso ao repositório do seu controle de versão, uma vez que quaisquer
credenciais confidenciais seriam expostas.

No entanto, é possível encriptar o arquivo de ambiente usando a
[encriptação de ambiente](#encriptando-arquivos-de-ambiente) integrada do
Laravel.
Arquivos de ambiente encriptados podem ser colocados no controle de versão com
segurança.

#### Arquivos de Ambiente Adicionais

Antes de carregar as variáveis de ambiente da sua aplicação, o Laravel determina
se uma variável de ambiente `APP_ENV` foi fornecida externamente ou se o
argumento CLI `--env` foi especificado.
Nesse caso, o Laravel tentará carregar um arquivo `.env.[APP_ENV]`, se ele
existir.
Caso não exista, o arquivo `.env` padrão será carregado.

### Tipos de Variáveis de Ambiente

Todas as variáveis nos arquivos `.env` são normalmente analisadas como strings,
portanto, alguns valores reservados foram criados para permitir que você retorne
uma gama mais ampla de tipos da função `env()`:

| Valor `.env` | Valor `env()` |
|--------------|---------------|
| true         | (bool) true   |
| (true)       | (bool) true   |
| false        | (bool) false  |
| (false)      | (bool) false  |
| empty        | (string) ''   |
| (empty)      | (string) ''   |
| null         | (null) null   |
| (null)       | (null) null   |

Se você precisar definir uma variável de ambiente com um valor que contenha
espaços, poderá fazê-lo colocando o valor entre aspas duplas:

```ini
APP_NAME = "Minha Aplicação"
```

### Recuperando a Configuração de Ambiente

Todas as variáveis listadas no arquivo `.env` serão carregadas na variável
`$_ENV` do PHP quando sua aplicação receber uma requisição.
No entanto, você pode usar a função `env` para recuperar valores dessas
variáveis nos arquivos de configuração.
Na verdade, se você revisar os arquivos de configuração do Laravel, notará que
muitas das opções já usam esta função:

```php
'debug' => env('APP_DEBUG', false),
```

O segundo valor passado para a função `env` é o valor padrão.
Este valor será retornado se não existir nenhuma variável de ambiente para a
chave fornecida.

### Determinando o Ambiente Atual

O ambiente atual da aplicação é determinado pela variável `APP_ENV` do arquivo
`.env`.
Você pode acessar esse valor através do método `environment` da
[fachada](../facades.md) `App`:

```php
use Illuminate\Support\Facades\App;

$environment = App::environment();
```

Você também pode passar argumentos para o método `environment` para determinar
se o ambiente corresponde a um determinado valor.
O método retornará `true` se o ambiente corresponder a qualquer um dos valores
fornecidos:

```php
if (App::environment('local')) {
    // O ambiente é local
}

if (App::environment(['local', 'staging'])) {
    // O ambiente é local OU staging...
}
```

> **Nota:**
> A detecção do ambiente de aplicação atual pode ser substituída definindo uma
> variável de ambiente `APP_ENV` no nível do servidor.

### Encriptando Arquivos de Ambiente

Arquivos de ambiente não encriptados nunca devem ser armazenados no controle de
versão.
No entanto, o Laravel permite encriptar os arquivos do seu ambiente para que
eles possam ser adicionados com segurança ao controle de versão com o resto da
sua aplicação.

#### Encriptação

Para encriptar um arquivo de ambiente, você pode usar o comando `env:encrypt`:

```shell
php artisan env:encrypt
```

A execução do comando `env:encrypt` criptografará o arquivo `.env` e colocará o
conteúdo criptografado em um arquivo `.env.encrypted`.
A chave de desencriptação é apresentada na saída do comando e deve ser
armazenada em um gerenciador de senhas seguro.
Se desejar fornecer sua própria chave de encriptação, você pode usar a opção
`--key` ao invocar o comando:

```shell
php artisan env:encrypt --key=3UVsEgGVK36XN82KKeyLFMhvosbZN1aF
```

> **Nota:**
> O comprimento da chave fornecida deve corresponder ao comprimento da chave
> exigido pela cifra de encriptação usada.
> Por padrão, o Laravel usará a cifra `AES-256-CBC` que requer uma chave de 32
> caracteres.
> Você está livre para usar qualquer cifra suportada pelo
> [encriptador](../encryption.md) do Laravel, passando a opção `--cipher` ao
> invocar o comando.

Se sua aplicação tiver vários arquivos de ambiente, como `.env` e
`.env.staging`, você poderá especificar o arquivo de ambiente que deve ser
encriptado fornecendo o nome do ambiente por meio da opção `--env`:

```shell
php artisan env:encrypt --env=staging
```

#### Desencriptação

Para desencriptar um arquivo de ambiente, você pode usar o comando
`env:decrypt`.
Este comando requer uma chave de desencriptação, que o Laravel irá recuperar da
variável de ambiente `LARAVEL_ENV_ENCRYPTION_KEY`:

```shell
php artisan env:decrypt
```

Ou a chave pode ser fornecida diretamente ao comando por meio da opção `--key`:

```shell
php artisan env:decrypt --key=3UVsEgGVK36XN82KKeyLFMhvosbZN1aF
```

Quando o comando `env:decrypt` for invocado, o Laravel irá desencriptar o
conteúdo do arquivo `.env.encrypted` e colocar o conteúdo desencriptado no
arquivo `.env`.

A opção `--cipher` pode ser fornecida ao comando `env:decrypt` para usar uma
cifra de desencriptação personalizada:

```shell
php artisan env:decrypt --key=qUWuNRdfuImXcKxZ --cipher=AES-128-CBC
```

Se sua aplicação tiver vários arquivos de ambiente, como `.env` e
`.env.staging`, você poderá especificar o arquivo de ambiente que deve ser
desencriptado fornecendo o nome do ambiente por meio da opção `--env`:

```shell
php artisan env:decrypt --env=staging
```

Para substituir um arquivo de ambiente existente, você pode fornecer a opção
`--force` ao comando `env:decrypt`:

```shell
php artisan env:decrypt --force
```

## Acessando Valores de Configuração

Você pode acessar facilmente os valores de configuração usando a fachada
`Config` ou a função global `config` de qualquer lugar na sua aplicação.
Os valores de configuração podem ser acessados usando a sintaxe de ponto, que
inclui o nome do arquivo e a opção que deseja acessar.
Um valor padrão também pode ser especificado e será retornado se a opção de
configuração não existir:

```php
use Illuminate\Support\Facades\Config;

$value = Config::get('app.timezone');

$value = config('app.timezone');

// Recupera um valor padrão se o valor da configuração não existir...
$value = config('app.timezone', 'Asia/Seoul');
```

Para definir valores de configuração em tempo de execução, você pode invocar o
método `set` da fachada `Config` ou passar um _array_ para a função `config`:

```php
Config::set('app.timezone', 'America/Chicago');

config(['app.timezone' => 'America/Chicago']);
```

Para auxiliar na análise estática, a fachada `Config` também fornece métodos de
recuperação de configuração tipados.
Se o valor de configuração recuperado não corresponder ao tipo esperado, uma
exceção será lançada:

```php
Config::string('chave-de-configuracao');
Config::integer('chave-de-configuracao');
Config::float('chave-de-configuracao');
Config::boolean('chave-de-configuracao');
Config::array('chave-de-configuracao');
```

## _Cache_ de Configuração

Para aumentar a velocidade da sua aplicação, você deve armazenar em _cache_
todos os arquivos de configuração em um único arquivo usando o comando
`config:cache` do Artisan.
Isso combinará todas as opções de configuração da sua aplicação em um único
arquivo que pode ser carregado rapidamente pelo _framework_.

Normalmente, você deve executar o comando `php artisan config:cache` como parte
do seu processo de implantação em produção.
O comando não deve ser executado durante o desenvolvimento local, pois as opções
de configuração precisarão ser alteradas frequentemente durante o
desenvolvimento da sua aplicação.

Depois que a configuração for armazenada em _cache_, o arquivo `.env` da sua
aplicação não será carregado pelo _framework_ durante requisições ou comandos do
Artisan; portanto, a função `env` retornará apenas variáveis de ambiente
externas no nível do sistema.

Por esse motivo, você deve garantir que está chamando a função `env` apenas de
dentro dos arquivos de configuração (`config`) da sua aplicação.
Você pode ver muitos exemplos disso examinando os arquivos de configuração
padrão do Laravel.
Os valores de configuração podem ser acessados de qualquer lugar na sua
aplicação usando a função `config`
[descrita acima](#acessando-valores-de-configuracao).

O comando `config:clear` pode ser usado para remover a configuração em _cache_:

```shell
php artisan config:clear
```

> **Aviso:**
> Se você executar o comando `config:cache` durante o processo de implantação,
> certifique-se de estar chamando a função `env` apenas de dentro dos seus
> arquivos de configuração.
> Depois que a configuração for armazenada em _cache_, o arquivo `.env` não será
> carregado; portanto, a função `env` retornará apenas variáveis de ambiente
> externas no nível do sistema.

## Publicação de Configuração

A maioria dos arquivos de configuração do Laravel já estão publicados no
diretório `config` da sua aplicação; entretanto, certos arquivos de
configuração como `cors.php` e `view.php` não são publicados por padrão, pois a
maioria das aplicações nunca precisará modificá-los.

No entanto, você pode usar o comando `config:publish` do Artisan para publicar
qualquer arquivo de configuração que não seja publicado por padrão:

```shell
php artisan config:publish

php artisan config:publish --all
```

## Modo de Depuração

A opção `debug` no arquivo de configuração `config/app.php` determina quanta
informação sobre um erro será exibida à pessoa usuária.
Por padrão, esta opção é definida para respeitar o valor da variável de ambiente
`APP_DEBUG`, que está armazenada no arquivo `.env`.

> **Aviso:**
> Para desenvolvimento local, você deve definir a variável de ambiente
> `APP_DEBUG` como `true`.
> **No seu ambiente de produção, esse valor deve ser sempre `false`.
> Se a variável for definida como `true` em produção, você corre o risco de
> expor valores de configuração confidenciais às pessoas usuárias da sua
> aplicação.**

## Modo de Manutenção

Quando sua aplicação estiver em modo de manutenção, uma visualização
personalizada será exibida para todas as requisições da aplicação.
Isso facilita desabilitar a aplicação durante a atualização ou durante a
manutenção.
Uma verificação do modo de manutenção está incluída na _stack middleware_ padrão
da sua aplicação.
Se a aplicação estiver em modo de manutenção, uma instância
`Symfony\Component\HttpKernel\Exception\HttpException` será lançada com um
código de _status_ `503`.

Para habilitar o modo de manutenção, execute o comando `down` do Artisan:

```shell
php artisan down
```

Se desejar que o cabeçalho HTTP `Refresh` seja enviado com todas as respostas do
modo de manutenção, você pode fornecer a opção `refresh` ao invocar o comando
`down`.
O cabeçalho `Refresh` instruirá o navegador a atualizar automaticamente a página
após o número especificado de segundos:

```shell
php artisan down --refresh=15
```

Você também pode fornecer uma opção `retry` ao comando `down`, que será definida
como o valor do cabeçalho HTTP `Retry-After`, embora os navegadores geralmente
ignorem este cabeçalho:

```shell
php artisan down --retry=60
```

#### Ignorando o Modo de Manutenção

Para permitir que o modo de manutenção seja ignorado usando um _token_ secreto,
você pode usar a opção `secret` para especificar um _token_ de desvio do modo de
manutenção:

```shell
php artisan down --secret="1630542a-246b-4b66-afa1-dd72a4c43515"
```

Após colocar a aplicação em modo de manutenção, você pode navegar até a URL da
aplicação correspondente a este _token_ e o Laravel emitirá um _cookie_ de
desvio do modo de manutenção para o seu navegador:

```shell
https://example.com/1630542a-246b-4b66-afa1-dd72a4c43515
```

Se quiser que o Laravel gere o _token_ secreto para você, você pode usar a opção
`with-secret`.
O segredo será exibido quando a aplicação estiver em modo de manutenção:

```shell
php artisan down --with-secret
```

Ao acessar esta rota oculta, o navegador então será redirecionado para a rota
`/` da aplicação.
Uma vez emitido o _cookie_ para o seu navegador, você poderá navegar na
aplicação normalmente como se ela não estivesse em modo de manutenção.

> **Nota:**
> Seu segredo do modo de manutenção normalmente deve consistir em caracteres
> alfanuméricos e, opcionalmente, traços.
> Você deve evitar usar caracteres com significado especial em URLs como `?` ou
> `&`.

#### Modo de Manutenção em Vários Servidores

Por padrão, o Laravel determina se sua aplicação está em modo de manutenção
usando um sistema baseado em arquivos.
Isso significa que para ativar o modo de manutenção, o comando
`php artisan down` deve ser executado em cada servidor que hospeda sua
aplicação.

Alternativamente, o Laravel oferece um método baseado em _cache_ para lidar com
o modo de manutenção.
Este método requer a execução do comando `php artisan down` em apenas um
servidor.
Para usar essa abordagem, modifique a configuração `driver` no arquivo
`config/app.php` da sua aplicação para `cache`.
Em seguida, selecione um `store` de _cache_ que seja acessível a todos os seus
servidores.
Isso garante que o _status_ do modo de manutenção seja mantido de forma
consistente em todos os servidores:

```php
'maintenance' => [
    'driver' => 'cache',
    'store' => 'database',
],
```

#### Pré-renderizando a Visualização do Modo de Manutenção

Se você usar o comando `php artisan down` durante a implantação, as pessoas
usuárias ainda poderão ocasionalmente encontrar erros se acessarem a aplicação
enquanto as dependências do Composer ou outros componentes de infraestrutura
estiverem sendo atualizados.
Isso ocorre porque uma parte significativa do _framework_ Laravel deve
inicializar para determinar se sua aplicação está em modo de manutenção e
renderizar a visualização do modo de manutenção usando o mecanismo de
_templating_.

Por esse motivo, o Laravel permite pré-renderizar uma visualização do modo de
manutenção que será retornada logo no início do ciclo da requisição.
Essa visualização é renderizada antes do carregamento de qualquer dependência da
sua aplicação.
Você pode pré-renderizar um _template_ de sua escolha usando a opção `render` do
comando `down`:

```shell
php artisan down --render="errors::503"
```

#### Redirecionando Requisições no Modo de Manutenção

Enquanto estiver em modo de manutenção, o Laravel exibirá a visualização do modo
de manutenção para todas as URLs da aplicação que a pessoa usuária tentar
acessar.
Se desejar, você pode instruir o Laravel a redirecionar todas as requisições
para uma URL específica.
Isso pode ser feito usando a opção `redirect`.
Por exemplo, você pode querer redirecionar todas as requisições para o URI `/`:

```shell
php artisan down --redirect=/
```

#### Desabilitando o Modo de Manutenção

Para desabilitar o modo de manutenção, use o comando `up`:

```shell
php artisan up
```

> **Nota:**
> Você pode personalizar o _template_ padrão do modo de manutenção definindo seu
> próprio _template_ em `resources/views/errors/503.blade.php`.

#### Modo de Manutenção e Filas

Enquanto sua aplicação estiver em modo de manutenção, nenhum
[trabalho na fila](../queues.md) será tratado.
Os trabalhos continuarão a ser tratados normalmente quando a aplicação sair do
modo de manutenção.

#### Alternativas ao Modo de Manutenção

Como o modo de manutenção exige que sua aplicação tenha vários segundos de
inatividade, considere alternativas como o
[Laravel Vapor](https://vapor.laravel.com) e o [Envoyer](https://envoyer.io)
para realizar uma implantação com tempo de inatividade zero com o Laravel.
