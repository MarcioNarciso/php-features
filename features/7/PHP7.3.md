# Principais Recursos do PHP 7.3

[&larr; Voltar](index.md)

**Índice**
- [Sintaxe Mais Flexível de Heredoc e Nowdoc ](#item-1)
- [A Desestruturação de Array tem Suporte para Atribuições por Referência](#item-2)
- [Operador "instanceof" Aceita Tipos Literais](#item-3)
- [Exceção "CompileError" em vez de Alguns Erros de Compilação](#item-4)
- [Vírgulas à Direita são Permitidas em Chamadas](#item-5)
- [FastCGI Process Manager](#item-6)
- [Suporte para Argon2id](#item-7)
- [Adicionada a Função "is_countable()"](#item-8)


<a id="item-1"></a>

## - Sintaxe Mais Flexível de Heredoc e Nowdoc
O marcador de fechamento para documentação não tem mais a necessidade de ser 
seguido por um ponto e vírgula ou uma nova linha. Além disso, o marcador de 
fechamento pode ser indentado, no caso, a indentação será removida de todas as 
linhas da documentação.


<a id="item-2"></a>

## - A Desestruturação de Array tem Suporte para Atribuições por Referência
A desestruturação de matriz agora suporta atribuições de referência usando a 
sintaxe [&$a, [$b, &$c]] = $d O mesmo também é suportado para "list()"".

### Exemplo
```php
$values = ['one', 'two', 'three'];
[&$a, $b, &c] = $values;
$a .= '-pio'; // $values[0] == ‘one-pio’
```


<a id="item-3"></a>

## - Operador "instanceof" Aceita Tipos Literais
"instanceof" agora permite literais como o primeiro operando, caso em que o 
resultado é sempre false. 


<a id="item-4"></a>

## - Exceção "CompileError" em vez de Alguns Erros de Compilação
Uma nova exceção "CompileError" foi adicionada, da qual "ParseError" herda. 
Um pequeno número de erros de compilação agora lançará um "CompileError" em vez de 
gerar um fatal error. Atualmente, isso afeta apenas erros de compilação que podem 
ser lançados por "token_get_all()" no modo "TOKEN_PARSE", mas mais erros podem ser 
convertidos no futuro. 


<a id="item-5"></a>

## - Vírgulas à Direita são Permitidas em Chamadas
Agora são permitidas vírgulas à direita em chamadas de função e método.


<a id="item-6"></a>

## - FastCGI Process Manager
Novas opções foram adicionadas para personalizar o log do FPM: 

 - log_limit

Esta opção global pode ser usada para definir o limite de log para a linha 
registrada, o que permite registrar mensagens com mais de 1024 caracteres sem 
quebrar. Ele também corrige vários problemas de empacotamento. 

 - log_buffering

Esta opção global permite um registro experimental sem buffer extra.

 - decorate_workers_output

Esta opção de pool permite desativar a decoração de saída para a saída dos 
workers quando "catch_workers_output" estiver ativado. 


<a id="item-7"></a>

## - Suporte para Argon2id
O argumento de configuração "--with-password-argon2[=dir]" agora fornece suporte 
para os hashes Argon2i e Argon2id nas funções "password_hash()", 
"password_verify()", "password_get_info()" e "password_needs_rehash()". 
As senhas podem ser hash e verificadas usando a constante "PASSWORD_ARGON2ID". 
O suporte para Argon2i e Argon2id nas funções "password_*()"" agora requer que 
o PHP seja vinculado à biblioteca de referência libargon2 ≥ 20161029. 


<a id="item-7"></a>

## - Adicionada a Função "is_countable()"
Ela verifica se o conteúdo de uma variável é um valor contável.

### Exemplo
```php
var_dump(is_countable([1, 2, 3])); // bool(true)
var_dump(is_countable(new ArrayIterator(['foo', 'bar', 'baz']))); // bool(true)
var_dump(is_countable(new ArrayIterator())); // bool(true)
var_dump(is_countable(new stdClass())); // bool(false)
```