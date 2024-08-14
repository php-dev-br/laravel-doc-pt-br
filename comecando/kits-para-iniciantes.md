---
source_url: https://github.com/laravel/docs/blob/11.x/starter-kits.md
revision: 8332d20b07cc639995649a335620b044151e6dd1
status: ready
---

# _Kits_ para Iniciantes

- [Introdução](#introducao)
- [Laravel Breeze](#laravel-breeze)
    - [Instalação](#instalacao-do-laravel-breeze)
    - [Breeze e Blade](#breeze-e-blade)
    - [Breeze e Livewire](#breeze-e-livewire)
    - [Breeze e React / Vue](#breeze-e-inertia)
    - [Breeze e Next.js / API](#breeze-e-next)
- [Laravel Jetstream](#laravel-jetstream)

## Introdução

Para te dar uma vantagem inicial na construção da sua nova aplicação Laravel,
temos o prazer de oferecer _kits_ de autenticação e aplicações para iniciantes.
Esses _kits_ geram automaticamente o código com as rotas, controladores e
visualizações necessários para registrar e autenticar as pessoas usuárias da sua
aplicação.

Embora você possa usar esses _kits_ para iniciantes, eles não são obrigatórios.
Você está livre para construir sua própria aplicação do zero, simplesmente
instalando uma nova cópia do Laravel.
De qualquer forma, sabemos que você construirá algo incrível!

## Laravel Breeze

O [Laravel Breeze](https://github.com/laravel/breeze) é uma implementação mínima
e simples de todos os [recursos de autenticação](../authentication.md) do
Laravel, incluindo _login_, registro, redefinição de senha, verificação de
_e-mail_ e confirmação de senha.
Além disso, o Breeze inclui uma página simples de perfil onde a pessoa usuária
pode atualizar seu nome, endereço de _e-mail_ e senha.

A camada de visualização padrão do Laravel Breeze é composta de
[_templates_ Blade](../blade.md) simples estilizados com
[Tailwind CSS](https://tailwindcss.com).
Além disso, o Breeze oferece opções de geração de código baseado no
[Livewire](https://livewire.laravel.com) ou [Inertia](https://inertiajs.com),
com a opção de usar Vue ou React para o código gerado baseado no Inertia.

<img
src="https://laravel.com/img/docs/breeze-register.png"
alt="Imagem da página de registro de nova pessoa usuário do Laravel" />

#### Laravel Bootcamp

Se você está começando agora no Laravel, sinta-se à vontade para entrar no
[Laravel Bootcamp](https://bootcamp.laravel.com).
O Laravel Bootcamp irá orientá-lo na construção da sua primeira aplicação
Laravel usando o Breeze.
É uma ótima maneira de conhecer tudo o que o Laravel e o Breeze têm a oferecer.

### Instalação {: #instalacao-do-laravel-breeze }

Primeiro, você deve [criar uma nova aplicação Laravel](instalacao.md).
Se você criar sua aplicação usando o
[instalador do Laravel](instalacao.md#criando-um-projeto-laravel), será
solicitado que você instale o Laravel Breeze durante o processo de instalação.
Caso contrário, você precisará seguir as instruções de instalação manual abaixo.

Se você já criou uma nova aplicação Laravel sem um _kit_ para iniciante, você
pode instalar manualmente o Laravel Breeze usando o Composer:

```shell
composer require laravel/breeze --dev
```

Após o Composer instalar o pacote Laravel Breeze, você deve executar o comando
`breeze:install` do Artisan.
Este comando publicará as visualizações, rotas, controladores e outros recursos
de autenticação na sua aplicação.
O Laravel Breeze publica todo o código na sua aplicação para que você tenha
total controle e visibilidade sobre seus recursos e implementação.

O comando `breeze:install` solicitará a sua _stack front-end_ e _framework_ de
testes preferidos:

```shell
php artisan breeze:install

php artisan migrate
npm install
npm run dev
```

### Breeze e Blade

A _stack_ padrão Breeze é a _stack_ Blade, que utiliza
[_templates_ Blade](../blade.md) simples para renderizar o _front-end_ da sua
aplicação.
A _stack_ Blade pode ser instalada invocando o comando `breeze:install` sem
argumentos adicionais e selecionando a _stack front-end_ Blade.
Após a instalação do código gerado pelo Breeze, você também deve compilar os
ativos do _front-end_ da sua aplicação:

```shell
php artisan breeze:install

php artisan migrate
npm install
npm run dev
```

Em seguida, você pode navegar até as URLs `/login` ou `/register` da sua
aplicação no seu navegador _web_.
Todas as rotas do Breeze são definidas no arquivo `routes/auth.php`.

> **Nota:**
> Para saber mais sobre como compilar o CSS e o JavaScript da sua aplicação,
> confira a [documentação do Vite](../vite.md#running-vite) do Laravel.

### Breeze e Livewire

O Laravel Breeze também oferece geração de código do
[Livewire](https://livewire.laravel.com).
O Livewire é uma maneira poderosa de construir interfaces de pessoas usuárias
_front-end_ dinâmicas e reativas usando apenas PHP.

O Livewire é uma ótima opção para equipes que usam principalmente _templates_
Blade e procuram uma alternativa mais simples para _frameworks_ SPA baseados em
JavaScript, como Vue e React.

Para usar a _stack_ Livewire, você pode selecionar a _stack front-end_ Livewire
ao executar o comando `breeze:install` do Artisan.
Após a instalação do código gerado pelo Breeze, você deve executar as migrações
do seu banco de dados:

```shell
php artisan breeze:install

php artisan migrate
```

### Breeze e React / Vue {: #breeze-e-inertia }

O Laravel Breeze também oferece geração de código React e Vue por meio de uma
implementação de _front-end_ do [Inertia](https://inertiajs.com).
O Inertia permite que você crie aplicações React e Vue modernas e de página
única usando roteamento e controladores clássicos do lado do servidor.

O Inertia permite que você aproveite o poder de _front-end_ do React e Vue
combinado com a incrível produtividade de _back-end_ do Laravel e a compilação
extremamente rápida do [Vite](https://vitejs.dev).
Para usar uma _stack_ Inertia, você pode selecionar as _stacks front-end_ Vue ou
React ao executar o comando `breeze:install` do Artisan.

Ao selecionar a _stack front-end_ Vue ou React, o instalador do Breeze também
solicitará que você determine se deseja suporte para
[SSR com Inertia](https://inertiajs.com/server-side-rendering) ou TypeScript.
Após a instalação do código gerado pelo Breeze, você também deve compilar os
ativos de _front-end_ da sua aplicação:

```shell
php artisan breeze:install

php artisan migrate
npm install
npm run dev
```

Em seguida, você pode navegar até as URLs `/login` ou `/register` da sua
aplicação no seu navegador _web_.
Todas as rotas do Breeze são definidas no arquivo `routes/auth.php`.

### Breeze e Next.js / API {: #breeze-e-next }

O Laravel Breeze também pode gerar o código de uma API de autenticação pronta
para autenticar aplicações JavaScript modernas, como aquelas baseadas em
[Next](https://nextjs.org), [Nuxt](https://nuxt.com), e outros.
Para começar, selecione a _stack_ de API como a _stack_ desejada ao executar o
comando `breeze:install` do Artisan:

```shell
php artisan breeze:install

php artisan migrate
```

Durante a instalação, o Breeze adicionará uma variável de ambiente
`FRONTEND_URL` ao arquivo `.env` da sua aplicação.
Esta URL deve ser a URL da sua aplicação JavaScript.
Normalmente será `http://localhost:3000` durante o desenvolvimento local.
Além disso, você deve garantir que sua `APP_URL` esteja definida como
`http://localhost:8000`, que é a URL padrão usada pelo comando `serve` do
Artisan.

#### Implementação de Referência Next.js

Finalmente, você está pronto para parear este _back-end_ com o _front-end_ de
sua escolha.
Uma implementação de referência Next do _front-end_ Breeze está
[disponível no GitHub](https://github.com/laravel/breeze-next).
Este _front-end_ é mantido pelo Laravel e contém a mesma interface de pessoa
usuária que as _stacks_ Blade e Inertia tradicionais fornecidas pelo Breeze.

## Laravel Jetstream

Embora o Laravel Breeze forneça um ponto de partida simples e mínimo para a
construção de uma aplicação Laravel, o Jetstream aumenta essa funcionalidade com
recursos mais robustos e _stacks_ adicionais de tecnologias de _front-end_.
**Para aquelas pessoas novas no Laravel, recomendamos aprender o básico com o
Laravel Breeze antes de se graduar no Laravel Jetstream.**

O Jetstream fornece a geração de código de uma aplicação lindamente projetada
para o Laravel e inclui _login_, registro, verificação de _e-mail_, autenticação
de dois fatores, gerenciamento de sessão, suporte de API via Laravel Sanctum e
gerenciamento de equipe opcional.
O Jetstream foi projetado usando [Tailwind CSS](https://tailwindcss.com) e
oferece sua escolha de geração de código _front-end_ orientado pelo
[Livewire](https://livewire.laravel.com) ou [Inertia](https://inertiajs.com).

A documentação completa para instalação do Laravel Jetstream pode ser encontrada
na [documentação do Jetstream](https://jetstream.laravel.com).
