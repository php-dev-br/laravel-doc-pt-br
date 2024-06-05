<!-- source_url: https://github.com/laravel/docs/blob/11.x/deployment.md -->
<!-- revision: 2a9cd61538f0b9d340d11f178129b090a183bcbd -->
<!-- status: ready -->

# Implantação

- [Introdução](#introducao)
- [Requisitos do Servidor](#requisitos-do-servidor)
- [Configuração do Servidor](#configuracao-do-servidor)
  - [Nginx](#nginx)
  - [FrankenPHP](#frankenphp)
- [Otimização](#otimizacao)
  - [Armazenando Configurações em _Cache_](#armazenando-configuracoes-em-cache)
  - [Armazenando Eventos em _Cache_](#armazenando-eventos-em-cache)
  - [Armazenando Rotas em _Cache_](#armazenando-rotas-em-cache)
  - [Armazenando Visualizações em _Cache_](#armazenando-visualizacoes-em-cache)
- [Modo de Depuração](#modo-de-depuracao)
- [A Rota de Integridade](#a-rota-de-integridade)
- [Implantação Fácil com Forge/Vapor](#implantacao-facil-com-forge-vapor)

## Introdução

Quando sua aplicação Laravel estiver pronta para ser implantada em produção, há
algumas coisas importantes que você pode fazer para garantir que ela seja
executada da maneira mais eficiente possível.
Neste documento, abordaremos alguns excelentes pontos de partida para garantir
que sua aplicação Laravel seja implantada corretamente.

## Requisitos do Servidor

O _framework_ Laravel possui alguns requisitos de sistema.
Você deve garantir que seu servidor _web_ tenha a seguinte versão mínima e
extensões do PHP:

- PHP >= 8.2
- Extensão Ctype
- Extensão cURL
- Extensão DOM
- Extensão Fileinfo
- Extensão Filter
- Extensão Hash
- Extensão Mbstring
- Extensão OpenSSL
- Extensão PCRE
- Extensão PDO
- Extensão Session
- Extensão Tokenizer
- Extensão XML

## Configuração do Servidor

### Nginx

Se você estiver implantando sua aplicação em um servidor que esteja executando
o Nginx, poderá usar o seguinte arquivo de configuração como ponto de partida
para configurar seu servidor _web_.
Muito provavelmente, este arquivo precisará ser personalizado dependendo da
configuração do seu servidor.
**Se você quiser assistência no gerenciamento de seu servidor, considere usar um
serviço de gerenciamento e implantação de servidor do Laravel, como o
[Laravel Forge](https://forge.laravel.com).**

Certifique-se, como na configuração abaixo, de que seu servidor _web_ direciona
todas as requisições para o arquivo `public/index.php` da sua aplicação.
Você nunca deve tentar mover o arquivo `index.php` para a raiz do seu projeto,
pois servir a aplicação a partir da raiz do projeto exporá muitos arquivos de
configuração confidenciais à _internet_ pública:

```nginx
server {
    listen 80;
    listen [::]:80;
    server_name example.com;
    root /srv/example.com/public;

    add_header X-Frame-Options "SAMEORIGIN";
    add_header X-Content-Type-Options "nosniff";

    index index.php;

    charset utf-8;

    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }

    location = /favicon.ico { access_log off; log_not_found off; }
    location = /robots.txt  { access_log off; log_not_found off; }

    error_page 404 /index.php;

    location ~ \.php$ {
        fastcgi_pass unix:/var/run/php/php8.2-fpm.sock;
        fastcgi_param SCRIPT_FILENAME $realpath_root$fastcgi_script_name;
        include fastcgi_params;
    }

    location ~ /\.(?!well-known).* {
        deny all;
    }
}
```

### FrankenPHP

O [FrankenPHP](https://frankenphp.dev/) também pode ser usado para servir suas
aplicações Laravel.
O FrankenPHP é um servidor de aplicações PHP moderno escrito em Go.
Para servir uma aplicação Laravel PHP usando FrankenPHP, você pode simplesmente
invocar o comando `php-server`:

```shell
frankenphp php-server -r public/
```

Para aproveitar os recursos mais poderosos suportados pelo FrankenPHP, como a
integração com o [Laravel Octane](../octane.md), HTTP/3, compressão moderna ou a
capacidade de empacotar aplicações Laravel como binários independentes, consulte
a [documentação do Laravel](https://frankenphp.dev/docs/laravel/) do FrankenPHP.

## Otimização

Ao implantar sua aplicação em produção, há vários arquivos que devem ser
armazenados em _cache_, incluindo configurações, eventos, rotas e visualizações.
O Laravel fornece um único e conveniente comando `optimize` do Artisan que irá
armazenar em _cache_ todos esses arquivos.
Este comando normalmente deve ser invocado como parte do processo de implantação
da sua aplicação:

```shell
php artisan optimize
```

O comando `optimize:clear` pode ser usado para remover todos os arquivos de
_cache_ gerados pelo comando `optimize`:

```shell
php artisan optimize:clear
```

Na documentação a seguir, discutiremos cada um dos comandos de otimização
granular executados pelo comando `optimize`.

### Armazenando Configurações em _Cache_

Ao implantar sua aplicação em produção, você deve se certificar de executar o
comando `config:cache` do Artisan durante o processo de implantação:

```shell
php artisan config:cache
```

Este comando combinará todos os arquivos de configuração do Laravel em um único
arquivo em _cache_, o que reduz bastante o número de viagens que o _framework_
deve fazer ao sistema de arquivos ao carregar seus valores de configuração.

> **Aviso:**
> Se você executar o comando `config:cache` durante o processo de implantação,
> certifique-se de estar chamando a função `env` apenas de dentro dos seus
> arquivos de configuração.
> Depois que a configuração for armazenada em _cache_, o arquivo `.env` não será
> carregado e todas as chamadas à função `env` para variáveis `.env` retornarão
> `null`.

### Armazenando Eventos em _Cache_

Durante a implantação você deve armazenar em _cache_ os mapeamentos de eventos
para ouvintes descobertos automaticamente na sua aplicação.
Isso pode ser feito invocando o comando `event:cache` do Artisan:

```shell
php artisan event:cache
```

### Armazenando Rotas em _Cache_

Se você estiver construindo uma aplicação grande com muitas rotas, certifique-se
de executar o comando `route:cache` do Artisan durante o processo de
implantação:

```shell
php artisan route:cache
```

Este comando reduz todos os seus registros de rota a uma única chamada de
método em um arquivo em _cache_, melhorando o desempenho do registro de
rota ao registrar centenas de rotas.

### Armazenando Visualizações em _Cache_

Ao implantar sua aplicação em produção, você deve certificar-se de executar o
comando `view:cache` do Artisan durante o processo de implantação:

```shell
php artisan view:cache
```

Este comando pré-compila todas as visualizações do Blade para não serem
compiladas sob demanda, melhorando o desempenho de cada requisição que retorna
uma visualização.

## Modo de Depuração

A opção `debug` no arquivo de configuração `config/app.php` determina quanta
informação sobre um erro será exibida à pessoa usuária.
Por padrão, esta opção é definida para respeitar o valor da variável de ambiente
`APP_DEBUG`, que está armazenada no arquivo `.env` da sua aplicação.

> **Aviso:**
> **No seu ambiente de produção, esse valor deve ser sempre `false`.
> Se a variável for definida como `true` em produção, você corre o risco de
> expor valores de configuração confidenciais às pessoas usuárias da sua
> aplicação.**

## A Rota de Integridade

O Laravel inclui uma rota integrada de verificação de saúde que pode ser usada
para monitorar o _status_ da sua aplicação.
Em produção, essa rota pode ser usada para reportar o _status_ da sua aplicação
a um monitor de tempo de atividade, balanceador de carga ou sistema de
orquestração, como o Kubernetes.

Por padrão, a rota de verificação de integridade é servida em `/up` e retornará
uma resposta HTTP `200` se a aplicação tiver sido inicializada sem exceções.
Caso contrário, uma resposta HTTP `500` será retornada.
Você pode configurar o URI para esta rota no arquivo `bootstrap/app.php` da sua
aplicação:

```php
->withRouting(
    web: __DIR__.'/../routes/web.php',
    commands: __DIR__.'/../routes/console.php',
-    health: '/up',
+    health: '/status',
)
```

Quando requisições HTTP forem feitas a esta rota, o Laravel também despachará
um evento `Illuminate\Foundation\Events\DiagnosingHealth`, permitindo que você
execute verificações de saúde adicionais relevantes para sua aplicação.
Em um [ouvinte](../events.md) para este evento, você pode verificar o banco de
dados ou o _status_ do _cache_ da sua aplicação.
Se você detectar um problema com a sua aplicação, poderá simplesmente lançar uma
exceção a partir do ouvinte.

<a name="implantacao-facil-com-forge-vapor"></a>

## Implantação Fácil com Forge/Vapor

#### Laravel Forge

Se você ainda não consegue gerenciar a configuração do seu próprio servidor ou
não se sente confortável em configurar todos os vários serviços necessários para
executar uma aplicação Laravel robusta, o
[Laravel Forge](https://forge.laravel.com) é uma alternativa maravilhosa.

O Laravel Forge pode criar servidores em vários provedores de infraestrutura,
como DigitalOcean, Linode, AWS e muito mais.
Além disso, o Forge instala e gerencia todas as ferramentas necessárias para
construir aplicações Laravel robustas, como Nginx, MySQL, Redis, Memcached,
Beanstalk e muito mais.

> **Nota:**
> Quer um guia completo para implantação com o Laravel Forge?
> Confira o [Laravel Bootcamp](https://bootcamp.laravel.com/deploying) e a
> [série de vídeos sobre o Forge disponível no Laracasts](https://laracasts.com/series/learn-laravel-forge-2022-edition).

#### Laravel Vapor

Se você deseja uma plataforma de implantação totalmente sem servidor e com
escalonamento automático ajustada para o Laravel, dê uma olhada no
[Laravel Vapor](https://vapor.laravel.com).
O Laravel Vapor é uma plataforma de implantação _serverless_ para Laravel,
mantida pela AWS.
Lance sua infraestrutura Laravel no Vapor e apaixone-se pela simplicidade
escalável do _serverless_.
O Laravel Vapor foi ajustado pelos criadores do Laravel para funcionar
perfeitamente com o _framework_, para que você possa continuar escrevendo suas
aplicações Laravel exatamente como você costuma fazer.
