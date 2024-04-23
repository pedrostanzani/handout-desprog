# Algoritmo de Karatsuba

## Introdução

O Algoritmo de Karatsuba, descoberto por Anatoly Karatsuba, é um método para multiplicar dois números grandes. Ele é um algoritmo de fast multiplication, e é mais eficiente do que o método tradicional de multiplicação numérica.

## Algoritmo padrão

O algoritmo padrão, também conhecido como "multiplicação longa", é a forma como aprendemos a multiplicar no colégio.

??? Checkpoint
Vamos começar com algo ridículo, só para verificar se vocês estão acordados. Qual é o resultado de `txt 23.958.233 × 5.830`?

Resolva à mão usando o algoritmo padrão. Não use calculadoras.

::: Gabarito
O resultado é de `txt 139.676.498.390`.

```
      23958233
×         5830
———————————————
      00000000 ( =  23.958.233 ×     0)
     71874699  ( =  23.958.233 ×    30)
   191665864   ( =  23.958.233 ×   800)
+ 119791165    ( =  23.958.233 × 5.000)
———————————————
  139676498390 ( = 139.676.498.390)
```

:::
???

O método de multiplicação longa necessita de $n^2$ multiplicações de um dígito simples.

## Fundamentos do algoritmo de Karatsuba

O algoritmo se baseia na observação de que:

$$A = a \cdot 10^n + b$$
$$B = c \cdot 10^n + d$$

O produto $A \cdot B$ pode ser expresso como:

$$A \cdot B = ac \cdot 10^{2n} + (ad + bc) \cdot 10^n + db$$

Essa fórmula precisa de 4 multiplicações para que se resolva o produto $A \cdot B$. Entretanto, Karatsuba observou que o produto pode ser computado em apenas 3 multiplicações com o custo adicional de mais adições. Para isso:

1. Computamos o produto $p_1 = ac$. Perceba que foi realizada apenas 1 multiplicação.
2. Computamos o produto $p_2 = bd$. Perceba que, assim como no passo anterior, foi realizada apenas 1 multiplicação.
3. Ao invés de computar os produtos $ad$ e $bc$ separadamente, computamos o produto $p_3 = (a + b) \cdot (c + d)$. Note que, após as somas, apenas 1 multiplicação é realizada.

Perceba que, aplicando a distributiva:

$$p_3 = ac + ad + bc + bd$$
$$p_3 = p_1 + ad + bc + p_2$$

Como temos os valores de $p_1$, $p_2$ e $p_3$, podemos calcular $ad + bc$ com a seguinte subtração:

$$ad + bc = p_3 - p_1 - p_2$$

E, com isso, temos que:

$$A \cdot B = p_1 \cdot 10^{2n} + (p_3 - p_1 - p_2) \cdot 10^n + p_2$$

## Como funciona?

## Desafios

??? Desafio 1
Agora que você entendeu o funcionamento do algoritmo (É o que se espera aqui), vamos partir para o primeiro Desafio: Escrever o algoritmo de karatsuba na form de um pseudo-código.  Para isso segue o passo a passo:
1. Determine o tamanho máximo (número de dígitos) dos números de entrada, o que é alcançado convertendo os números em strings e calculando o comprimento das strings (pode fazer isso em uma função separada chamada get_size)
2. Divida X em (a e b) e Y em (c e d) de forma que as suas partes posteriores (b e d) sempre recebam o dígito extra no caso de o tamanho (n) ser ímpar. Lembre-se:

```txt
n = ceil(tamanho/2)
p = potência(10,n) // equivalente a 10^n
a = piso(X/p)
b = X%p
c = piso(Y/p)
d = Y%p
X = m^ceil(n/2) * a + b, Y = m^ceil(n/2) * c + d
```
$$ X = m^{\text{ceil}\left(\frac{n}{2}\right)} \times a + b, Y = m^{\text{ceil}\left(\frac{n}{2}\right)} \times c + d $$
Por exemplo, se X é 2023 e Y é 3003, então n = 2, p = 100, a = 20, b = 23, c = 30, d = 3.
Portanto, 
$$X = 10^2 * 20 + 23 e Y = 10^2 * 30 + 3$$
3. Agora, repita os passos anteriores para multiplicar ainda mais os números divididos dos passos 1 e 2 até que o caso base seja alcançado, ou seja, quando X e Y se tornam menores que 10.

Aqui karatsuba é uma chamada recursiva.
```txt
ac = karatsuba(a,c)
bd = karatsuba(b,d)
e = karatsuba(a+c, b+d) - ac - bd
```

4. Por último retorne a equação resultante como mostrado,
return power(10,2*n)ac + power(10,n)e + bd

::: Gabarito

```
Function karatsuba(X, Y){
	// caso base
	If (X < 10 and Y < 10)
		return X*Y;

	// determine o tamanho dos numeros
	size = maximum(Length(string(X)), Length(string(Y)))


	// Divida X e Y
n = ceil(size/2)
p = power(10,n) // equivalente a 10n
a = floor(X/p)
b = X%p
c = floor(Y/p)
d = Y%p

// Recurção até caso base
ac = karatsuba(a,c)
bd = karatsuba(b,d)
e = karatsuba(a+c, b+d) - ac - bd
	
	// Retorne multiplicação
	return power(10,2*n)*ac + power(10,n)*e + b

}
```

:::
???

??? Desafio 2
Agora traduza seu pseudo-codigo para um codigo em c. Faça duas funções, uma chamada getSize que recebe um número e devolve o tamanho de dígitos e outra chamada karatsuba que recebe dois números e devolve o produto deles utilizando o método de karatsuba.
Não se preocupe em ter o código igual ao do gabarito, mas teste sua função com os seguintes casos:
```txt
123456*789101 = 97 419 253 056
7888999*6574831 = 51 868 835 184 169
50*125 = 6.250
```

::: Gabarito
```c
#include <stdio.h>
#include <math.h>

// Get size of the numbers
int getSize(long num)
{
    int count = 0;
    while (num > 0)
    {
        count++;
        num /= 10;
    }
    return count;
}

long karatsuba(long X, long Y)
{
    // Caso base
    if (X < 10 && Y < 10)
        return X * Y;

    // Determine o tamanho de X e Y
    int size = fmax(getSize(X), getSize(Y));

    // Divida X e Y
    int n = (int)ceil(size / 2.0);
    long p = (long)pow(10, n);
    long a = (long)floor(X / (double)p);
    long b = X % p;
    long c = (long)floor(Y / (double)p);
    long d = Y % p;

    // Recorra até caso base
    long ac = karatsuba(a, c);
    long bd = karatsuba(b, d);
    long e = karatsuba(a + b, c + d) - ac - bd;

    // Retorne a equação
    return (long)(pow(10 * 1L, 2 * n) * ac + pow(10 * 1L, n) * e + bd);
}
```

:::
???

