# Principais Recursos do PHP 8.2
<a id="topo"></a>

[&larr; Voltar](index.md)

**Índice**
- [Classes Somente Leitura](#item-1)
- [Tipos DNF (Disjunctive Normal Form)](#item-2)
- ['null', 'false' e 'true' são Tipos Autônomos](#item-3)
- [Nova Extensão 'Random'](#item-4)
- [Constantes em Traits](#item-5)
- [Propriedades Dinâmicas Obsoletas](#item-6)
- [Propriedades de Enumerações em Expressões Constantes](#item-7)


<a id="item-1"></a>

## - Classes Somente Leitura
[Topo &uarr;](#topo)

Uma classe pode ser marcada com o modificador 'readonly'. Marcar uma classe como 
'readonly' irá acrescentar o modificador 'readonly' em cada propriedade declarada, 
e prevenir a criação de propriedades dinâmicas. Além disso, será impossível de 
acrescentar suportes a propriedades dinâmicas utilizando o atributo ("anotação") 
'AllowDynamicProperties'. Acrescentar o atributo em classes somente leitura irá 
disparar um erro de compilação.

⚠️ Como as propriedades estáticas ou sem tipo não podem ser marcadas com o 
modificador readonly, as classes somente leitura também não podem declará-las.

### Exemplo
```php
<?php
readonly class BlogData
{
    public string $title;

    public Status $status;

    public function __construct(string $title, Status $status)
    {
        $this->title = $title;
        $this->status = $status;
    }
}
```


<a id="item-2"></a>

## - Tipos DNF (Disjunctive Normal Form)
[Topo &uarr;](#topo)

Tipos DNF nos permite união e interseção de tipos, seguindo uma regra estrita: 
ao combinar tipos de união e interseção, os tipos de interseção devem ser 
agrupados com parênteses.

### Exemplo
```php
<?php
class Foo {
    public function bar((A&B)|null $entity) {
        return $entity;
    }
}
```


<a id="item-3"></a>

## - 'null', 'false' e 'true' são Tipos Autônomos
[Topo &uarr;](#topo)


### Exemplo
```php
<?php
class Falsy
{
    public function alwaysFalse(): false { /* ... */ *}

    public function alwaysTrue(): true { /* ... */ *}

    public function alwaysNull(): null { /* ... */ *}
}
```


<a id="item-4"></a>

## - Nova Extensão 'Random'
[Topo &uarr;](#topo)

A extensão "Random" fornece uma nova API orientada a objetos para geração de 
números aleatórios. Em vez de depender de um gerador de números aleatórios 
globalmente semeado (RNG) usando o algoritmo Mersenne Twister, a API orientada 
a objetos fornece várias classes ("Engines") que fornecem acesso a algoritmos 
modernos que armazenam seu estado em objetos para permitir várias sequências 
semeáveis ​​independentes .

A classe '\Random\Randomizer' fornece uma interface de alto nível para usar a 
aleatoriedade do mecanismo para gerar um número inteiro aleatório, embaralhar 
um array ou string, selecionar chaves de array aleatórias e muito mais.


<a id="item-5"></a>

## - Constantes em Traits
[Topo &uarr;](#topo)

Você não pode acessar a constante através do nome da Trait, mas você pode acessar 
a constante através da classe que usa a Trait.


### Exemplo
```php
<?php
trait Foo
{
    public const CONSTANT = 1;
}

class Bar
{
    use Foo;
}

var_dump(Bar::CONSTANT); // 1
var_dump(Foo::CONSTANT); // Error
```


<a id="item-6"></a>

## - Propriedades Dinâmicas Obsoletas
[Topo &uarr;](#topo)

A criação de propriedades dinâmicas está obsoleta para ajudar a evitar enganos e 
erros de digitação, a menos que a classe opte por usar o atributo ("notação") de 
#[\AllowDynamicProperties]. 'stdClass' permite propriedades dinâmicas.

⚠️ O uso dos métodos mágicos '__get'/'__set' não é afetado por esta alteração.

### Exemplo
```php
<?php
class User
{
    public $name;
}

$user = new User();
$user->last_name = 'Doe'; // Deprecated notice

$user = new stdClass();
$user->last_name = 'Doe'; // Still allowed
```


<a id="item-7"></a>

## - Propriedades de Enumerações em Expressões Constantes
[Topo &uarr;](#topo)

Agora é possível buscar propriedades de Enumerações em expressões constantes.

Como os casos são representados como constantes no próprio 'enum', eles podem ser 
usados como valores estáticos na maioria das expressões constantes: padrões de 
propriedades, padrões de variáveis estáticas, padrões de parâmetros, valores 
constantes globais e de classes. Eles não podem ser usados em outros valores de 
caso 'enum', mas constantes normais podem se referir a um caso 'enum'. 


### Exemplo
```php
<?php

// Esta é uma definição de Enum perfeitamente legal.
enum Direcao implements ArrayAccess
{
    case Cima;
    case Baixo;

    public function offsetExists($offset): bool
    {
        return false;
    }

    public function offsetGet($offset): mixed
    {
        return null;
    }

    public function offsetSet($offset, $value): void
    {
        throw new Exception();
    }

    public function offsetUnset($offset): void
    {
        throw new Exception();
    }
}

class Foo
{
    // Isto é permitido.
    const BAIXO = Direcao::Baixo;

    // Isto não é permitido, pois pode não ser determinístico.
    const CIMA = Direcao::Cima['curta'];
    // Fatal error: Cannot use [] on enums in constant expression
}

// Isto é perfeitamente legal, porque não é uma expressão constante.
$x = Direcao::Cima['curta'];
var_dump("\$x is " . var_export($x, true));

$foo = new Foo();
```