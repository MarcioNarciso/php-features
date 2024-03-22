# Principais Recursos do PHP 7.1

**Índice**
- [Tipos Anuláveis](#item-1)
- [Funções "void"](#item-2)
- [Desestruturação Simétrica de Array](#item-3)
- [Visibilidade de Constante de Classe](#item-4)
- [Pseudo-tipo "iterable"](#item-5)
- [Manipulação de Exceção de Captura Múltipla](#item-6)
- [Suporte para Chaves em "list()"](#item-7)
- [Suporte a Deslocamento Negativo em string](#item-8)
- [Conversão de callables para Closures com Closure::fromCallable()](#item-9)
- [Manipulação Assíncrona de sinal](#item-10)
- [Operadores de números e números malformados](#item-11)


<a id="item-1"></a>

## - Tipos Anuláveis (Nullable Types)
As declarações de tipos para parâmetros e retorno agora podem ser marcadas como 
anuláveis (nullable) prefixando o nome do tipo com um ponto de interrogação (?).

### Exemplo
```php
<?php

function testReturn(): ?string
{
    return 'elePHPant';
}

var_dump(testReturn());

function testReturn(): ?string
{
    return null;
}

var_dump(testReturn());

function test(?string $name)
{
    var_dump($name);
}

test('elePHPant');
test(null);
test();
```


<a id="item-2"></a>

## - Funções "void"
Um tipo de retorno vazio (void) foi introduzido. Funções declaradas com void 
como seu tipo de retorno precisa tanto omitir sua declaração de retorno (return) 
completamente ou usar um retorno vazio. Null não é um valor de retorno válido 
para uma função void.

### Exemplo
```php
<?php
function swap(&$left, &$right): void
{
    if ($left === $right) {
        return;
    }

    $tmp = $left;
    $left = $right;
    $right = $tmp;
}

$a = 1;
$b = 2;
var_dump(swap($a, $b), $a, $b);
```


<a id="item-3"></a>

## - Desestruturação Simétrica de Array
A sintaxe reduzida de um array ("[]") agora pode ser usada para desestruturar 
arrays para atribuições (incluindo dentro do foreach), como uma alternativa à 
sintaxe existente "list()", que ainda é suportada.

### Exemplo
```php
<?php
$data = [
    [1, 'Tom'],
    [2, 'Fred'],
];

// estilo list()
list($id1, $name1) = $data[0];

// estilo []
[$id1, $name1] = $data[0];

// estilo list()
foreach ($data as list($id, $name)) {
    // lógica aqui com $id e $name
}

// estilo []
foreach ($data as [$id, $name]) {
    // lógica aqui com $id e $name
}
```


<a id="item-4"></a>

## - Visibilidade de Constante de Classe
Foi adicionado suporte para especificação de visibilidade de constantes de classe. 

### Exemplo
```php
<?php
class ConstDemo
{
    const PUBLIC_CONST_A = 1;
    public const PUBLIC_CONST_B = 2;
    protected const PROTECTED_CONST = 3;
    private const PRIVATE_CONST = 4;
}
```


<a id="item-5"></a>

## - Pseudo-tipo "iterable"
Um novo pseudo-tipo (similar ao "callable") chamado "iterable" foi introduzido. 
Pode ser utilizado em tipos de parâmetros e retorno, em que aceita tanto arrays 
ou objetos que implementam a interface "Traversable".

### Exemplo
```php
<?php
function iterator(iterable $iter)
{
    foreach ($iter as $val) {
        //
    }
}
```


<a id="item-6"></a>

## - Manipulação de Exceção de Captura Múltipla
Múltiplas exceções por bloco catch agora podem ser especificadas usando o 
caractere pipe (|). Isso é útil quando diferentes exceções de diferentes 
hierarquias de classe são manipuladas da mesma forma.

### Exemplo
```php
<?php
try {
    // algum código
} catch (FirstException | SecondException $e) {
    // manipula a primeira e a segunda exceções
}
```


<a id="item-7"></a>

## - Suporte para Chaves em "list()"
Agora é possível especificar chaves em "list()", ou em sua nova sintaxe 
abreviada ("[]"). Isso permite a desestruturação de arrays com chaves 
não-inteiras ou não-sequenciais. 

### Exemplo
```php
<?php
$data = [
    ["id" => 1, "name" => 'Tom'],
    ["id" => 2, "name" => 'Fred'],
];

// estilo list()
list("id" => $id1, "name" => $name1) = $data[0];

// estilo []
["id" => $id1, "name" => $name1] = $data[0];

// estilo list()
foreach ($data as list("id" => $id, "name" => $name)) {
    // lógica aqui com $id e $name
}

// estilo []
foreach ($data as ["id" => $id, "name" => $name]) {
    // lógica aqui com $id e $name
}
```


<a id="item-8"></a>

## - Suporte a Deslocamento Negativo em string
Suporte para deslocamento negativo em strings foi adicionado às funções de 
manipulação de string que aceitam deslocamento (offset), assim como para indexação 
de string com [] ou {}. Nesse casos, um deslocamento negativo é interpretado 
como sendo um deslocamento do final da string.

Deslocamento negativo de string e array agora também é suportado na sintaxe 
avaliativa simples de variáveis dentro de strings (aspas duplas). 

### Exemplo
```php
<?php
var_dump("abcdef"[-2]);
var_dump(strpos("aabbcc", "b", -3));

$string = 'bar';
echo "O último caractere de '$string' é '$string[-1]'.\n";
```


<a id="item-9"></a>

## - Conversão de callables para Closures com Closure::fromCallable()
Um novo método estático foi introduzido à classe Closure para permitir que 
objetos do tipo callable sejam facilmente convertidos em objetos da classe Closure. 


### Exemplo
```php
<?php
class Test
{
    public function exposeFunction()
    {
        return Closure::fromCallable([$this, 'privateFunction']);
    }

    private function privateFunction($param)
    {
        var_dump($param);
    }
}

$privFunc = (new Test)->exposeFunction();
$privFunc('algum valor'); // imprime 'string(11) "algum valor"'
```


<a id="item-10"></a>

## - Manipulação Assíncrona de sinal
Uma nova função chamada “pcntl_async_signals()” foi adicionada para permitir a 
manipulação assíncrona de sinal sem usar ticks (que aumenta muito o overhead).

No Linux, os sinais são uma forma de comunicação usada pelos processos para que 
o sistema consiga interferir em seu funcionamento.

### Exemplo
```php
<?php
pcntl_async_signals(true); // liga sinais assíncronos

pcntl_signal(SIGHUP,  function($sig) {
    echo "SIGHUP\n";
});

posix_kill(posix_getpid(), SIGHUP);
```


<a id="item-11"></a>

## - Operadores de números e números malformados
Agora o PHP emite um "notice" ou "warning" para alertar operações matemáticas erradas.

### Exemplo
```php
$foo = 5 + "three";
```