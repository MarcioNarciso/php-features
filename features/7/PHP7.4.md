# Principais Recursos do PHP 7.4
<a id="topo"></a>

[&larr; Voltar](index.md)

**Índice**
- [Propriedades Tipadas](#item-1)
- [Arrow functions](#item-2)
- [Covariância do tipo de retorno limitado e contravariância do tipo de argumento](#item-3)
- [Operador de Atribuição de Coalescência Nula](#item-4)
- [Descompactamento Dentro de Arrays](#item-5)
- [Separador Literal Numérico](#item-6)
- [Referências Fracas (classe WeakReference)](#item-7)
- [Permite lançar exceções de __toString()](#item-8)
- [Filter](#item-9)
- [Hash](#item-10)
- [Multibyte String](#item-11)
- [OPcache (pré-carregamento)](#item-12)
- [PDO](#item-13)
- ["strip_tags()" com Array de Nomes de Tags](#item-14)
- [Serialização de Objetos Personalizados](#item-15)
- [Funções de Mesclagem de Array sem Argumentos](#item-16)
- [Função "proc_open()"](#item-17)


<a id="item-1"></a>

## - Propriedades Tipadas
[Topo &uarr;](#topo)

As propriedades de classe agora suportam declarações de tipo.

Este exemplo reforçará que "$user->id" pode ser atribuído apenas com valores 
inteiros e "$user->name" somente com valores de string.

### Exemplo
```php
<?php

class User {
    public int $id;
    public string $name;
}
```


<a id="item-2"></a>

## - Arrow functions
[Topo &uarr;](#topo)

As "arrow functions" fornecem uma sintaxe abreviada para definir funções com 
ligação implícita por valor de escopo.

### Exemplo
```php
<?php

$factor = 10;
$nums = array_map(fn($n) => $n * $factor, [1, 2, 3, 4]);
// $nums = array(10, 20, 30, 40);
```


<a id="item-3"></a>

## - Covariância do tipo de retorno limitado e contravariância do tipo de argumento
[Topo &uarr;](#topo)

O suporte completo à variação só está disponível se o carregamento automático for 
usado. 

Dentro de um único arquivo, apenas as referências de tipo não cíclico são possíveis, 
porque todas as classes precisam estar disponíveis antes de serem referenciadas. 

O código a seguir agora funcionará:

### Exemplo
```php
<?php
class A {}
class B extends A {}

class Producer {
    public function method(): A {}
}
class ChildProducer extends Producer {
    public function method(): B {}
}
```


<a id="item-4"></a>

## - Operador de Atribuição de Coalescência Nula (??=)
[Topo &uarr;](#topo)


### Exemplo
```php
<?php

$array['key'] ??= computeDefault();

// é equivalente a
if (!isset($array['key'])) {
    $array['key'] = computeDefault();
}
```


<a id="item-5"></a>

## - Descompactamento Dentro de Arrays (operador "spread")
[Topo &uarr;](#topo)


### Exemplo
```php
<?php

$parts = ['apple', 'pear'];
$fruits = ['banana', 'orange', ...$parts, 'watermelon']; // ['banana', 'orange', 'apple', 'pear', 'watermelon'];

```


<a id="item-6"></a>

## - Separador Literal Numérico
[Topo &uarr;](#topo)

Literais numéricos podem conter underscores entre dígitos.

### Exemplo
```php
<?php

6.674_083e-11; // float
299_792_458;   // decimal
0xCAFE_F00D;   // hexadecimal
0b0101_1111;   // binary

```


<a id="item-7"></a>

## - Referências Fracas (classe WeakReference)
[Topo &uarr;](#topo)

Permite ao programador manter uma referência a um objeto que não impede que o 
objeto seja destruído. Elas são úteis para implementar estruturas como cache.

### Exemplo
```php
<?php

$obj = new stdClass;
$weakref = WeakReference::create($obj);
var_dump($weakref->get()); // imprime "object(stdClass)#1 (0) {}"
unset($obj);
var_dump($weakref->get()); // imprime "NULL"
```


<a id="item-8"></a>

## - Permite lançar exceções de __toString()
[Topo &uarr;](#topo)

Lançar exceções de dentro de "__toString()" agora é permitido. Antes isso 
resultava um erro fatal. Os erros fatais recuperáveis ​​existentes nas conversões 
de string foram convertidos em exceções de "Error". 


<a id="item-9"></a>

## - Filter
[Topo &uarr;](#topo)

O filtro "FILTER_VALIDATE_FLOAT" agora suporta as opções "min_range" e "max_range" 
com a mesma semântica que "FILTER_VALIDATE_INT". 


<a id="item-10"></a>

## - Hash
[Topo &uarr;](#topo)

Adicionado o hash "crc32c" usando o polinômio de Castagnoli. Essa variante CRC32 
é usada por sistemas de armazenamento, como iSCSI, SCTP, Btrfs e ext4.


<a id="item-11"></a>

## - Multibyte String (função "mb_str_split()")
[Topo &uarr;](#topo)

Adicionada a função "mb_str_split()", que fornece a mesma funcionalidade que 
"str_split()", mas operando em pontos de código em vez de bytes. 



<a id="item-12"></a>

## - OPcache (pré-carregamento)
[Topo &uarr;](#topo)

A partir do PHP 7.4.0, o PHP pode ser configurado para pré-carregar scripts no 
"opcache" quando o mecanismo é iniciado. Quaisquer funções, classes, interfaces 
ou traits (mas não constantes) nestes arquivos ficarão globalmente disponíveis 
para todas as solicitações sem precisar ser incluídas explicitamente. Isso troca 
conveniência e desempenho (porque o código está sempre disponível) para uso de 
memória de linha de base. Também requer a reinicialização do processo PHP para 
limpar os scripts pré-carregados, o que significa que esse recurso é prático 
apenas para uso em produção, não em um ambiente de desenvolvimento. 

⚠️ NÃO É SUPORTADO NO WINDOWS.


<a id="item-13"></a>

## - PDO
[Topo &uarr;](#topo)

### Usuário e Senha
O nome de usuário e a senha agora podem ser especificados como parte do "PDO DSN" 
para os drivers mysql, mssql, sybase, dblib, firebird e oci. Antes, isso era 
suportado apenas pelo driver pgsql. Se um nome de usuário/senha for especificado 
no construtor e no DSN, o construtor terá precedência. 

### Escape de Pontos de Interrogação
Agora é possível escapar pontos de interrogação nas consultas SQL para evitar 
que elas sejam interpretadas como espaços reservados para parâmetros. 
Escrever "??" permite enviar um único ponto de interrogação para o banco de 
dados e, por exemplo, usar o operador PostgreSQL JSON key exists (?). 


<a id="item-14"></a>

## - "strip_tags()" com Array de Nomes de Tags
[Topo &uarr;](#topo)

"strip_tags()" agora também aceita um array de tags permitidas: em vez de 
"strip_tags($str, '\<a>\<p>')" agora você pode escrever "strip_tags($str, ['a', 'p'])". 


<a id="item-15"></a>

## - Serialização de Objetos Personalizados
[Topo &uarr;](#topo)

Um novo mecanismo para serialização de objetos personalizados foi adicionado, 
que usa dois novos métodos mágicos: "__serialize()" e "__unserialize()". 

⚠️ O novo mecanismo de serialização substitui a interface Serializable, que será 
descontinuada no futuro. 

### Exemplo
```php
<?php
// Retorna um array contendo todo o estado necessário do objeto.
public function __serialize(): array;

// Restaura o estado do objeto a partir do array de dados fornecido.
public function __unserialize(array $data): void;
```


<a id="item-16"></a>

## - Funções de Mesclagem de Array sem Argumentos
[Topo &uarr;](#topo)

"array_merge()" e "array_merge_recursive()" agora podem ser chamados sem nenhum 
argumento; nesse caso, eles retornarão um array vazio. Isso é útil em conjunto 
com o operador de espalhamento (spread operator), por exemplo, "array_merge(...$arrays)".


<a id="item-17"></a>

## - Função "proc_open()"
[Topo &uarr;](#topo)

"proc_open()" agora aceita um array em vez de uma string para o comando. Nesse 
caso, o processo será aberto diretamente (sem passar por um shell) e o PHP 
cuidará de qualquer argumento necessário para escapar. 

### Exemplo
```php
<?php
proc_open(['php', '-r', 'echo "Hello World\n";'], $descriptors, $pipes);
```

"proc_open()" agora suporta redirect e descritores "null".

### Exemplo
```php
<?php
// Like 2>&1 on the shell
proc_open($cmd, [1 => ['pipe', 'w'], 2 => ['redirect', 1]], $pipes);
// Like 2>/dev/null or 2>nul on the shell
proc_open($cmd, [1 => ['pipe', 'w'], 2 => ['null']], $pipes);
```