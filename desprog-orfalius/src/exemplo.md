Algorítimo Rabin-Karop para busca em texto
======

**Introdução**

Algoritmos para busca em texto são muito comuns na computação. Esse consiste basicamente em achar um padrão pré estabelecido em um texto. Apesar das diferentes implementações que existem, todas têm as mesmas entradas e saídas.

Entradas:
* Padrão a ser encontrado ex. "projeto";
* Texto ex. "projeto de desafios da computação, entrega 2 do projeto 1."

Saída:
* Achar uma palavra em um texto;
* Busca por arquivos que contêm certas palavras chaves;
* Detecção de plágio

**O algoritmo ingênuo**

Esse algoritmo faz uso da força bruta para achar o padrão no texto. Ele separa seções do texto que tem o mesmo tamanho do padrão, compara letra por letra, depois descarta a primeira letra e usa a próxima letra, até o texto acabar.
??? Checkpoint 1
Escreva o pseudo código descrito acima

::: Gabarito
```
algoritmo_ingenuo(texto,padrao){
    n = tamanho do texto;
    m = tamanho do padrão
    lista = []
    Enquanto( contador <= n-m){
        Se (padrão == texto[contador:n-1]{
            lista.append(contador)
        }
        contador +=1;
    }
    return lista
}
```
:::
???


??? Checkpoint 2
Qual a complexidade do algoritmo

::: Gabarito
O loop vai rodar por n-m vezes. Em cada iteração ele vai comparar o padrão, como esse tem m letras, vai fazer m operações.
Dessa forma, fazendo as simplificações de complexidade, temos que a complexidade é: O(nm)
:::
???



**O Algoritmo melhorado**

Ao invés de compararmos letra por letra, podemos usar a tabela ASCII e somar as letras. Transformar um texto em um número é uma forma de fazer um HASH. Dessa forma se o padrão mencionado na secção acima seria:

Ao invés de compararmos letra por letra, podemos usar a tabela ASCII e somar as letras. Transformar um texto em um número é uma forma de fazer um HASH. Dessa forma se o padrão mencionado na secção acima seria:
![](ASCII_1.gif)



$H(projeto) = H(p) + H(r) + H(o) + H(j) + H(e) + H(t) + H(o)$

$H(projeto) = 112 + 114 + 111 + 106 + 101 + 116 +111$

$H(projeto) = 771$


??? Checkpoint 3
Calcule os hashes das palavras: projeto, rojetoa. ojetoab. e escreva uma reflexão sobre os cálculos

::: Gabarito
$H(projeto) = 771$

$H(rojetoa) = 756$

$H(rojetoab) = 740$

Para calcular o hash da palavra subsequente basta subtrair o hash da letra que sai e adicionar o hash da letra nova.
:::
???

**Rolling Hash**

É o método que usa o hash anterior para calcular o novo, evitando cálculos repetidos e, assim, agilizando o processo.

??? Checkpoint 4
Qual é o grande problema de usar a solução descrita acima


::: Gabarito
O grande problema de usar o método descrito acima, é que a ordem não é levada em consideração, então as palavras abc e bca são iguais e, além disso, palavras diferentes podem ter o mesmo hash.
:::
???

**Rabin-Karp Algorithm**

O algoritmo de Rabin-Karp foi desenvolvido por Richard M. Karp e Michael O. Rabin. Esse é uma algoritmo de busca em testo e faz o uso dos conceitos mencionados anteriormente, com algumas diferenças que lidam com os problemas mencionados.

Se multiplicarmos o valor ASCII do charachter por uma constante elevada a sua posição evitamos todo tipo de coincidência.

Fazendo a somatória dos valores referentes a cada charachter, acabamos com o valor Hash referente a essa string!

!!! Atenção
As posições são de tras pra frente! Ou seja, na string "abc" a posição de "a" é 2, de "b" é 1 e de "c" é 0.
!!!


??? Checkpoint 5

Usando a tabela ASCII, calcule o valor Hash das strings "bb", "ca" e "ac".

::: Gabarito

| string      |     hash    |
|-------------|-------------|
| bb          | 98k¹ + 98k⁰   |
| ac          | 97k¹ + 99k⁰   |
| ca          | 99k¹ + 97k⁰   |

:::
???

podemos então fazer um pseudo código para calular o hash de uma string.

``` 
calcula_hash (string, tamanho, k):
    i = tamanho - 1
    total = 0

    enquanto(i != 0):
        total += ASCII(string[i]) * k ** i
        i -= 1
    
    retorna total
```

Agora que sabemos o que é um valor hash, fica fácil de encontrar strings dentro de textos. Basta procurar dentro do texto uma string de n caracteres que tenha o mesmo valor hash que a referência!

seria como nesse pseudo código:

``` 
rabin_karp (texto, referencia, tamanho_texto, tamanho_referencia):
    hash_referencia = calcula_hash(referencia)
    
    l = 0
    r = l + tamanho_referencia

    encontrados = 0
    enquanto(r <= tamanho_texto):
        string = texto de l até r
        hash_string = calcula_hash(string)

        se(hash_string == hash_referencia):
            encontrados += 1

        l += 1
        r += 1
    
    retorna encontrados
```

??? Checkpoint 6

Como podemos implementar o rolling hash usando o novo jeito cálcular o hash?

::: Gabarito
Ao calcularmos o hash value da string referente aos n primeiros charachteres do texto, estamos calculando o hash referente aos n-1 charachteres da próxima string dividios por k!
Se utilizarmos o hash value da string anterior, remover o hash do primeiro charachter, atualizar a constante que multiplicao resto e somar o hash do próximo charachter, podemos otimizar o algorítimo!

$h(rojetoa) = ((h(projeto) - h(p)*k^{posicao} )*k)+h(a)$
:::
???

??? Checkpoint 7
Agora tente escrever um pseudo código como o descrito no checkpoint anterior

::: Gabarito

``` 
rolling_rabin_karp (texto, referencia, tamanho_texto, tamanho_referencia):
    hash_referencia = calcula_hash(referencia)
    
    l = 0
    r = l + tamanho_referencia

    string = texto de l até r
    hash_string = calcula_hash(string)

    encontrados = 0
    enquanto(r <= tamanho_texto):

        r += 1

        hash_string = (hash_string - ASCII(l)*k**tamanho_referencia) * k + ASCII(r)*k

        se(hash_string == hash_referencia):
            encontrados += 1

        l += 1
    
    retorna encontrados
```

:::
???

**Implementações de Rabin-Karp na prática**

Podemos implementar o algoritmo de Rabin-Kharp de duas maneiras.

***Monte Carlo***

Implementado como monte carlo, quando um hash calculado é igual ao hash buscado, o índice desse valor é salvo como uma correspondência, porém podem existir duas strings diferentes que geram o mesmo hash, levando o algoritmo a cometer erros. 


??? Checkpoint 8
Qual é a complexidade

::: Gabarito
O loop vai rodar por n-m vezes, até o final do texto. Assim a complexidade será O(n-m)
:::
???

***Las Vegas***

A versão Las Vegas do Algoritmo, ao encontrar uma correspondência, compara letra por letra com a palavra buscada, garantido que são iguais.


??? Checkpoint 9
Qual é a complexidade

::: Gabarito
Como o algoritmo faz a comparação de strings na força bruta apenas nas correspondências.No melhor caso o algoritmo rodará por todo o texto e quando achar um hash igual vai comparar letra a letra, tendo a complexidade igual a O(n+m).
No pior dos casos o todos os hash do texto serão iguais ao do padrão, dessa forma, o algoritmo vai rodas pelo texto inteiro e a cada iteração vai comparar letra a letra, tendo uma complexidade igual a O(nm)

:::
???