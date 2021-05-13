Algorítimo Rabin-Karop para busca em texto
======

Antes de entender o algorítimo Rabin-Karp, precisamos entender no que ele se baseia.

Sabendo que todo charachter possui um valor ASCII associado, responda:

**como podemos fazer para diferenciar uma string de outra usando o valor ASCII de seus charachteres?**

se você imaginou que devemos somar o valor relativo a cada charachter e comparar com o da outra string, esta no caminho certo!!

??? Checkpoint

Essa lógica apresenta uma falha, qual?

::: Gabarito
Ela não nos previne contra coincidências!

Imagine a seguinte tabela ASCII:
| charachter |    ascii   |
|------------|------------|
| a          | 1          |
| b          | 2          |
| c          | 3          |


As strings "bb", "ca" e "ac" teriam o mesmo valor.
:::
???

??? Checkpoint

Como podemos resolver essa falha?

::: Gabarito

Fácil! se multiplicarmos o valor ASCII do charachter por uma constante elevada a sua posição evitamos todo tipo de coincidência.

Fazendo a somatória dos valores referentes a cada charachter, acabamos com o valor Hash referente a essa string!

!!! Atenção
As posições são de tras pra frente! Ou seja, na string "abc" a posição de "a" é 2, de "b" é 1 e de "c" é 0.
!!!

:::
???

??? Questão 1

Usando a tabela do gabarito do checkpoint 1, calcule o valor Hash das strings "bb", "ca" e "ac".

::: Gabarito

| string      |     hash    |
|-------------|-------------|
| bb          | 2k¹ + 2k⁰   |
| ac          | 1k¹ + 3k⁰   |
| ca          | 3k¹ + 1k⁰   |

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

Agora que sabemos o que é um valor hash, fica fácil de encontrar strings dentro de textos. Basta procurar dentro do texto uma string de n charachteres que tenha o mesmo valor hash que a referência!

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

??? Checkpoint

Esse método pode ser aprimorado? Como?

::: Gabarito
Ao calcularmos o hash value da string referente aos n primeiros charachteres do texto, estamos calculando o hash referente aos n-1 charachteres da próxima string dividios por k!
Se utilizarmos o hash value da string anterior, remover o hash do primeiro charachter, atualizar a constante que multiplicao resto e somar o hash do próximo charachter, podemos otimizar o algorítimo!
:::
???


??? Questão 2
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

Esse método que descrevemos é conhecido como Rolling Hash!


referencia
---------

Para criar um parágrafo, basta escrever um texto contínuo, sem pular linhas.

Você também pode criar

1. listas;

2. ordenadas,

assim como

* listas;

* não-ordenadas

e imagens. Lembre que todas as imagens devem estar em uma subpasta *img*.

![](logo.png)

Para tabelas, usa-se a [notação do
MultiMarkdown](https://fletcher.github.io/MultiMarkdown-6/syntax/tables.html),
que é muito flexível. Vale a pena abrir esse link para saber todas as
possibilidades.

| coluna a | coluna b |
|----------|----------|
| 1        | 2        |

Ao longo de um texto, você pode usar *itálico*, **negrito**, {red}(vermelho) e
[[tecla]]. Também pode usar uma equação LaTeX: $f(n) \leq g(n)$. Se for muito
grande, você pode isolá-la em um parágrafo.

$$\lim_{n \rightarrow \infty} \frac{f(n)}{g(n)} \leq 1$$

Para inserir uma animação, use `md ;` seguido do nome de uma pasta onde as
imagens estão. Essa pasta também deve estar em *img*.

;bubble

Você também pode inserir código, inclusive especificando a linguagem.

``` py
def f():
    print('hello world')
```

``` c
void f() {
    printf("hello world\n");
}
```

Se não especificar nenhuma, o código fica com colorização de terminal.

```
hello world
```


!!! Aviso
Este é um exemplo de aviso, entre `md !!!`.
!!!


??? Exercício

Este é um exemplo de exercício, entre `md ???`.

::: Gabarito
Este é um exemplo de gabarito, entre `md :::`.
:::

???
