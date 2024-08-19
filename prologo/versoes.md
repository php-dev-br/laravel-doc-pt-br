---
source_url: https://github.com/laravel/docs/blob/10.x/releases.md
revision: 7a21c9cbe2164e560c9861e972d16bf6d306d938
status: ready
---

# Notas de Versão

- [Esquema de Versionamento](#esquema-de-versionamento)
- [Política de Suporte](#politica-de-suporte)
- [Laravel 10](#laravel-10)

## Esquema de Versionamento

O Laravel e seus outros pacotes originais seguem o
[Versionamento Semântico](https://semver.org/lang/pt-BR/).
As versões maiores do _framework_ são lançadas todos os anos (por volta do 1º
trimestre), enquanto as versões menores e de correções podem ser lançadas
semanalmente.
Versões menores e de correções **nunca** devem conter alterações significativas.

Ao referenciar o _framework_ Laravel e seus componentes a partir da sua
aplicação ou pacote, você deve sempre usar uma restrição de versão como `^10.0`,
uma vez que as principais versões do Laravel incluem alterações significativas.
No entanto, nos esforçamos para sempre garantir que você possa atualizar para
uma nova versão maior em um dia ou menos.

### Argumentos Nomeados

[Argumentos nomeados](/docs/php/doc/8/functions.arguments.html#functions.named-arguments)
não são cobertos pelas diretrizes de compatibilidade com versões anteriores do
Laravel.
Podemos optar por renomear os argumentos de funções quando necessário para
melhorar a base de código do Laravel.
Portanto, o uso de argumentos nomeados ao chamar métodos do Laravel deve ser
feito com cautela e com o entendimento de que os nomes dos parâmetros podem
mudar no futuro.

## Política de Suporte

Para todas as versões do Laravel, as correções de falhas são fornecidas por 18
meses e as correções de segurança são fornecidas por 2 anos.
Para todas as bibliotecas adicionais, incluindo Lumen, apenas a versão maior
mais recente recebe correções de falhas.
Além disso, revise as versões do banco de dados
[suportadas pelo Laravel](../database.md#introduction).

| Versão |  PHP (*)  |       Lançamento        | Correções de falhas até | Correções de segurança até |
|:------:|:---------:|:-----------------------:|:-----------------------:|:--------------------------:|
|   8    | 7.3 - 8.1 |  8 de setembro de 2020  |   26 de julho de 2022   |   24 de janeiro de 2023    |
|   9    | 8.0 - 8.2 | 8 de fevereiro de 2022  |   8 de agosto de 2023   |   6 de fevereiro de 2024   |
|   10   | 8.1 - 8.3 | 14 de fevereiro de 2023 |   6 de agosto de 2024   |   4 de fevereiro de 2025   |
|   11   | 8.2 - 8.3 |   12 de março de 2024   |   5 de agosto de 2025   |   3 de fevereiro de 2026   |

<div class="version-colors">
  <div class="end-of-life">
    <div class="color-box"></div>
    <div>Fim de vida</div>
  </div>
  <div class="security-fixes">
    <div class="color-box"></div>
    <div>Apenas correções de segurança</div>
  </div>
</div>

(*) Versões suportadas do PHP

## Laravel 10

Como você deve saber, o Laravel fez a transição para versões anuais com o
lançamento do Laravel 8.
Anteriormente, as versões principais eram lançadas a cada 6 meses.
Essa transição visa aliviar a carga de manutenção da comunidade e desafiar nosso
time de desenvolvimento a lançar novos recursos incríveis e poderosos sem
introduzir alterações significativas.
Portanto, lançamos uma variedade de recursos robustos no Laravel 9 sem quebrar a
compatibilidade com versões anteriores.

Portanto, esse compromisso de lançar novos recursos excelentes durante a versão
atual provavelmente fará com que futuras versões “principais” sejam usadas
principalmente para tarefas de “manutenção”, como atualização das dependências
originais, que podem ser vistas nestas notas de versão.

O Laravel 10 continua as melhorias feitas no Laravel 9.x, introduzindo tipos de
argumentos e retorno em todos os métodos do esqueleto da aplicação, bem como em
todos os arquivos de exemplo usados para gerar classes em todo o _framework_.
Além disso, foi introduzida uma nova camada de abstração amigável à pessoa
desenvolvedora para iniciar e interagir com processos externos.
Além disso, o Laravel Pennant foi introduzido para fornecer uma abordagem
maravilhosa para gerenciar as “_flags_ de recursos” da sua aplicação.

### PHP 8.1 {: #php-8-1 }

O Laravel 10.x requer no mínimo a versão 8.1 do PHP.

### Tipos

_O esqueleto da aplicação e as declarações de tipo dos arquivos de exemplos
foram contribuídas por [Nuno Maduro](https://github.com/nunomaduro)_.

Em seu lançamento inicial, o Laravel utilizou todos os recursos de declaração de
tipo disponíveis no PHP na época.
No entanto, muitos novos recursos foram adicionados ao PHP nos anos seguintes,
incluindo declarações adicionais de tipos primitivos, tipos de retorno e tipos
de união.

O Laravel 10.x atualiza completamente o esqueleto da aplicação e todos os
arquivos de exemplo utilizados pelo _framework_ para introduzir tipos nos
argumentos e no retorno de todas as assinaturas de métodos.
Além disso, informações estranhas de declaração de tipo em “blocos de
documentação” foram excluídas.

Esta mudança é totalmente compatível com aplicações existentes.
Portanto, as aplicações existentes que não possuem essas declarações de tipo
continuarão funcionando normalmente.

### Laravel Pennant

_O Laravel Pennant foi desenvolvido por
[Tim MacDonald](https://github.com/timacdonald)_.

Um novo pacote original, Laravel Pennant, foi lançado.
O Laravel Pennant oferece uma abordagem leve e simplificada para gerenciar as
_flags_ de recursos da sua aplicação.
Pronto para uso, o Pennant inclui um _driver_ `array` em memória e um _driver_
`database` para armazenamento persistente de recursos.

Os recursos podem ser facilmente definidos através do método `Feature::define`:

```php
use Laravel\Pennant\Feature;
use Illuminate\Support\Lottery;

Feature::define('novo-fluxo-de-integracao', function () {
    return Lottery::odds(1, 10);
});
```

Depois que um recurso for definido, você poderá determinar facilmente se a
pessoa usuária atual tem acesso ao recurso específico:

```php
if (Feature::active('novo-fluxo-de-integracao')) {
    // ...
}
```

Claro, por conveniência, diretivas do Blade também estão disponíveis:

```bladehtml
@feature('novo-fluxo-de-integracao')
    <div>
        <!-- ... -->
    </div>
@endfeature
```

O Pennant oferece uma variedade de APIs e recursos mais avançados.
Para obter mais informações, consulte a
[documentação abrangente do Pennant](../pennant.md).

### Interação com Processos

_A camada de abstração de processos foi contribuída por
[Nuno Maduro](https://github.com/nunomaduro) e
[Taylor Otwell](https://github.com/taylorotwell)_.

O Laravel 10.x introduz uma bela camada de abstração para iniciar e interagir
com processos externos usando uma nova fachada `Process`:

```php
use Illuminate\Support\Facades\Process;

$result = Process::run('ls -la');

return $result->output();
```

Os processos podem até ser iniciados em grupos, permitindo a execução e o
gerenciamento convenientes de processos concorrentes:

```php
use Illuminate\Process\Pool;
use Illuminate\Support\Facades\Process;

[$first, $second, $third] = Process::concurrently(function (Pool $pool) {
    $pool->command('cat primeiro.txt');
    $pool->command('cat segundo.txt');
    $pool->command('cat terceiro.txt');
});

return $first->output();
```

Além disso, os processos podem ser simulados para testes convenientes:

```php
Process::fake();

// ...

Process::assertRan('ls -la');
```

Para obter mais informações sobre a interação com processos, consulte a
[documentação abrangente de processos](../processes.md).

### Criação de Perfis de Testes

_A criação de perfis de testes foi contribuída por
[Nuno Maduro](https://github.com/nunomaduro)_.

O comando `test` do Artisan recebeu uma nova opção `--profile` que permite
identificar facilmente os testes mais lentos da sua aplicação:

```shell
php artisan test --profile
```

Por conveniência, os testes mais lentos serão exibidos diretamente na saída da
CLI:

<img
alt="Captura de tela da execução do comando 'php artisan test --profile'"
src="https://user-images.githubusercontent.com/5457236/217328439-d8d983ec-d0fc-4cde-93d9-ae5bccf5df14.png" />

### Geração Automática de Código do Pest

Por padrão, os novos projetos Laravel agora podem ser criados com geração
automática de código de testes do Pest.
Para ativar esse recurso, forneça a _flag_ `--pest` ao criar uma nova aplicação
usando o instalador do Laravel:

```shell
laravel new aplicacao-exemplo --pest
```

### Prompts da CLI do Gerador

_Os prompts da CLI do gerador foram contribuídos por
[Jess Archer](https://github.com/jessarcher)_.

Para melhorar a experiência da pessoa desenvolvedora do _framework_, todos os
comandos `make` nativos do Laravel não requerem mais nenhuma entrada.
Se os comandos forem invocados sem entrada, os argumentos necessários serão
solicitados:

```shell
php artisan make:controller
```

### Melhorias Visuais no Horizon / Telescope

O [Horizon](../horizon.md) e o [Telescope](../telescope.md) foram atualizados
com uma aparência nova e moderna, incluindo tipografia, espaçamento e _design_
aprimorados:

<img
alt="Captura de tela do novo design do Horizon"
src="/content/img/docs/laravel/laravel/doc/10.x/horizon-example.png">
