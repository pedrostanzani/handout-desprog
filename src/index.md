# Algoritmo de Karatsuba

## Introdução

O **Algoritmo de Karatsuba**, descoberto por Anatoly Karatsuba, é um método para multiplicar dois números grandes. Ele é um algoritmo de fast multiplication, e é mais eficiente do que o método tradicional de multiplicação numérica.

<div style="display: flex; flex-direction: column; justify-content: center; align-items: center; gap: 6px; padding-top: 16px;">
  <img src="Karatsuba.jpg" alt="Anatoly Karatsuba" width="200"/>
  <p style="font-size: 0.75rem; margin-bottom: 0;">Anatoly Karatsuba, matemático russo (1937 - 2008).</p>
</div>

## Motivação

Se as multiplicações em computadores já são quase instantâneas, por que que alguém iria querer um algoritmo de multiplicação mais rápido?

Para números pequenos, de fato não faz sentido utilizar o algoritmo de Karatsuba. Inclusive, para números muito pequenos a quantidade de chamadas recursivas e outras operações do algoritmo podem fazer sua complexidade ser superior à múltiplicação tradicional.

Mas o algoritmo de Karatsuba começa a ser útil quando você estiver tratando números muito grandes, de mais de 100 dígitos! Para números maiores de por exemplo 570 dígitos, o algoritmo tradicional leva $324.900$ computações entre números de um dígito, enquanto o algoritmo de Karatsuba leva aproximadamente $23.332$ computações equivalentes.

??? Aplicações de Karatsuba (opcional)

Se quiser entender porque alguém iria querer fazer operações entre 2 números de 570 dígitos, esse será um ótimo ponto de partida.

Mas não se preocupe: esse não é o foco da nossa aula.

::: Gabarito
Este é um exemplo de gabarito, entre `md :::`.
:::

Quando falamos em números excessivamente extensos, a primeira área que deve vir à sua mente é a criptografia.

Espeficamente falando, uma aplicação do algoritmo de Karatsuba é na criptografia RSA (Rivest-Shamir-Adleman), um dos primeiros sistemas de criptografia de chave pública que é amplamente utilizado para transmissão segura de dados.

Mudando um pouco de área, Karatsuba também é usado no mundo da computação científica em áreas como dinâmica de fluidos computacional, em uma transformada de Fourier discreta (DFT), especialmente aquelas que são aplicadas em grids de alta resolução, a multiplicação de polinômios torna-se uma operação computacionalmente intensiva.

Fique à vontade para pesquisar mais aplicações desse algoritmo. Sugiro começar por [aqui](https://dergipark.org.tr/en/download/article-file/2160206).

???

<hr>

## Algoritmo padrão

O algoritmo padrão, também conhecido como "multiplicação longa" ou "método escolar", é a forma como aprendemos a multiplicar no colégio. É aquela que multiplicamos cada dígito de um número pelo outro e somamos as linhas ao final. 

Digamos que, por exemplo, você tinha que multiplicar $1370$ por $5830$ em uma prova do ensino médio, onde não era permitido o uso de calculadora. Você realizaria a operação da seguninte maneira (algoritmo):

```md
      1370
×     5830
—————————————
      0000 
     41100  
   1096000  
+  6850000
—————————————
   7987100 
```

??? Checkpoint

Vamos considerar que uma operação atômica é aquela que envolve estritamente **2 valores de dígitos únicos**. Por exemplo:

- $3 \cdot 7$
- $8 \cdot 1$

Pensando nisso, qual é a complexidade da "multiplicação longa"?

Não precisa fazer conta, pense na maneira geral de como o algoritmo intuitivamente funciona.

::: Gabarito

Como você deve ter imaginado, se multiplicamos um número de n dígitos por outro de n dígitos, necessita-se de $n \cdot n$ multiplicações de um dígito simples. Portanto a complexidade desse algoritmo é $O(n^2)$.

:::

???

Tecnicamente, a complexidade da "multiplicação longa" será um pouco maior que $n^2$, pois temos que realizar n somas, referente aos digitos indiviuais multiplicados. Mas, por simplificação, podemos considerar $n^2 + n$ como sendo $O(n^2)$. 

!!! Aviso
Para o restante do handout, se preocupe apenas com as multiplicações quando pensarmos em complexidade. Elas são mais custosas para o computador do que somas e subtrações.
!!!


## Fundamentos do algoritmo de Karatsuba

Em 1960, o jovem Anatoly Karatsuba de 23 anos, enquanto atendia à um seminário do grande matemático [Andrei Kolmogorov](https://pt.wikipedia.org/wiki/Andrei_Kolmogorov) foi exposto à seguinte afirmação: 

— É impossível fazer uma multiplicação em menos de $2n^2-1$ operações. 

Intrigado pela alegação, Karatsuba começou a pensar sobre o problema e, em menos de uma semana, ele descobriu um algoritmo mais eficiente.

Vamos agora tentar entender como ele chegou à solução tão rapidamente. 

<hr>

Você tem em mãos dois números de n dígitos, mas só pode lidar com operações entre dígitos individuais. Você precisa "quebrar" seus números de alguma forma a obter 1 dígito.

No método tradicional, você "quebra" esse valor da direita (casa das unidades) para esquerda (subindo para as dezenas, centenas, milhares...) e realiza a operação entre cada dígito dos dois números.

Karatsuba teve que pensar numa forma inteligente de dividir os dígitos de um número: dividir para conquistar! É exatamente essa a caracterização do algoritmo, **divide and conquer**.

Imagine uma multiplicação entre dois números de 2 dígitos que queremos multiplicar: 15 e 20. Vamos, de forma grosseira, "dividir ao meio" cada um desses números, separando dezenas e unidades, e chamaremos então:
$$ a = 1 $$
$$ b = 5 $$
$$ c = 2 $$
$$ d = 0 $$

??? Checkpoint
Escreva:

- em função apenas de $a$ e $b$ o número $15$; e 
- em função apenas de $c$ e $d$ o número 20.

::: Gabarito

- $15 = a \cdot 10 + b$
- $20 = c \cdot 10 + d$

:::
???

Com isso, o próximo exercício se torna trivial. Se preferir, não se preocupe em escrever no papel, esses checkpoints são para mantê-lo ativo mentalmente.
??? Checkpoint
Escreva a multiplicação $15 \cdot 20$ em função de $a$, $b$, $c$, $d$:

::: Gabarito

$$15 \cdot 20 = (a \cdot 10+b) \cdot (c \cdot 10+d)$$
$$15 \cdot 20 = 100 \cdot ac + 10 \cdot ad + 10 \cdot bc + bd$$
Ou, simplificando:
$$15 \cdot 20 = 100 \cdot ac + 10 \cdot (ad + bc) + bd$$

:::
???

Voltando ao método escolar, levamos 4 multiplicações individuais para alcançarmos o resultado final: $ac$, $ad$, $bc$, $bd$. Com esses valores, os inserimos na equação do checkpoint acima. Vamos prosseguir com a conclusão de Karatsuba:
??? Checkpoint
Karatsuba notou o seguinte: Vamos computar $ac$ e $bd$. Será que não é possível agora escrever $ad+bc$ (a soma das computações restantes) sem fazer necessariamente mais 2 multiplicações ($ad$ e $bc$)?

Tente chegar à essa conclusão, mas não se sinta mal se não conseguir e nem perca muito tempo aqui tentando. Lembre-se que até 1960, ninguém na história da humanidade conseguiu concluir isso.

::: Dica
Se atente à essa versão da equação:
$$15 \cdot 20 = 100 \cdot ac+10 \cdot (ad+bc) + bd$$
Olhe especificamente para $(ad+bc)$. Tente reescrever essa seção, utilizando apenas 1 multiplicação a mais (lembrese que $ac$ e $bd$ já estão calculados).

Se você não conseguiu, não se preocupe: tente novamente com mais uma dica.

::: Dica
O termo $ad + bc$ possui 2 multiplicações. O que será que acontece quando nós o isolamos?

::: Gabarito

É possível realizar a operação $15 \cdot 20$ com 3 multiplicações de dígitos indivíduais, ao invés de 4. Foi o seguinte que Anatoly Karatsuba conseguiu notar:
$$ad+bc = (a+b)(c+d)−ac−bd$$
Assim, ao invés de computar $ad$ e $bc$ (2 multiplicações) e somá-las, eu computo $(a+b)(c+d)$, que equivale à uma multiplicação. 

Depois disso, consigo subtrair $ac$ e $bd$ (que eu já tenho calculado) e encontrar $ad+bc$, que é o que eu preciso para fazer $15 \cdot 20 = 100 \cdot ac + 10 \cdot (ad+bc) + bd$.

O número de somas aumentou. Mas pense que multiplicações são mais custosas para o computador. Se tratando de números muito grandes, vamos ganhar eficiência fazendo menos multiplicações, mesmo que isso custe algumas somas a mais.
:::
???

## Generalização do Algoritmo

Consideramos apenas números de dois dígitos. Já se questinou se cada número tivesse diversos dígitos?

??? Checkpoint
Se vou multiplicar dois números A e B de n dígitos nós vamos primeiro quebrá-los, tanto A quanto B, em 2, como fizemos anteriormente. 

Se não ficou claro, pense no número $143127$: $a$ seria $143$ e $b$ seria $127$.

Agora escreva A e B em função apenas de $n$ (número de dígitos) e suas metades ($a$ e $b$ referentes ao número inteiro A e $c$ e $d$ referentes ao número inteiro B):

::: Gabarito

$$A = a \cdot 10^{n/2} + b$$
$$B = c \cdot10^{n/2} + d$$

:::
???

Assim podemos generalizar também que
$$A \cdot B = ac \cdot 10^{n} + (ad + bc) \cdot 10^{n/2} + db$$
Como $ad+bc = (a+b)(c+d)−ac−bd$ ainda é válido, podemos, para qualquer número, realizar as 3 multiplicações elementares:
$$a \cdot c$$
$$(a+b)\cdot(c+d)$$
$$d \cdot b$$
Como foi explicado anteriormente, precisamos dividir para conquistar! 

Lembre-se que analisamos multiplicações de dígitos simples para cálculo de complexidade, então para o número $143127$, não basta fazer essa quebra ao meio 1 vez. Precisamos continuar quebrando o número até alcançarmos 1 dígito (sabemos que isso acontece quando um valor A é menor que $10$). No caso de $15 \cdot 20$, uma quebra já é o suficiente para chegarmos a um dígito.

??? Checkpoint
Sem pensar muito em código por agora (se tiver interessado nisso, teremos 2 desafios no final), tente resumir o algoritmo de Karatsuba em seus principais pontos-chave.  Não se preocupe em ser preciso, tente se lembrar do que fizemos e já olhe o gabarito, pois ele será uma boa síntese do conteúdo apresentado até aqui.
::: Gabarito

Devolve $A \cdot B$ se ambos os números forem menores que 10, se não faça o seguinte:

1. Divide o número A em duas metades $a$ e $b$.
2. Divide o numero B em duas metades $c$ e $d$.
3. Calcule as três multiplicações de Karatsuba $ac$, $bd$ e $(a+b)\cdot(c+d)$ **recursivamente**, até que os valores de a, b, c, d sejam menores que 10 (tenham 1 dígito).
4. Finalmente encontre $A \cdot B = ac \cdot 10^{n} + (ad + bc) \cdot 10^{n/2} + db$.

:::
???
Talvez você ainda ficou na dúvida quanto à essa recursão do passo 3. Tente refletir a respeito e se continuar com dúvida, discuta com seus colegas. A ideia aqui é realizar a quebra do número até que se tenha 1 dígito. Quando esse dígito único for alcançado, será a condição em que a função "escapa" da recursão e retorna o valor da multiplicação.
Se a dúvida ainda persistir, faça o checkpoint a seguir:

??? Checkpoint
Multiplique, no papel, utilizando o algoritmo de Karatsuba os valores $1461$ e $3521$. Primeiro tente desenhar como ficariam as quebras dos números para fazer a multiplicação.
::: Gabarito

:Karatsuba

::: Continuar
Começando pela ramificação mais à esquerda, onde multiplicamos 14\*35, podemos fazer
$$ac \cdot 10^{n} + [(a+b)(c+d)−ac−bd] \cdot 10^{n/2} + bd$$
Sendo $ac$ igual a 3, $bd$ igual a 20 e $(a+b)(c+d)$ igual a 40.

Assim, substituindo na formula acima, encontramos que:
$$14*35 = 300+170+20 = 490$$
Para a segunda ramificação (61*21), fazemos o processo análogo, com $ac = 12$, $bd = 1$ e $(a+b)*(c+d) = 21$. Portanto,
$$61\cdot21 = 1200+80+1 = 1281$$
Calcule agora você a terceira ramificação  e conclua finalmente a a resposta de $1461\cdot3521$ com o algoritmo de Karatsuba
::: Gabarito final
Para encontrarmos $(a+b)*(c+d)$ da terceira ramificação, precisamos fazer mais uma recursão do algoritmo (lembre que só podemos multiplicar dígitos únicos). Assim,
$$12\cdot11 = ac \cdot 10^{2} + [(a+b)(c+d)−ac−bd] \cdot 10 + bd = 100+30+2 = 132$$
Podemos concluir então, que na terceira ramificação:
$$75\cdot56 = ac \cdot 10^{2} + [(a+b)(c+d)−ac−bd] \cdot 10 + bd = 3500+670+30 = 4200$$

Assim, conseguimos calcular $1461\cdot3521$, pois temos calculados seus respectivos valores $ac$, $bd$ e $(a+b)\cdot(c+d)$. Portanto:
$$1461\cdot3521 = ac \cdot 10^{4} + [(a+b)(c+d)−ac−bd] \cdot 10^{2} + bd$$
$$1461\cdot3521 = 4900000+242900+1281=5.144.181$$

:::
???

## Cálculo de Complexidade

Por fim, se tiver interesse em um cálculo detalhado da complexiade, vamos lhe apresentar exatamente a conta aqui. Não se preoucupe se tiver dificuldade de seguir a matemática, o foco do handout era o seu entendimento, em um nível mais alto, dos procedimentos do método de Karatsuba.

![Descrição da imagem](arvore_de_recursao.png)

O algoritmo de Karatsuba reduz uma multiplicação de n bits para três
multiplicações de n/2 bits, que por sua vez são reduzidas a nove multiplicação de n/4 bits
e assim por diante. Podemos representar o custo computacional de todas
essas multiplicações em uma árvore ternária de profundidade log₂ n, onde na raiz
o custo extra é cn operações, no primeiro nível o custo extra é c(n/2)
operações, e em cada um dos nós $3^i$ do nível i, o custo extra é
$c(n/2^i)$. O custo total é cn Σ de i=0 até log₂ n de $(3/2)^i$ ≤ 10cn $log₂^3$
pela fórmula de soma de uma série geométrica.

Cada nó no nível $i$ da árvore ternária representa o custo extra de $c \space (\frac{n}{2^i})$ operações, onde $c$ é uma constante que abrange as operações de adição e subtração necessárias para combinar os resultados das multiplicações. Em cada nível $i$ da árvore, temos $3^i$ nós, cada um com um custo extra de $c \space (\frac{n}{2^i})$ operações.

Assim, o custo total $T(n)$ no nível $i$ é dado por:

$$T_i(n) = 3^i \cdot c \space (\frac{n}{2^i}) = 3^i \cdot \frac{c \cdot n}{2^i}$$

Portanto, o custo total em todos os níveis da árvore até a profundidade $\log_2 n$ é:

$$T(n) = cn \cdot \sum_{i = 0}^{log_2 n} (\frac{3}{2})^i$$

Usando a fórmula da soma de uma série geométrica, onde a razão $r = \frac{3}{2}$ é maior que 1, podemos calcular a soma como:

$$cn \cdot \sum_{i = 0}^{log_2 n} (\frac{3}{2})^i = \frac{1 - (\frac{3}{2})^{log_2 \space n+1}}{1 - \frac{3}{2}}$$

Simplificando, isso se torna:

$$\frac{2 \left[1 - \left(\frac{3}{2} \right)^{\log_2 n + 1} \right]}{-\frac{1}{2}} = 2 \left[\left(\frac{3}{2} \right)^{\log_2 n + 1} - 1 \right]$$

Como $\left(\frac{3}{2} \right)^{\log_2 n}$ é $n^{\log_2 \frac{3}{2}}$, temos:

$$T(n) = 2cn \left[n^{\log_2 \frac{3}{2}} - 1 \right] \approx 2cn \cdot n^{\log_2 \frac{3}{2}}$$

Como $\log_2 \frac{3}{2} \approx 0.585$, a complexidade do algoritmo de Karatsuba é então:

$$T(n) = \Theta(n^{\log_2 3}) \approx \Theta(n^{1.585})$$

Isso mostra que o algoritmo de Karatsuba tem uma complexidade substancialmente menor do que a multiplicação tradicional, que é $\Theta(n^2)$, e explica por que ele é mais eficiente para números grandes.

## Desafios

??? Desafio 1
Agora que você entendeu o funcionamento do algoritmo (É o que se espera aqui), vamos partir para o primeiro Desafio: Escrever o algoritmo de Karatsuba na forma de um pseudo-código. Para isso segue o passo a passo:

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
$$X = 10^2 _ 20 + 23 e Y = 10^2 _ 30 + 3$$ 3. Agora, repita os passos anteriores para multiplicar ainda mais os números divididos dos passos 1 e 2 até que o caso base seja alcançado, ou seja, quando X e Y se tornam menores que 10.

Aqui Karatsuba é uma chamada recursiva.

```txt
ac = Karatsuba(a,c)
bd = Karatsuba(b,d)
e = Karatsuba(a+c, b+d) - ac - bd
```

4. Por último retorne a equação resultante como mostrado,
   return power(10,2\*n)ac + power(10,n)e + bd

::: Gabarito

```
Function Karatsuba(X, Y){
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
    ac = Karatsuba(a,c)
    bd = Karatsuba(b,d)
    e = Karatsuba(a+c, b+d) - ac - bd

        // Retorne multiplicação
        return power(10,2*n)*ac + power(10,n)*e + b

}
```

:::
???

??? Desafio 2
Agora traduza seu pseudo-codigo para um codigo em c. Faça duas funções, uma chamada getSize que recebe um número e devolve o tamanho de dígitos e outra chamada Karatsuba que recebe dois números e devolve o produto deles utilizando o método de Karatsuba.
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

// Tamanho dos números
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

long Karatsuba(long X, long Y)
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
    long ac = Karatsuba(a, c);
    long bd = Karatsuba(b, d);
    long e = Karatsuba(a + b, c + d) - ac - bd;

    // Retorne a equação
    return (long)(pow(10 * 1L, 2 * n) * ac + pow(10 * 1L, n) * e + bd);
}
```

:::
???
