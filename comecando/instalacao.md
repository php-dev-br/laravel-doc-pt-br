<!-- source_url: https://github.com/laravel/docs/blob/11.x/installation.md -->
<!-- revision: 5f49bc6610d86ab145d99407df04d94c0c83a52d -->
<!-- status: ready -->

# Instalação

- [Conheça o Laravel](#conheca-o-laravel)
  - [Por que Laravel?](#por-que-laravel)
- [Criando um Projeto Laravel](#criando-um-projeto-laravel)
- [Configuração Inicial](#configuracao-inicial)
  - [Configuração Baseada em Ambiente](#configuracao-baseada-em-ambiente)
  - [Bancos de Dados e Migrações](#bancos-de-dados-e-migracoes)
  - [Configuração de Diretório](#configuracao-de-diretorio)
- [Instalação do Docker Usando o Sail](#instalacao-do-docker-usando-o-sail)
  - [Sail no macOS](#sail-no-macos)
  - [Sail no Windows](#sail-no-windows)
  - [Sail no Linux](#sail-no-linux)
  - [Escolhendo Seus Serviços Sail](#escolhendo-seus-servicos-sail)
- [Suporte à IDE](#suporte-a-ide)
- [Próximos Passos](#proximos-passos)
  - [Laravel, o _Framework Full-Stack_](#laravel-o-framework-full-stack)
  - [Laravel, o _Backend_ de API](#laravel-o-backend-de-api)

## Conheça o Laravel

Laravel é um _framework_ de aplicação _web_ com sintaxe expressiva e elegante.
Um _framework web_ fornece uma estrutura e um ponto de partida para a criação da
sua aplicação, permitindo que você se concentre na criação de algo incrível
enquanto nos preocupamos com os detalhes.

O Laravel se esforça para fornecer uma experiência incrível à pessoa
desenvolvedora, enquanto fornece recursos poderosos, como injeção completa de
dependência, uma camada expressiva de abstração de banco de dados, filas e
trabalhos agendados, testes unitários e de integração e muito mais.

Quer você seja uma pessoa nova em _frameworks web_ PHP ou tenha anos de
experiência, o Laravel é um _framework_ que pode crescer com você.
Nós te ajudaremos a dar os primeiros passos como pessoa desenvolvedora _web_ ou
te daremos um impulso à medida que você leva seu conhecimento para o próximo
nível.
Mal podemos esperar para ver o que você construirá.

> **Nota:**
> Começando agora com o Laravel?
> Confira o [Bootcamp Laravel](https://bootcamp.laravel.com) para uma jornada
> prática pelo _framework_ enquanto orientamos você na construção da sua
> primeira aplicação Laravel.

### Por que Laravel?

Há uma variedade de ferramentas e _frameworks_ disponíveis para você construir
uma aplicação _web_.
No entanto, acreditamos que o Laravel é a melhor escolha para construir
aplicações _web_ modernas e completas.

#### Um _Framework_ Progressivo

Gostamos de chamar o Laravel de _framework_ "progressivo".
Com isso queremos dizer que o Laravel cresce com você.
Se você está apenas dando os primeiros passos no desenvolvimento _web_, a vasta
biblioteca de documentação, guias e [tutoriais em vídeo](https://laracasts.com)
do Laravel te ajudará a aprender o básico sem se sobrecarregar.

Se você é uma pessoa desenvolvedora sênior, o Laravel oferece ferramentas
robustas para [injeção de dependência](../container.md),
[testes unitários](../testing.md), [filas](../queues.md),
[eventos em tempo real](../broadcasting.md), e muito mais.
O Laravel vem ajustado para construir aplicações _web_ profissionais e está
pronto para lidar com cargas de trabalho empresariais.

#### Um _Framework_ Escalável

O Laravel é incrivelmente escalável.
Graças à natureza amigável ao escalonamento do PHP e ao suporte integrado do
Laravel para sistemas de _cache_ distribuídos e rápidos como o Redis, o
escalonamento horizontal com o Laravel é muito fácil.
Na verdade, as aplicações Laravel têm sido facilmente dimensionadas para lidar
com centenas de milhões de requisições mensais.

Precisa de dimensionamento extremo?
Plataformas como o [Laravel Vapor](https://vapor.laravel.com) permitem que você
execute sua aplicação Laravel em escala quase ilimitada na mais recente
tecnologia _serverless_ da AWS.

#### Um _Framework_ da Comunidade

O Laravel combina os melhores pacotes do ecossistema PHP para oferecer o
_framework_ mais robusto e amigável disponível para a pessoa desenvolvedora.
Além disso, milhares de pessoas desenvolvedoras talentosas de todo o mundo
[contribuíram para o _framework_](https://github.com/laravel/framework).
Quem sabe você até se tornará uma pessoa contribuidora do Laravel.

## Criando um Projeto Laravel

Antes de criar seu primeiro projeto Laravel, certifique-se de que sua máquina
local tenha o PHP e o [Composer](https://getcomposer.org) instalados.
Se você estiver desenvolvendo no macOS ou Windows, o PHP e o Composer podem ser
instalados em minutos usando o [Laravel Herd](https://herd.laravel.com).
Além disso, recomendamos a [instalação do Node e do NPM](https://nodejs.org).

Após instalar o PHP e o Composer, você pode criar um novo projeto Laravel usando
o comando `create-project` do Composer:

```shell
composer create-project laravel/laravel app-exemplo
```

Ou você pode criar novos projetos Laravel instalando globalmente o
[instalador do Laravel](https://github.com/laravel/installer) usando o Composer:

```shell
composer global require laravel/installer

laravel new app-exemplo
```

Assim que o projeto for criado, inicie o servidor de desenvolvimento local do
Laravel usando o comando `serve` do Laravel Artisan:

```shell
cd app-exemplo

php artisan serve
```

Após iniciar o servidor de desenvolvimento Artisan, sua aplicação estará
acessível no seu navegador em [http://localhost:8000](http://localhost:8000).
A seguir, você já pode
[começar a dar os próximos passos no ecossistema Laravel](#proximos-passos).
Claro, você também pode querer
[configurar um banco de dados](#bancos-de-dados-e-migracoes).

> **Nota:**
> Se você quiser sair na frente no desenvolvimento da sua aplicação Laravel,
> confira um dos nossos [pacotes oficiais para iniciantes](../starter-kits.md).
> Os pacotes para iniciantes do Laravel fornecem estruturas de autenticação de
> _backend_ e _frontend_ para sua nova aplicação Laravel.

## Configuração Inicial

Todos os arquivos de configuração do _framework_ Laravel são armazenados no
diretório `config`.
Cada opção está documentada, então fique à vontade para examinar os arquivos e
se familiarizar com as opções disponíveis.

O Laravel quase não precisa de configuração adicional imediata.
Você está livre para começar a desenvolver!
No entanto, você pode querer revisar o arquivo `config/app.php` e sua
documentação.
Ele contém várias opções, como `timezone` e `locale`, que você pode alterar de
acordo com sua aplicação.

### Configuração Baseada em Ambiente

Como muitos dos valores das opções de configuração do Laravel podem variar
dependendo se a sua aplicação está sendo executada na sua máquina local ou em um
servidor _web_ de produção, muitos valores de configuração importantes são
definidos usando o arquivo `.env` que existe na raiz da sua aplicação.

Seu arquivo `.env` não deve ser enviado para o controle de versão da sua
aplicação, pois cada pessoa desenvolvedora e servidor que usa sua aplicação pode
exigir uma configuração de ambiente diferente.
Além disso, isso seria um risco de segurança caso alguém sem autorização
obtivesse acesso ao repositório do seu controle de versão, uma vez que quaisquer
credenciais confidenciais seriam expostas.

> **Nota:**
> Para obter mais informações sobre o arquivo `.env` e a configuração baseada em
> ambiente, verifique a
> [documentação completa de configuração](../configuration.md#environment-configuration).

### Bancos de Dados e Migrações

Agora que você criou sua aplicação Laravel, provavelmente deseja usar um banco
de dados.
Por padrão, o arquivo de configuração `.env` da sua aplicação especifica que o
Laravel irá interagir com um banco de dados SQLite.

Durante a criação do projeto, o Laravel criou um arquivo
`database/database.sqlite` para você, e executou as migrações necessárias para
criar as tabelas do banco de dados da aplicação.

Se preferir usar outro _driver_ de banco de dados, como MySQL ou PostgreSQL,
você pode atualizar seu arquivo de configuração `.env` para usar o banco de
dados apropriado.
Por exemplo, se você deseja usar o MySQL, atualize as variáveis `DB_*` do seu
arquivo de configuração `.env` assim:

```ini
DB_CONNECTION = mysql
DB_HOST = 127.0.0.1
DB_PORT = 3306
DB_DATABASE = laravel
DB_USERNAME = root
DB_PASSWORD =
```

Caso opte por usar um banco de dados diferente do SQLite, você precisará criar o
banco de dados e executar as [migrações de banco de dados](../migrations.md) da
sua aplicação:

```shell
php artisan migrate
```

> **Nota:**
> Se você estiver desenvolvendo no macOS e precisar instalar o MySQL, PostgreSQL
> ou Redis localmente, confira o [DBngin](https://dbngin.com/).

### Configuração de Diretório

O Laravel deve sempre ser servido a partir da raiz do "diretório _web_"
configurado para o seu servidor _web_.
Você não deve tentar servir uma aplicação Laravel a partir de um subdiretório
do "diretório _web_".
Tentar fazer isso pode expor arquivos confidenciais presentes na sua aplicação.

## Instalação do Docker Usando o Sail

Queremos que seja o mais fácil possível começar a usar o Laravel,
independentemente do seu sistema operacional preferido.
Portanto, há várias opções para desenvolver e executar um projeto Laravel na sua
máquina local.
Embora você possa querer explorar essas opções posteriormente, o Laravel fornece
o [Sail](../sail.md), uma solução integrada para executar seu projeto Laravel
usando o [Docker](https://www.docker.com).

O Docker é uma ferramenta para executar aplicações e serviços em "contêineres"
pequenos e leves que não interferem no software instalado ou na configuração da
sua máquina local.
Isso significa que você não precisa se preocupar em instalar ou configurar
ferramentas de desenvolvimento complicadas, como servidores _web_ e bancos de
dados na sua máquina local.
Para começar, você só precisa instalar o
[Docker Desktop](https://www.docker.com/products/docker-desktop).

O Laravel Sail é uma interface de linha de comando leve para interagir com a
configuração padrão do Docker do Laravel.
O Sail fornece um excelente ponto de partida para construir uma aplicação
Laravel usando PHP, MySQL e Redis sem exigir experiência prévia em Docker.

> **Nota:**
> Já é um especialista em Docker?
> Não se preocupe!
> Tudo sobre o Sail pode ser personalizado usando o arquivo `docker-compose.yml`
> incluído no Laravel.

### Sail no macOS

Se você estiver desenvolvendo em um Mac e o
[Docker Desktop](https://www.docker.com/products/docker-desktop) já estiver
instalado, você pode usar um simples comando de terminal para criar um novo
projeto Laravel.
Por exemplo, para criar uma nova aplicação Laravel em um diretório chamado
`app-exemplo`, você pode executar o seguinte comando no seu terminal:

```shell
curl -s https://laravel.build/app-exemplo | bash
```

Claro, você pode alterar `app-exemplo` nesta URL para o que quiser - apenas se
certifique de que o nome da aplicação contenha apenas caracteres alfanuméricos,
hífens e sublinhados.
O diretório da aplicação Laravel será criado dentro do diretório a partir do
qual você executar o comando.

A instalação do Sail pode levar vários minutos enquanto os contêineres da
aplicação Sail são construídos na sua máquina local.

Após a criação do projeto, você pode navegar até o diretório da aplicação e
iniciar o Laravel Sail.
O Laravel Sail fornece uma interface de linha de comando simples para interagir
com a configuração padrão do Docker do Laravel:

```shell
cd app-exemplo

./vendor/bin/sail up
```

Depois que os contêineres Docker da aplicação forem iniciados, você deverá
executar as [migrações de banco de dados](../migrations.md) da sua aplicação:

```shell
./vendor/bin/sail artisan migrate
```

Por fim, você pode acessar a aplicação no seu navegador em:
[http://localhost](http://localhost).

> **Nota:**
> Para continuar aprendendo mais sobre o Laravel Sail, revise sua
> [documentação completa](../sail.md).

### Sail no Windows

Antes de criarmos uma nova aplicação Laravel na sua máquina Windows,
certifique-se de instalar o
[Docker Desktop](https://www.docker.com/products/docker-desktop).
Em seguida, você deve garantir que o Windows Subsystem for Linux 2 (WSL2) esteja
instalado e habilitado.
O WSL permite que você execute binários do Linux nativamente no Windows 10.
Informações sobre como instalar e habilitar o WSL2 podem ser encontradas na
[documentação do ambiente de desenvolvedor da Microsoft](https://docs.microsoft.com/en-us/windows/wsl/install-win10).

> **Nota:**
> Após instalar e habilitar o WSL2, você deve garantir que o Docker Desktop
> esteja
> [configurado para usar o
_backend_ do WSL2](https://docs.docker.com/docker-for-windows/wsl/).

A seguir, você já pode criar seu primeiro projeto Laravel.
Inicie o
[Terminal do Windows](https://www.microsoft.com/en-us/p/windows-terminal/9n0dx20hk701?rtc=1&activetab=pivot:overviewtab)
e inicie uma nova sessão de terminal para seu sistema operacional Linux WSL2.
A seguir, você pode usar um comando de terminal simples para criar um novo
projeto Laravel.
Por exemplo, para criar uma nova aplicação Laravel em um diretório chamado
`app-exemplo`, você pode executar o seguinte comando no seu terminal:

```shell
curl -s https://laravel.build/app-exemplo | bash
```

Claro, você pode alterar `app-exemplo` nesta URL para o que quiser - apenas se
certifique de que o nome da aplicação contenha apenas caracteres alfanuméricos,
hífens e sublinhados.
O diretório da aplicação Laravel será criado dentro do diretório a partir do
qual você executar o comando.

A instalação do Sail pode levar vários minutos enquanto os contêineres da
aplicação Sail são construídos na sua máquina local.

Após a criação do projeto, você pode navegar até o diretório da aplicação e
iniciar o Laravel Sail.
O Laravel Sail fornece uma interface de linha de comando simples para interagir
com a configuração padrão do Docker do Laravel:

```shell
cd app-exemplo

./vendor/bin/sail up
```

Depois que os contêineres Docker da aplicação forem iniciados, você deverá
executar as [migrações de banco de dados](../migrations.md) da sua aplicação:

```shell
./vendor/bin/sail artisan migrate
```

Por fim, você pode acessar a aplicação no seu navegador em:
[http://localhost](http://localhost).

> **Nota:**
> Para continuar aprendendo mais sobre o Laravel Sail, revise sua
> [documentação completa](../sail.md).

#### Desenvolvendo no WSL2

Claro, você precisará modificar os arquivos da aplicação Laravel que foram
criados na instalação do WSL2.
Para fazer isso, recomendamos usar o editor
[Visual Studio Code](https://code.visualstudio.com) da Microsoft e sua
[extensão para Desenvolvimento Remoto](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.vscode-remote-extensionpack).

Depois que essas ferramentas estiverem instaladas, você poderá abrir qualquer
projeto Laravel executando o comando `code .` a partir do diretório raiz da sua
aplicação usando o Terminal Windows.

### Sail no Linux

Se você estiver desenvolvendo no Linux e o
[Docker Compose](https://docs.docker.com/compose/install/) já estiver instalado,
você pode usar um simples comando de terminal para criar um novo projeto
Laravel.

Primeiro, se estiver usando o Docker Desktop para Linux, você deve executar o
seguinte comando.
Se não estiver usando o Docker Desktop para Linux, você pode pular esta etapa:

```shell
docker context use default
```

Então, para criar uma nova aplicação Laravel em um diretório chamado
`app-exemplo`, você pode executar o seguinte comando no seu terminal:

```shell
curl -s https://laravel.build/app-exemplo | bash
```

Claro, você pode alterar `app-exemplo` nesta URL para o que quiser - apenas se
certifique de que o nome da aplicação contenha apenas caracteres alfanuméricos,
hífens e sublinhados.
O diretório da aplicação Laravel será criado dentro do diretório a partir do
qual você executar o comando.

A instalação do Sail pode levar vários minutos enquanto os contêineres da
aplicação Sail são construídos na sua máquina local.

Após a criação do projeto, você pode navegar até o diretório da aplicação e
iniciar o Laravel Sail.
O Laravel Sail fornece uma interface de linha de comando simples para interagir
com a configuração padrão do Docker do Laravel:

```shell
cd app-exemplo

./vendor/bin/sail up
```

Depois que os contêineres Docker da aplicação forem iniciados, você deverá
executar as [migrações de banco de dados](../migrations.md) da sua aplicação:

```shell
./vendor/bin/sail artisan migrate
```

Por fim, você pode acessar a aplicação no seu navegador em:
[http://localhost](http://localhost).

> **Nota:**
> Para continuar aprendendo mais sobre o Laravel Sail, revise sua
> [documentação completa](../sail.md).

### Escolhendo Seus Serviços Sail

Ao criar uma nova aplicação Laravel usando o Sail, você pode usar a variável de
_string_ de consulta `with` para escolher quais serviços devem ser configurados
no arquivo `docker-compose.yml` da sua nova aplicação.
Os serviços disponíveis incluem `mysql`, `pgsql`, `mariadb`, `redis`,
`memcached`, `meilisearch`, `typesense`, `minio`, `selenium` e `mailpit`:

```shell
curl -s "https://laravel.build/app-exemplo?with=mysql,redis" | bash
```

Se você não especificar quais serviços deseja configurar, será configurado uma
_stack_ padrão com `mysql`, `redis`, `meilisearch`, `mailpit` e `selenium`.

Você pode instruir o Sail a instalar um
[Devcontainer](../sail.md#using-devcontainers) padrão adicionando o parâmetro
`devcontainer` à URL:

```shell
curl -s "https://laravel.build/app-exemplo?with=mysql,redis&devcontainer" | bash
```

## Suporte à IDE

Você é livre para usar qualquer editor de código que desejar ao desenvolver
aplicações Laravel; no entanto, o
[PhpStorm](https://www.jetbrains.com/phpstorm/laravel/) oferece amplo suporte ao
Laravel e seu ecossistema, incluindo o
[Laravel Pint](https://www.jetbrains.com/help/phpstorm/using-laravel-pint.html).

Além disso, o _plugin_ [Laravel Idea](https://laravel-idea.com/) do PhpStorm
mantido pela comunidade oferece uma variedade de melhorias úteis da IDE,
incluindo geração de código, preenchimento de sintaxe Eloquent, preenchimento de
regra de validação e muito mais.

## Próximos Passos

Agora que criou seu projeto Laravel, você deve estar se perguntando o que
aprender a seguir.
Primeiro, recomendamos fortemente que você se familiarize com o funcionamento do
Laravel lendo a seguinte documentação:

- [Request Lifecycle](../lifecycle.md)
- [Configuration](../configuration.md)
- [Directory Structure](../structure.md)
- [Frontend](../frontend.md)
- [Service Container](../container.md)
- [Facades](../facades.md)

A maneira como você deseja usar o Laravel também ditará os próximos passos em
sua jornada.
Existem várias maneiras de usar o Laravel e exploraremos dois casos de uso
principais para o _framework_ abaixo.

> **Nota:**
> Começando agora com o Laravel?
> Confira o [Bootcamp Laravel](https://bootcamp.laravel.com) para uma jornada
> prática pelo _framework_ enquanto orientamos você na construção da sua
> primeira aplicação Laravel.

### Laravel, o _Framework Full-Stack_

O Laravel pode servir como um _framework full-stack_.
Por _framework_ "_full-stack_" queremos dizer que você usará o Laravel para
rotear requisições para sua aplicação e renderizar seu _frontend_ usando
[_templates_ Blade](../blade.md) ou uma tecnologia híbrida de aplicação de
página única como o [Inertia](https://inertiajs.com).
Esta é a forma mais comum de usar o _framework_ Laravel e, em nossa opinião, a
forma mais produtiva de usar o Laravel.

Se você planeja usar o Laravel dessa forma, você pode querer verificar nossa
documentação sobre [desenvolvimento _frontend_](../frontend.md),
[roteamento](../routing.md), [visualizações](../views.md) ou o
[ORM Eloquent](../eloquent.md).
Além disso, você pode estar interessado em aprender sobre pacotes comunitários
como o [Livewire](https://livewire.laravel.com) e o
[Inertia](https://inertiajs.com).
Esses pacotes permitem que você use o Laravel como um _framework full-stack_
enquanto aproveita muitos dos benefícios da _UI_ fornecidos por aplicações
JavaScript de página única.

Se você estiver usando o Laravel como um _framework full-stack_, também
recomendamos fortemente que você aprenda como compilar o CSS e o JavaScript da
sua aplicação usando o [Vite](../vite.md).

> **Nota:**
> Se você quiser sair na frente no desenvolvimento da sua aplicação, confira um
> dos nossos [pacotes oficiais para iniciantes](../starter-kits.md).

### Laravel, o _Backend_ de API

O Laravel também pode servir como _backend_ de API para uma aplicação JavaScript
de página única ou aplicativo móvel.
Por exemplo, você pode usar o Laravel como _backend_ de API para sua aplicação
[Next.js](https://nextjs.org).
Neste contexto, você pode usar o Laravel para fornecer
[autenticação](../sanctum.md) e armazenamento e recuperação de dados para sua
aplicação, enquanto aproveita os poderosos serviços do Laravel, como filas,
e-mails, notificações e muito mais.

Se você planeja usar o Laravel dessa forma, você pode verificar nossa
documentação sobre [roteamento](../routing.md), o
[Laravel Sanctum](../sanctum.md) e o [ORM Eloquent](../eloquent.md).

> **Nota:**
> Precisa sair na frente na estruturação do seu _backend_ Laravel e _frontend_
> Next.js?
> O Laravel Breeze oferece uma
> [_stack_ de API](../starter-kits.md#breeze-and-next), bem como uma
> [implementação de _frontend_ Next.js](https://github.com/laravel/breeze-next)
> para que você possa começar em minutos.
