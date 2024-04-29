# Principais Recursos do PHP 7.2
<a id="topo"></a>

[&larr; Voltar](index.md)

**Índice**
- [Novo Tipo: object](#item-1)
- [Carregamento de Extensão por Nome](#item-2)
- [Sobrescrita de Método Abstrato](#item-3)
- [Sodium Agora é uma Extensão do core](#item-4)
- [Tipos Estendidos de strings para PDO](#item-5)
- [Adição de Informação de Endereço à Extensão Sockets](#item-6)
- [Ampliação de Tipagem de Parâmetro](#item-7)
- [Permite-se uma Vírgula no Final de Espaços de Nomes Agrupados](#item-8)
- [SQLite3 Permite Escrever BLOBs](#item-9)
- [Melhorias à Extensão ZIP](#item-10)





<a id="item-1"></a>

## - Novo Tipo: object
[Topo &uarr;](#topo)

Um novo tipo, object, foi introduzido e pode ser usado para tipagem de parâmetro 
(contravariante) e tipagem de retorno (covariante) de qualquer objeto. 

### Exemplo
```php
<?php

function test(object $obj) : object
{
    return new SplQueue();
}

test(new stdClass());
```


<a id="item-2"></a>

## - Carregamento de Extensão por Nome
[Topo &uarr;](#topo)

Extensões compartilhadas não requerem mais que as extensões de arquivo (.so para 
Unix ou .dll para Windows) sejam especificadas. Isto está habilitado no arquivo 
php.ini, assim como na função dl(). 


<a id="item-3"></a>

## - Sobrescrita de Método Abstrato
[Topo &uarr;](#topo)

Métodos abstratos agora podem ser substituídos quando uma classe abstrata 
estende outra classe abstrata.  

### Exemplo
```php
<?php

abstract class A
{
    abstract function test(string $s);
}
abstract class B extends A
{
    // substituído - ainda mantendo contravariância para parâmetros e covariância para retorno
    abstract function test($s) : int;
}
```


<a id="item-4"></a>

## - Sodium Agora é uma Extensão do core
[Topo &uarr;](#topo)

Argon2 foi adicionado à API de hash de senha, onde as seguintes constantes 
foram expostas:    

### Exemplo
```php
PASSWORD_ARGON2I
PASSWORD_ARGON2_DEFAULT_MEMORY_COST
PASSWORD_ARGON2_DEFAULT_TIME_COST
PASSWORD_ARGON2_DEFAULT_THREADS
```


<a id="item-5"></a>

## - Tipos Estendidos de strings para PDO
[Topo &uarr;](#topo)

O tipo string da PDO foi estendido para suportar o tipo de caractere nacional 
ao emular preparações. Isto foi feito com as seguintes constantes:

    PDO::PARAM_STR_NATL
    PDO::PARAM_STR_CHAR
    PDO::ATTR_DEFAULT_STR_PARAM


Estas constantes são utilizadas com a operação binária OR entre elas e a constante 
"PDO::PARAM_STR": 

### Exemplo
```php
<?php

$db->quote('über', PDO::PARAM_STR | PDO::PARAM_STR_NATL);
```


<a id="item-6"></a>

## - Adição de Informação de Endereço à Extensão Sockets
[Topo &uarr;](#topo)

A extensão sockets agora tem a capacidade de procurar por Informação de Endereço, 
assim como se conectar, vincular e examinar. As quatro funções a seguir foram 
adicionadas para isto: 

### Exemplo
```php
<?php
socket_addrinfo_lookup();
socket_addrinfo_connect();
socket_addrinfo_bind();
socket_addrinfo_explain();
```


<a id="item-7"></a>

## - Ampliação de Tipagem de Parâmetro
[Topo &uarr;](#topo)

Os tipos de parâmetros de métodos sobrescritos e de implementações de interface 
podem ser omitidos. Isso ainda continua em conformidade com LSP (Language Server 
Protocol), desde que os tipos de parâmetros sejam contravariante.

### Exemplo
```php
<?php

interface A
{
    public function Test(array $input);
}

class B implements A
{
    public function Test($input){} // tipo omitido para $input
}
```


<a id="item-8"></a>

## - Permite-se uma Vírgula no Final de Espaços de Nomes Agrupados
[Topo &uarr;](#topo)

Uma vírgula agora pode ser adicionada ao final do grupo na sintaxe 'use' 
introduzida no PHP 7.0. 

### Exemplo
```php
<?php

use Foo\Bar\{
    Foo,
    Bar,
    Baz,
};
```


<a id="item-9"></a>

## - SQLite3 Permite Escrever BLOBs
[Topo &uarr;](#topo)

SQLite3::openBlob() agora permite abrir campos BLOB em modo de escrita; 
anteriormente apenas leitura era suportada. 


<a id="item-10"></a>

## - Melhorias à Extensão ZIP
[Topo &uarr;](#topo)

Suporte foi adicionado a leitura e escrita de arquivos criptografados 
(requer libzip 1.2.0).

A classe ZipArchive agora implementa a interface Countable.

O stream zip:// agora aceita uma opção de contexto 'password'. 