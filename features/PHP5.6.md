# Principais Recursos até o PHP 5.6
<a id="topo"></a>

[&larr; Voltar](../README.md)

**Índice**
- [Funções variádicas via "..." (operador "splat"/"spread")](#item-1)
- [Desempacotamento de argumentos via "..." (operador "splat"/"spread")](#item-2)
- [Exponenciação via "**"](#item-3)
- ["use function" e "use const"](#item-4)
- [Suporte a "pgsql" Assíncrono](#item-5)
- [Traits](#item-6)


<a id="item-1"></a>

## - Funções variádicas via "..." (operador "splat"/"spread")
[Topo &uarr;](#topo)

A partir do PHP 5.6 as funções variádicas (funções com listas de argumentos de tamanho variável) 
podem ser implementadas usando o operador "...", em vez de depender da função
"func_get_args()".

### Exemplo
```php
<?php
function f($req, $opt = null, ...$params) {
    // $params é um array contendo os argumentos restantes.
    printf('$req: %d; $opt: %d; número de parâmetros: %d'."\n",
           $req, $opt, count($params));
}

f(1); // imprime "$req: 1; $opt: 0; número de parâmetros: 0"
f(1, 2); // imprime "$req: 1; $opt: 2; número de parâmetros: 0"
f(1, 2, 3); // imprime "$req: 1; $opt: 2; número de parâmetros: 1"
f(1, 2, 3, 4); // imprime "$req: 1; $opt: 2; número de parâmetros: 2"
f(1, 2, 3, 4, 5); // imprime "$req: 1; $opt: 2; número de parâmetros: 3"
?>
```


<a id="item-2"></a>

## - Desempacotamento de argumentos via "..." (operador "splat"/"spread")
[Topo &uarr;](#topo)

Arrays e objetos do tipo "Traversable" podem ser desempacotados em listas de 
argumentos ao chamar funções usando o operador "...". Isso também é conhecido 
como operador "splat"/"spread" em outras linguagens, incluindo o Ruby.

### Exemplo
```php
<?php
function add($a, $b, $c) {
    return $a + $b + $c;
}

$operators = [2, 3];
echo add(1, ...$operators); // imprime "6"
?>
```


<a id="item-3"></a>

## - Exponenciação via "**"
[Topo &uarr;](#topo)

Um operador associativo à direita ("**") foi adicionado para suportar exponenciação, 
juntamente com o operador curto de atribuição "**=".

### Exemplo
```php
<?php
printf("2 ** 3 ==      %d\n", 2 ** 3); // imprime "2 ** 3 ==      8"
printf("2 ** 3 ** 2 == %d\n", 2 ** 3 ** 2); // imprime "2 ** 3 ** 2 == 512"

$a = 2;
$a **= 3;
printf("a ==           %d\n", $a); // imprime "a ==           8"
?>
```


<a id="item-4"></a>

## - "use function" e "use const"
[Topo &uarr;](#topo)

O operador "use" foi estendido para suportar importação de funções e constantes 
em adição à importação de classes. Isso pode ser realizado pelas construções 
"use function" e "use const", respectivamente.

### Exemplo
```php
<?php
namespace Name\Space {
    const FOO = 42;
    function f() { echo __FUNCTION__."\n"; }
}

namespace {
    use const Name\Space\FOO;
    use function Name\Space\f;

    echo FOO."\n"; // imprime "42"
    f(); // imprime "Name\Space\f"
}
?>
```


<a id="item-5"></a>

## - Suporte a "pgsql" Assíncrono
[Topo &uarr;](#topo)

A extensão "pgsql" agora suporta conexões e consultas assíncronas, assim 
habilitando o comportamento sem bloqueio ao interagir com banco de dados 
PostgreSQL. 

Conexões assíncronas podem ser estabalacidas através da constante 
"PGSQL_CONNECT_ASYNC". 

As novas funções "pg_connect_poll()", "pg_socket()", 
"pg_consume_input()" e "pg_flush()" podem ser usadas para lidar com conexões e 
consultas assíncronas. 


<a id="item-6"></a>

## - Traits
[Topo &uarr;](#topo)

Traits é um mecanismo para reúso de código em linguagens de herança simples como 
o PHP. A intenção de uma Trait é reduzir algumas limitações de herança simples 
permitindo que um desenvolvedor reutilize livremente conjuntos de métodos em 
várias classes independentes habitando em diferentes hierarquias de classe.

### Exemplo
```php
<?php
trait Foo {
    public function bar() {/* implementação */}
    public function foobar() {/* implementação */}
}

class BarFoo {
    use Foo;
    /* implementação */
}
```

Um membro (métodos, propriedades) herdado de uma classe base é substituído por 
um membro inserido por uma Trait. Na ordem de precedência, os membros da classe 
atual substituem os métodos da Trait, que por sua vez substituem os métodos 
herdados.

### Exemplo
```php
<?php
class Base {
    public function sayHello() { echo 'Hello '; }
}

trait SayWorld {
    public function sayHello() {
        parent::sayHello();
        echo 'World!';
    }
}

class MyHelloWorld extends Base {
    use SayWorld;
}

$o = new MyHelloWorld();
$o->sayHello(); // imprime: "Hello World!"
```