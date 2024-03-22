# Principais Recursos do PHP 7.0

**Índice**
- [Declarações de Tipos Escalares](#item-1)
- [Declarações de Tipos de Retorno](#item-2)
- [Operador de coalescência nula](#item-3)
- [Operador "nave espacial" (spaceship)](#item-4)
- [Arrays de constantes utilizando "define()"](#item-5)
- [Classes Anônimas](#item-6)
- [Closure::call()](#item-7)
- ["unserialize()" Filtrado](#item-8)
- [Expectations](#item-9)
- [Agrupamento de declarações “use”](#item-10)
- [Expressões de Retorno nos Geradores](#item-11)
- [Delegação de geradores](#item-12)
- [Divisão de Inteiros com "intdiv()"](#item-13)
- [Opções de sessões](#item-14)
- [preg_replace_callback_array()](#item-15)
- [Throwables](#item-16)
- [A função list() com ArrayAccess](#item-17)
- [Outros Recursos](#item-18)


<a id="item-1"></a>

## - Declarações de Tipos Escalares
Agora, os tipos de dados escalares podem ser declarados nos parâmetros das funções e métodos. Antes só podiam ser declarados os tipos classes, interfaces, array e callable.

### Exemplo
```php
<?php
// Coercive mode
function sumOfInts(int ...$ints)
{
    return array_sum($ints);
}

var_dump(sumOfInts(2, '3', 4.1));

```


<a id="item-2"></a>

### - Declarações de Tipos de Retorno
Pode-se declarar os tipos (com qualquer tipo) de retornos de métodos e funções.

#### Exemplo
```php
<?php

function arraysSum(array ...$arrays): array
{
    return array_map(function(array $array): int {
        return array_sum($array);
    }, $arrays);
}

print_r(arraysSum([1,2,3], [4,5,6], [7,8,9]));

```


<a id="item-3"></a>

### - Operador de coalescência nula (??)
É um truque sintático (“apelido”) para: isset($x) ? $x : $y;

#### Exemplo
```php
$foo = $x ?? y;
```


<a id="item-4"></a>

### - Operador "nave espacial" (spaceship [<=>])
Compra duas expressões. Retorna -1, 0 ou 1 quando a 1a expressão for 
respectivamente menor que, igual ou maior a 2a expressão.

#### Exemplo
```php
// Integers
echo 1 <=> 1; // 0
echo 1 <=> 2; // -1
echo 2 <=> 1; // 1

// Floats
echo 1.5 <=> 1.5; // 0
echo 1.5 <=> 2.5; // -1
echo 2.5 <=> 1.5; // 1
 
// Strings
echo "a" <=> "a"; // 0
echo "a" <=> "b"; // -1
echo "b" <=> "a"; // 1
```


<a id="item-5"></a>

### - Constantes de arrays utilizando "define()"
As constantes de arrays agora podem ser definidas com a função "define()".
No PHP 5.6, elas poderiam somente ser definidas com "const".

#### Exemplo
```php
<?php
define('ANIMALS', [
    'dog',
    'cat',
    'bird'
]);

echo ANIMALS[1]; // imprime "cat"
?>
```


<a id="item-6"></a>

### - Classes Anônimas
O suporte à classes anônimas foi adicionado utilizando “new class”. 
Essas podem ser utilizadas no lugar de definições completas de classes para 
objetos descartáveis.

#### Exemplo
```php
$logger = new class implements Logger {
	public function log (string $msg): string {
		return $msg;
	}
};

$bar = new class {
	public function foo(string $msg) : string 
	{
		return $msg;
	}
};

echo $bar->foo('Hello, World!!'); // imprime "Hello, World!!"
```


<a id="item-7"></a>

### - Closure::call()
O método “Closure::call()” é uma forma mais eficaz e abreviada de associar 
temporariamente um escopo de objeto a uma closure e invocá-la.

#### Exemplo
```php
<?php
class A {private $x = 1;}

// Código anterior ao PHP 7
$getXCB = function() {return $this->x;};
$getX = $getXCB->bindTo(new A, 'A'); // intermediate closure
echo $getX(); // imprime 1

// Código do PHP 7+
$getX = function() {return $this->x;};
echo $getX->call(new A); // imprime 1
```


<a id="item-8"></a>

### - "unserialize()" Filtrado
Esse recurso busca prover uma melhor segurança ao desserializar objetos com 
informações não confiáveis.
Ele impede possíveis injeções de código ao permitir que o desenvolvedor liste as 
classes que podem ser desserializadas. 

#### Exemplo
```php
// converte todos os objetos em objetos __PHP_Incomplete_Class
$data = unserialize($foo, ["allowed_classes" => false]);

// converte todos os objetos em objetos __PHP_Incomplete_Class exceto aqueles de MyClass e MyClass2
$data = unserialize($foo, ["allowed_classes" => ["MyClass", "MyClass2"]]);

// comportamento padrão (o mesmo que omitir o segundo argumento) que aceita todas as classes
$data = unserialize($foo, ["allowed_classes" => true]);
```


<a id="item-9"></a>

### - Expectations
Expectations são aprimoramentos compatíveis com versões anteriores para a antiga 
função “assert()”. Permitem asserções com custo zero em código em produção e 
fornecem a capacidade de lançar exceções personalizadas quando a asserção falhar.

#### Exemplo
```php
ini_set('assert.exception', 1);

class CustomError extends AssertionError {}

assert(false, new CustomError('Alguma mensagem de erro'));
```


<a id="item-10"></a>

### - Agrupamento de declarações “use” (namespaces)
Classes, funções e constantes importadas do mesmo namespace, agora podem ser 
agrupadas em uma única declaração “use”.

#### Exemplo
```php
use some\namespace\{ClassA, ClassB, ClassC as C};
use function some\namespace\{fn_a, fn_b, fn_c};
use const some\namespace\{ConstA, ConstB, ConstC};
```


<a id="item-11"></a>

### - Expressões de Retorno nos Geradores
Este recurso foi construído em cima da funcionalidade de geradores introduzida 
no PHP 5.5. Ele permite que uma declaração "return" seja usada dentro de um 
gerador para permitir que uma expressão final seja retornada (retornar por 
referência não é permitido). Este valor pode ser obtido usando o novo método 
Generetor::getReturn(), que deve ser usado somente quando o gerador terminar a 
geração dos valores.

#### Exemplo
```php
<?php

$gen = (function() {
    yield 1;
    yield 2;

    return 3;
})();

foreach ($gen as $val) {
    echo $val, PHP_EOL; // imprime 1 e 2 (em cada iteração)
}

echo $gen->getReturn(), PHP_EOL; // imprime 3
```


<a id="item-12"></a>

### - Delegação de geradores
Geradores agora podem delegar para outros geradores, objetos Traversable ou 
array automaticamente, sem a necessidade de escrever código repetitivo no 
gerador externo utilizando o construtor “yield from”.

#### Exemplo
```php
function gen()
{
    yield 1;
    yield 2;
    yield from gen2();
}

function gen2()
{
    yield 3;
    yield 4;
}

foreach (gen() as $val)
{
    echo $val, PHP_EOL; // imprime 1, 2, 3 e 4 (em cada iteração)
}
```


<a id="item-13"></a>

### - Divisão de Inteiros com "intdiv()"
A nova função intdiv() realiza a divisão de inteiros de seus operandos e a retorna. 

#### Exemplo
```php
<?php
var_dump(intdiv(10, 3)); // imprime "int(3)"
?>
```


<a id="item-14"></a>

### - Opções de sessões
A função session_start() agora aceita um array de opções que sobrescrevem as diretivas de configuração de sessões normalmente configuradas no php.ini.

#### Exemplo
```php
<?php
session_start([
    'cache_limiter' => 'private',
    'read_and_close' => true,
]);
?>
```


<a id="item-15"></a>

### - preg_replace_callback_array()
A nova função preg_replace_callback_array() permite que o código seja escrito 
de forma mais limpa com a utilização da função preg_replace_callback(). 
Antes do PHP7, callbacks que precisam ser executadas por expressões regulares 
exigiam que a função de callback fosse poluída com muitas ramificações.

Agora, funções de callback podem ser registradas em cada expressão regular 
utilizando um array associativo, onde a chave é uma expressão regular e o valor 
é uma função de callback.


<a id="item-16"></a>

### - Throwables
Erros agora se comportam de uma forma similar às exceções. Ambos herdam a 
interface Throwable.
Pode-se pegar tanto exceções quanto erros como Throwables, mas também pode-se 
pegar erros como objetos Error.


<a id="item-17"></a>

### - A função list() pode sempre desempacotar objetos que implementam ArrayAccess
Antes a função list() não garantia corretude em operações com objetos que 
implementam ArrayAccess. Isso foi corrigido. 


<a id="item-18"></a>

### - Outros Recursos
Acesso a membros de classe na clonagem foi adicionado, por exemplo, (clone $foo)->bar().