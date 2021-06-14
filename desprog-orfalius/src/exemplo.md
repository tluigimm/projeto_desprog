Algoritimo Rabin-Karp para busca em texto
======

**Introdução**

Algoritmos para busca em texto são muito comuns na computação. Esses consistem basicamente em achar um padrão pré estabelecido em um texto.
Apesar das diferentes implementações que existem, todas têm as mesmas entradas e saídas.

Entradas:
* Padrão a ser encontrado ex. "projeto";
* Texto ex. "desafios - projeto 1"

Saída:
* 11 - que é o índice onde começam o padrão

A busca em texto tem uma série aplicações:

* Achar uma palavra em um texto;
* Busca por arquivos que contêm certas palavras chaves;
* Detecção de plágio

**O algoritmo ingênuo**

Quando você pensa em algorítimos de comparação de texto, provavelmente a primeira coisa que vem a sua cabeça é alguma forma de comparar letra por letra das entradas.

Esse é um método funcional, apesar de ser longe do mais eficiente. Todavia, vamos nos manter com ele por enquanto.

Sabemos que o padrão que queremos encontrar no texto deve ser menor que o texto em si. Portanto, supondo que o tamanho do padrão seja ``py m`` e ``py n`` o tamanho do texto, devemos pegar as m primeiras letras do texto para poder fazer a comparação. Dessa forma, separamos as m primeiras letras do texto e comparamos uma a uma com o padrão.

Agora, precisamos achar uma forma de varrer o texto inteiro em busca do padrão.
O que podemos fazer é: deslocar o padrão no texto em uma casa, da seguinte forma:

;rolling

Feito isso, precisamos, para cada passo, comparar o trecho selecionado com o padrão pré estabelecido. O jeito mais simples de se fazer isso é comparar letra por letra.

Se todas as letras forem iguais, é necessário salvar o índice, uma vez que foi achado um ocorrência do padrão no texto.


```c
algoritmo_ingenuo(texto, padrao, n, m){
    lista = []
    Enquanto( contador <= n-m){
        For(letras no padrão)
        Se (padrão == texto[contador:n-1]{
            lista.append(contador)
        }
        contador +=1;
    }
    return lista
}
```
A simulação do loop principal é:

;rolling_index

Simulação do loop interno na primeira iteração:

;ingenuo




??? Checkpoint 1
Qual a complexidade do algoritmo

::: Gabarito
Observando o loop principal, é possível perceber que esse vai rodar por n-m vezes.
O loopo interno, vai compara letra por letra, sabendo que o padrão tem m letras, sabemos que haverá m iterações.
Assim, para cada iteração do loop principal, temos m iterações do loop interno. Portanto a complexidade será O(m*(n-m)).
Simplificando, temos que a complexidade é de O(nm)
:::
???

Isso não parece muito eficiente... Os valores de m e n na prática podem ser muito grandes, a complexidade seria enorme!

O problema com esse método é que esse possui uma complexidade muito alta. No nosso exemplo, usamos um texto pequena, mas imagine uma implementação para a detecção de plágio, na qual procura-se uma frase em todos os trabalhos ja enviados pelos alunos do Insper. Teriamos um gasto de processamento muito grande, o que tornaria o processo inviável.

Ao analisar o problema que o algoritmo tenta resolver e sua implementação é possível perceber que é um processo muito custoso.
No entando, o seu loop principal (que varre o texto) é essencial, uma vez que precisamos, analisar a ocorrência no texto.
O processo de comparar letra por letra, no entando, pode ser revisto e simplicado, de modo a tentar reduzir a complexidade.


**O Algoritmo (quase) melhorado**

Vamos partir do seguinte pressuposto: cada string tem um identificador único (como se fosse uma impressão digital). Então se quisermos saber se duas strings são iguais podemos apenas comparar esse identificador!

Mas o que poderia ser esse identificador?

Uma boa idéia seria somar o valor ASCII referente aos caracteres da string, obtendo assim um valor que funciona como "impressão digital" da string. 

![](ascii_table.png)

Agora vamos usar a tabela ASCII para calcular o hash da palavra "projeto". É muito simples basta, para cada letra, encontrar o seu valor numérico e depois somar todos os valores:


$H(projeto) = H(p) + H(r) + H(o) + H(j) + H(e) + H(t) + H(o)$

$H(projeto) = 112 + 114 + 111 + 106 + 101 + 116 +111$

$H(projeto) = 771$


Legal! Vamos exercitar.

??? Checkpoint 2
Calcule os hashes das palavras: amor, omar e roma 

::: Gabarito
$H(amor) = 431$

$H(omar) = 431$

$H(roma) = 431$

![](ihrapaz.gif)

:::
???

É... ainda não estamos lá... 

Como é possível perceber pelos resultados dos hashes das palavras calculadas a cima, existe um grande problema nesse método: palavras diferentes podem ter o mesmo valor, causando um falso positivo.
Dessa forma, usando a implementação como foi apresentada, o algoritimo pode indicar trecho do texto que não é igual ao padrão fornecido pelo usuário.

Esse erro ocorre porque, apesar de as letras serem as mesmas, as palavras não são iguais. 

**Algorítimo melhorado v2.0**

??? Checkpoint 3
Que outras informações podemos levar em conta para diferenciar as palavras?
::: Gabarito
Podemos levar em conta a sua posição na palavra. Dessa forma, por exemplo, a letra "a" nos exemplos do checkpoint 2 somariam valores diferentes ao resultado final.
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

??? Checkpoint 4
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

Agora temos um código que compara impressão digital de string ao invés de comparar letra por letra.

??? Checkpoint 5
Houve melhoria na complexidade?
::: Gabarito
Assim como o algoritimo ingênuo, esse, em seu loop principal terá n-m iterações. Em seu loop interno, as m letras do padrão serão somadas, ou seja haverá m operações por iteração.
Dessa forma, a complexidade será de O(m*(n-m)).
Simplificando, temos que a complexidade é de O(nm).

Portanto, não, não houve melhoria na complexidade.
:::
???


**Algorítimo melhorado v3.0**

Até agora não tratamos a complexidade do algorítimo, apenas mudamos a forma dele identificar a string.

A complexidade do algorítimo continua O(nm) porque continuamos fazendo uma loop de m repetições por comparação (função calcula_hash).

Como poderiamos superar isso?

Vamos praticar com uma situação mais próxima do real para procurar alguma saida.

??? Checkpoint 6
Mantendo k=2, aplique o algoritimo_quase_melhorado na string "abcdefg" procurando a string "def". Preste atenção em como poderiamos otimizar o processo.
::: Gabarito
;abcdefg
:::
???

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
Legal! Vamos praticar mais.

??? Checkpoint 8
Utilize o rolling_rabin_karp com a string "ADAABDCB" procurando a string "BDC".
Percebe algum erro?
::: Gabarito
As strings "BDC" e "DAA" possuem o mesmo valor hash.
:::
???

Apesar de funcional, esse método ainda é suscetível a falhas. Contudo, existem meios para superar elas.

**Implementações de Rabin-Karp na prática**

Com o que vimos até agora, ainda existe a possibilidade de erro. Podemos fazer algumas suposiçoes sobre a complexidade para então encontrarmos uma maneira eficiente de aprimorarmos a solução.


??? Checkpoint 9
Sabendo que o algoritmo percorrerá o texto inteiro, qual será a complexidade?

::: Gabarito
O loop irá rodar por n-m vezes, até o final do texto. Assim a complexidade será O(n)
:::
???

Existe então uma complexidade linear porém ainda há a possibilidade de erro, isso é chamado de algoritmo de Monte Carlo.
 Podemos aprimorar os resultados sem aumentar muito a complexidade adicionando um metódo de checagem, toda vez que o algoritmo encontra uma corresponencia.

??? Checkpoint 10
Como poderiamos implementar uma checagem, de forma a garantir o resultado positivo?

::: Gabarito
Ao encontrar uma correspodencia, podemos checar se todas as letras são iguais, garantindo o acerto do algoritimo.
:::
???

??? Checkpoint 11
Levando em consideração que existem um tempo adicional para checar a resposta a cada correspondencia, qual seria a complexidade no pior caso?

::: Gabarito
No pior caso, o algoritmo encontrará hashes em todas as posiçoes, portanto terá que checar a igualdade letra a letra. A complexidade nesse caso será igual ao algoritmo ingenuo O(n+m).
:::
???

??? Checkpoint 12
Agora pensando que existe apenas uma corresponcia, qual seria a complexidade no melhor caso?

::: Gabarito
No melhor caso, o algoritmo percorre o texto por completo, porém, como só existem uma correspondencia, só é comparado letra a letra uma vez, resultando na complexidade O(n*m).
:::
???


Essa forma de pensar é classificada com algoritmo de Las Vegas, onde é garantida a certeza da resposta, porem existe a possibilidade aletoria de uma complexidade alta, algo comparavel á um cassino.
