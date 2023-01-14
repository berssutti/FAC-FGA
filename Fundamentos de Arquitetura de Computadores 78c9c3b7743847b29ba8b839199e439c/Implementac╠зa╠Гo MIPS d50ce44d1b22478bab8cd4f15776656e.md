# Implementação MIPS

### Introdução

O desempenho de um máquina pode ser determinado por três fatores:

- Número de instruções executadas
- Período do clock (ou frequência)
- Número de ciclos por instrução(CPI)

Para um determinado algoritmo, o compilador e a ISA (Instruction Set Architecture) determinam o número de instruções a serem executadas.

A implementação do processador determina o período de clock e o CPI.

Este módulo aborda a implementação de apenas um subconjunto das instruções do MIPS

O inter-relacionamento entre ISA e a implementação é exemplificado em três projetos alternativos do processador

- Processador uniciclo
- Processador multiciclo
- Processador pipeline

**Arquitetura von Neumann**

Estrutura Básica:

![Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled.png](Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled.png)

**Arquitetura Harvard**

Estrutura Básica:

![Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%201.png](Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%201.png)

### Unidade Operativa

Também chamada “Parte Operativa”, “Caminho de Dados” ou Datapath

É construída a partir dos seguintes componentes:

- Elementos de armazenamento(registradores, flip-flops);
- Operadores lógicos aritméticos;
- Recursos de interconexão (barramentos, mux).

Será projetada para implementar o seguinte subconjunto de instruções do MIPS:

- Instruções de referência a memória: lw e sw;
- Instruções lógicas e aritméticas: add, sub, and, or e slt;
- Instruções de fluxo de controle: beq e j.

O conjunto acima é incompleto, mas demonstra princípios básicos de projeto.

### Sinopse da Implementação

Implementação genérica:

- Use o contador de programa (PC) para fornecer endereço da instrução
- Obtenha a instrução da memória
- Leia os registradores
- Use a instrução para decidir exatamente o que fazer

Todas as instruções usam a ULA após lerem os registradores (exceto jump).

**Visão de Alto Nível da Implementação**

Estrutura Básica:

![Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%202.png](Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%202.png)

Obs: Em vários pontos temos dados vindo de origens diferentes. As unidades precisam ser controladas

![Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%203.png](Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%203.png)

Unidades de controle: único ciclo de clock ou multiciclo.

Elementos combinacionais e sequenciais.

### Componentes/Circuitos Sequenciais

Componentes sequenciais tem um estado, que define seu valor em um dado instante de tempo.

Implementação de um Registrador:

- Um conjunto de flip-flops tipo D;
- Portas:
    - Entradas: n linhas/bits de entrada (dados), entrada de habilitação de escrita e entrada de sinal de relógio;
    - Saída: n linhas/bits de saída.
- Habilitação de Escrita (Write Enable -WE)
    - 0: o dado de saída não muda;
    - 1: o dado de entrada será carregado e saída = entrada na transição do clock (relógio).

![Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%204.png](Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%204.png)

Comportamento Dinâmico:

![Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%205.png](Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%205.png)

### Memória

Memória (idealizada)

- Um barramento de entrada: Data In;
- Um barramento de saída: Data Out.

Uma palavra é selecionada por:

- Um endereço seleciona a palavra para ser colocada na saída (Data Out);
- Write Enable = 1: Permite que a palavra selecionada seja escrita com a informação na entrada (Data In)

Entrada de clock (CLK):

- Sincroniza os processos de acesso à memória;
- Geralmente, os processos são sincronizados pela borda de subida ou de descida do clock.

![Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%206.png](Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%206.png)

**Estratégia de Temporização**

- Uma metodologia de temporização define quando os sinais podem ser lidos e quando poder ser escritos;
- É necessário evitar situações de conflito, por exemplo, querer ler uma palavra e escrevê-la simultaneamente;
- Será adotada uma metodologia de temporização sensível às transições do sinal clock.
- Nesta metodologia, qualquer valor armazenado nos elementos de estado só pode ser atualizado durante a transição do sinal de relógio (clock).

Lógica combinacional usualmente tem entradas e saídas conectadas a elementos de estado (sequenciais)

O elemento de estado 2 só pode ser escrito depois de os dados em sua entrada estares estáveis

- Atraso de propagação no elemento de estado 1;
- Atraso da lógica combinacional;
- Tempo de pré-carga (setup time) no elemento de estado 2.

![Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%207.png](Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%207.png)

**Criando a Unidade Operativa**

Uma maneira de se começar o projeto de uma unidade operativa (data path) é examinar cada um dos componentes necessários para a execução de cada uma das classes de instruções do MIPS.

Elementos necessários:

- Um lugar para armazenar as instruções (memória de instruções);
- Um registrador para armazenar o endereço de instrução (Program Counter: PC);
- Um contador para incrementar o PC, para compor o endereço da próxima instrução (soma 4 para endereçar próxima instrução).

Elementos básicos:

![Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%208.png](Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%208.png)

Busca de instruções: **Fetch**

![Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%209.png](Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%209.png)

**Instruções Lógicos Aritméticas**

Formato de uma instrução tipo R no MIPS:

![Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%2010.png](Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%2010.png)

Semântica: $rd <= op($rs, $rt)

Estrutura de suporte: banco de registradores.

### Banco de Registradores

Dupla porta: leitura de dois registradores ao mesmo tempo

Sinal de controle para escrita - leitura não necessita controle

![Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%2011.png](Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%2011.png)

![Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%2012.png](Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%2012.png)

### Projeto de ULA

4 bits de controle indicam a operação a ser realizada

![Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%2013.png](Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%2013.png)

![Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%2014.png](Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%2014.png)

**Unidade Operativa: Instruções do Tipo R**

![Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%2015.png](Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%2015.png)

Ex: add $s1, $s2, $s3

![Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%2016.png](Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%2016.png)

**Unidade Operativa: Instruções tipo I**

![Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%2017.png](Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%2017.png)

Ex: lw $t0, 32($t1)

end = endereço base de $t1 + 32

![Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%2018.png](Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%2018.png)

### Memória de Dados

- Memória com um barramento de entrada independe de saída
- Controle de escrita (write) e leitura (read)
- Barramento de endereços
- Um acesso de cada vez

![Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%2019.png](Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%2019.png)

**Extensão de Sinal do Imediato**

Imediato deve ser estendido de 16 para 32 bits, mantendo-se o sinal

- Se for negativo, 16 bits superiores = 1
- Se for positivo, 16 bits superiores = 0

![Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%2020.png](Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%2020.png)

![Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%2021.png](Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%2021.png)

**Acesso à Memória**

- lw lê um dado da memória e escreve em um registrador → conexão entre saída da memória e a entrada do banco de registradores
- sw lê um dado de um registrador e escreve na memória → ligação entre saída do banco de registradores e entrada de dados da memória
- endereço calculado através da ULA → saída da ULA ligada ao barramento de endereços da memória

**Unidade Operativa: Instruções do Tipo I (acesso à  memória)**

![Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%2022.png](Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%2022.png)

### Instruções de Desvio

beq $t1, $t2, desloc

- compara dois registradores
- soma desloc words ao endereço PC+4 se $t1 = $t2 → PC + 4 é o endereço da próxima instrução
- No montador utiliza-se uma versão simplificada, com o rótulo do destino beq $t1, $t2, LABEL → neste caso, o montador calcula o deslocamento a ser usado
- desloc é um deslocamento de palavras, ou seja, cada unidade de descloc corresponde a 4 bytes
- A comparação entre os registradores é feita subtraindo-os na ULA e verificando se o resultado é zero
- O PC deve ser carregado com PC + 4 ou PC + 4 + desloc*4, de acordo com o resultado do teste
- A multiplicação de desloc por 4 é feita deslocando-se 2 bits à esquerda do seu valor.

![Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%2023.png](Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%2023.png)

- Para realizar a comparação dos dois operandos, precisamos usar o banco de registradores
- O cálculo do endereço de desvio foi incluído n circuito
- Na instrução, não é preciso escrever no banco de registradores
- A comparação será feita pela ULA, subtraindo-se os registradores e utilizando a saída zero (=1) para verificar a igualdade

**Unidade Operativa: Versão Melhorada**

![Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%2024.png](Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%2024.png)

### MIPS Uniciclo - Unidade de Controle

Foi desenvolvida uma unidade operativa baseada nos três tipos básicos de instruções:

- Um submódulo para formato R (add, sub, etc.)
- Um submódulo para acesso a memória lw e sw - formato I
- Um submódulo para condicionais (beq) - também formato I

A via de dados mais simples deve-se propor executar as instruções num único período do clock.

Isto quer dizer que nenhum dos recursos pode ser usado mais de uma vez por instrução (por quê?).

Desse maneira, faz-se necessário construir uma unidade de controle que seja capaz de coordenar a operação dos três submódulos acima durante o processamento de uma instrução (fetch-decode-execute).

![Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%2025.png](Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%2025.png)

Combinando as unidades estruturais:

![Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%2026.png](Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%2026.png)

Adicionando a unidade de Busca/Fetch

![Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%2027.png](Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%2027.png)

**Unidade Operativa: versão melhorada II**

![Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%2028.png](Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%2028.png)

**MIPS Uniciclo: Controle**

Cada operação requer a utilização adequada dos recursos do MIPS

Ex: add $t0, $s1, $s2

- É necessário que os endereços dos operandos $s1 e $s2 sejam enviados ao banco de registradores
- Da mesma maneira, o endereço do registrador destino ($t0) deverá ser informando ao banco de registradores
- Uma vez que o banco de registradores disponibilize os valores de $s1 e $s2, estes deverão ser encaminhados à ULA
- Posteriormente, o resultado deverá ser escrito no banco de registradores (registrador $t0)

As operações da ULA são controladas por um código de 4 bits:

![Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%2029.png](Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%2029.png)

- As instruções lw e sw utilizam a operação de soma da ULA
- As instruções do Tipó R utilizam uma das 5 operações
- A instrução beq utiliza a subtração da ULA
- Nor não é usada nesta implementação

**Identificação da Operação**

O campo funct de 6 bits (no formato R) indica que tipo de operação aritmética/lógica será executada:

![Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%2030.png](Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%2030.png)

Vamos definir 2 bits para identificar cada uma das categorias de instruções (opALU):

- 00: acesso à memória (lw, sw) [add]
- 01: desvio (beq, bne) [sub]
- 10: lógico-aritiméticas ([add, sub, and, or, slt])

![Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%2031.png](Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%2031.png)

**Lógica de Controle da Unidade Operativa (Caminho de Dados)**

![Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%2032.png](Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%2032.png)

![Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%2033.png](Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%2033.png)

**Circuito de Controle da ULA**

Projeto de Circuito de Controle de ULA

![Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%2034.png](Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%2034.png)

Com base nas definições anteriores podemos montar a Tabela Verdade do circuito que gera os 4 bits de controle da ULA

![Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%2035.png](Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%2035.png)

**Unidade de Controle Uniciclo**

A unidade de controle deve, a partir do código da instrução, fornecer os sinais que realizam as instruções na unidade operativa

![Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%2036.png](Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%2036.png)

**Entrada da Unidade de Controle**

As informações necessárias a execução de uma instrução são retiradas da própria instrução

- O opcode (código de operação) sempre está nos bits [31-26]=0p[5:0]
- Os 2 registradores a serem lidos (rs e rt) sempre estão nas posições [25-21] e [20-16] (tipo-R, beq e sw)
- O registrador base (rs) para as instruções beq, lw e sw estão sempre nas posições [15-0]
- O registrador destino estão em uma das duas posições:
    - [20-16] para lw (registrador rt)
    - [15-11] para instruções aritméticas/lógicas (registrador rd)

**Saída da Unidade de Controle: Sinais de Controle**

A unidade de controle deve prover:

- Sinais para os multiplexadores
- Sinais de leitura e escrita para as memórias
- Seleção da operação da ULA
- Controle do novo endereço a ser carregado no PC, para instruções de salto.

![Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%2037.png](Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%2037.png)

![Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%2038.png](Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%2038.png)

**Temporização**

Todas as tarefas executadas em 1 período de clock

Elementos de estado alteram seu valor apenas na borda de subida do clock

![Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%2039.png](Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%2039.png)

**Problemas no MIPS Uniciclo**

Período do relógio determinado pelo caminho mais longo:

- instrução lw:
    - leitura de instrução
    - leitura do registrador de base, extensão de sinal
    - cálculo do endereço
    - leitura do dado da memória
    - escrita em registrador

Qual o impacto dessa estratégia se houvesse operações com precisão dupla?

Casos mais graves:

- Viola o princípio de tornar o caso comum rápido (”filosofia” de projeto)
- Unidades funcionais duplicadas (maior espaço em chip!)

**Análise de Desempenho Uniciclo**

Supondo os seguintes tempos de execução das unidades operativas:

- Acesso a memória: 200ps
- ULA e somadores: 100ps
- Acesso ao banco de registradores (leitura ou escrita): 50px
- Multiplexadores, Unidade de Controle, acesso ao PC, Unidade de Extensão de Sinal e fios considerados sem atrasado (Impossível!)

Qual das seguintes implementações seria mais rápida e quando?

1. Implementação onde toda instrução é feita em 1 ciclo de clock de duração fixa.
2. Implementação onde toda instrução tem como duração somente o necessário para acionar os módulos funcionais de que depende (exemplo ideal? CNTP!)

Para comparação consideramos um workload composto de:

25% loads, 10% stores, 45% ALU, 15% desvios condicionais, 5% jumps.

Tempo Execução = Contagem de Instruções x CPI x Período de clock

![Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%2040.png](Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%2040.png)

Tempo médio:

Uniciclo fixo: 600ps

Uniciclo variável: 600x0.25 + 550x0.1 + 400x0.45 + 350x0.15 + 200x0.05 = 447.5ps

Fator de desempenho: n = 600/447.5 = 1.34

### MIPS Multiciclo

Análise de desempenho:

![Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%2041.png](Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%2041.png)

Ideia: cada fase da execução de uma instrução dura um ciclo de relógio. Instruções podem ser executadas em um número diferente de ciclos.

Exemplos:

- lw demora 5 ciclos
- jump demora 3 ciclos
- add demora 4 ciclos

Ciclo dimensionado de acordo com a etapa/módulo mais demorada

Vamos reutilizar unidades funcionais:

- ULA usada também para calcular endereço e incrementar o PC
- Memória usada para armazenar instrução e dados

A organização da parte operativa pode ser re-estruturada em função destas características

Sinais de controle não serão determinados diretamente pela instrução

Usaremos uma máquina de estado finito para controle

Dividir as instruções em etapas, cada uma durando um ciclo:

- Equilibrar a quantidade de trabalho a ser feito
- Restringir cada ciclo para usar apenas uma vez cada unidade funcional

No final de um ciclo:

- Armazenar os valores para uso em ciclos posteriores
- Logo, necessita introduzir registradores “internos” adicionais

**Visão Abstrata**

- Uma única ULA
- Uma única Unidade de Memória
- Registradores após unidades funcionais

![Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%2042.png](Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%2042.png)

Obs:

Dados utilizados pelas etapas subsequentes armazenados em registradores (elementos de estado invisível ao programador)

Dados utilizados pelas instruções subsequentes armazenados na Memória, Banco de Registradores e PC (visível ao programador)

Detalhamento:

- Todos os registradores precisam manter o dado por 1 ciclo apenas (EXCEÇÃO IR).
    
    Logo não necessitam de pino de controle de escrita
    
- **Incluir** e **expandir** Multiplexadores para poder **reusar** Unidade Funcionais

![Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%2043.png](Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%2043.png)

**Caminho de Dados**

Definição de linhas de controle

![Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%2044.png](Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%2044.png)

Implementação do controle + controle do PC (PC+4, beq, jump)

![Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%2045.png](Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%2045.png)

![Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%2046.png](Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%2046.png)

![Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%2047.png](Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%2047.png)

**Instrução Tipo-R**

Poderia ser pensada na seguinte instrução em linguagem de descrição de hardware (HDL) Verilog:

```
Reg[Memory[PC][15:11]] <= Reg[Memory[PC][25:21]] op Reg[Memory[PC][20:16]];

```

Decomposta nas seguintes etapas usando "variáveis locais":

```
IR <= Memory[PC];
A <= Reg[IR[25:21]];
B <= Reg[IR[20:16]];
saidaALU <= A op B;
Reg[IR[15:11]] = saidaALU;
PC <= PC + 4;

```

Controle Multiciclo: 5 Etapas

Considerando:

- 1 acesso à memória, 1 acesso ao banco de registradores ou 1 operação da ULA por ciclo. (determina o ciclo de clock).
- Projeto acionado por transição (valores dos regs não muda até então).

**Etapa 1: Etapa da Busca da Instrução (Fetch)**

```
IR <= Memoria[PC];
PC <= PC + 4;

```

Execução: Ler a Instrução da memória e incrementar PC:

- Ativar os sinais de controle LeMem e Escreve IR;
- Definir PC como origem do endereço: Colocar louD em 0;
- Incrementar PC + 4: OrigAALU em 0 (PC para ALU), OrigBALU 01 (4 para ALU) e opALU 00 (soma);
- Armazenar o endereço de instrução incrementado em PC: origPC 00 e ativar EscreverPC.

**Etapa 2: Etapa de Decodificação da Instrução e busca de operandos (Decode)**

```
A <= Reg[IR[25:21]];
B <= Reg[IR[20:16]];
SaidaALU <= PC + (extensão - sinal(IR[15:0]) << 2);

```

Execução: Como não sabemos qual a instrução ainda, podemos fazer ações gerais que não causem problemas depois!

- Acessar o banco de registradores e ler rs e rt e armazenar em A e B;
- Cálculo prévio do endereço de desvio e coloca em SaidaALU: OrigAALU em 0 (PC para ALU), OrigBALU em 11 (offset extendido em ALU) e opALU 00 (soma).

**Etapa 3: Etapa de Execução, cálculo do endereço de memória ou conclusão de desvio (execute)**

*Referência à memória (lw, sw): SaidaULA = A + extensão-sinal(IR[15-0])*

Execução: Calcular o endereço. OrigAALU em 1 (A na ALU), OrigBALU em 10 (saída ext-sinal na ALU), opALU 00 (soma)

Instrução Tipo-R: SaidaULA <= A op B

Execução: os valores de A e B estão prontos! OrigAALU em 1 (A na ALU), OrigBALU em 00 (B na ALU), opALU 10 (lógico-aritmética pelo campo funct)

beq: if (A==B) PC <= SaidaALU

Execução: o endereço de desvio já está pronto.

- OrigAALU em 1 (A na ALU), OrigBALU em 00(B na ALU), opALU 01 (subtrai)
- Ativar sinal EscrevePCCond
- Colocar OrigPC em 01 (valor do PC vem da ULA)

*Jump: PC <= {PC[31:28], (IR[25:0]], 2b00)}*

Execução: PC é substituído pelo endereço de jump

- Colcoar OrigPC em 10
- Ativar Escreve PC

**Etapa 4: Etapa de acesso à memória ou conclusão de instrução Tipo-R (execute)**

Referência à memória (lw, sw):

MDR <= Memoria(SaidaALU); # para load (MDR → Registrador de Dados da Memória) ou

Memória[saidaALU] <= B # para store

Execução: Em qualquer dos casos o enderço já está na SaidaALU.

- louD colocado em 1 (Endereço da ALU)
- LeMem ou EscreveMem deve ser ativado

Instrução Tipo-R: Reg[IR[15:11]] <= SaidaALU

Execução: Colocar o resultado no reg destino

- Colocar RegDST em 1 (usar rd como destino)
- Ativar EscreveReg
- Colocar MemparaReg em 0 (saída da ALU na entrada do banco de reg)

**Etapa 5: Etapa de conclusão da Leitura da Memória (lw-execute)**

Reg[IR[20:16]] <= MDR;

Execução: Escrever o dado do MDR no banco de reg

- Colocar MemparaReg em 1 (resultado da memória)
- Ativar o EscreveReg
- Colocar RegDst em 0 (campo rt é o reg destino)

**Resumo do Controle Multicilo**

![Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%2048.png](Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%2048.png)

*Fetch → Decode → Execute*

**Caminho de Dados Multiciclo: Máquina de Estados Finitos**

Diagrama de Estados:

- Cada nó do diagrama representa um estado
- A transição entre estados é indicada por arcos
- As condições de disparo de uma transição são associadas aos arcos
- Cada estado corresponde a um ciclo de relógio

**Exceções e Interrupções**

Exceções são mudanças no fluxo de execução devido a eventos inesperados gerados internamente ao processador.

- Instrução inválida
- Overflow em operações aritméticas

Interrupções são mudanças no fluxo devido a eventos externos, tipicamente entrada e saída

- DMA
- Acesso ao barramento

A unidade de controle deve identificar a causa da exceção e armazená-la para posterior tratamento pelo sistema operacional

Além disso, deve informar o endereço onde ocorreu a execeção

MIPS utiliza dois registradores para isso:

- EPC: endereço da instrução.
- Cause: indicação da causa da exceção.

### Pipeline

**Objetivo**

Apresentar uma técnica de projeto para aumento de desempenho de arquiteturas de processadores:

- Pipeline: uma técnica de projeto onde o hardware processa mais de uma instrução de cada vez sem esperar que uma instrução termine antes de começar a próxima.
- Superescalar: uso eficiente de múltiplas unidades funcionais.

**Pipelining: Conceito Básico**

Lavar Roupas

Sequência:

1. Inserir na lava roupas uma carga de roupas sujas;
2. Quando a lava roupas concluir a lavagem, transferir a carga de roupas lavadas na secadora;
3. Quando a secadora concluir a secagem, colocar a carga de roupas secas na mesa e organizar as peças: dobrar e empilhar;
4. Quando a organização estiver concluída, guardar as roupas no armário.

![Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%2049.png](Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%2049.png)

**Metodologia do Módulo**

A ideia é compilar um pipeline para o MIPS;

Atenção especial para 8(oito) instruções: lw, sw, add, sub, and, or, slt e beq;

Mesmas etapas das implementações Uniciclo e Multiciclo: 5 etapas;

- Busca de Instruções(IF),
- Decodificação da Instrução(ID),
- Execução ou Calculo de Endereço(EX),
- Acesso à memória de dados(MEM) e
- Escrever o Resultado em um Registrador(WB).

![Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%2050.png](Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%2050.png)

**Uniciclo vs Pipeline**

Comparação de desempenho:

![Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%2051.png](Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%2051.png)

Análise:

Latência: 5 ciclos
Vazão: 1 instrução/ciclo

Qual a aceleração ideal?

- Para estágios balanceados, condições ideais e grande número de instruções:
    - Tempo de instruções com pipline = tempo entre instruções sem pipeline/Número de estágios do pipeline
- Por que estágios balanceados? (lacunas)
- Por que condições ideais? (hazards)
- Por que grande número de instruções? (overhead final)

Efeito da Exploração do Pipeline:

- Melhorar o desempenho aumentando a vazão de instruões, em vez de diminuir o tempo de execução de uma instrução individual.

**Pipeline no MIPS**

O que facilita o pipeline:

- Todas as instruções tem o mesmo comprimento.
    - obs: IA-32 instruções de 1 a 17 bytes (mais desafiador!)
- Poucos formatos de instruções
    - obs: Tipo-R, I, J operandos “quase” nas mesmas posições
- Operandos de memória só aparecem em loads e stores.
    - obs: Pode-se usar a ULA para cálculos de endereço. O mesmo não vale para busca de operandos da ULA da memória (IA-32)

O que complica(hazards):

- Riscos estruturais;
- Riscos de controle;
- Riscos de dados.

**Riscos de Dados**

Pipeline precisa ser interrompido por que uma etapa precisa esperar até que outras seja concluída.

- O que fazer se 1 pé de meia seu foi lavado junto com as roupas do colega?
- Exemplo:

```
add $s0, $t0, $t1
sub $t2, $s0. $t3

```

Soluções:

- Inserir 3 “bolhas”. (dado $s0, só escrito no fim da etapa 5)
- Execução fora de ordem (compilador e processador)

Solução: Forwarding ou bypassing.

**Forwarding**

Observação: não é necessário esperar que a instrução termine antes de tentar resolver o risco de dados.

![Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%2052.png](Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%2052.png)

E se fosse um load?

- Pipeline Stall
    
    Ex.:
    
    C:
    
    ```
    A = B + C;
    C = B + F;
    
    ```
    
    Assembly:
    
    ```
    lw $t1 ,0( $t0)
    lw $t2 ,4( $t0)
    add $t3 ,$t1 ,$t2
    sw $t3 ,12( $t0)
    lw $t4 ,8( $t0)
    add $t5 ,$t1 ,$t4
    sw $t5 ,16( $t0)
    
    ```
    

Nas instruções add

- Forwarding: passar o resultado à frente
- Bypassing: passar o resultado pelo banco de registradores pra a unidade desejada

Código melhorado:

```
lw $t1 , 0($t0)
lw $t2 , 4($t1)
lw $t4 , 8($t1)
add $t3 , $t1 , $t2
sw $t3 , 12( $t0)
add $t5 , $t1 , $t4
sw $t5 , 16( $t0)

```

![Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%2053.png](Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%2053.png)

**Risco de Controle**

Necessidade de tomar uma decisão com base nos resultados de uma instrução enquanto outras estão sendo executadas.

![Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%2054.png](Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%2054.png)

Prevendo desvios: não tomado e tomado

![Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%2055.png](Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%2055.png)

Formas dinâmicas: Endereço é anterior? Prevê tomar o desvio (implementação de loops)

Armazenamento do histórico para decisão mais adequada.

Delayed Branch: desvio adiado (eficiente para pequenos desvios). Executa a próxima instrução (montador). Abordagem MIPS!

**Aceleração decorrente do Pipeline**

![Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%2056.png](Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%2056.png)

Atrasos: reescalonamento de instruções

lw $t0, 0($t1)

lw $t2, 4($t1)

sw $t2, 0($t1)

sw $t0, 4($t1)

Acrescentar um branch delay slot

- A próxima instrução após o desvio é sempre executada
- Confia no compilador para preencher o slot com alguma coisa útil

Superscalar: iniciar mais de uma instrução no mesmo ciclo.

Duas técnicas para iniciar múltiplas instruções por ciclo de clock:

- Arquitetura superpipeline
- Arquitetura superescalares

Aceleração decorrente do Superpipeline

- A arquitetura Superpipeline subdivide cada estágio do pipeline em subestágios e multiplica o clock internamente.
- Cada (sub)estágio continua executando uma instrução por clock. Mas como o clock interno é multiplicado, o pipeline pode aceitar duas ou mais instruções para cada clock externo.

**Aceleração Decorrente do Superpipeline**

![Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%2057.png](Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%2057.png)

- A arquitetura superescalar contém multiplas unidades de executação que são capazes de fazer a mesma coisa.
- Isto permite ao processor executar várias instruções similares concorrentemente, pelo roteamento (escalonamento) das instruções às unidades de execução disponíveis.

![Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%2058.png](Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%2058.png)

![Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%2059.png](Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%2059.png)

Execução Dinâmica: escalonamento

O hardware executa o escalonamento

- O hardware tenta encontrar instruções para executar
- É possível a execução de instruções fora de ordem
- Execução especulativa e predição dinâmica de desvios

Todos processadores modernos são bastate complicados

- Compaq/DEC Alpha 21264: 9 estágios de pipeline, 6 instruction issue
- Inter: Pentium III: estágios de pipeline
    - Pentium IV: 20 estágios de pipeline (nível de microinstruções)
    - Pentium D: 31 estágios (maior frequência, maior num. bolhas)
    - Core2: 14 estágios
    - Core i7: 24 estágios
    - Athlon 64: 12 estágios
- PowerPC e Pentium: tabela de histórico de desvios
- O papel do compilador é muito importante na buca por desempenho.

**Pipeline com Escalonamento Dinâmico**

Três unidades primárias de um pipeline com escalonamento dinâmico.

![Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%2060.png](Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%2060.png)

**Controle do Pipeline**

Temos 5 estágios. O que deve ser controlado em cada estágio?

- Busca de Instruções ou Instruction Fetch e PC increment);
- Decodificação da Instrução ou Instruction Decode/Register Fetch;
- Execução ou Cálculo de endereço ou Execution;
- Acesso à memória de dados ou Memory Stage;
- Escreve o Resultado em um registrador ou Write Back.

**Caminho de Dados Uniciclo**

![Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%2061.png](Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%2061.png)

**Duas Exceções**

- O estágio de escrita (WB) coloca o resultado de volta no banco de registradores;
- A seleção do próximo PC:
    
    PC + 4; ou
    
    Endereço de Desvio do Estágio MEM;
    
- Os dados na direção contrária não afetam a instrução atual, somente a próxima instrução.

**Caminho de Dados com Pipeline**

![Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%2062.png](Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%2062.png)

As barras que delimitam os estágios consistem em registradores dos estágios do pipeline.

**Tamanho dos Registradores de Pipeline**

Precisam ser grandes o suficiente para suportar o armazenamento dos dados em cada estágio:

- IF/ID: 64 bits (32 bits da instrução + 32 bits do contador de programa incrementado).
- ID/EX: 128 bits (leitura de dois registradores de 32 bits e valor de 32 bits proveniente de extenção de sinal + 32 bits do contador de programa incrementado).
- Ex/MEM: 97 bits (resulto da ULA + 1 bit da ULA + contador do programa em branch + valor a ser escrito no endereço calculado).
- MEM/WB: 64 bits (resultado da ULA + valor lido da memória).

Evolução da instrução Load (5 estágios)

*Busca de instrução (32 bits da instrução + 32 bits do contador de programa incrementado):*

![Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%2063.png](Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%2063.png)

*Decodificação (leitura de dois registradores de 32 bits e valor de 32 bits proviniente de extensão de sinal + 32 bits do contador de programa incrementado):*

![Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%2064.png](Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%2064.png)

Execução/Cálculo do Endereço (resultado da ULA + 1 bit da ULA + contador do programa em branch + valor a ser escrito no endereço calculado):

![Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%2065.png](Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%2065.png)

*Acesso à Memória (resultado da ULA + valor lido da memória):*

![Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%2066.png](Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%2066.png)

*Escrita do Resultado:*

![Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%2067.png](Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%2067.png)

**Bug (inconsistência estrutural) e solução**

- Ao realizar uma instrução do tipo Load (lw) temos mais um estágio após o uso do registrador MEM/WB para inserir o dado lido da memória no registrador de destino.
- Deve-se preservar a instrução lida após o estágio IF.
- Solução: passar o número do registrador de escrita primeiro para ID/EX e depois para o EX/MEM, e finalmente, para MEM/WB.

**Atenção**: faz-se necessária mudança no tamanho dos registradores de pipeline após essa alteração.

![Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%2068.png](Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%2068.png)

**Fluxo de Execução do Pipeline**

- Diagrama Visual representando 5 estágios do Pipeline MIPS;
- Incorpora os módulos de Bypassing e Forwarding na representação do Pipeline

![Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%2069.png](Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%2069.png)

Facilita no entendimento do processo:

- Quantos ciclos leva para executar esse código?
- Qual é o valor da ALU sendo executado durante o ciclo 4?

Essa representação é fundamental para entender o caminho de dados e os possíveis hazards no pipeline.

**Controle do Pipeline**

Temos 5 estágios. O que precisa ser controlado em cada estágio?

- Busca da instrução:
    - Nenhum sinal de controle
- Decodificação da instrução/leitura do banco de regs.:
    - Nenhum sinal de controle
- Execução/cálculo de endereço:
    - RegDst, ALUOp, ALUSrc
- Acesso a memória:
    - Branch, MemRead, MemWrite
- Escrita do resultado:
    - MemtoReg, RegWrite
    
    ![Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%2070.png](Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%2070.png)
    

Sinais de controle são transmitidos da mesma forma que os dados

![Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%2071.png](Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%2071.png)

**Controle de Pipeline: Sinais**

![Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%2072.png](Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%2072.png)

**Caminho de Dados com Pipeline e Controle**

![Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%2073.png](Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%2073.png)

**Risco de Dados e Forwarding**

- Os hazards são obstáculos para a execução do Pipeline.
- Hazard de dados estão diretamente correlacionados ao código a ser executado: provocado principalmente por dependências entre a instrução atual e a anterior.

**Risco de Dados e Forwarding**

- Suponha a execução das instrução a seguir:

```
sub $2, $1, $3
and $12, $2, $5
or $13, $6, $2
add $14, $2, $2
sw $15, 100($2)

```

- Suponha $2 iniciado com valor 10 e depois do sub com valor -20.
- É facil perceber que todos são dependentes do resultado da função sub!

**Risco de Dados e Forwarding**

![Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%2074.png](Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%2074.png)

Pares de Risco de Dados - Detecção

Os tipos são:

- 1a) EX/MEM.RegistradorRd = ID/EX.RegistradorRs
- 1b) EX/MEM.RegistradorRd = ID/EX.RegistradorRt
- 1a) EX/WB.RegistradorRd = ID/EX.RegistradorRs
- 1a) EX/WB.RegistradorRd = ID/EX.RegistradorRt

Para o exemplo anterior: EX/MEM.RegistradorRd = ID/EX.RegistradorRs = $2

**Hazards de Dados: Forwarding**

![Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%2075.png](Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%2075.png)

![Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%2076.png](Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%2076.png)

**Detecção de Hazards de Dados**

Compar índices dos registradores a serem lidos e escritos, armazenados nos registradores do pipeline:

- Deve-se compar igualmente se a instrução escreve em um registrador (RegWrite = 1) ou lê da memória (MemRead = 1)

![Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%2077.png](Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%2077.png)

![Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%2078.png](Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%2078.png)

**Harzards de Dados: quando o forward não é possível**

![Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%2079.png](Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%2079.png)

**Hazard de Controle**

Prevendo desvio não tomado:

![Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%2080.png](Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%2080.png)

Hazard de Controle Otimizado

![Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%2081.png](Implementac%CC%A7a%CC%83o%20MIPS%20d50ce44d1b22478bab8cd4f15776656e/Untitled%2081.png)