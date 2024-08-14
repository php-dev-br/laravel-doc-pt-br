---
source_url: https://github.com/laravel/docs/blob/11.x/lifecycle.md
revision: 51c2103e2594f1655666b965fbfd5893a0cbec1b
status: ready
---

# Ciclo de Vida da Requisição

- [Introdução](#introducao)
- [Visão Geral do Ciclo de Vida](#visao-geral-do-ciclo-de-vida)
    - [Primeiros Passos](#primeiros-passos)
    - [_Kernels_ HTTP / do Console](#kernels-http-e-do-console)
    - [Provedores de Serviço](#provedores-de-servico)
    - [Roteamento](#roteamento)
    - [Concluindo](#concluindo)
- [Foco em Provedores de Serviço](#foco-em-provedores-de-servico)

## Introdução

Ao usar qualquer ferramenta no mundo real, você se sente mais confiante se
entender como essa ferramenta funciona.
O desenvolvimento de aplicações não é diferente.
Quando você entende como suas ferramentas de desenvolvimento funcionam, você se
sente mais confortável e confiante ao usá-las.

O objetivo deste documento é fornecer uma visão geral de alto nível de como o
_framework_ Laravel funciona.
Ao conhecer melhor o _framework_ de forma geral, tudo parecerá menos mágico e
você terá mais confiança na construção das suas aplicações.
Se você não entender todos os termos imediatamente, não desanime!
Apenas tente entender o básico do que está acontecendo e seu conhecimento
aumentará à medida que você explorar outras seções da documentação.

## Visão Geral do Ciclo de Vida

### Primeiros Passos

O ponto de entrada para todas as requisições para uma aplicação Laravel é o
arquivo `public/index.php`.
Todas as requisições são direcionadas para este arquivo pela configuração do seu
servidor _web_ (Apache/Nginx).
O arquivo `index.php` não contém muito código.
Em vez disso, é um ponto de partida para carregar o restante do _framework_.

O arquivo `index.php` carrega a definição do carregador automático gerada pelo
Composer e, em seguida, recupera uma instância da aplicação Laravel de
`bootstrap/app.php`.
A primeira ação realizada pelo próprio Laravel é criar uma instância da
aplicação/[contêiner de serviço](conteiner.md).

### _Kernels_ HTTP / do Console {- #kernels-http-e-do-console }

Em seguida, a requisição recebida é enviada para o _kernel_ HTTP ou para o
_kernel_ do console, usando os métodos `handleRequest` ou `handleCommand` da
instância da aplicação, dependendo do tipo de requisição que entrou na
aplicação.
Esses dois _kernels_ servem como local central através do qual fluem todas as
requisições.
Por enquanto, vamos nos concentrar apenas no _kernel_ HTTP, que é uma instância
de `Illuminate\Foundation\Http\Kernel`.

O _kernel_ HTTP define um _array_ de `bootstrappers` que serão executados antes
da execução da requisição.
Esses _bootstrappers_ configuram o tratamento de erros, configuram o _log_,
[detectam o ambiente da aplicação](../comecando/configuracao.md#configuracao-do-ambiente),
e executam outras tarefas que precisam ser executadas antes que a requisição
seja realmente tratada.
Normalmente, essas classes lidam com configurações internas do Laravel com as
quais você não precisa se preocupar.

O _kernel_ HTTP também é responsável por passar a requisição pela pilha de
_middlewares_ da aplicação.
Esses _middlewares_ lidam com a leitura e gravação da
[sessão HTTP](../session.md), determinando se a aplicação está em modo de
manutenção, [verificando o token CSRF](../csrf.md) e muito mais.
Falaremos mais sobre isso em breve.

A assinatura do método `handle` do _kernel_ HTTP é bem simples: ele recebe uma
requisição e retorna uma resposta.
Pense no _kernel_ como uma grande caixa preta que representa toda a aplicação.
Alimente-o com requisições HTTP e ele retornará respostas HTTP.

### Provedores de Serviço

Uma das ações mais importantes de inicialização do _kernel_ é carregar os
[provedores de serviço](provedores.md) da sua aplicação.
Os provedores de serviço são responsáveis pela inicialização de todos os vários
componentes do _framework_, como componentes de banco de dados, fila, validação
e de roteamento.

O Laravel irá percorrer esta lista de provedores e instanciar cada um deles.
Após instanciar os provedores, o método `register` será chamado em todos os
provedores.
Então, uma vez registrados todos os provedores, o método `boot` será chamado em
cada provedor.
Isso é necessário para que os provedores de serviço possam depender de cada
ligação de contêiner registrada e disponibilizada no momento em que o método
`boot` for executado.

Essencialmente, todos os principais recursos oferecidos pelo Laravel são
inicializados e configurados por um provedor de serviço.
Como eles inicializam e configuram tantos recursos oferecidos pelo _framework_,
os provedores de serviço são o aspecto mais importante de todo o processo de
inicialização do Laravel.

Embora o _framework_ use internamente dezenas de provedores de serviço, você
também tem a opção de criar o seu próprio.
Você pode encontrar uma lista dos provedores de serviço definidos pela pessoa
desenvolvedora ou de terceiros que sua aplicação está usando no arquivo
`bootstrap/providers.php`.

### Roteamento

Depois que a aplicação tiver sido inicializada e todos os provedores de serviço
registrados, a `Request` será entregue ao roteador para despacho.
O roteador despachará a requisição para uma rota ou controlador, e também
executará qualquer _middleware_ específico da rota.

Os _middlewares_ fornecem um mecanismo conveniente para filtrar ou examinar
requisições HTTP que entram na sua aplicação.
Por exemplo, o Laravel inclui um _middleware_ que verifica se a pessoa usuária
da sua aplicação está autenticada.
Caso a pessoa usuária não esteja autenticada, o _middleware_ irá redirecioná-la
para a tela de _login_.
Entretanto, se a pessoa usuária estiver autenticada, o _middleware_ permitirá
que a requisição siga adiante na aplicação.
Alguns _middlewares_ são atribuídos a todas as rotas da aplicação, como
`PreventRequestsDuringMaintenance`, enquanto outros são atribuídos apenas a
rotas ou grupos de rotas específicos.
Você pode aprender mais sobre _middlewares_ lendo a
[documentação completa de middlewares](../middleware.md).

Se a requisição passar por todos os _middlewares_ atribuídos à rota
correspondente, a rota ou método controlador será executado e a resposta
retornada pela rota ou método controlador será enviada de volta através da
cadeia de _middlewares_ da rota.

### Concluindo

Depois que a rota ou o método controlador retornar uma resposta, a resposta
retornará através dos _middlewares_ da rota, dando à aplicação a chance de
modificar ou examinar a resposta de saída.

Finalmente, uma vez que a resposta retorne através dos _middlewares_, o método
`handle` do _kernel_ HTTP retorna o objeto de resposta para o `handleRequest` da
instância da aplicação, e esse método chama o método `send` na resposta
retornada.
O método `send` envia o conteúdo da resposta ao navegador da pessoa usuária.
Agora completamos nossa jornada por todo o ciclo de vida da requisição do
Laravel!

## Foco em Provedores de Serviço

Os provedores de serviço são realmente a chave para inicializar uma aplicação
Laravel.
A instância da aplicação é criada, os provedores de serviço são registrados e a
requisição é entregue à aplicação inicializada.
É realmente simples assim!

Ter um bom entendimento de como uma aplicação Laravel é construída e
inicializada por meio dos provedores de serviço é muito valioso.
Os provedores de serviço definidos pela pessoa desenvolvedora da sua aplicação
são armazenados no diretório `app/Providers`.

Por padrão, o `AppServiceProvider` está bem vazio.
Esse provedor é um ótimo lugar para adicionar a inicialização e as ligações de
contêiner de serviço da sua aplicação.
Para aplicações grandes, você pode criar vários provedores de serviço, cada um
com uma inicialização mais granular para os serviços específicos usados pela sua
aplicação.
