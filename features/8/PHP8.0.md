# Principais Recursos do PHP 8

[&larr; Voltar](index.md)

**Índice**
- [Compilação Just-In-Time (JIT)](#item-1)
- [Argumentos Nomeados](#item-2)
- [Atributos](#item-3)
- [Promoção de Propriedade de Construtor](#item-4)
- [União de Tipos](#item-5)
- [Expressão "match"](#item-6)
- [Operador 'nullsafe'](#item-7)
- [Comparações mais Inteligentes entre Strings e Números RFC](#item-8)
- [Erros Consistentes para Tipos de Dados em Funções Internas RFC](#item-9)
- [Argumento Variádico](#item-10)
- [Tipo de Retorno 'static'](#item-11)
- [Nome Totalmente Qualificado da Classe via Objeto](#item-12)
- ['new' e 'instanceof' com Expressões Arbitrárias](#item-13)
- [Interface Stringable](#item-14)
- [Traits e Métodos Privados Abstratos](#item-15)
- ["throw" como Expressão](#item-16)
- ["catch (Exception)"](#item-17)
- [O Tipo 'mixed'](#item-18)
- [Métodos Privados da Classe Pai](#item-19)
- [A Classe InternalIterator](#item-20)



<a id="item-1"></a>

## - Compilação Just-In-Time (JIT)
PHP 8 apresenta dois motores de compilação JIT: Tracing JIT e Function JIT. 
Tracing JIT, o mais promissor dos dois, mostra desempenho cerca de 3 vezes 
melhor em benchmarks sintéticos e melhoria de 1,5 a 2 vezes em alguns aplicativos 
específicos de longa execução. 
O desempenho típico das aplicações está no mesmo nível do PHP 7.4. 


<a id="item-2"></a>

## - Argumentos Nomeados
Especifique apenas os parâmetros obrigatórios, pulando os 
opcionais. Os argumentos são independentes da ordem e 
autodocumentados.

### Exemplo
```php
htmlspecialchars($string, double_encode: false);
```


<a id="item-3"></a>

## - Atributos
Em vez de anotações PHPDoc, agora pode-se usar metadados 
estruturados com a sintaxe nativa do PHP.

### Exemplo
```php
class PostsController
{
    #[Route("/api/posts/{id}", methods: ["GET"])]
    public function get($id) { /* ... */ }
}
```


<a id="item-4"></a>

## - Promoção de Propriedade de Construtor
Menos código boilerplate para definir e inicializar propriedades.
Os parâmetros do __construct() quando especificado a visibilidade, são promovidos 
a propriedade da classe.

### Exemplo
```php
class Point {
  public function __construct(
    public float $x = 0.0,
    public float $y = 0.0,
    public float $z = 0.0,
  ) {}
}
```


<a id="item-5"></a>

## - União de Tipos
Em vez de anotações PHPDoc para uma combinação de tipos, você pode usar declarações 
de união de tipos nativa que são validados em tempo de execução.

### Exemplo
```php
class Number {
  public function __construct(
    private int|float $number
  ) {}
}

new Number('NaN'); // TypeErrors
```


<a id="item-6"></a>

## - Expressão "match"
A nova expressão match é semelhante ao switch e tem os seguintes recursos:

- Match é uma expressão, o que significa que seu resultado pode ser armazenado 
em uma variável ou retornado;
- Match suporta apenas expressões de uma linha e não precisa de uma declaração 
break;
- Match faz comparações estritas.

### Exemplo
```php
echo match (8.0) {
  '8.0' => "Oh no!",
  8.0 => "This is what I expected",
};
//> imprime 'This is what I expected'
```


<a id="item-7"></a>

## - Operador 'nullsafe' (?->)
Em vez de verificar condições nulas, agora você pode usar uma cadeia de chamadas 
com o novo operador nullsafe. Quando a avaliação de um elemento da cadeia falha, 
a execução de toda a cadeia é abortada e toda a cadeia é avaliada como nula.

### Exemplo
```php
$country = $session?->user?->getAddress()?->country;
```


<a id="item-8"></a>

## - Comparações mais Inteligentes entre Strings e Números RFC
Ao comparar com uma string numérica, o PHP 8 usa uma comparação numérica. Caso 
contrário, ele converte o número em uma string e usa uma comparação de string.


### Exemplo
```php
0 == 'foobar' // false
```


<a id="item-9"></a>

## - Erros Consistentes para Tipos de Dados em Funções Internas RFC
A maioria das funções internas agora lançam uma exceção Error se a validação do 
parâmetro falhar, não lança mais warnings e notices.

### Exemplo
```php
strlen([]); // TypeError: strlen(): Argument #1 ($str) must be of type string, array given

array_chunk([], -1); // ValueError: array_chunk(): Argument #2 ($length) must be greater than 0
```


<a id="item-10"></a>

## - Argumento Variádico
Qualquer número de parâmetros de função agora pode ser substituído por um 
argumento variádico, desde que os tipos sejam compatíveis. 

Por exemplo, o seguinte código agora é permitido: 

### Exemplo
```php
<?php
class A {
     public function method(int $many, string $parameters, $here) {}
}
class B extends A {
     public function method(...$everything) {}
}
```


<a id="item-11"></a>

## - Tipo de Retorno 'static'
'static' (como em "vinculação estática tardia") agora pode ser usado como um 
tipo de retorno.

### Exemplo
```php
<?php
class Test {
     public function create(): static {
          return new static();
     }
}
```


<a id="item-12"></a>

## - Nome Totalmente Qualificado da Classe via Objeto
Agora é possível buscar o nome da classe de um objeto usando "$object::class". 
O resultado é o mesmo que get_class($object). 


<a id="item-13"></a>

## - 'new' e 'instanceof' com Expressões Arbitrárias
'new' e 'instanceof' agora podem ser usados com expressões arbitrárias 
(se a expressão produzir uma string [as expressões devem estar em parênteses]), 
usando "new (expressão)(...$argsConstrutor)" e "$obj instanceof (expressão)". 

### Exemplo
```php
class ClasseA extends \stdClass {}
class ClasseB extends \stdClass {}
class ClasseC extends ClasseB {}
class ClasseD extends ClasseA {}

function obterAlgumaClasse(): string
{
    return 'ClasseA';
}

var_dump(new (obterAlgumaClasse())); // imprime "object(ClasseA)#1 (0) {}"
var_dump(new ('Classe' . 'B')); // imprime "object(ClasseB)#1 (0) {}"
var_dump(new ('Classe' . 'C')); // imprime "object(ClasseC)#1 (0) {}"
var_dump(new (ClasseD::class)); // imprime "object(ClasseD)#1 (0) {}"
```


<a id="item-14"></a>

## - Interface Stringable
Adicionada interface Stringable, que é implementada automaticamente se uma 
classe definir um método "__toString()". 


<a id="item-15"></a>

## - Traits e Métodos Privados Abstratos
As Traits agora podem definir métodos privados abstratos. Tais métodos devem ser 
implementados pela classe que usa o "trait". 


<a id="item-16"></a>

## - "throw" como Expressão
'throw' agora pode ser usado como uma expressão. Isso permite usos como: 

### Exemplo
```php
<?php
$fn = fn() => throw new Exception('Exception in arrow function');
$user = $session->user ?? throw new Exception('Must have user');
```


<a id="item-17"></a>

## - "catch (Exception)"
Agora é possível escrever "catch (Exception)" para capturar uma exceção sem 
armazená-la em uma variável. 


<a id="item-18"></a>

## - O Tipo 'mixed'
O suporte para tipo mixed foi adicionado.


<a id="item-19"></a>

## - Métodos Privados da Classe Pai
Os métodos privados declarados em uma classe pai não impõem mais nenhuma regra 
de herança nos métodos de uma classe filha (com exceção dos construtores 
privados finais)


<a id="item-20"></a>

## - A Classe InternalIterator
Foi adicionada a classe para facilitar a implementação de IteratorAggregate para 
classes internas. 