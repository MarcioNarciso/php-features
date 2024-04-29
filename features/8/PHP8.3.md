# Principais Recursos do PHP 8.3
<a id="topo"></a>

[&larr; Voltar](index.md)

**Índice**
- [Constantes Tipadas de Classe](#item-1)
- [Acesso Dinâmico de Constantes de Classe](#item-2)
- [Novo Atributo '#[\Override]'](#item-3)
- [Clonagem Profunda de Propriedades Somente Leitura](#item-4)
- [Nova Função 'json_validate()'](#item-5)
- [Novo Método 'Randomizer::getBytesFromString()'](#item-6)
- [Novos Métodos 'Randomizer::getFloat()' e 'Randomizer::nextFloat()'](#item-7)
- [O Linter de Linha de Comando Suporta Vários Arquivos](#item-8)
- [Propriedades Somente Leitura](#item-9)
- [Closures Criados a Partir de Métodos Mágicos](#item-10)
- [O Modificador 'final' com um Método de uma 'trait'](#item-11)
- [Inicializadores de Variáveis Estáticas](#item-12)


<a id="item-1"></a>

## - Constantes Tipadas de Classe
[Topo &uarr;](#topo)

Constantes de 'classe', 'interface', 'trait' e 'enum' agora suportam declarações 
de tipo. 

### Exemplo
```php
<?php
interface I {
    const string PHP = 'PHP 8.3';
}

class Foo implements I {
    const string PHP = [];
}

// Fatal error: Cannot use array as value for class constant
// Foo::PHP of type string
```


<a id="item-2"></a>

## - Acesso Dinâmico de Constantes de Classe
[Topo &uarr;](#topo)

### Exemplo
```php
<?php
class Foo {
    const PHP = 'PHP 8.3';
}

$searchableConstant = 'PHP';

var_dump(Foo::{$searchableConstant});
```


<a id="item-3"></a>

## - Novo Atributo '#[\Override]'
[Topo &uarr;](#topo)

Ao adicionar o atributo '#[\Override]' a um método, o PHP garantirá que um método 
com o mesmo nome exista em uma classe pai ou em uma interface implementada. 
Adicionar o atributo torna claro que a sobreposição de um método pai é intencional 
e simplifica a refatoração, pois a remoção de um método pai sobreposto será detectada. 

### Exemplo
```php
<?php
use PHPUnit\Framework\TestCase;

final class MyTest extends TestCase {
    protected $logFile;

    protected function setUp(): void {
        $this->logFile = fopen('/tmp/logfile', 'w');
    }

    #[\Override]
    protected function taerDown(): void {
        fclose($this->logFile);
        unlink('/tmp/logfile');
    }
}

// Fatal error: MyTest::taerDown() has #[\Override] attribute,
// but no matching parent method exists
```


<a id="item-4"></a>

## - Clonagem Profunda de Propriedades Somente Leitura
[Topo &uarr;](#topo)

Propriedades 'readonly' agora podem ser modificadas uma vez dentro do método 
mágico '__clone' para permitir a clonagem profunda de propriedades somente leitura.


### Exemplo
```php
<?php
class PHP {
    public string $version = '8.2';
}

readonly class Foo {
    public function __construct(
        public PHP $php
    ) {}

    public function __clone(): void {
        $this->php = clone $this->php;
    }
}

$instance = new Foo(new PHP());
$cloned = clone $instance;

$cloned->php->version = '8.3';
```


<a id="item-5"></a>

## - Nova Função 'json_validate()'
[Topo &uarr;](#topo)

'json_validate()' permite verificar se uma string é sintaticamente válida em 
JSON, sendo mais eficiente do que 'json_decode()'.


### Exemplo
```php
<?php
var_dump(json_validate('{ "test": { "foo": "bar" } }')); // true
```


<a id="item-6"></a>

## - Novo Método 'Randomizer::getBytesFromString()'
[Topo &uarr;](#topo)

A extensão 'Random' que foi adicionada no PHP 8.2 foi ampliada com um novo 
método para gerar strings aleatórias consistindo apenas de bytes específicos. 
Este método permite que o desenvolvedor gere facilmente identificadores 
aleatórios, como nomes de domínio e strings numéricas de comprimento arbitrário.

### Exemplo
```php
<?php
// Um '\Random\Engine' pode ser passado para a semente,
//  o padrão é o motor seguro.
$randomizer = new \Random\Randomizer();

$randomDomain = sprintf(
    "%s.example.com",
    $randomizer->getBytesFromString(
        'abcdefghijklmnopqrstuvwxyz0123456789',
        16,
    ),
);

echo $randomDomain;
```


<a id="item-7"></a>

## - Novos Métodos 'Randomizer::getFloat()' e 'Randomizer::nextFloat()'
[Topo &uarr;](#topo)

Devido à precisão limitada e ao arredondamento implícito de números de ponto 
flutuante, gerar um 'float' imparcial dentro de um intervalo específico não é 
trivial, e as soluções comumente usadas no nível do usuário podem gerar resultados 
tendenciosos ou números fora do intervalo solicitado.

O Randomizer também foi ampliado com dois métodos para gerar 'floats' de maneira 
imparcial. O método 'Randomizer::getFloat()' utiliza o algoritmo da seção 'γ' 
que foi publicado em 'Drawing Random Floating-Point Numbers from an Interval. 
Frédéric Goualard, ACM Trans. Model. Comput. Simul., 32:3, 2022'.

### Exemplo
```php
<?php

$randomizer = new \Random\Randomizer();

$temperature = $randomizer->getFloat(
    -89.2,
    56.7,
    \Random\IntervalBoundary::ClosedClosed,
);

$chanceForTrue = 0.1;
// Randomizer::nextFloat() é equivalente a
// Randomizer::getFloat(0, 1, \Random\IntervalBoundary::ClosedOpen).
// O limite superior, ou seja, 1, não será retornado.
$myBoolean = $randomizer->nextFloat() < $chanceForTrue;

```


<a id="item-8"></a>

## - O Linter de Linha de Comando Suporta Vários Arquivos
[Topo &uarr;](#topo)

O 'linter' da linha de comando agora aceita vários nomes de arquivos para 'lint'.


### Exemplo
```
php -l foo.php bar.php
No syntax errors detected in foo.php
No syntax errors detected in bar.php

```


<a id="item-9"></a>

## - Propriedades Somente Leitura
[Topo &uarr;](#topo)

Classes anônimas agora podem ser marcadas como somente leitura.

Propriedades somente leitura agora podem ser reinicializadas durante a clonagem. 


<a id="item-10"></a>

## - Closures Criados a Partir de Métodos Mágicos
[Topo &uarr;](#topo)

Closures criados a partir de métodos mágicos agora podem aceitar argumentos nomeados. 


<a id="item-11"></a>

## - O Modificador 'final' com um Método de uma 'trait'
[Topo &uarr;](#topo)

O modificador final agora pode ser usado ao utilizar um método de uma trait. 


<a id="item-12"></a>

## - Inicializadores de Variáveis Estáticas
[Topo &uarr;](#topo)

Os inicializadores de variáveis estáticas agora podem conter expressões arbitrárias. 