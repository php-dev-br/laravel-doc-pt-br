<!-- source_url: https://github.com/laravel/docs/blob/11.x/installation.md -->
<!-- revision: 5f49bc6610d86ab145d99407df04d94c0c83a52d -->
<!-- status: wip -->

# Instalação

- [Conheça o Laravel](#conheca-o-laravel)
  - [Por que Laravel?](#por-que-laravel)
- [Creating a Laravel Project](#creating-a-laravel-project)
- [Initial Configuration](#initial-configuration)
  - [Environment Based Configuration](#environment-based-configuration)
  - [Databases and Migrations](#databases-and-migrations)
  - [Directory Configuration](#directory-configuration)
- [Docker Installation Using Sail](#docker-installation-using-sail)
  - [Sail on macOS](#sail-on-macos)
  - [Sail on Windows](#sail-on-windows)
  - [Sail on Linux](#sail-on-linux)
  - [Choosing Your Sail Services](#choosing-your-sail-services)
- [IDE Support](#ide-support)
- [Next Steps](#next-steps)
  - [Laravel the Full Stack Framework](#laravel-the-fullstack-framework)
  - [Laravel the API Backend](#laravel-the-api-backend)

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

> **Nota:** Começando agora com o Laravel?
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
com centenas de milhões de solicitações mensais.

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

## Creating a Laravel Project

Before creating your first Laravel project, make sure that your local machine
has PHP and [Composer](https://getcomposer.org) installed.
If you are developing on macOS or Windows, PHP and Composer can be installed in
minutes via [Laravel Herd](https://herd.laravel.com).
In addition, we recommend [installing Node and NPM](https://nodejs.org).

After you have installed PHP and Composer, you may create a new Laravel project
via Composer's `create-project` command:

```nothing
composer create-project laravel/laravel example-app
```

Or, you may create new Laravel projects by globally
installing [the Laravel installer](https://github.com/laravel/installer) via
Composer:

```nothing
composer global require laravel/installer

laravel new example-app
```

Once the project has been created, start Laravel's local development server
using Laravel Artisan's `serve` command:

```nothing
cd example-app

php artisan serve
```

Once you have started the Artisan development server, your application will be
accessible in your web browser
at [http://localhost:8000](http://localhost:8000).
Next, you're ready
to [start taking your next steps into the Laravel ecosystem](#next-steps).
Of course, you may also want
to [configure a database](#databases-and-migrations).

> [!NOTE]
> If you would like a head start when developing your Laravel application,
> consider using one of our [starter kits](../starter-kits.md).
> Laravel's starter kits provide backend and frontend authentication scaffolding
> for your new Laravel application.

<a name="initial-configuration"></a>

## Initial Configuration

All of the configuration files for the Laravel framework are stored in
the `config` directory.
Each option is documented, so feel free to look through the files and get
familiar with the options available to you.

Laravel needs almost no additional configuration out of the box.
You are free to get started developing! However, you may wish to review
the `config/app.php` file and its documentation.
It contains several options such as `timezone` and `locale` that you may wish to
change according to your application.

<a name="environment-based-configuration"></a>

### Environment Based Configuration

Since many of Laravel's configuration option values may vary depending on
whether your application is running on your local machine or on a production web
server, many important configuration values are defined using the `.env` file
that exists at the root of your application.

Your `.env` file should not be committed to your application's source control,
since each developer / server using your application could require a different
environment configuration.
Furthermore, this would be a security risk in the event an intruder gains access
to your source control repository, since any sensitive credentials would be
exposed.

> [!NOTE]
> For more information about the `.env` file and environment based
> configuration, check out the
>
full [configuration documentation](../configuration.md#environment-configuration).

<a name="databases-and-migrations"></a>

### Databases and Migrations

Now that you have created your Laravel application, you probably want to store
some data in a database.
By default, your application's `.env` configuration file specifies that Laravel
will be interacting with a SQLite database.

During the creation of the project, Laravel created a `database/database.sqlite`
file for you, and ran the necessary migrations to create the application's
database tables.

If you prefer to use another database driver such as MySQL or PostgreSQL, you
can update your `.env` configuration file to use the appropriate database.
For example, if you wish to use MySQL, update your `.env` configuration
file's `DB_*` variables like so:

```ini
DB_CONNECTION = mysql
DB_HOST = 127.0.0.1
DB_PORT = 3306
DB_DATABASE = laravel
DB_USERNAME = root
DB_PASSWORD =
```

If you choose to use a database other than SQLite, you will need to create the
database and run your application's [database migrations](../migrations.md):

```shell
php artisan migrate
```

> [!NOTE]
> If you are developing on macOS and need to install MySQL, PostgreSQL, or Redis
> locally, consider using [DBngin](https://dbngin.com/).

<a name="directory-configuration"></a>

### Directory Configuration

Laravel should always be served out of the root of the "web directory"
configured for your web server.
You should not attempt to serve a Laravel application out of a subdirectory of
the "web directory".
Attempting to do so could expose sensitive files present within your
application.

<a name="docker-installation-using-sail"></a>

## Docker Installation Using Sail

We want it to be as easy as possible to get started with Laravel regardless of
your preferred operating system.
So, there are a variety of options for developing and running a Laravel project
on your local machine.
While you may wish to explore these options at a later time, Laravel
provides [Sail](../sail.md), a built-in solution for running your Laravel
project using [Docker](https://www.docker.com).

Docker is a tool for running applications and services in small, light-weight "
containers" which do not interfere with your local machine's installed software
or configuration.
This means you don't have to worry about configuring or setting up complicated
development tools such as web servers and databases on your local machine.
To get started, you only need to
install [Docker Desktop](https://www.docker.com/products/docker-desktop).

Laravel Sail is a light-weight command-line interface for interacting with
Laravel's default Docker configuration.
Sail provides a great starting point for building a Laravel application using
PHP, MySQL, and Redis without requiring prior Docker experience.

> [!NOTE]
> Already a Docker expert? Don't worry! Everything about Sail can be customized
> using the `docker-compose.yml` file included with Laravel.

<a name="sail-on-macos"></a>

### Sail on macOS

If you're developing on a Mac
and [Docker Desktop](https://www.docker.com/products/docker-desktop) is already
installed, you can use a simple terminal command to create a new Laravel
project.
For example, to create a new Laravel application in a directory named "
example-app", you may run the following command in your terminal:

```shell
curl -s "https://laravel.build/example-app" | bash
```

Of course, you can change "example-app" in this URL to anything you like - just
make sure the application name only contains alpha-numeric characters, dashes,
and underscores.
The Laravel application's directory will be created within the directory you
execute the command from.

Sail installation may take several minutes while Sail's application containers
are built on your local machine.

After the project has been created, you can navigate to the application
directory and start Laravel Sail.
Laravel Sail provides a simple command-line interface for interacting with
Laravel's default Docker configuration:

```shell
cd example-app

./vendor/bin/sail up
```

Once the application's Docker containers have started, you should run your
application's [database migrations](../migrations.md):

```shell
./vendor/bin/sail artisan migrate
```

Finally, you can access the application in your web browser
at: http://localhost.

> [!NOTE]
> To continue learning more about Laravel Sail, review
> its [complete documentation](../sail.md).

<a name="sail-on-windows"></a>

### Sail on Windows

Before we create a new Laravel application on your Windows machine, make sure to
install [Docker Desktop](https://www.docker.com/products/docker-desktop).
Next, you should ensure that Windows Subsystem for Linux 2 (WSL2) is installed
and enabled.
WSL allows you to run Linux binary executables natively on Windows 10.
Information on how to install and enable WSL2 can be found within
Microsoft's [developer environment documentation](https://docs.microsoft.com/en-us/windows/wsl/install-win10).

> [!NOTE]
> After installing and enabling WSL2, you should ensure that Docker Desktop
>
is [configured to use the WSL2 backend](https://docs.docker.com/docker-for-windows/wsl/).

Next, you are ready to create your first Laravel project.
Launch [Windows Terminal](https://www.microsoft.com/en-us/p/windows-terminal/9n0dx20hk701?rtc=1&activetab=pivot:overviewtab)
and begin a new terminal session for your WSL2 Linux operating system.
Next, you can use a simple terminal command to create a new Laravel project.
For example, to create a new Laravel application in a directory named "
example-app", you may run the following command in your terminal:

```shell
curl -s https://laravel.build/example-app | bash
```

Of course, you can change "example-app" in this URL to anything you like - just
make sure the application name only contains alpha-numeric characters, dashes,
and underscores.
The Laravel application's directory will be created within the directory you
execute the command from.

Sail installation may take several minutes while Sail's application containers
are built on your local machine.

After the project has been created, you can navigate to the application
directory and start Laravel Sail.
Laravel Sail provides a simple command-line interface for interacting with
Laravel's default Docker configuration:

```shell
cd example-app

./vendor/bin/sail up
```

Once the application's Docker containers have started, you should run your
application's [database migrations](../migrations.md):

```shell
./vendor/bin/sail artisan migrate
```

Finally, you can access the application in your web browser
at: http://localhost.

> [!NOTE]
> To continue learning more about Laravel Sail, review
> its [complete documentation](../sail.md).

#### Developing Within WSL2

Of course, you will need to be able to modify the Laravel application files that
were created within your WSL2 installation.
To accomplish this, we recommend using
Microsoft's [Visual Studio Code](https://code.visualstudio.com) editor and their
first-party extension
for [Remote Development](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.vscode-remote-extensionpack).

Once these tools are installed, you may open any Laravel project by executing
the `code .` command from your application's root directory using Windows
Terminal.

<a name="sail-on-linux"></a>

### Sail on Linux

If you're developing on Linux
and [Docker Compose](https://docs.docker.com/compose/install/) is already
installed, you can use a simple terminal command to create a new Laravel
project.

First, if you are using Docker Desktop for Linux, you should execute the
following command.
If you are not using Docker Desktop for Linux, you may skip this step:

```shell
docker context use default
```

Then, to create a new Laravel application in a directory named "example-app",
you may run the following command in your terminal:

```shell
curl -s https://laravel.build/example-app | bash
```

Of course, you can change "example-app" in this URL to anything you like - just
make sure the application name only contains alpha-numeric characters, dashes,
and underscores.
The Laravel application's directory will be created within the directory you
execute the command from.

Sail installation may take several minutes while Sail's application containers
are built on your local machine.

After the project has been created, you can navigate to the application
directory and start Laravel Sail.
Laravel Sail provides a simple command-line interface for interacting with
Laravel's default Docker configuration:

```shell
cd example-app

./vendor/bin/sail up
```

Once the application's Docker containers have started, you should run your
application's [database migrations](../migrations.md):

```shell
./vendor/bin/sail artisan migrate
```

Finally, you can access the application in your web browser
at: http://localhost.

> [!NOTE]
> To continue learning more about Laravel Sail, review
> its [complete documentation](../sail.md).

<a name="choosing-your-sail-services"></a>

### Choosing Your Sail Services

When creating a new Laravel application via Sail, you may use the `with` query
string variable to choose which services should be configured in your new
application's `docker-compose.yml` file.
Available services
include `mysql`, `pgsql`, `mariadb`, `redis`, `memcached`, `meilisearch`, `typesense`, `minio`, `selenium`,
and `mailpit`:

```shell
curl -s "https://laravel.build/example-app?with=mysql,redis" | bash
```

If you do not specify which services you would like configured, a default stack
of `mysql`, `redis`, `meilisearch`, `mailpit`, and `selenium` will be
configured.

You may instruct Sail to install a
default [Devcontainer](../sail.md#using-devcontainers) by adding
the `devcontainer` parameter to the URL:

```shell
curl -s "https://laravel.build/example-app?with=mysql,redis&devcontainer" | bash
```

<a name="ide-support"></a>

## IDE Support

You are free to use any code editor you wish when developing Laravel
applications; however, [PhpStorm](https://www.jetbrains.com/phpstorm/laravel/)
offers extensive support for Laravel and its ecosystem,
including [Laravel Pint](https://www.jetbrains.com/help/phpstorm/using-laravel-pint.html).

In addition, the community maintained [Laravel Idea](https://laravel-idea.com/)
PhpStorm plugin offers a variety of helpful IDE augmentations, including code
generation, Eloquent syntax completion, validation rule completion, and more.

<a name="next-steps"></a>

## Next Steps

Now that you have created your Laravel project, you may be wondering what to
learn next.
First, we strongly recommend becoming familiar with how Laravel works by reading
the following documentation:

<div class="content-list" markdown="1">

- [Request Lifecycle](../lifecycle.md)
- [Configuration](../configuration.md)
- [Directory Structure](../structure.md)
- [Frontend](../frontend.md)
- [Service Container](../container.md)
- [Facades](../facades.md)

</div>

How you want to use Laravel will also dictate the next steps on your journey.
There are a variety of ways to use Laravel, and we'll explore two primary use
cases for the framework below.

> [!NOTE]
> New to Laravel? Check out the [Laravel Bootcamp](https://bootcamp.laravel.com)
> for a hands-on tour of the framework while we walk you through building your
> first Laravel application.

<a name="laravel-the-fullstack-framework"></a>

### Laravel the Full Stack Framework

Laravel may serve as a full stack framework.
By "full stack" framework we mean that you are going to use Laravel to route
requests to your application and render your frontend
via [Blade templates](../blade.md) or a single-page application hybrid
technology like [Inertia](https://inertiajs.com).
This is the most common way to use the Laravel framework, and, in our opinion,
the most productive way to use Laravel.

If this is how you plan to use Laravel, you may want to check out our
documentation
on [frontend development](../frontend.md), [routing](../routing.md), [views](../views.md),
or the [Eloquent ORM](../eloquent.md).
In addition, you might be interested in learning about community packages
like [Livewire](https://livewire.laravel.com)
and [Inertia](https://inertiajs.com).
These packages allow you to use Laravel as a full-stack framework while enjoying
many of the UI benefits provided by single-page JavaScript applications.

If you are using Laravel as a full stack framework, we also strongly encourage
you to learn how to compile your application's CSS and JavaScript
using [Vite](../vite.md).

> [!NOTE]
> If you want to get a head start building your application, check out one of
> our official [application starter kits](../starter-kits.md).

<a name="laravel-the-api-backend"></a>

### Laravel the API Backend

Laravel may also serve as an API backend to a JavaScript single-page application
or mobile application.
For example, you might use Laravel as an API backend for
your [Next.js](https://nextjs.org) application.
In this context, you may use Laravel to provide [authentication](../sanctum.md)
and data storage / retrieval for your application, while also taking advantage
of Laravel's powerful services such as queues, emails, notifications, and more.

If this is how you plan to use Laravel, you may want to check out our
documentation on [routing](../routing.md), [Laravel Sanctum](../sanctum.md), and
the [Eloquent ORM](../eloquent.md).

> [!NOTE]
> Need a head start scaffolding your Laravel backend and Next.js frontend?
> Laravel Breeze offers an [API stack](../starter-kits.md#breeze-and-next) as
> well
> as a [Next.js frontend implementation](https://github.com/laravel/breeze-next)
> so you can get started in minutes.
