# Circuitos Sequenciais

### Flip-flops

**Tipo RS**

Os circuitos que vimos até agora são latcher, isto é, a saída muda a qualquer momento e que a entrada de enable estiver ativa. Porém, queremos um circuito que mude na borda (de subida ou de descida) do clock.

![Untitled](Circuitos%20Sequenciais%20b9b204c1e442403599cd3087f8398213/Untitled.png)

![Untitled](Circuitos%20Sequenciais%20b9b204c1e442403599cd3087f8398213/Untitled%201.png)

Note que, quando C = 1, o primeiro latch (chamado de mestre) está ativo, enquando o segundo latch ( chamado de escravo) está inativo. Quando C muda 1 → 0, a situação se inverte, e o latch mestre está inativo enquanto o escravo está ativo. Assim, na borda de descida do clock, a informação é passada do mestre para o escravo, que então apresenta essa informação na saída.

Porém, note que precisamos de um pulso completo no clock para transferirmos a informação!

Entretanto, note que o nosso flip-flop não amostra as entradas na borda do clock ( isto é o mesmo que afirmar que o flip-flop não é, realmente, edge-triggered ou acionado por borda).

Como o latch SR tem um estado hold, se um pulso ocorrer na entrada S enquando o mestre estiver ativo, a informação é transferida para o mestre, que então transfere para o escravo quando C muda de 1 → 0, mesmo que SR = 00 no momento da borda de descida.

![Untitled](Circuitos%20Sequenciais%20b9b204c1e442403599cd3087f8398213/Untitled%202.png)

Este comportamento é chamado de pulse-triggered flip-flop ou flip-flip aconado por pulso. Na figura do elemento, usamos o síbolo de postponed output indicator .

![Untitled](Circuitos%20Sequenciais%20b9b204c1e442403599cd3087f8398213/Untitled%203.png)

Porém, ainda temos o problema de quando as entradas SET e RESET estão ativas ao mesmo tempo.

**Tipo JK**

Podemos resolver esse problema das entradas SET e RESET (em seu modo proibido) usando flip-Oflop JK mestre-escravo:

![Untitled](Circuitos%20Sequenciais%20b9b204c1e442403599cd3087f8398213/Untitled%204.png)

![Untitled](Circuitos%20Sequenciais%20b9b204c1e442403599cd3087f8398213/Untitled%205.png)

Ou seja, temos um flip-flop SR mestre escravo com:

$$
S = j.QN\\
R = k.Q
$$

As entradas J e K são análogas às entradas S e R. Porém, ativas J apenas ativa S se QN = 1, e ativar K apenas R se Q = 1. Assim, se ativarmos J e K, invertemos o estado do flip-flop. Entretanto, ainda temos um pulse-Triggered flip-flop, e não um edge-treggered flip-flop, pois JK ainda tem um estado hold.

![Untitled](Circuitos%20Sequenciais%20b9b204c1e442403599cd3087f8398213/Untitled%206.png)

**Tipo D**

Vimos que o latch D não tem um estado hold. Logo, podemos tentar fazer o flip-flop D mestre-escravo: 

![Untitled](Circuitos%20Sequenciais%20b9b204c1e442403599cd3087f8398213/Untitled%207.png)

No símbolo do flip-flop vemos um dynamic input indicator.

O funcionamento desse flip-flop é análogo ao mestre-escravo: quando CLK = 1, o mestre está aberto e a entrada D é copiada para o mestre. Quando CLK muda de 1→ , o mestre fica inativo e a informação é tranferida para o escravo e, por sua vez, à saída. Porém, a informação transferida para o escravo é sempre a última informação: afinal, o latch D não tem um estado hold!

![Untitled](Circuitos%20Sequenciais%20b9b204c1e442403599cd3087f8398213/Untitled%208.png)

Note que essse flip-flop nuda na borda de descida do clock. Podemos facilme fazer um flip-flop que muda na borda de subida. Como o latch D, o Flip-flop D ainda tem problemas com meta-estabilidade se seus tempos de setup e hold não forem observados.

É comum utilizarmos flip-flops D com entradas assíncronicas de PRESET e CLEAR

![Untitled](Circuitos%20Sequenciais%20b9b204c1e442403599cd3087f8398213/Untitled%209.png)

![Untitled](Circuitos%20Sequenciais%20b9b204c1e442403599cd3087f8398213/Untitled%2010.png)

**Tipo JK**

Agora que temos um flip-flop realmente ativado/disparado pela borda, podemos fazer facilmente um flip-flop JK gatilhado pela borda.

![Untitled](Circuitos%20Sequenciais%20b9b204c1e442403599cd3087f8398213/Untitled%2011.png)

![Untitled](Circuitos%20Sequenciais%20b9b204c1e442403599cd3087f8398213/Untitled%2012.png)

![Untitled](Circuitos%20Sequenciais%20b9b204c1e442403599cd3087f8398213/Untitled%2013.png)

**Tipo T**

Outro circuito bastante útil é o flip-flop T (toggle).

![Untitled](Circuitos%20Sequenciais%20b9b204c1e442403599cd3087f8398213/Untitled%2014.png)

E o flip-flop T com enable:

![Untitled](Circuitos%20Sequenciais%20b9b204c1e442403599cd3087f8398213/Untitled%2015.png)

Comercialmente, o CI mais conhecido que contém flip-flops D é o 74x74, que contém dois flip-flops D independentes com PRESET e CLEAR, ativo na borda de subida do clock. As funcionalidades de PRESET e CLEAR são assíncronas, isto é, independente do clock do flip-flop.

![Untitled](Circuitos%20Sequenciais%20b9b204c1e442403599cd3087f8398213/Untitled%2016.png)

Para flip-flops JK, existem vários tipos de encapsulamento: 74x76 (flip-flop JK mestre-escravo, com transferência por pulso), 74x112 (flip-flop JK ativado pela borda) e 74x109 (flip-flop JK ativado pela borda, com entrada K negada). Todos eles tem funções assíncros de PRESET e CLEAR.

Para latches, temos o 74x375, que possui quatro latcher D com apenas dois enables (cada enable controla dois latcher).

### Registradores

Uma coleção de dois ou mais flip-flops D que compartilham o mesmo clock é chamado de registrador. Um exemplo é o 74x175, que contém quatro latches D.

![Untitled](Circuitos%20Sequenciais%20b9b204c1e442403599cd3087f8398213/Untitled%2017.png)

Outro registrador interessante é o 74x374, que tem oito flip-flops D sem saída de 3 estados (tri-state), de forma que podemos “desocupar” o barramento de saída.