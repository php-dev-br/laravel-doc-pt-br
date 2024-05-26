<!-- source_url: https://github.com/laravel/docs/blob/11.x/contributions.md -->
<!-- revision: fbf431b3d30df9fbb3ac58194c752619b6c354a2 -->
<!-- status: wip -->

# Guia de Contribuição

- [Relatório de Erro](#relatorio-de-erro)
- [Perguntas de Suporte](#perguntas-de-suporte)
- [Discussão do Desenvolvimento Principal](#discussao-do-desenvolvimento-principal)
- [Which Branch?](#which-branch)
- [Compiled Assets](#compiled-assets)
- [Security Vulnerabilities](#security-vulnerabilities)
- [Coding Style](#coding-style)
  - [PHPDoc](#phpdoc)
  - [StyleCI](#styleci)
- [Code of Conduct](#code-of-conduct)

## Relatório de Erro

Para encorajar a colaboração ativa, o Laravel incentiva fortemente _pull
requests_, não apenas relatórios de erro.
As _pull requests_ só serão revisadas quando marcadas como "prontas para
revisão" (não no estado "rascunho") e todos os testes de novos recursos forem
aprovados.
_Pull requests_ persistentes e inativas deixadas no estado "rascunho" serão
encerradas após alguns dias.

No entanto, se você enviar um relatório de erro, sua _issue_ deverá conter um
título e uma descrição clara do problema.
Você também deve incluir o máximo possível de informações relevantes e um
exemplo de código que demonstre o problema.
O objetivo de um relatório de erro é facilitar para você - e para outras
pessoas - a replicação do erro e o desenvolvimento de uma correção.

Lembre-se de que os relatórios de erros são criados na esperança de que outras
pessoas com o mesmo problema possam colaborar com você para resolvê-lo.
Não espere que haja automaticamente qualquer atividade no relatório do erro ou
que outras pessoas corram para corrigi-lo.
Criar um relatório de erro permite ajudar você e outras pessoas a começarem a
corrigir o problema.
Se quiser contribuir, você pode ajudar corrigindo
[qualquer _issue_ listada nos rastreadores de _issues_ do
GitHub](https://github.com/issues?q=is%3Aopen+is%3Aissue+label%3Abug+user%3Alaravel).
Você deve estar autenticado no GitHub para visualizar todas as _issues_ do
Laravel.

Se você notar avisos de DocBlock inadequados, do PHPStan ou da IDE ao usar o
Laravel, não crie uma _issue_ no GitHub.
Em vez disso, envie uma _pull request_ para corrigir o problema.

O código-fonte do Laravel é gerenciado no GitHub e existem repositórios para
cada um dos projetos do Laravel:

- [Aplicação Laravel](https://github.com/laravel/laravel)
- [Laravel Art](https://github.com/laravel/art)
- [Documentação do Laravel](https://github.com/laravel/docs)
- [Laravel Dusk](https://github.com/laravel/dusk)
- [Laravel Cashier Stripe](https://github.com/laravel/cashier)
- [Laravel Cashier Paddle](https://github.com/laravel/cashier-paddle)
- [Laravel Echo](https://github.com/laravel/echo)
- [Laravel Envoy](https://github.com/laravel/envoy)
- [Laravel Folio](https://github.com/laravel/folio)
- [_Framework_ Laravel](https://github.com/laravel/framework)
- [Laravel Homestead](https://github.com/laravel/homestead)
- [_Scripts_ de Construção do Laravel
  Homestead](https://github.com/laravel/settler)
- [Laravel Horizon](https://github.com/laravel/horizon)
- [Laravel Jetstream](https://github.com/laravel/jetstream)
- [Laravel Passport](https://github.com/laravel/passport)
- [Laravel Pennant](https://github.com/laravel/pennant)
- [Laravel Pint](https://github.com/laravel/pint)
- [Laravel Prompts](https://github.com/laravel/prompts)
- [Laravel Reverb](https://github.com/laravel/reverb)
- [Laravel Sail](https://github.com/laravel/sail)
- [Laravel Sanctum](https://github.com/laravel/sanctum)
- [Laravel Scout](https://github.com/laravel/scout)
- [Laravel Socialite](https://github.com/laravel/socialite)
- [Laravel Telescope](https://github.com/laravel/telescope)
- [_Site_ do Laravel](https://github.com/laravel/laravel.com-next)

## Perguntas de Suporte

Os rastreadores de _issues_ do Laravel no GitHub não se destinam a fornecer
ajuda ou suporte ao Laravel.
Em vez disso, use um dos seguintes canais:

- [Discussões do GitHub](https://github.com/laravel/framework/discussions)
- [Fóruns do Laracasts](https://laracasts.com/discuss)
- [Fóruns do Laravel.io](https://laravel.io/forum)
- [StackOverflow](https://stackoverflow.com/questions/tagged/laravel)
- [Discord](https://discord.gg/laravel)
- [Larachat](https://larachat.co)
- [IRC](https://web.libera.chat/?nick=artisan&channels=#laravel)

## Discussão do Desenvolvimento Principal

Você pode propor novos recursos ou melhorias do comportamento existente do
Laravel no
[fórum de discussão do repositório](https://github.com/laravel/framework/discussions)
do _framework_ Laravel no GitHub.
Se você propor um novo recurso, esteja disposto a implementar pelo menos parte
do código que seria necessário para completar o recurso.

Discussões informais sobre erros, novos recursos e implementação de recursos
existentes ocorre no canal `#internals` do
[servidor Discord do Laravel](https://discord.gg/laravel).
Taylor Otwell, o mantenedor do Laravel, normalmente está presente no canal
durante a semana das 8h às 17h (UTC-06:00 ou América/Chicago), e esporadicamente
presente no canal em outros horários.

## Qual _Branch_?

**Todas** as correções de erros devem ser enviadas para a versão mais recente
que suporta correções de erros (atualmente `10.x`).
Correções de erros **nunca** devem ser enviadas para o _branch_ `master`, a
menos que corrijam recursos que existem apenas na próxima versão.

Recursos **secundários** que são **totalmente compatíveis com versões
anteriores** da versão atual podem ser enviados para o _branch_ estável mais
recente (atualmente `11.x`).

Novos recursos **principais** ou recursos com alterações significativas devem
sempre ser enviados para o _branch_ `master`, que contém a próxima versão.

## Arquivos Compilados

Se você estiver enviando uma alteração que afetará um arquivo compilado, como a
maioria dos arquivos em `resources/css` ou `resources/js` do repositório
`laravel/laravel`, não envie os arquivos compilados.
Devido ao seu grande tamanho, eles não podem ser revisados de forma realista por
um mantenedor.
Isso poderia ser explorado como uma forma de injetar código malicioso no
Laravel.
Para defensivamente evitar isso, todos os arquivos compilados serão gerados e
terão o _commit_ feito pelos mantenedores do Laravel.

## Vulnerabilidades de Segurança

Se você descobrir uma vulnerabilidade de segurança no Laravel, envie um email
para Taylor Otwell em
<a href="mailto:taylor@laravel.com">taylor@laravel.com</a>.
Todas as vulnerabilidades de segurança serão prontamente resolvidas.

## Estilo de Código

O Laravel segue o padrão de código
[PSR-2](https://github.com/php-fig/fig-standards/blob/master/accepted/PSR-2-coding-style-guide.md)
e o padrão de carregamento automático
[PSR-4](https://github.com/php-fig/fig-standards/blob/master/accepted/PSR-4-autoloader.md).

### PHPDoc

Abaixo está um exemplo de um bloco de documentação válido do Laravel.
Observe que o atributo `@param` é seguido por dois espaços, o tipo de argumento,
mais dois espaços e, finalmente, o nome da variável:

```php
/**
 * Registra uma ligação com o contêiner.
 *
 * @param  string|array  $abstract
 * @param  \Closure|string|null  $concrete
 * @param  bool  $shared
 * @return void
 *
 * @throws \Exception
 */
public function bind($abstract, $concrete = null, $shared = false)
{
    // ...
}
```

Quando os atributos `@param` ou `@return` forem redundantes devido ao uso de
tipos nativos, eles podem ser removidos:

```php
/**
 * Executa o trabalho.
 */
public function handle(AudioProcessor $processor): void
{
    //
}
```

No entanto, quando o tipo nativo for genérico, especifique o tipo genérico
através do uso dos atributos `@param` ou `@return`:

```php
/**
 * Obtém os anexos da mensagem.
 *
 * @return array<int, \Illuminate\Mail\Mailables\Attachment>
 */
public function attachments(): array
{
    return [
        Attachment::fromStorage('/caminho/para/o/arquivo'),
    ];
}
```

### StyleCI

Não se preocupe se o estilo do seu código não for perfeito!
O [StyleCI](https://styleci.io/) irá fazer o _merge_ automaticamente de
quaisquer
correções de estilo no repositório Laravel após o _merge_ das _pull requests_.
Isso nos permite focar no conteúdo da contribuição e não no estilo do código.

## Código de Conduta

O código de conduta do Laravel é derivado do código de conduta do Ruby.
Quaisquer violações do código de conduta podem ser relatadas a Taylor Otwell
(taylor@laravel.com):

- Os participantes serão tolerantes com pontos de vista opostos.
- Os participantes devem garantir que a sua linguagem e ações estão livres de
  ataques pessoais e comentários pessoais depreciativos.
- Ao interpretar as palavras e ações dos outros, os participantes devem sempre
  assumir boas intenções.
- Comportamentos que possam ser razoavelmente considerados assédio não serão
  tolerados.
