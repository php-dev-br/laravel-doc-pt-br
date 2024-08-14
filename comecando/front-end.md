---
source_url: https://github.com/laravel/docs/blob/11.x/frontend.md
revision: 46c2634ef5a4f15427c94a3157b626cf5bd3937f
status: ready
---

# _Front-end_

- [Introdução](#introducao)
- [Usando PHP](#usando-php)
    - [PHP e Blade](#php-e-blade)
    - [Livewire](#livewire)
    - [_Kits_ para Iniciantes](#kits-php-para-iniciantes)
- [Usando Vue / React](#usando-vue-react)
    - [Inertia](#inertia)
    - [_Kits_ para Iniciantes](#kits-inertia-para-iniciantes)
- [Empacotando Ativos](#empacotando-ativos)

## Introdução

Laravel é um _framework back-end_ que fornece todos os recursos necessários
para construir aplicações _web_ modernas, como [roteamento](../routing.md),
[validação](../validation.md), [_cache_](../cache.md), [filas](../queues.md),
[armazenamento de arquivos](../filesystem.md) e muito mais.
No entanto, acreditamos que é importante oferecer às pessoas desenvolvedoras uma
bela experiência _full-stack_, incluindo abordagens poderosas para construir o
_front-end_ da sua aplicação.

Existem duas maneiras principais de lidar com o desenvolvimento _front-end_ ao
construir uma aplicação com o Laravel, e a abordagem escolhida depende se você
deseja construir seu _front-end_ aproveitando o PHP ou usando _frameworks_
JavaScript como Vue e React.
Discutiremos essas duas opções abaixo para que você possa decidir com
embasamento sobre a melhor abordagem de desenvolvimento _front-end_ para a sua
aplicação.

## Usando PHP

### PHP e Blade

No passado, a maioria das aplicações PHP renderizavam HTML para o navegador
usando _templates_ HTML simples intercalados com instruções `echo` PHP que
renderizavam dados recuperados de um banco de dados durante a requisição:

```blade
<div>
    <?php foreach ($users as $user): ?>
        Olá, <?php echo $user->name; ?> <br />
    <?php endforeach; ?>
</div>
```

No Laravel, esta abordagem para renderizar HTML ainda pode ser alcançada usando
[visualizações](../views.md) e [Blade](../blade.md).
Blade é uma linguagem de _templating_ extremamente leve que fornece sintaxe
curta e conveniente para exibir dados, iterar sobre dados e muito mais:

```blade
<div>
    @foreach ($users as $user)
        Olá, {{ $user->name }} <br />
    @endforeach
</div>
```

Ao criar aplicações dessa maneira, os envios de formulários e outras interações
de páginas normalmente recebem um documento HTML inteiramente novo do servidor e
a página inteira é renderizada novamente pelo navegador.
Ainda hoje, muitas aplicações podem ser perfeitamente adequadas para ter seus
_front-ends_ construídos dessa forma, usando _templates_ simples do Blade.

#### Expectativas Crescentes

No entanto, à medida que as expectativas das pessoas usuárias em relação às
aplicações _web_ amadureceram, muitas pessoas desenvolvedoras descobriram a
necessidade de construir _front-ends_ mais dinâmicos com interações mais
refinadas.
Diante disso, algumas pessoas desenvolvedoras optam por começar a construir o
_front-end_ das suas aplicações usando _frameworks_ JavaScript como Vue e React.

Outras pessoas, preferindo ficar com a linguagem de _back-end_ com a qual se
sentem confortáveis, desenvolveram soluções que permitem a construção de
interfaces modernas de aplicações _web_, enquanto utilizam principalmente a
linguagem de _back-end_ de sua escolha.
Por exemplo, no ecossistema [Rails](https://rubyonrails.org/), isso estimulou a
criação de bibliotecas como [Turbo](https://turbo.hotwired.dev/)
[Hotwire](https://hotwired.dev/) e [Stimulus](https://stimulus.hotwired.dev/).

No ecossistema Laravel, a necessidade de criar _front-ends_ modernos e dinâmicos
usando principalmente o PHP levou à criação do
[Laravel Livewire](https://livewire.laravel.com) e do
[Alpine.js](https://alpinejs.dev/).

### Livewire

[Laravel Livewire](https://livewire.laravel.com) é um _framework_ para construir
_front-ends_ baseados em Laravel que parecem dinâmicos, modernos e vivos, assim
como _front-ends_ construídos com _frameworks_ JavaScript modernos como Vue e
React.

Ao usar o Livewire, você criará componentes Livewire que renderizarão uma parte
discreta da sua interface da pessoa usuária e exporão métodos e dados que podem
ser invocados e interagidos a partir do _front-end_ da sua aplicação.
Por exemplo, um simples componente `Counter` pode parecer o seguinte:

```php
<?php

namespace App\Http\Livewire;

use Livewire\Component;

class Counter extends Component
{
    public $count = 0;

    public function increment()
    {
        $this->count++;
    }

    public function render()
    {
        return view('livewire.counter');
    }
}
```

E o _template_ correspondente para o componente seria escrito assim:

```blade
<div>
    <button wire:click="increment">+</button>
    <h1>{{ $count }}</h1>
</div>
```

Como você pode ver, o Livewire permite que você escreva novos atributos HTML,
como `wire:click`, que conectam o _front-end_ e o _back-end_ da sua aplicação
Laravel.
Além disso, você pode renderizar o estado atual do seu componente usando
expressões simples do Blade.

Para muitas pessoas, o Livewire revolucionou o desenvolvimento _front-end_ com o
Laravel, permitindo permanecerem no conforto do Laravel enquanto constroem
aplicações web modernas e dinâmicas.
Normalmente, as pessoas desenvolvedoras que usam Livewire também usarão
[Alpine.js](https://alpinejs.dev/) para espalhar JavaScript pelo _front-end_
apenas onde for necessário, como para renderizar uma janela de diálogo.

Se você está começando agora no Laravel, recomendamos se familiarizar com o uso
básico das [visualizações](../views.md) e do [Blade](../blade.md).
Em seguida, consulte a
[documentação do Laravel Livewire](https://livewire.laravel.com/docs)
para saber como levar sua aplicação para o próximo nível com componentes
interativos do Livewire.

### _Kits_ para Iniciantes {: #kits-php-para-iniciantes }

Se desejar construir seu _front-end_ usando PHP e Livewire, você pode aproveitar
nossos [_kits_ Breeze e Jetstream para iniciantes](kits-para-iniciantes.md)
para iniciar o desenvolvimento da sua aplicação.
Ambos os _kits_ para iniciantes geram o código do fluxo de autenticação do
_back-end_ e do _front-end_ da sua aplicação usando [Blade](../blade.md) e
[Tailwind](https://tailwindcss.com) para que você possa simplesmente começar a
construir sua próxima grande ideia.

## Usando Vue / React

Embora seja possível construir _front-ends_ modernos usando Laravel e Livewire,
muitas pessoas desenvolvedoras ainda preferem aproveitar o poder de um
_framework_ JavaScript como Vue ou React.
Isso permite que as pessoas desenvolvedoras aproveitem o rico ecossistema de
pacotes e ferramentas JavaScript disponíveis via NPM.

No entanto, sem ferramentas adicionais, parear o Laravel com Vue ou React nos
deixaria tendo que resolver uma variedade de problemas complicados, como
roteamento do lado do cliente, hidratação de dados e autenticação.
O roteamento do lado do cliente é frequentemente simplificado usando
_frameworks_ Vue ou React opinativos, como [Nuxt](https://nuxt.com/) e
[Next](https://nextjs.org/); no entanto, a hidratação de dados e a autenticação
continuam sendo problemas complicados e difíceis de resolver ao parear um
_framework back-end_ como o Laravel com esses _frameworks front-end_.

Além disso, as pessoas desenvolvedoras acabam mantendo dois repositórios de
código separados, muitas vezes precisando coordenar manutenção, lançamentos e
implantações em ambos os repositórios.
Embora esses problemas não sejam intransponíveis, não acreditamos que seja uma
forma produtiva ou agradável de desenvolver aplicações.

### Inertia

Felizmente, o Laravel oferece o melhor dos dois mundos.
O [Inertia](https://inertiajs.com) preenche a lacuna entre sua aplicação Laravel
e seu _front-end_ Vue ou React moderno, permitindo que você construa
_front-ends_ modernos e completos usando Vue ou React enquanto aproveita rotas e
controladores Laravel para roteamento, hidratação de dados e autenticação - tudo
em um único repositório de código.
Com essa abordagem, você pode aproveitar todo o poder do Laravel e do Vue/React
sem prejudicar os recursos de nenhuma das ferramentas.

Após instalar o Inertia em sua aplicação Laravel, você escreverá rotas e
controladores normalmente.
No entanto, em vez de retornar um _template_ Blade do seu controlador, você
retornará uma página Inertia:

```php
<?php

namespace App\Http\Controllers;

use App\Http\Controllers\Controller;
use App\Models\User;
use Inertia\Inertia;
use Inertia\Response;

class UserController extends Controller
{
    /**
     * Mostra o perfil de um determinado usuário.
     */
    public function show(string $id): Response
    {
        return Inertia::render('Users/Profile', [
            'user' => User::findOrFail($id)
        ]);
    }
}
```

Uma página Inertia corresponde a um componente Vue ou React, normalmente
armazenado no diretório `resources/js/Pages` da sua aplicação.
Os dados fornecidos à página através do método `Inertia::render` serão usados
para hidratar as `props` do componente da página:

```vue

<script setup>
    import Layout from '@/Layouts/Authenticated.vue';
    import {Head} from '@inertiajs/vue3';

    const props = defineProps(['user']);
</script>

<template>
    <Head title="Perfil da Pessoa Usuária"/>

    <Layout>
        <template #header>
            <h2 class="font-semibold text-xl text-gray-800 leading-tight">
                Perfil
            </h2>
        </template>

        <div class="py-12">
            Olá, {{ user.name }}
        </div>
    </Layout>
</template>
```

Como você pode ver, o Inertia permite que você aproveite todo o poder do Vue ou
React ao construir seu _front-end_, enquanto fornece uma ponte leve entre seu
_back-end_ com Laravel e seu _front-end_ com JavaScript.

#### Renderização no Lado do Servidor

Se você tem preocupação em mergulhar no Inertia porque sua aplicação requer
renderização no servidor, não se preocupe.
O Inertia oferece
[suporte à renderização no servidor](https://inertiajs.com/server-side-rendering).
E, ao implantar sua aplicação via [Laravel Forge](https://forge.laravel.com), é
muito fácil garantir que o processo de renderização do lado do servidor do
Inertia esteja sempre em execução.

### _Kits_ para Iniciantes {: #kits-inertia-para-iniciantes }

Se desejar construir seu _front-end_ usando Inertia e Vue/React, você pode
aproveitar nossos _kits_ [Breeze](kits-para-iniciantes.md#laravel-breeze) e
[Jetstream](kits-para-iniciantes.md#laravel-jetstream) para iniciantes para
iniciar o desenvolvimento da sua aplicação.
Ambos os _kits_ para iniciantes geram o código do fluxo de autenticação do
_back-end_ e do _front-end_ da sua aplicação usando Inertia, Vue/React,
[Tailwind](https://tailwindcss.com) e [Vite](https://vitejs.dev) para que você
possa começar a construir sua próxima grande ideia.

## Empacotando Ativos

Independentemente de você optar por desenvolver seu _front-end_ usando Blade e
Livewire ou Vue/React e Inertia, você provavelmente precisará empacotar o CSS da
sua aplicação em ativos prontos para produção.
Claro, se você optar por construir o _front-end_ da sua aplicação com Vue ou
React, você também precisará empacotar seus componentes em ativos JavaScript
prontos para uso pelo navegador.

Por padrão, o Laravel utiliza o [Vite](https://vitejs.dev) para empacotar seus
ativos.
O Vite fornece tempos de construção extremamente rápidos e Hot Module
Replacement (HMR) quase instantânea durante o desenvolvimento local.
Em todas as novas aplicações Laravel, incluindo aquelas que usam nossos
[_kits_ para iniciantes](kits-para-iniciantes.md), você encontrará um arquivo
`vite.config.js` que carrega nosso _plug-in_ leve do Laravel Vite que torna o
Vite uma alegria de usar com aplicações Laravel.

A forma mais rápida de começar a usar o Laravel e o Vite é iniciar o
desenvolvimento da sua aplicação usando o
[Laravel Breeze](kits-para-iniciantes.md#laravel-breeze), nosso _kit_ para
iniciantes mais simples que dá início à sua aplicação fornecendo a geração do
código de autenticação do _front-end_ e do _back-end_.

> **Nota:**
> Para mais detalhes sobre o uso do Vite com Laravel, consulte nossa
> [documentação dedicada sobre empacotamento e compilação dos seus ativos](../vite.md).
