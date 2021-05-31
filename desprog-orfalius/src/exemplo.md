Algoritimo Rabin-Karpp para busca em texto
======

**Introdução**

Algoritmos para busca em texto são muito comuns na computação. Esses consistem basicamente em achar um padrão pré estabelecido em um texto. Apesar das diferentes implementações que existem, todas têm as mesmas entradas e saídas.

Entradas:
* Padrão a ser encontrado ex. "projeto";
* Texto ex. "projeto de desafios da computação, entrega 2 do projeto 1."

Saída:
* 0 e 48 - que são os índices onde começam o padrão

A busca em texto tem uma série aplicações:

* Achar uma palavra em um texto;
* Busca por arquivos que contêm certas palavras chaves;
* Detecção de plágio

**O algoritmo ingênuo**

Quando você pensa em algorítimos de comparação de texto, provavelmente a primeira coisa que vem a sua cabeça é alguma forma de comparar letra por letra das entradas.

Esse é um método funcional, apesar de ser longe do mais eficiente. Todavia, vamos nos manter com ele por enquanto.

Sabemos que um padrão que queremos encontrar no texto deve ser menor que o texto em si. Portanto, supondo que o tamanho do padrão seja ```py m``` e ```py n``` o tamanho do texto, podemos pegar as m primeiras letras para comparar, caso não seja igual, excluimos a primeira letra e adicionamos a próxima, caso contrário retorna a posição no texto onde encontrou. Repetindo isso até ```py m==n``` teremos varrido o texto todo!

```c
algoritmo_ingenuo(texto, padrao, n, m){
    lista = []
    Enquanto(contador <= n-m){
        Se (padrão == texto[contador:n-1]{
            lista.append(contador)
        }
        contador +=1;
    }
    return lista
}
```

Vamos ver isso na prática! As imágens abaixo ilustram o que esta acontecendo nele:

I N S E R I R  I M A G E M

??? Checkpoint 1
Qual a complexidade do algoritmo

MELHORAR EXPLICAÇÃO DESSA RESPOSTA
::: Gabarito
O loop vai rodar por n-m vezes. Em cada iteração ele vai comparar o padrão, como esse tem m letras, vai fazer m operações.
Dessa forma, fazendo as simplificações de complexidade, temos que a complexidade é: O(nm)
:::
???

Isso não parece muito eficiente... Os valores de m na prática são muito grandes, a complexidade seria enorme!

??? Checkpoint 2
Qual o problema com esse método?

::: Gabarito

esse custo vem da comparação letra em letra

:::
???

Com isso em mente, vamos pensar em como melhorar ele

**O Algoritmo (quase) melhorado**

Vamos partir do seguinte pressuposto: cada string tem um identificador único (como se fosse uma impressão digital). Então se quisermos saber se duas strings são iguais podemos apenas comparar esse identificador!

Mas o que poderia ser esse identificador?

Uma boa idéia foi elaborada pelos cientistas da computação Richard M. Karp e Michael O. Rabin. Eles decidiram somar o valor ASCII referente aos charachteres da string, obtendo assim um valor que funciona como "impressão digital" da string. 

![](ascii_table.png)

$H(projeto) = H(p) + H(r) + H(o) + H(j) + H(e) + H(t) + H(o)$

$H(projeto) = 112 + 114 + 111 + 106 + 101 + 116 +111$

$H(projeto) = 771$

Legal! Vamos exercitar.

??? Checkpoint 3
Calcule os hashes das palavras: amor, omar e roma 

::: Gabarito
$H(amor) = 431$

$H(omar) = 431$

$H(roma) = 431$

![](ihrapaz.gif)

:::
???

É... ainda não estamos lá... 

**Algorítimo melhorado v2.0**

??? Checkpoint 4
O que gerou o erro acima?

::: Gabarito
O algorítimo não leva em conta a posição da letra
:::
???

Resolver isso é simples! Vamos multiplicar o valor por uma constante elevada a sua posição, assim duas letras iguais em posições diferentes afetam o hash da string de formas diferents. 

``` c
calcula_hash (string, tamanho, k):
    i = tamanho - 1
    total = 0

    enquanto(i != 0):
        total += ASCII(string[i]) * k ** i
        i -= 1
    
    retorna total
```

Usando a constante como 2, por exemplo, obtemos o seguinte resultado com a palavra "bug"

$H(bug) = H(b)*2³ + H(u)*2² + H(g)*2¹$

$H(bug) = 98*8 + 117*4 + 103*2$

$H(bug) = 1458$

!!! Atenção
As posições são de tras pra frente! Ou seja, na string "abc" a posição de "a" é 2, de "b" é 1 e de "c" é 0.
!!!

Vamos testar esse conceito.

??? Checkpoint 5
Calcule os hashes das palavras: amor, omar e roma 

Considere k=2

::: Gabarito
$H(amor) = 97*2⁴ + 109*2³ + 111*2² + 114*2¹ = 3096$

$H(omar) = 111*2⁴ + 109*2³ + 97*2² + 114*2¹ = 3264$

$H(roma) = 114*2⁴ + 111*2³ + 109*2² + 97*2¹ = 3342$

:::
???

Ótimo! Funciona. Vamos atualizar o primeiro pseudo código.

```c
algoritmo_quase_melhorado(texto, padrao, n, m, k){
    lista = []
    hash_padrao = calcula_hash(string, m, k)
    Enquanto(contador <= n-m){
        hash_techo = calcula_hash(texto[contador:n-1], m, k)
        Se (hash_padrao == hash_techo){
            adiciona contador a lista
        }
        contador +=1;
    }
    return lista
}
```

??? Checkpoint 6
Qual a complexidade do algoritmo?
::: Gabarito
O(nm)
:::
???

**Algorítimo melhorado v3.0**

Até agora não tratamos a complexidade do algorítimo, apenas mudamos a forma dele identificar a string.

A complexidade do algorítimo continua O(nm) porque continuamos fazendo uma loop de m repetições por comparação (função calcula_hash).

Como poderiamos superar isso?

Quando falamos de situações reais a próxima string a ser analisada vai ser sempre quase identica a anterior, mudando apenas a ultima letra e a poisção das outras.

Será que conseguimos pensar em uma forma de aproveitar o hash da string anterior para calcular o hash da atual?

Para isso, vamos voltar ao exemplo roma/omar 

??? Checkpoint 7
Ainda considerando k=2, como podemos calcular o valor hash de "omar" a partir do hash de "roma"?
::: Gabarito

Se subtrairmos o valor $H(r)*2⁴$, podemos atualizar o valor da constante para os outros charachteres e somar $H(r)*2¹$ 

$H(roma) = 114*2⁴ + 111*2³ + 109*2² + 97*2¹ = 3342$

$H(oma) = 111*2³ + 109*2² + 97*2¹ = 3342 - 114*2⁴ = 1518$

$(111*2³ + 109*2² + 97*2¹) * 2 = 3036$

$H(omar) = 111*2⁴ + 109*2³ + 97*2² + 114*2¹ = 3036 + H(r)*2¹$

$H(omar) = 3264$

funciona como se estivessemos "deslizando" a string para a próxima letra

I M A G E N S 

:::
???

Essa técnica se chama Roling Hash, vamos implementa-la no nosso pseudo código.


```c
rolling_rabin_karp (texto, padrao, n, m):
    hash_referencia = calcula_hash(padrao)
    
    l = 0
    r = l + m

    string = texto de l até r
    hash_string = calcula_hash(string)

    encontrados = []
    enquanto(r <= n):

        r += 1

        hash_string = (hash_string - ASCII(l)*k**m + ASCII(r)) * k

        se(hash_string == hash_referencia):
            adiciona l a encontrados

        l += 1
    
    retorna encontrados
```
Apesar de funcional, esse método ainda é suscetível a falhas, por exemplo, as strings ``py 2d`` e ``py K2`` possuem o mesmo valor hash (400) para constante k=2.

**Implementações de Rabin-Karp na prática**

Podemos implementar o algoritmo de Rabin-Kharp de duas maneiras.

***Monte Carlo***

Implementado como Monte Carlo, quando o hash calculado é igual ao hash buscado, o índice desse valor é salvo como uma correspondência.
Apesar de ser improvável, podem existir duas strings diferentes que geram o mesmo hash, levando o algoritmo a cometer erros. 


??? Checkpoint 8
Qual é a complexidade

::: Gabarito
O loop vai rodar por n-m vezes, até o final do texto. Assim a complexidade será O(n)
:::
???

***Las Vegas***

A versão Las Vegas do Algoritmo, ao encontrar uma correspondência, compara letra por letra com a palavra buscada, garantido que são iguais.
Diferentemente do Monte Carlo, esse algoritmo não erra, porém ele tem maior complexidade, sendo que no pior dos casos ele pode ter a mesma eficiência do algoritmo ingênuo.


??? Checkpoint 9
Qual é a complexidade

::: Gabarito
Como o algoritmo faz a comparação de strings na força bruta apenas nas correspondências.No melhor caso o algoritmo rodará por todo o texto e quando achar um hash igual vai comparar letra a letra, tendo a complexidade igual a O(n+m).
No pior dos casos o todos os hash do texto serão iguais ao do padrão, dessa forma, o algoritmo vai rodas pelo texto inteiro e a cada iteração vai comparar letra a letra, tendo uma complexidade igual a O(nm)

:::
???