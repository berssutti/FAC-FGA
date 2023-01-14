# Hierarquia de Memória

**Explorando a Hierarquia de Memória**

![Untitled](Hierarquia%20de%20Memo%CC%81ria%20cb35171e5cfe4f1ab2d4490da0d3d5b0/Untitled.png)

Evolução DRAM:

![Untitled](Hierarquia%20de%20Memo%CC%81ria%20cb35171e5cfe4f1ab2d4490da0d3d5b0/Untitled%201.png)

### Princípio da Localidade

Princípio arquitetural que justifica a utilização da hierarquia de memória.

Na ocorrência de uma referência:

- Localidade temporal: o mesmo item tende a ser referenciado novamente em breve
- Localidade espacial: a mesma região (itens vizinhos) tendem a ser referenciada(os) novamente em breve.

Por que um código possui localidade?

- Consequência da simples e natural estrutura utilizada em algoritmos aplicados na solução de problemas.
    - Localidade temporal: loops. Instruções e dados com alta probabilidade de serem acessados repetidamente
    - Localidade espacial: instruções são lidas sequencialmente (normalmente). Acessos a dados respeitam padrões sequencias que implicam localidade espacial: arrays!
    
    Foco inicial de estudos: dois níveis da hierarquia (superior e inferior).
    

Definições:

- bloco: unidade mínima de dados
- acerto/hit: os dados solicitados estão no nível superior
- falta/miss: os dados solicitados não estão no nível superior
- Taxa de acertos: é a fração dos acessos à memória encontrados no nível superior
- Taxa de faltas: é a fração dos acessos à memória não encontrados no nível superior
    
    Taxa de Faltas = (1-Taxa de Acertos)
    
- Tempo de Acerto: tempo necessário para acessar o nível superior da hierarquia de memória + o tempo para determinar o acerto (ou falta);
- Penalidade de Falta: é o tempo necessário para substituir o bloco do nível superior pelo bloco do nível inferior + o tempo de transferência para o processador

### Memória Cache

- Definição (dicionario): “lugar seguro para esconder ou guardar coisas”.
- Cache: denominação escolhida para representar o nível de hierarquia de memória entre processador e a memória principal.
- O termo Cache tem sido utilizado para tirar proveito da localidade de acesso (espacial e temporal)
- Primeiros registros: aplicação inicial nos computadores de pesquisa da década de 1960

Estratégia de estudos: inicialmente trataremos a implementação de uma cache simples: cada requisição do processador é um word e os blocos também consistem em uma única word.

![Untitled](Hierarquia%20de%20Memo%CC%81ria%20cb35171e5cfe4f1ab2d4490da0d3d5b0/Untitled%202.png)

Dois problemas (Q:)

- Como sabemos se um item de dados está na cache?
- Se estiver, como encontrá-los?

Neste exemplo:

- O tamanho to bloco é uma word.
- Cache do tipo diretamente mapeado: para cada item de dados no nível inferior, existe exatamente um local onde ele pode estar

Logo, muitos itens no nível inferior compartilham locais no nível superior

Dois problemas (A:)

- A cada word, atribui-se um endereço na memória: mapeamento direto
- Aplicar uma função de mapeamento (do tipo função hash):
    - posição = (endereço_de_bloco) mod (numero_de_blocos_da_cache)
    - Caso o número de entradas (posições) na cache seja uma potência de 2:
        - Economiza-se na redução modular! Basta tomar os $log_2$(numero_de_blocos_da_cache) bits menos significativos do endereço

Cache com capacidade par armazenar 8 words = numero_de_blocos_da_cache

Endereço X é mapeado em posições da cache na seguinte forma: End_X = X mod 8

Mas 8 é potencia de 2: $2^3$. Logo $log_28$ = 3, a redução modular pode ser substituída por leitura dos 3 bits menos significativos do endereço.

São esse bits que fornecerão o índice de endereçamento na Cache

Mapeamento direto para memória de 32 words a cache de 8 words

- **Tags**: contém informações de endereços necessários para identificar se uma word na cache corresponde à word requisitada
    
    Precisa somente da parte superior do endereço na word (2 bits MSB) dos 5 bits de endereço na Tag (ex. anterior)
    
- **Bit de validade:** indica se contém um endereço válido. Se não estiver ativado, não pode haver correspondência para o respectivo bloco;

![Untitled](Hierarquia%20de%20Memo%CC%81ria%20cb35171e5cfe4f1ab2d4490da0d3d5b0/Untitled%203.png)

![Untitled](Hierarquia%20de%20Memo%CC%81ria%20cb35171e5cfe4f1ab2d4490da0d3d5b0/Untitled%204.png)

**Tamanho dos Campos da Cache**

Na prática uma Cache tem várias words armazenadas:

- Considerando o endereço em bytes de 32 bits
- Cache mapeada diretamente  $2^n$ x (tamanho_bloco + tamanho_tag+tamanho_campo_validade)

Quantos bits no total são necessários para uma cache em uma arquitetura MIPS diretamente mapeada com 16KB de dados e blocos de 4 words, considerando um endereço de 32 bits?

16KB = 4 Kwords = $2^{12}$ words com tamanho do bloco de 4 words ($2^2$) ou seja, $2^{10}$  blocos

Cada bloco possui 4 x 32 = 128 bits de dados mais uma tag que é 32 - 10 - 2- 2, mais um bit de validade, então:

tamanho_cache = $2^{10}$ x (128 + (32 - 10 -2 -2) + 1) = 147 Kbits

### Acertos x Faltas

- Acertos de leitura: sempre desejáveis
- Falhas de leitura: ocasiona um stall na CPU. Para saná-las, demanda-se que se busque o bloco da memória, que se distribua-o para o cache e que se reinicie e execução da instrução
- Acertos de escrita:
    - Escrita direta: podem substituir os dados no cache e na memória
    - Escrita adiada na memória: escrevem os dados apenas no cache
- Faltas de escrita: Leem o bloco inteiro para o cache, depois escrevem a word

Curva Taxa de Faltas vs. Tamanho do Bloco

![Untitled](Hierarquia%20de%20Memo%CC%81ria%20cb35171e5cfe4f1ab2d4490da0d3d5b0/Untitled%205.png)

**Tratando Faltas na Cache**

- Unidade de Controle precisa detectar a falta
- Utilizamos a mesma estrutura de controle (Uniciclo, Multiciclo e Pipelining);
    
    Substituem-se Memórias pela Cache
    
- Cria-se uma unidade de controle exclusiva
- Em caso de falta, gera-se um stall semelhante ao do pipeline e “congela” o conteúdo dos registradores temporários
1. Enviar o PC original PCatual - 4 para a memória
2. Solicitar leitura da memória principal e esperar o término do acesso (vários ciclos)
3. Escrever na entrada da cache (atualizar tags e bit de validade)
4. Reiniciar a execução da instrução (1) e procurando a instrução na cache
- Risco de valores inconsistentes (valor da Cache ≠ memória principal)
- Usar o método write-through (escrever os dados tanto na cache, quanto na memória principal)
    
    Processo lento(aprox. 100 ciclos)
    
- Solução: Buffer de escrita (write buffer). Fila que contém os dados esperando para serem escritos na memória
    
    Problema: Escrita em rajadas (bursts)
    
- Solução alternativa: Write-Back
    
    Quando ocorre um escrita, o novo valor é somente escrito no bloco da Cache. Só é escrito na hierarquia inferior somente quando for substituído na Cache.
    
- E se ocorre um “Write-Miss”? Solução: Write-allocation

**Write-Through**

- Durante um acerto de escrita o dado na cache pode ser atualizado
    
    Mas os dados podem ser inconsistentes!
    
- Usando o Write through: atualiza-se também a memória
- Processo demorado: se a CPI base igual a 1, 10% são tipo ‘store’, o processo de escrever na memória leva em média 100 ciclos, então:
    
    CPI efetiva = 1 + 0,1 x 100 = 11
    

**Write-Back**

- Alternativa: durante a escrita do dados, atualiza somente o bloco na cache
    
    Mantenha o controle de cada bloco escrito evitando sobreposições
    
- Quando o bloco já escrito tiver de ser escrito novamente:
    - Escreva-o na memória
    - Utilize um write buffer para permitir que o bloco seja primariamente lido
- Para write-back: usualmente, busque o bloco

Alternar os tamanhos de bloco tende a reduzir  a taxa de fatas:

![Untitled](Hierarquia%20de%20Memo%CC%81ria%20cb35171e5cfe4f1ab2d4490da0d3d5b0/Untitled%206.png)

### Desempenho

Modelo simplificado:

- Tempo de execução = (ciclos de execução + ciclos de stall) x tempo de ciclo
- Ciclos de stall = numero de instruções x taxa de faltas x penalidade de falta

Alternativas para melhorar o desempenho (diminuir tempo de execução):

- Reduzir a taxa de faltas: diminui os ciclos de stall e, por sua vez, o tempo de execução
- Reduzir a penalidade de faltas: idem!

Quais são os efeitos de se aumentar o tamanho do bloco básico da cache?

Ciclos de stall na memória:

- ciclos_de_stall_memoria = ciclos_stall_leitura + ciclos_stall_escrita

A análise da leitura é mais simples. Stalls provocados por:

- Acesso de leitura e
- Penalidade de falta

Ciclos_stall_leitura = Leituras/Programa x taxa_faltas_leitura x Penalidade_falta_leitura

Pode-se combinar taxas de faltas de leituras e escritas:

- Ciclos_stall_memoria = Acesso_memoria/Programa x Taxa_faltas x Penalidade_faltas

Que pode ser fatorado como:

- Ciclos_stall_memoria = instruções/programa x faltas/instrução x penalidade_faltas

Taxa de faltas de cache de instruções de um programa em 2%. A taxa de falta de dados de cache 4%. Um processador possui um CPI 2 sem qualquer stall de memória e a penalidade de falta é de 100 ciclos para todas as faltas

Determine o quanto mais rápido um processador executaria com um cache perfeita (sem faltas).

Workload (SPECint2000) de 36% para acessos à memória (dados).

Número de ciclos de falta de instrução para I instruções:

- Ciclos_falta_instrução =  I x 2% x 100 = 2xI

Supondo a frequência de todos os loads e stores de 36% (Ex.: SPEint2000)

- Ciclos_falta_dados = I x 36% x 4% x 100 = 1,44 x I
- Ciclos_total_memoria = 2 x I + 1,44 x I = 3,44 x I

Assim a CPI com stalls de memória é 2 + 3,44 = 5,44

Como não há mudanças no número de instruções ou na velocidade do clock, temos:

![Untitled](Hierarquia%20de%20Memo%CC%81ria%20cb35171e5cfe4f1ab2d4490da0d3d5b0/Untitled%207.png)

Pode-se melhorar o desempenho, também, a partir da frequência de relógio (clock) e da CPI. Consequências:

- Quando menor a CPI, maior será o impacto
- Se as memórias principais tiverem o mesmo tempo de acesso absolutos, uma velocidade maior produzirá uma penalidade de falta maior.

**Reduzindo Faltas**

Em uma extremidade: utilizamos o mapeamento direto

- O bloco só pode ser colocado exatamente em um local

Em outra extremidade: Totalmente Associativo

- Bloco posicionado em qualquer local na cache
- Faz-se buscas pelo bloco (funcional para pequenas caches)

Na faixa intermediária: Associativa por conjunto

- Existem locais fixos (mapeamento direto)
- N locais para uma cache de n vias
- Cada bloco é mapeado para um conjunto único da cache, determinado pelo índice, e um bloco pode ser colocado em qualquer elemento desse conjunto;

![Untitled](Hierarquia%20de%20Memo%CC%81ria%20cb35171e5cfe4f1ab2d4490da0d3d5b0/Untitled%208.png)

**Mapeamento Direto x Associativo por Conjunto**

Mapeamento Direto:

- Numero_bloco mod Capacidade_blocos_cache

Associativa por Conjunto:

- Numero_bloco mod Numero_Conjuntos_Cache

Localizando bloco na cache:

Na associativa por conjunto:

- Como na diretamente mapeada, cada bloco inclui uma tag de endereço para o endereço do bloco
- Possui índice para selecionar o conjunto de interesse
- Offset é o endereço dos dados desejados dentro do bloco

![Untitled](Hierarquia%20de%20Memo%CC%81ria%20cb35171e5cfe4f1ab2d4490da0d3d5b0/Untitled%209.png)

Cache de 8 blocos em diferentes configurações:

![Untitled](Hierarquia%20de%20Memo%CC%81ria%20cb35171e5cfe4f1ab2d4490da0d3d5b0/Untitled%2010.png)

Implementação viável para agrupamentos de 4 (4-way set associative)

![Untitled](Hierarquia%20de%20Memo%CC%81ria%20cb35171e5cfe4f1ab2d4490da0d3d5b0/Untitled%2011.png)

Desempenho x Associatividade

![Untitled](Hierarquia%20de%20Memo%CC%81ria%20cb35171e5cfe4f1ab2d4490da0d3d5b0/Untitled%2012.png)

**Reduzindo as penalidades das faltas com caches multiníveis**

Acrescente um segundo nível da cache:

- Normalmente, o cache está no mesmo chip do processador
- Use SRAMs para acrescentar outro cache acima (antes) da memória principal
- A penalidade de faltas diminui se os dados estiverem no cache de segundo nível

Exemplo:

- CPI de 1,0 em uma máquina de 5 GHz com taxa de faltas de 5% acesso a DRAM de 100ns
- Acrescentar cache de segundo nível com tempo de acesso de 5 ns diminui a taxa de faltas para 0,5%

Usando cache multinível

- Tente otimizar o tempo de acerto no cache de primeiro nível
- Tente otimizar a taxa de faltas no cache de segundo nível

Complexidade da cache:

![Untitled](Hierarquia%20de%20Memo%CC%81ria%20cb35171e5cfe4f1ab2d4490da0d3d5b0/Untitled%2013.png)

O desempenho do sistema de memória é, normalmente, um fator crítico:

- Caches multinível e processadores em pipeline dificultam a previsão de resultados (baseados exclusivamente pela análise de complexidade algorítma)
- Otimizações de compilador aumentam a localidade e prejudicam, algumas vezes, o ILP (camada de integração de Processamento)

Logo, fica difícil de prever, baseado excluivamente em complexidade algorítmica, qual é o melhor algoritmo, pois a decisão poderá ser fortemente afetada pela implementação em software e pela arquitetura de hardware do sistema computacional.

### Memória Virtual

A memória virtual pode atuar como cache para o armazenamento secundário

![Untitled](Hierarquia%20de%20Memo%CC%81ria%20cb35171e5cfe4f1ab2d4490da0d3d5b0/Untitled%2014.png)

Vantagens:

- Ilusão de se ter mais memória física do que o volume instalado na memória RAM;
- Remanejamento do programa
- Proteção

Objetivos básicos:

- Recolocação: auxiliar diretamente para que tenha seu próprio espaço de endereçamentos
- Proteção: impedir o uso indevido de endereços
- Swapping: utilizar mais memória física do que existe

**Paginação**

Uma página é o bloco básico de memória virtual

Faltas de página: semelhante a faltas de cache. Os dados não estão na memória (armazenamento de alta velocidade); recupere-os do disco (armazenamento de baixa velocidade).

- Penalidade alta em decorrência da falta. Boa prática é ajustar tamanhos de páginas grandes o suficiente para compensar latências decorrentes de acesso a disco. Tamanhos típicos em torno de 4 KB.
- Faz-se necessário reduzir as faltas de página. Há heurísticas de reposicionamento de quadros em memória que tratam desse problema. Algoritmos LRU
- Pode manipular as faltas no software em vez de no hardware.
- Quando se vai tratar problemas de consistência devido a eventos de escrita em páginas, adota-se a técnica de escrita adiada para se otimizar a manipulação de memória virtual. (escrita direta é muito onerosa).

Endereçamento Lógico (Virtual) vs. Endereçamento Físico

![Untitled](Hierarquia%20de%20Memo%CC%81ria%20cb35171e5cfe4f1ab2d4490da0d3d5b0/Untitled%2015.png)

![Untitled](Hierarquia%20de%20Memo%CC%81ria%20cb35171e5cfe4f1ab2d4490da0d3d5b0/Untitled%2016.png)

![Untitled](Hierarquia%20de%20Memo%CC%81ria%20cb35171e5cfe4f1ab2d4490da0d3d5b0/Untitled%2017.png)

![Untitled](Hierarquia%20de%20Memo%CC%81ria%20cb35171e5cfe4f1ab2d4490da0d3d5b0/Untitled%2018.png)

![Untitled](Hierarquia%20de%20Memo%CC%81ria%20cb35171e5cfe4f1ab2d4490da0d3d5b0/Untitled%2019.png)

![Untitled](Hierarquia%20de%20Memo%CC%81ria%20cb35171e5cfe4f1ab2d4490da0d3d5b0/Untitled%2020.png)

**Alguns Problemas** 

As velocidades do processadores continuam a aumentar muito rápido.

- Muito mais rápido do que os tempos de acesso à DRAM ou ao disco.

Desafio de projeto: como lidar com essa crescente disparidade? Prefechng? Caches de terceiro nível (ou main)? Projeto de memória?