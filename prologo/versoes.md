---
source_url: https://github.com/laravel/docs/blob/6.x/releases.md
revision: 31944de4e5e7f59d50d5de0f5d064f49bffae723
status: ready
---

# Notas de Versão

- [Esquema de Versionamento](#esquema-de-versionamento)
- [Política de Suporte](#politica-de-suporte)
- [Laravel 6](#laravel-6)

## Esquema de Versionamento

O Laravel e seus outros pacotes originais seguem o
[Versionamento Semântico](https://semver.org/lang/pt-BR/).
As versões maiores do _framework_ são lançadas a cada seis meses (em fevereiro e
agosto), enquanto as versões menores e de correções podem ser lançadas
semanalmente.
Versões menores e de correções **nunca** devem conter alterações significativas.

Ao referenciar o _framework_ Laravel e seus componentes a partir da sua
aplicação ou pacote, você deve sempre usar uma restrição de versão como `^6.0`,
uma vez que as principais versões do Laravel incluem alterações significativas.
No entanto, nos esforçamos para sempre garantir que você possa atualizar para
uma nova versão maior em um dia ou menos.

## Política de Suporte

Para versões LTS, como o Laravel 6, as correções de falhas são fornecidas por 2
anos e as correções de segurança são fornecidas por 3 anos.
Essas versões oferecem a janela mais longa de suporte e manutenção.
Para versões gerais, as correções de falhas são fornecidas por 6 meses e as
correções de segurança são fornecidas por 1 ano.
Para todas as bibliotecas adicionais, incluindo Lumen, apenas a versão maior
mais recente recebe correções de falhas.
Além disso, revise as versões do banco de dados
[suportadas pelo Laravel](../database.md#introduction).

|  Versão   |       Lançamento        | Correções de falhas até | Correções de segurança até |
|:---------:|:-----------------------:|:-----------------------:|:--------------------------:|
| 5.5 (LTS) |  30 de agosto de 2017   |  30 de agosto de 2019   |    30 de agosto de 2020    |
|    5.6    | 7 de fevereiro de 2018  |   7 de agosto de 2018   |   7 de fevereiro de 2019   |
|    5.7    |  4 de setembro de 2018  |   4 de março de 2019    |   4 de setembro de 2019    |
|    5.8    | 26 de fevereiro de 2019 |  26 de agosto de 2019   |  26 de fevereiro de 2020   |
|  6 (LTS)  |  3 de setembro de 2019  |  3 de setembro de 2021  |   3 de setembro de 2022    |

## Laravel 6

Laravel 6 (LTS) continues the improvements made in Laravel 5.8 by introducing
semantic versioning, compatibility
with [Laravel Vapor](https://vapor.laravel.com), improved authorization
responses, job middleware, lazy collections, subquery improvements, the
extraction of frontend scaffolding to the `laravel/ui` Composer package, and a
variety of other bug fixes and usability improvements.

### Semantic Versioning

The Laravel framework (`laravel/framework`) package now follows
the [semantic versioning](https://semver.org/) standard. This makes the
framework consistent with the other first-party Laravel packages which already
followed this versioning standard. The Laravel release cycle will remain
unchanged.

### Laravel Vapor Compatibility

_Laravel Vapor was built by [Taylor Otwell](https://github.com/taylorotwell)_.

Laravel 6 provides compatibility
with [Laravel Vapor](https://vapor.laravel.com), an auto-scaling serverless
deployment platform for Laravel. Vapor abstracts the complexity of managing
Laravel applications on AWS Lambda, as well as interfacing those applications
with SQS queues, databases, Redis clusters, networks, CloudFront CDN, and more.

### Improved Exceptions Via Ignition

Laravel 6 ships with [Ignition](https://github.com/facade/ignition), a new open
source exception detail page created by Freek Van der Herten and Marcel Pociot.
Ignition offers many benefits over previous releases, such as improved Blade
error file and line number handling, runnable solutions for common problems,
code editing, exception sharing, and an improved UX.

### Improved Authorization Responses

_Improved authorization responses were implemented
by [Gary Green](https://github.com/garygreen)_.

In previous releases of Laravel, it was difficult to retrieve and expose custom
authorization messages to end users. This made it difficult to explain to
end-users exactly why a particular request was denied. In Laravel 6, this is now
much easier using authorization response messages and the new `Gate::inspect`
method. For example, given the following policy method:

    /**
     * Determine if the user can view the given flight.
     *
     * @param  \App\User  $user
     * @param  \App\Flight  $flight
     * @return mixed
     */
    public function view(User $user, Flight $flight)
    {
        return $this->deny('Explanation of denial.');
    }

The authorization policy's response and message may be easily retrieved using
the `Gate::inspect` method:

    $response = Gate::inspect('view', $flight);

    if ($response->allowed()) {
        // User is authorized to view the flight...
    }

    if ($response->denied()) {
        echo $response->message();
    }

In addition, these custom messages will automatically be returned to your
frontend when using helper methods such as `$this->authorize`
or `Gate::authorize` from your routes or controllers.

### Job Middleware

_Job middleware were implemented
by [Taylor Otwell](https://github.com/taylorotwell)_.

Job middleware allow you to wrap custom logic around the execution of queued
jobs, reducing boilerplate in the jobs themselves. For example, in previous
releases of Laravel, you may have wrapped the logic of a job's `handle` method
within a rate-limited callback:

    /**
     * Execute the job.
     *
     * @return void
     */
    public function handle()
    {
        Redis::throttle('key')->block(0)->allow(1)->every(5)->then(function () {
            info('Lock obtained...');

            // Handle job...
        }, function () {
            // Could not obtain lock...

            return $this->release(5);
        });
    }

In Laravel 6, this logic may be extracted into a job middleware, allowing you to
keep your job's `handle` method free of any rate limiting responsibilities:

    <?php

    namespace App\Jobs\Middleware;

    use Illuminate\Support\Facades\Redis;

    class RateLimited
    {
        /**
         * Process the queued job.
         *
         * @param  mixed  $job
         * @param  callable  $next
         * @return mixed
         */
        public function handle($job, $next)
        {
            Redis::throttle('key')
                    ->block(0)->allow(1)->every(5)
                    ->then(function () use ($job, $next) {
                        // Lock obtained...

                        $next($job);
                    }, function () use ($job) {
                        // Could not obtain lock...

                        $job->release(5);
                    });
        }
    }

After creating middleware, they may be attached to a job by returning them from
the job's `middleware` method:

    use App\Jobs\Middleware\RateLimited;

    /**
     * Get the middleware the job should pass through.
     *
     * @return array
     */
    public function middleware()
    {
        return [new RateLimited];
    }

### Lazy Collections

_Lazy collections were implemented
by [Joseph Silber](https://github.com/JosephSilber)_.

Many developers already enjoy Laravel's
powerful [Collection methods](https://laravel.com/docs/collections). To
supplement the already powerful `Collection` class, Laravel 6 introduces
a `LazyCollection`, which leverages
PHP's [generators](https://www.php.net/manual/en/language.generators.overview.php)
to allow you to work with very large datasets while keeping memory usage low.

For example, imagine your application needs to process a multi-gigabyte log file
while taking advantage of Laravel's collection methods to parse the logs.
Instead of reading the entire file into memory at once, lazy collections may be
used to keep only a small part of the file in memory at a given time:

    use App\LogEntry;
    use Illuminate\Support\LazyCollection;

    LazyCollection::make(function () {
        $handle = fopen('log.txt', 'r');

        while (($line = fgets($handle)) !== false) {
            yield $line;
        }
    })
    ->chunk(4)
    ->map(function ($lines) {
        return LogEntry::fromLines($lines);
    })
    ->each(function (LogEntry $logEntry) {
        // Process the log entry...
    });

Or, imagine you need to iterate through 10,000 Eloquent models. When using
traditional Laravel collections, all 10,000 Eloquent models must be loaded into
memory at the same time:

    $users = App\User::all()->filter(function ($user) {
        return $user->id > 500;
    });

However, beginning in Laravel 6, the query builder's `cursor` method has been
updated to return a `LazyCollection` instance. This allows you to still only run
a single query against the database but also only keep one Eloquent model loaded
in memory at a time. In this example, the `filter` callback is not executed
until we actually iterate over each user individually, allowing for a drastic
reduction in memory usage:

    $users = App\User::cursor()->filter(function ($user) {
        return $user->id > 500;
    });

    foreach ($users as $user) {
        echo $user->id;
    }

### Eloquent Subquery Enhancements

_Eloquent subquery enhancements were implemented
by [Jonathan Reinink](https://github.com/reinink)_.

Laravel 6 introduces several new enhancements and improvements to database
subquery support. For example, let's imagine that we have a table of
flight `destinations` and a table of `flights` to destinations. The `flights`
table contains an `arrived_at` column which indicates when the flight arrived at
the destination.

Using the new subquery select functionality in Laravel 6, we can select all of
the `destinations` and the name of the flight that most recently arrived at that
destination using a single query:

    return Destination::addSelect(['last_flight' => Flight::select('name')
        ->whereColumn('destination_id', 'destinations.id')
        ->orderBy('arrived_at', 'desc')
        ->limit(1)
    ])->get();

In addition, we can use new subquery features added to the query
builder's `orderBy` function to sort all destinations based on when the last
flight arrived at that destination. Again, this may be done while executing a
single query against the database:

    return Destination::orderByDesc(
        Flight::select('arrived_at')
            ->whereColumn('destination_id', 'destinations.id')
            ->orderBy('arrived_at', 'desc')
            ->limit(1)
    )->get();

### Laravel UI

The frontend scaffolding typically provided with previous releases of Laravel
has been extracted into a `laravel/ui` Composer package. This allows the
first-party UI scaffolding to be developed and versioned separately from the
primary framework. As a result of this change, no Bootstrap or Vue code is
present in default framework scaffolding, and the `make:auth` command has been
extracted from the framework as well.

In order to restore the traditional Vue / Bootstrap scaffolding present in
previous releases of Laravel, you may install the `laravel/ui` package and use
the `ui` Artisan command to install the frontend scaffolding:

    composer require laravel/ui "^1.0" --dev

    php artisan ui vue --auth
