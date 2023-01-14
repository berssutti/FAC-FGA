# Circuitos Combinacionais

### Portas Lógicas

Normalmente representamos funções booleanas utilizando um diagrama lógico. As três portas lógicas básicas são, não surpreendentemente, as portas e, ou e não (também chamada de porta inversora). Os símbolos dessas três portas são:

![Untitled](Circuitos%20Combinacionais%20d1894efa47bd42fca3701a04764814c1/Untitled.png)

Usando apenas os três símbolos básicos (not, and, or) podemos desenhar diagramas lógicos para qualquer função booleana.  Apesar disso outras portas importantes tem seu próprio simbolo, como as portas nand, nor, xor.

![Untitled](Circuitos%20Combinacionais%20d1894efa47bd42fca3701a04764814c1/Untitled%201.png)

obs: Não existe port xor de mais de duas entradas.

obs: É comum utilizarmos o complemento de várias variáveis na implementação de funções booleanas. Por isso existe uma notação mais simples para a inversão de uma variável qualquer: a bolha.

**Algebra de Boole**

- $A + 1 = 1$
- $A + A = A$
- $A+ \overline{A} = 1$
- $A.1 = A$
- $A.A = A$
- $A. \overline{A} = 0$

**Propriedades**

- $A + B = B + A$
- $A.B=B.A$
- $A + (B+C) = (A+B)+C = A+B+C$
- $A.(B.C) = (A.B).C = A.B.C$

**Identidades**

- $A + A.B = A$
- $(A+B).(A+C) = A + B.C$
- $A + \overline{A}.B = A + B$

**Teoremas**

- $(\overline{A.B}) = \overline{A} + \overline{B}$
- $\overline{A} . \overline{B} = (\overline{A+B})$

![Untitled](Circuitos%20Combinacionais%20d1894efa47bd42fca3701a04764814c1/Untitled%202.png)

As portas lógicas que vimos podem ser implementadas utilizando transistores. *Transistores* são dispositivos de 3 terminais que agem como uma resistência controlada por tensão. A tensão aplicada no terminal de entrada (Vin) controla a resistência entre os demais terminais.

De maneira simplificada, se a tensão de entrada é 0, a resistência entre os demais terminais é muito alta e o transistor é dito desligado (off). Caso contrário, se a tensão de entrada é alta (5V), a resistencia entre os demais terminais é muito baixa e o transistor é dito ligado (on).

Portas lógicas podem ser encontradas na forma de circuitos integrados (CIs)

- No mesmo CI, em geral temos várias portas lógicas.
- O CI deve ser “alimentado” (isto é, deve-se fornecer a tensão V+ e uma referência gnd): Sim, ele deve ser ligado e, ao funcionar, consome energia!
- As carácteristicas específicas das portas de um CI, como o fan-in, fan-out, atraso etc … são fornecidas pelo fabricante no datesheet no CI.

Existem diversas famílias lógicas de CIs. As mais comuns:

- CMOS - *Complementary Metal Oxide Semiconductors.*
- ECL - *Emitter Coupled Logic.*
- TTL - *Transistor-Transistor Logic*

É mais facil implementar portar NAND e NOR com transistores do que implementar portas ANS e OR. Por essa razão é importante aprender a implementar circuitos utilizando apenas essas portas.

Para isso, é importante saber implementar funções booleanas com portas NAND. Para isso, utilizamos o Teorema de De Morgan.

### Decodificadores

Um *decodificador* é um circuito de múltiplas entradas e múltiplas saídas que converte códigos de entradas em códigos de saída, onde os dois códigos são diferentes. Em geral, o código de entrada tem menos bits que o código de saída. e existe um mapeamento um-para-um de código de entrada para o código de saída. O código de saída mais utilizado é o 1-de-m, que usa m bits para m códigos e, para cada código, apenas um dos m bits está ativo. Por exemplo:

| Ativo em alto | Ativo em baixo |
| --- | --- |
| 1000 | 0111 |
| 0100 | 1011 |
| 0010 | 1101 |
| 0001 | 1110 |

Para que o decodificador funcione, todas as suas entradas de enable (habilitadoras) devem estar ativadas. Caso qualquer uma delas não esteja ativa, o decodificador mapeia qualquer entrada para um código “desativado”.

Exemplo de decodificador 2-para-4:

![Untitled](Circuitos%20Combinacionais%20d1894efa47bd42fca3701a04764814c1/Untitled%203.png)

### Multiplexadores

Um *multiplexador* ( ou mux) é um switch/computador digital. Para cada saída, usamos n bits de seleção (em geral, com o nome de $s_i$) para selecionar um dentre $2^n$ entradas (logo, $s = [log_2n]$).

De forma geral, a equação do mux é:  $Y = \sum_{
\begin{subarray}{l}
   i = 0\\
n-1
\end{subarray}}
EN.m_i.X_i$

Podemos projetar um mux de 4 entradas de 1 saída. Para isso, precisamos de $s = [log_24] =2$

![Untitled](Circuitos%20Combinacionais%20d1894efa47bd42fca3701a04764814c1/Untitled%204.png)

### Somadores

A adição é a operação aritmética mais comumente realizada em circuitos. Um circuito somador (adder) combina dois operandos aritméticos usando as regras da adição binária. Vimos que o mesmo circuito pode ser usado para somar palavras sem sinal (usigned) e em complemento-de-2.

O circuito para somar dois bits (ou somar duas palavras de 1 bit) é chamado de somador parcial (half adder). Este circuito soma dois bits, A e B, e gera uma soma de  dois bits (de 0 a 2).

| A | B | C | S |
| --- | --- | --- | --- |
| 0 | 0 | 0 | 0 |
| 0 | 1 | 0 | 1 |
| 1 | 0 | 0 | 1 |
| 1 | 1 | 1 | 0 |

O que gera as esquações de soma parcial: 

$$
C = A.B\\
S = A\oplus B
$$

O circuito somador para somar três bits (ou somar duas palavras de 1 bit mais um carry) é chamado de somador total (full adder). Este circuito soma três bits, A, B e $C{
\begin{subarray}{l}
in
\end{subarray}}$, e gera uma soma de dois bits (de 0 a 3).

O que gera as esquações da soma total:

$$
C{
\begin{subarray}{l}
out
\end{subarray}}
= A.B + A.C{
\begin{subarray}{l}
in
\end{subarray}}
+ B.C{
\begin{subarray}{l}
in
\end{subarray}}\\
S = A\oplus B\oplus C{
\begin{subarray}{l}
in
\end{subarray}}
$$

Considere um circuito somador de duas palavras de n bits. Como este é um circuito puramente combinacional, podemos montar sua tabela verdade (com$2^{2n}$ linhas ou entradas possíveis), e minimizá-lo usando qualquer técnica que desejarmos.

Porém, para somar apenas palavras de 4 bits, temos 8 variáveis (9, se considerarmos o carry in da entrada) e 256 linhas (512 com o carry) na tabela verdade. Uma outra opção é utilizar um circuito iteratico. Note que, durante a soma, fazemos a mesma operação em cada bit.

A adição binárioa pode então ser feita usando o algoritmo:

1. Ajuste $C_0=0$ e $i = 0$.
2. Some os bits $C_i$, $A_i$ e $B_i$  para obter $S_i$ (saída primária) e $C_{out}$ (saída de cascateamento).
3. Incremente i.
4. Se i < n, volte ao passo 2.

Assim, precisamos de um módulo que realize a soma entre $C_i, A_i$  e $B_i$ para obter $S_i$  e C_{out}. Bom, mas eu já conheço um módulo digital capaz de realizar essa função: O circuito somador total.

O circuito somador iterativo (agrupando somadores totais) é chamado de Ripple Adder.

![Untitled](Circuitos%20Combinacionais%20d1894efa47bd42fca3701a04764814c1/Untitled%205.png)

Podemos construir um circuito “subtrator” binário análogo ou somador usando a tabela verdade da subtração com borrow, que realiza a subtração $A - B$.

![Untitled](Circuitos%20Combinacionais%20d1894efa47bd42fca3701a04764814c1/Untitled%206.png)

Porem, vimos que, para subtrair números em complemento de 2, basta somar o número com o seu complemento (isto é, ao invés de fazer A - B, fazemos A + (- B) ). Para obter o inverso de um numéro em complemento de 2, basta inverter todos os bits e esoma 1.

O maior problema do ripple adder é o atraso.

Podemos criar uma lógica de soma com apenas 3 níveis de atraso (típico da estrutura AND-OR) se tentarmos minimizar o circuito diretamente da tabela verdade, ou seja, fazendo as equações para cada bit de saída s_i a partir das entradas ($a_i…a_0$, $b_i…b_0$ e $c{in}$ ).

Porém, a partir de $s_2$ (ou seja, apenas o terceiro bit da soma!), as equações já ficam muito grandes e necessitariam de 14 4-input ANDs, 4 5-input ANDs uma 18-input OR. E isso apenas para $s_2$. Podemos acelerar este atraso olhando as equações: $s_i=a_i \oplus b_i \oplus c_i$. Já vimos que tentar abrir completamento esse termos gera muitas complicações. A ideia do circuito co carry lookahead é manter as portas xor e gerar apenas o carry.

### Multiplicadores

A multiplicação em binário se dá da mesma forma que a multiplicação em dicimal: adicionando uma lista de multiplancdo deslocados comp8utados de acordo com os dígitos do multiplicador.

Na multiplicação em binário esse processo é ainda mais fácil, pois ou o bit do multiplicador é 0 ( e 0 multiplicando deslocado é 0) ou é 1 (e 0 multiplicando deslocado é igual ao multiplicando). 

Embora possamos projetar circuitos sequenciais para a multiplicação, não há nada inerentemente sequencial ou dependente do tempo na multiplicação binária.

Podemos expressar a multiplicação de dois operando X e Y de n bits em uma tabela verdade e elaborar um circuito AND-OR para realizar essa multiplicação. O problema é que, novamente, essa tabela verdade cresce muito rapidamente. Porém assim como a multiplicação, a adição também é binária e pode gerar um carry out. Podemos usar somadores completos para realizar essa soma. Porém, note que o termo $p_2$ soma quatro bits e, portanto, pode precisar ser propagado até o termo $p_4$.

No multiplicador de apenas 3 bits, carry save additon economiza apenas 1 atraso, pois o pior caso circuito anterior é que um carry se propague por 4 somadores. Pode não parecer muito, especialmente porque o entendimento do circuito fica um pouco mais complexo, mas em um circuito multiplicador de 8 bits a economia é de 20 somadores para 14 ( e ainda menos de utilizarmos um circuito carry lookahead na última linha).

### Unidade Logica Aritmética (ULA)

A CPU é o circuito eletrônico em um computador que executa instruções de um programa. Essas instruções são compostas de operações classificadas em, basicamente três categorias:

- Entrada / saída;
- Operações de controle e
- e operações básicas aritméticas e lógicas.

Para executar uma  instrução ( que poderá pertencer às três categorias anteriores), a CPU faz três passos:

1. Fetch (busca) - Busca na memória qual instrução deve ser executada.
2. Decode (decodifica) - Decodifica essa instrução, lendo o que deve ser feito e com quais operandos.
3. Execute (executa) - Efetivamente executa essa instrução.

Ao fim de uma instrução, a CPU atualiza o contador de programa e repete esses passos novamente.

As instruções que uma CPU pode executar são (em geral) simples e sempre bem definidas.

![Untitled](Circuitos%20Combinacionais%20d1894efa47bd42fca3701a04764814c1/Untitled%207.png)

A memória pode guardar tanto o programa a ser executado quando os dados (resultados).

A unidade de controle coordena os operandos e as operações, buscando os dados na mamória e disponibilizando para que outros circuitos executem essas operações.

A unidade lógico-aritmética executa propriamente essas operações, retornando um valor.

Um unidade lógico-aritmética (ULA) (ou ALU - arithmetic logic unit) é um circuito combinacional que pode realizar uma série de operações aritméticas e/ou lógicas em um par de operandos de n-bits. A operação é especificada em suas entradas de seleção.

Considere uma ULA com dois operandos de 4 bits com dois bits de seleção que execute as seguintes operações com seus operandos.

![Untitled](Circuitos%20Combinacionais%20d1894efa47bd42fca3701a04764814c1/Untitled%208.png)

Individualmente sabemos fazer todos estes circuitos. Portanto, uma primeira solução poderia ser: 

![Untitled](Circuitos%20Combinacionais%20d1894efa47bd42fca3701a04764814c1/Untitled%209.png)

Para somar A e B, usamos M = 0 e S = 1001 (usando a função A plus B plus CIN).

Para fazer A - B em complemento 2, usamos M = 0, S = 0110 e CIN = 1 (usando a função A minus B minus 1 plus CIN).

Para decrementar A, fazemos M = 0, S = 0000 e CIN = 0 (usando a função A minus 1 plus CIN).

Algumas operações podem não fazer muito sentido, mas “vem de graça”, como por exemplo “F = A.B’ plus (A+B) plus CIN’’.

Onde plus é soma aritmética, minus é subtração aritmética, e os simbolos .,+ e $\oplus$ se referem aos símbolos lógicos operados bit a bit.