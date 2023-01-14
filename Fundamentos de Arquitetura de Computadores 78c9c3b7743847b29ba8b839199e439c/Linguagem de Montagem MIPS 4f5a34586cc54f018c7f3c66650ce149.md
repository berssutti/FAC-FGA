# Linguagem de Montagem: MIPS

![Untitled](Linguagem%20de%20Montagem%20MIPS%204f5a34586cc54f018c7f3c66650ce149/Untitled.png)

### Arquitetura dos Processadores MIPS

Um processador MIPS consiste em uma unidade processadora de inteiros (CPU) e uma coleçãode co-processadores que executam auxiliares ou operam sobre tipos de dados como números em ponto flutuante.

- O co-processadore 0 gerencia traps (desvios e/ou chamadas de sistema), exceções e o sistema de memória virtual.
- O co-processador 1 é a unidade de ponto flutuante (FPU).

A CPU MIPS contém 32 registradores de uso geral numerados de 0 a 31. O registrador n é designado por $n.

Conceitos e boas praticas:

- O registrador $0 ($zero) contém sempre o valor 0 (hardwired).
- Os registradores $1 ($at), $26 ($k0), $27 ($k1) são reservados para o uso do montador e sistema operacional.
- Os registradores $2 e $3 ($v0,$v1) são utilizados para retornar valores de funções.
- Os registradores $4…$7 ($a0…$a3) são utilizados para passagem dos primeiros quatro argumentos para sub-programas/funções (os argumentos restantes são passados através da pilha)
- Os registradores $8…$15,$24,$25 ($t0…$t9) não são preservados pelo callee. Ou seja, convenientemente usado para receber dados temporários que não necessitam ser preservados durante as chamadas de junções/sub-rotinas.
- Os registradores $16…$23 ($s0…$s7) são callee-saved para dados que necessitam ser preservados durante as chamadas. Ou seja, o caller tem a garantia de que esse registradores são serão alterados durante as chamadas de outras rotinas. (Assemelham-se funcionalmente às variáveis locais.)
    
    obs: Callee = Função ou subrotina chamada pelo Caller.
    
- O registrador $28 ($gp) é um ponteiro global que aponta para o meio de um bloco de memória de 64k, no segmento de dados estáticos.
- O registrador $29 ($sp) é o ponteiro de pilha, apontando sempre para o primeiro elemento da pilha.
- O registrador $30 ($fp) é o ponteiro da frame. Pode ser utilizado como0 registrador callee-saved $s8.
- Registrador $31 ($ra) armazena o endereço de retorno quando é executada a instrução jal. jal → instrução de jump to subroutine label.

Convenções de uso de registradores:

![Untitled](Linguagem%20de%20Montagem%20MIPS%204f5a34586cc54f018c7f3c66650ce149/Untitled%201.png)

### Organização da Memória

A memória é vista como um grande array  unidimensional, com endereços sequenciais. Um endereço de memória é um índice do array.

“Byte addressing” significa que o índice aponta para um byte na memória.

![Untitled](Linguagem%20de%20Montagem%20MIPS%204f5a34586cc54f018c7f3c66650ce149/Untitled%202.png)

Bytes são práticos, porém a maioria dos dados utiliza “words”: Unidade básica de referenciamento à memória. Definida pela arquitetura. Para MIPS, uma word tem 32 bits ou 4 bytes. Registradores armazenam 32 bits.

![Untitled](Linguagem%20de%20Montagem%20MIPS%204f5a34586cc54f018c7f3c66650ce149/Untitled%203.png)

Com 32 bits, é possível indexar $2^{32}$ bytes com endereços de byte de 0 a $(2^{32} - 1)$. Ou,  $2^{30}$ words com endereços de byte de 0,4,8, … $(2^{30} - 4)$. Words são alinhadas (Restrição de Alinhamento).

![Untitled](Linguagem%20de%20Montagem%20MIPS%204f5a34586cc54f018c7f3c66650ce149/Untitled%204.png)

**Ordenamento dos Bytes**

Processadores MIPS podem operar tanto no esquema big-endian: 

![Untitled](Linguagem%20de%20Montagem%20MIPS%204f5a34586cc54f018c7f3c66650ce149/Untitled%205.png)

Quanto no little-endian: 

![Untitled](Linguagem%20de%20Montagem%20MIPS%204f5a34586cc54f018c7f3c66650ce149/Untitled%206.png)

**Modos de Endereçamento**

MIPS é uma arquitetura load/store, isto é somente instruções load e store têm acesso à memória. As instruções da ULA operam somente com valores em registradores. A máquina básica provê unicamente o modo de endereçamento imm (register) que utiliza como endereçamento a soma de um inteiro imediato e o conteúdo de um registrador.

**Operandos Constantes ou Imediatos**

- É recorrente a necessidade de se usar uma constante em uma operação.
- Simplicidade arquitetural: Evitar a carga da constante na memória para posterior transferência da mesma antes para um registrador antes do seu uso.
- A arquitetura provê instruções que operam diretamente com constantes (imediatos).

**Formato de Instruções**

![Untitled](Linguagem%20de%20Montagem%20MIPS%204f5a34586cc54f018c7f3c66650ce149/Untitled%207.png)

![Untitled](Linguagem%20de%20Montagem%20MIPS%204f5a34586cc54f018c7f3c66650ce149/Untitled%208.png)

### Estrutura Básica de um Programa

Duas áreas distintas: .text e .data

- .text: área do programa (instruções) em si.
- .data: área para declarações de variáveis estáticas.

Áreas independente da ordem: Montador responsável pela colocação.

.globl: Declaração para rótulos globais.

**Instruções**

Linguagem de Máquina (de Montagem)

→ Mais primitiva que linguagens de alto nível. Sem controle de fluxo sofisticado.

→ Muito restritiva: MIPS Aritchmetifc Instructions.

![Abstrações](Linguagem%20de%20Montagem%20MIPS%204f5a34586cc54f018c7f3c66650ce149/Untitled%209.png)

Abstrações

*Primeiro Princípio de Projeto: Simplicidade favorece regularidade.*

**Aritmética MIPS**

- Todas as instruções possuem 3 operandos
- A ordem dos operandos é fixa.

Exemplo:

- Código C:

```c
 A = B + C;
```

- Código MIPS:

```wasm
add $s0, $s1, $s2
```

- (associado às variáveis pelo compilador).

Algumas coisas ficam mais complicadas

- Código C:

```c
A = B + C + D;
E = F - A;
```

- Código MIPS:

```wasm
add $t0, $s1, $s2
add $s0, $t0, $s3
sub $s4, $s5, $s0
```

- Operandos devem ser registrados
- 32 registradores disponíveis

*Segundo Princípio de Projeto: Menor significa mais rápido.*

Geralmente uma quantidade grande de registradores pode aumentar o tempo de ciclo de Clock, pois aumenta a destância física entre os sinais eletrônicos.

Ex código de swap:

```c
swap(int v[], int k);
{
	int  temp;
	temp = v[k];
	v[k] = v[k+1];
	v[k+1] = temp;
}
```

```wasm
swap:
	muli $2 ,$5 , 4
	add $2 ,$4 ,$2
	lw $15 , 0($2)
	lw $16 , 4($2)
	sw $16 , 0($2)
	sw $15 , 4($2)
	jr $31

swap:
	muli $v0 ,$a1 , 4
	add $v0 ,$a0 ,$v0
	lw $t7 , 0($v0)
	lw $s0 , 4($v0)
	sw $s0 , 0($v0)
	sw $t7 , 4($v0)
	jr $ra
```

**Programa Armazenado**

- Instruções são compostas por bits
- Programas são armazenados na memória para serem lidos da mesma forma que os dados

![Untitled](Linguagem%20de%20Montagem%20MIPS%204f5a34586cc54f018c7f3c66650ce149/Untitled%2010.png)

**Ciclos de Busca e Execução**

1. Instruções são buscadas e colocadas num registrador especial (IR: Instruction Register)
2. Bits neste registrador “controlam” as ações subsequentes necessárias a execução da instrução
3. Busca a próxima instrução e continua

**Formato de Instruções MIPS**

Instruções , assim como registradores e words de  dados, também tem 32 bits de comprimento

![Untitled](Linguagem%20de%20Montagem%20MIPS%204f5a34586cc54f018c7f3c66650ce149/Untitled%2011.png)

Campos (fields)

- op: operação básica da instrução: opcode
- rs: primeiro registrador fonte (source)
- rt: segundo registrador fonte (source)
- rd: registrado de destino: resultado (destination)
- shamt: shift amount, para instruções de descolamento
- funct: seleciona variações da operação: function code

Exemplo: add $t0, $s1, $s2

→ Registradores são identicos por seus números (vide tabelas): $t0=8,$s1=17, $s2=18

Formato de Instruções Tipo-R (add: op=0 funct=32)

![Untitled](Linguagem%20de%20Montagem%20MIPS%204f5a34586cc54f018c7f3c66650ce149/Untitled%2012.png)

O que acontece quando uma instrução necessita de campos maiores?

→ usa o imediato: 

```c
addi $t0,$t1,Imm
lw $t0,Imm($t1)
```

*Quarto Princípio de Projeto: Um bom projeto exige bons compromissos.*

Compromisso do MIPS: Manter todas as instruções com o mesmo comprimento de bits, independente do tipo de formato da instrução!

Novo tipo de formato de instrução para instruções com dados Imediatos.

Exemplo: lw $t0, 32($s3)

→ O comprimento do quarto campo de Tipo-I é a soma dos comprimentos dos últimos três campos do Tipo-R

Formato de Instrução Tipo-I

![Untitled](Linguagem%20de%20Montagem%20MIPS%204f5a34586cc54f018c7f3c66650ce149/Untitled%2013.png)

**Compilação Manual**

Suponha que $t1 tenha o endereço base de A e que $s2 corresponda a h, traduza a seguinte linha em C para código de máquina MIPS:

A[300] = h + A[300];

Primeiro, temos que o código em assembly correspondente é:

```c
lw $t0, 1200($t1) # $t0 = A[300]
add $t0, $s2, $t0 # $tt0 = h + A[300]
sw $t0, 1200($t1) # A[300] = h + A[300]
```

Qual o código em linguagem de máquina destas 3 instruções?

![Untitled](Linguagem%20de%20Montagem%20MIPS%204f5a34586cc54f018c7f3c66650ce149/Untitled%2014.png)

**Operações Logicas**

![Untitled](Linguagem%20de%20Montagem%20MIPS%204f5a34586cc54f018c7f3c66650ce149/Untitled%2015.png)

NOT

- Realizado utilizando o NOR.
- Utiliza-se o $zero (Hardwired) de preferência!
- Expressão Booleana: A NOR B = NOT (A OR B)
- Se B = 0: NOT (A OR 0) = NOT(A)

sll e srl (deslocamento lógicos ou logical shifts)

- Instrução do Tipo-R
- Utiliza o campo shamt (shit amount)
    - Quantidade de Deslocament (de acordo com a direção!)
    - Ex: `sll $t2, $s0, 4 # $t2 = $s0 << 4 bits`

![Untitled](Linguagem%20de%20Montagem%20MIPS%204f5a34586cc54f018c7f3c66650ce149/Untitled%2016.png)

AND e OR

- Mesma atribuição do AND e OR booleanos
- Realizados Bit-a-bit
    - AND: Operação de “mascaramento” (ocultar bits) e “chave lógica”
    - OR: “Junção de sinais lógicos”

MIPS: Existem os equivalentes para AND e OR com Imediatos (ori e andi)

**Controle de Fluxo**

Desvio incodicional

→ Registrador Especial PC (Program Computer) : Indica qual o endereço da proxima instrução a ser buscada na memória. 

→ Instruções MIPS:

```wasm
jr $t0 # jump regiter: PC=[$t0]
j Label # jump label: PC=Label
jal Label # jump and link: $ra=PC+4; PC=Label
```

→ Formato da instrução tipo-J 

![Untitled](Linguagem%20de%20Montagem%20MIPS%204f5a34586cc54f018c7f3c66650ce149/Untitled%2017.png)

Exemplo de desvio condicional:

```wasm
bne $t0 , $t1 , Label # Branch if Not Equal: $t0 != $t1 ? PC=Label
beq $t0 , $t1 , Label # Branch if Equal: $t0 == $t1 ? PC=Label
```

Em C:

```c
if (i!=j)
	h=i+j;
else
	h=i-j;
```

Assembly:

```wasm
	
	beq $s4 , $s5 , Label1
	add $s3 , $s4 , $s5
	j Label2
Label1:   sub $s3 , $s4 , $s5
Label2:   ...
```

**Comparações**

No MIPS, são implementadas as comparações: == e !=. Como implementar: <,>,<=, >= ?

Instrução MIPS: Set on Less Than

- slt **$t0,$t1,$t2 # $t0=1 se $t1<$t2;    $t0=0 caso contrário**
- slti **$t0,$t1,Imm     # $t0=1  se $t1<Imm; $t0=0 caso contrário**
- Apenas com estas instruções podemos montar várias estruturas de controle
- Ao montador, é reservado o registrador $1 ($at) para essa tarefa

**Constantes**

Constantes pequenas são usadas muito frequentemente (50% dos operandos)

```wasm
A = A + B
B = B + 1
C = C - 18
```

Como tratar constantes no MIPS?

→ Coloque constantes típicas na memória e carregue-as

→ Crie registradores hardwired (como $zero) para constantes como um (1)

Instruções com imediato MIPS:

```wasm
addi $29 , $29 , 4
slti $8, $18 , 10
andi $29 , $29 , 6
ori $29 , $29 , 4;
```

Princípio de Projeto: Agilizar o caso mais comum!

Para carregar uma constante de 32 bits num registrador são necessárias duas instruções. Nova instrução: load upper immediate

![Untitled](Linguagem%20de%20Montagem%20MIPS%204f5a34586cc54f018c7f3c66650ce149/Untitled%2018.png)

Carga dos bits menos significativos

![Untitled](Linguagem%20de%20Montagem%20MIPS%204f5a34586cc54f018c7f3c66650ce149/Untitled%2019.png)

**Assembly vs Linguagem de Máquina**

O assembly fornece uma representação simbólica conveniente

- Muito mais fácil do que escrever números binários, por exemplo, destino primeiro
- Pode-se usar Labels em vez de endereços numéricos

A linguagem de máquina é realidade subjacente

- Por exemplo, o destino não é mais o primeiro
- labels são convertidos em números apropriados

O assembly pode fornecer “pseudo-instruções”

- Por exemplo, move $t0, $t1 existe apenas no assembly
- Pode ser implementada usando add $t0, $t1, $zero

Ao  considerar o desempenho, você deve contar as instruções reais.

**Outras Questões**

- Suporte para procedimentos
- Linkers, carregadores, layout da memória
- Pilhas, frames, recursão
- Manipulaão de strings e ponteiros
- Interrupções e exceções
- Chamadas de sistema e convenções

**Resumo**

![Untitled](Linguagem%20de%20Montagem%20MIPS%204f5a34586cc54f018c7f3c66650ce149/Untitled%2020.png)

Endereços (do Contador de Programa PC) em desvios

Instruções

```wasm
bne $t4 , $t5 , Label # Pr ́oxima  instru ̧c~ao  em  Label se $t4 != $t5
beq $t4 , $t5 , Label # Pr ́oxima  instru ̧c~ao  em  Label se $t4 == $t5
j Label # Próxima  instrução  em  Label
jal  Label # $ra=PC+4;  Próxima  Instrução  em  Label
```

Formatos

![Untitled](Linguagem%20de%20Montagem%20MIPS%204f5a34586cc54f018c7f3c66650ce149/Untitled%2021.png)

**Linguagem Assembly vs Linguagem de Máquina**

Pode-se especificar um registrador (como lw e sw) e adicioná-lo ao endereço (Endereço Relativo)

- Utilizar Instruction Address Register (PC = program counter)
- Maioria dos desvios dão locais (Princípio da Localidade)

Instruções tipo-J, j e jal, utilizam os 4 bits de alta ordem do PC e concatenam ao endereço do (Label (26)<<2)(28) totalizando os 32 bits:

- Limites de endereço de 256 MB (64M instruções)
- O montador e o linker precisam cuidar disso

![Untitled](Linguagem%20de%20Montagem%20MIPS%204f5a34586cc54f018c7f3c66650ce149/Untitled%2022.png)

Exemplo: while(save[i] == k) i++;

- MIPS: Possuem endereços em bytes, diferenciando em 4 bytes (word)
- A instrução bne acrescenta 2 words (8 bytes) na instrução seguinte, especificando o endereço de desvio (8 + 80016) e não em relação à instrução atual (12 + 80012), ou ao endereço completo/absoluto (80024)
- O jump utiliza o endereço completo (20000 x 4 = 80000)

![Untitled](Linguagem%20de%20Montagem%20MIPS%204f5a34586cc54f018c7f3c66650ce149/Untitled%2023.png)

**Instruções de suporte a procedimentos**

Também conhecido como funções (com retorno tipo void)

Passos em um procedimento:

1. Colocar os parâmetro em um lugar onde o procedimento possa acessa-los
2. Tranferir o controle para o procedimento
3. Adquirir recursos de armazenamento necessários par o procedimento
4. Realizar a tarefa desejada
5. Colocar o valor de retorno em um lugar onde  o programa que o chamou possas acessá-lo
6. Retornar o controle para o ponto de origem

Qual o lugar mais rápido em que se pode armazenar/manipular dados em um sistema eletronico? Registradosres MIPS:

- $a0 - $a3: parâmetros para os procedimentos;
- $v0 - $v1: valores de retorno do procedimento;
- $ra: registrador de endereço de retorno ao ponto de origem (ra = return address).

![Untitled](Linguagem%20de%20Montagem%20MIPS%204f5a34586cc54f018c7f3c66650ce149/Untitled%2024.png)

**jal (jump and link)**

Link, neste caso, quer dizer que é armazenada, no registrador $ra, o endereço da instrução que vem logo após a instrução jal Label:

Código equivalente:

```wasm
addi $ra, $PC, 4
j Label
```

ex:

![Untitled](Linguagem%20de%20Montagem%20MIPS%204f5a34586cc54f018c7f3c66650ce149/Untitled%2025.png)

Caso seja necessário mais de 4 argumentos e 2 valores de retorno ou o procedimento necessitar utilizar registradores salvos $sx, será feito o processo conhecido por register spilling:

- Uso de uma pilha;
- Temos um apontador para o topo da pilha;
- Este apontador é ajustado em uma palavra para cada registrador que é colocado na pilha (push), ou retirado da pilha (pop).
- Em MIPS, o registrador $29 é utilizado somente para indicar o topo da pilha: $sp (stack pointer)

**Pilha**

Por razões históricas, a pilha “cresce” do maior endereço para o menor endereço. Para colocar um valor na pilha (push), devemos decrementar $sp em uma palavra e mover o valor desejado para a posição de memória apontada por $sp. Para retirar um valor da pilha (pop), devemos ler este valor da posição de memória apontado por $sp, e então incrementar $sp em uma palavra.

Exemplo de procedimento de folha (que não chamam outros procedimentos)

Suponha que tenhamos o seguinte código:

```c
int exemplo_folha (int g, int h, int i, int, j)
{
int f;
f = (g+h) - (i+j);
return f;
}
```

Definição: Os argumentos g,h,i e j correspondem aos registradores $a0, $a1, $a2 e $a3, e f corresponde a $s0.

Definir o rótulo do procedimento: exemplo_folha:

Devemos então armazenar na pilha o registradores que serão utilizados pelo procedimento:

```wasm
addi $sp, $sp, -12 # cria espaço para 3 itens na pilha
sw $t1, 8($sp) # empilha $t1
sw $t0, 4($sp) # empilha $t0
sw $s0, 0($sp) # empilha $s0
```

Comportamento da pilha:

![Untitled](Linguagem%20de%20Montagem%20MIPS%204f5a34586cc54f018c7f3c66650ce149/Untitled%2026.png)

Antes de sair do procedimento, restaurar os valores dos registradores salvos na pilha:

```wasm
lw $s0, 0($sp) # desempilha $s0
lw $t0, 4($sp) # desempilha $t0
lw $t1, 8($sp) # desempilha $t1
addi $sp, $sp, 12 # remove 3 itens da pilha
```

Voltar ao fluxo do programa para a instrução seguinte ao ponto em que a função exemplo_folha foi chamada:

```wasm
jr $ra # retorna para a subrotina que chamou
```

**Procedimentos Aninhados**

problema: conflito com registradores $ax e $ra.

![Untitled](Linguagem%20de%20Montagem%20MIPS%204f5a34586cc54f018c7f3c66650ce149/Untitled%2027.png)

Convenção sobre registradores:

- Uma solução é empilhar todos os registradores que precisam ser preservados
- Estabelecer uma convenção entre subrotinas chamada e chamadora sobre a preservação dos registradores (uso eficiente da pilha)
- Definições:
    - Chamadora(Caller): função que faz a chamada, utilizando jal;
    - Chamada(Callee): função sendo chamada/receptora.

Benefícios:

- Programadores podem escrever funções que funcionam juntas;
- Funções que chamam outras funções - como as recursivas - funcionam corretamente.

Exemplo: soma_recursiva

```c
int soma_recursiva (int n)
{
	if (n<1)
		return 0;
	else
		return n + soma_recursiva(n-1)
}
```

O parâmetro n corresponde ao registrador $a0.

Devemos inicialmente colocar um rótulo para a função, e salvar o endereço de retorno $ra e o parâmetro $a0:

```c
Soma_recursiva:
	addi $sp, $sp, -8 # prepara a pilha para receber 2 itens
	sw $ra, 4($sp) # empilha $ra (End. Retorno)
	sw $a0, 0($sp) # empilha $a0 (n)
```

Na primeira vez que soma_recursiva é chamada, o valor de $ra que é armazenado corresponde ao endereço que está na rotina chamadora.

Vamos agora compilar o corpo da função. Inicialmente, testamos se n<1:

```c
slti $t0, $a0, 1 # testa se n < 1
beq $t0, $zero, L1 # se n>=1, vá para L1
```

Se n<1, a função deve retornar o valor 0. Não podemos nos esquecer de restaurar a pilha.

```c
add $v0, $zero, $zero # valor de retorno é 0
addi $sp, $sp, 8 # remove 2 itens da pilha
jr $ra # retorne para depois de jal
```

Se n ≥ 1, decrementamos n e chamamos novamente a função soma_recursiva como novo valor de n.

```c
L1: addi $a0, $a0, -1 # argumento passa a ser (n-1)
		jal soma_recursiva # calcula a soma para (n-1)
```

Quando a soma para (n-1) é calculada, o programa volta a executar na próxima instrução. Restauramos o endereço de retorno e o argumento anteriores, e incrementamos o apontador de topo de pilha:

```c
lw $a0, 0($sp) # restaura o valor de n
lw $ra, 4($sp) # restaura o endereço de retorno
addi $sp, $sp, 8 # retira 2 itens da pilha.
```

Agora o registrador $v0 recebe a soma do argumento antigo $a0 como valor atual em $v0 (soma_recursiva para n-1):

```c
add $c0, $a0, $v0 # retorne n + soma_recursiva(n-1)
```

Por última, voltamos para a instrução seguite à que chamou o procedimento:

```c
jr $ra # retorne para a chamadora
```

O que deve ser preservado?

![Untitled](Linguagem%20de%20Montagem%20MIPS%204f5a34586cc54f018c7f3c66650ce149/Untitled%2028.png)

**Alocando espaço para novos dados (locais) na pilha**

Frame de procedimento (Registro de ativação)

- Armazenar variáveis locais a um procedimento
- Facilita o acesso a essas variáveis locais ter um apontador estável $fp

![Untitled](Linguagem%20de%20Montagem%20MIPS%204f5a34586cc54f018c7f3c66650ce149/Untitled%2029.png)

**Alocação de memória (SPIM e MARS)**

![Untitled](Linguagem%20de%20Montagem%20MIPS%204f5a34586cc54f018c7f3c66650ce149/Untitled%2030.png)

**Política de Convenção de Uso de Registradores**

![Untitled](Linguagem%20de%20Montagem%20MIPS%204f5a34586cc54f018c7f3c66650ce149/Untitled%2031.png)

**Simuladores SPIM/MARS: chamadas de sistema**

Implementa o montador, pseudo-instruções, simula um sistema operacional com funções de Entrada/Saída em console próprio.

Ex.: Aplicação que escreve na tela: the answer = 5

```jsx
.data
str:
	.asciiz "the answer = "
	.text
	li $v0, 4 # código de chamada ao sistema para print_str
	la $a0, str # endereço da string a imprimir
	syscall # imprime a string
	li $v0, 1 # código de chamada ao sistema para print_int
	li @a0, 5 # inteiro a imprimir
	syscall # imprime
```

Chamadas de sistema:

![Untitled](Linguagem%20de%20Montagem%20MIPS%204f5a34586cc54f018c7f3c66650ce149/Untitled%2032.png)

Exemplo: clear (ponteiro vs array)

Objetivo: Zerar os componentes do array de tamanho size

```c
void clear1(int array[], int size)
{
	int i;
	for(i=0;i<size;i++)
		array[i] = 0;
}
```

```c
void clear2(int *array, int size)
{
	int *p;
	for(p=&array[0]; p<&array[size];p++)
	*p=0;
}
```

```wasm
clear1: move $t0, $zero # i = 0
Loop1: sll $t1, $t0, 2 # $t1 = 4*$t0
	add $t2, $a0, $t1 # $t2 = &array[i]
	sw $zero, 0($t2) # array[i] = 0
	addi $t0, $t0, 1 # i = i+1
	slt $t3, $t0, $a1 # $t3 = (i<size)
	bne $t3, $zero, Loop1 # if (), go to Loop1
	jr $ra # bye!
```

```wasm
clear2: move $t0 ,$a1      # $t0 = size
				move $t5 ,$a0      # $t5 = &array [0]
Loop2:   sw $zero ,0($t5)   # array[i] = 0
	addi $t5 ,$t5 ,4           # $t5 = $t5+4 = &array[i+1]
	addi $t0 ,$t0 ,-1          # $t0 = $t0 -1
	bne $t0 ,$zero ,Loop2     # if (), go to  Loop2
	jr $ra                     # bye!
```

**Arquiteturas alternativas**

Alternativas de projeto:

- Forneça operações mais poderosas;
- O objetivo é reduzir o número de instruções executadas;
- O risco é um tempo de ciclo mais lento e/ou uma CPI mais alta.

**Resumo**

A complexidade da instrução é apenas um variável

- Instrução mais simples versus CPI mais alta / velocidade de clock mais baixa

Princípios de projeto:

- Simplicidade favorece a regularidade
- Menor é melhor
- Bom projeto exige comprometimento
- Agilizar o caso comum

Arquitetura do conjunto de instruções: uma abstração muito importante **