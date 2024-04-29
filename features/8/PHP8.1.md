# Principais Recursos do PHP 8.1
<a id="topo"></a>

[&larr; Voltar](index.md)

**Índice**
- [Enumerações](#item-1)
- [Propriedades Somente Leitura](#item-2)
- [Sintaxe de Callables de Primeira Classe](#item-3)
- ['new' em Inicializadores](#item-4)
- [Tipos de Interseção Puros](#item-5)
- [Tipo de Retorno 'never'](#item-6)
- [Constantes de Classe Finais](#item-7)
- [Notação Explícita de Numeral Octal](#item-8)
- [Fibers](#item-9)
- [Desempacotamento de Array para Arrays com Chaves String](#item-10)
- [Argumentos Nomeados Após Desempacotamento de Argumentos](#item-11)
- ['fputcsv()'](#item-12)
- ['SplFileObject'](#item-13)


<a id="item-1"></a>

## - Enumerações
[Topo &uarr;](#topo)

Use 'enum' em vez de um conjunto de constantes e obtenha validação de forma 
transparente.

### Exemplo
```php
enum Status
{
    case Draft;
    case Published;
    case Archived;
}

function acceptStatus(Status $status) {...}
```


<a id="item-2"></a>

## - Propriedades Somente Leitura
[Topo &uarr;](#topo)

Propriedades somente leitura não podem ser alteradas após a inicialização, ou 
seja, após um valor ser atribuido a elas.
Elas são uma ótima maneira de modelar objetos de valor (Value Objects) e objetos 
de transferência de dados (DTO). Elas também podem ser usadas no lugar de 
propriedades privadas e acessadas diretamente (sem getters), já que seu valor 
não poderá ser alterado.

### Exemplo
```php
class BlogData
{
    public readonly Status $status;
   
    public function __construct(Status $status) 
    {
        $this->status = $status;
    }
}
```


<a id="item-3"></a>

## - Sintaxe de Callables de Primeira Classe
[Topo &uarr;](#topo)

Agora é possível obter a referência de qualquer função. Isso é chamado de sintaxe
de callable de primeira classe.

     As '...' são parte da sintaxe, e não uma omissão. 

### Exemplo
```php
$foo = $this->foo(...);

$fn = strlen(...);
```


<a id="item-4"></a>

## - 'new' em Inicializadores
[Topo &uarr;](#topo)

Objetos agora podem ser usados como valor padrão de parâmetros, variáveis 
estáticas, e constantes globais, bem como em argumentos de atributos.

### Exemplo
```php
class Service 
{
    private Logger $logger;
    
    public function __construct(
        Logger $logger = new NullLogger(),
    ) {
        $this->logger = $logger;
    }
}
```

Isso efetivamente torna possível usar atributos aninhados.

### Exemplo
```php
class User 
{
    #[\Assert\All(
        new \Assert\NotNull,
        new \Assert\Length(min: 5))
    ]
    public string $name = '';
}
```


<a id="item-5"></a>

## - Tipos de Interseção Puros
[Topo &uarr;](#topo)

Use tipos de interseção quando um valor precisa satisfazer múltiplas restrições 
de tipo ao mesmo tempo.

Atualmente não é possível misturar tipos de interseção e união como 'A&B|C'.

### Exemplo
```php
function count_and_iterate(Iterator&Countable $value) {
    foreach ($value as $val) {
        echo $val;
    }

    count($value);
}
```


<a id="item-6"></a>

## - Tipo de Retorno 'never'
[Topo &uarr;](#topo)

Uma função ou método declarada com o tipo 'never' indica que ela não irá retornar 
um valor e irá lançar uma exceção ou terminar a execução do script com uma 
chamada de 'die()', 'exit()', 'trigger_error()', ou algo similar.

### Exemplo
```php
function redirect(string $uri): never {
    header('Location: ' . $uri);
    exit();
}
 
function redirectToLoginPage(): never {
    redirect('/login');
    echo 'Hello'; // <- código morto detectado por análise estática
}
```


<a id="item-7"></a>

## - Constantes de Classe Finais
[Topo &uarr;](#topo)

É possível declarar constantes de classe como 'final', de forma que elas não 
possam ser sobrescritas em classes filhas.

### Exemplo
```php
class Foo
{
    final public const XX = "foo";
}

class Bar extends Foo
{
    public const XX = "bar"; // Fatal error
}
```


<a id="item-8"></a>

## - Notação Explícita de Numeral Octal
[Topo &uarr;](#topo)

Agora é possível escrever números octais com o prefixo explícito '0o'.

### Exemplo
```php
0o16 === 16; // false — não confundir com a notação explícita
0o16 === 14; // true 
```


<a id="item-9"></a>

## - Fibers
[Topo &uarr;](#topo)

Fibers são primitivos para implementar concorrência cooperativa leve. Elas são 
meios de criar blocos de código que podem ser pausados e retomados como Geradores, 
mas de qualquer lugar da pilha de execução. Fibers em si não fornecem concorrência 
magicamente, um laço de eventos ainda é necessário. No entanto, elas permitem que 
implementações bloqueantes e não bloqueantes compartilhem a mesma API.

Fibers permitem livrar-se de código boilerplate visto anteriormente com 
'Promise::then()' ou corrotinas baseadas em Geradores. Bibliotecas geralmente 
constróem abstrações adicionais em torno das Fibers, então não há necessidade 
de interagir com elas diretamente.

### Exemplo
```php
$fiber = new Fiber(function (): void {
   $value = Fiber::suspend('fiber');
   echo "Value used to resume fiber: ", $value, PHP_EOL; // será impresso segundo
});

$value = $fiber->start();

echo "Value from fiber suspending: ", $value, PHP_EOL; // será impresso primeiro

$fiber->resume('test');
```


<a id="item-10"></a>

## - Desempacotamento de Array para Arrays com Chaves String
[Topo &uarr;](#topo)

PHP já suportava o desempacotamento dentro de arrays através do operador de 
espalhamento (spread operator), mas somente se o array tivesse chaves de inteiro. 
Agora também é possível desempacotar arrays com chaves string.

### Exemplo
```php
$arrayA = ['a' => 1];
$arrayB = ['b' => 2];

$result = ['a' => 0, ...$arrayA, ...$arrayB];

// ['a' => 1, 'b' => 2]
```


<a id="item-11"></a>

## - Argumentos Nomeados Após Desempacotamento de Argumentos
[Topo &uarr;](#topo)

Agora é possível especificar argumentos nomeados após um desempacotamento de 
argumentos.

### Exemplo
```php
foo(...$args, nomeado: $arg)
```


<a id="item-13"></a>

## - 'SplFileObject'
[Topo &uarr;](#topo)

'SplFileObject::fputcsv()' agora aceita um novo argumento, 'eol', que permite a 
definição de uma sequência de fim de linha personalizada, o padrão continua o 
mesmo e é "\n". 