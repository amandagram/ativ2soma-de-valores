# Relatório da Atividade 2 Avaliar o desempenho da soma de valores em paralelo

**Disciplina: PROGRAMAÇÃO CONCORRENTE E DISTRIBUÍDA** 
**Aluno(s): Amanda Gonçalves Ramos**
**Turma: SI**
**Professor: Rafael**
**Data: 18/03/2026**



# 1. Descrição do Problema

 
O programa resolve o problema de soma de um grande vetor de números inteiros
(10.000.000 elementos), armazenados em um arquivo de texto (numero2.txt). O
objetivo é medir o desempenho de diferentes estratégias de execução — serial e
paralela — para identificar o impacto do paralelismo em uma tarefa memory-bound.
 
Qual é o objetivo do programa?
  Calcular a soma de todos os valores contidos no arquivo numero2.txt e comparar
  o desempenho entre execução serial e execução paralela com 2, 4, 8 e 12
  processos.
 
Qual o volume de dados processados?
  10.000.000 números inteiros (valores +1 e -1), totalizando aproximadamente
  33 MB de dados em disco.
 
Qual algoritmo foi utilizado?
  Soma linear iterativa (redução). Na versão serial, utiliza-se a função sum()
  nativa do Python. Na versão paralela, o vetor é dividido em chunks iguais,
  cada processo calcula uma soma parcial, e as somas parciais são somadas ao
  final (redução de dois níveis).
 
Qual a complexidade aproximada do algoritmo?
  O(n) — linear no número de elementos. Cada elemento é lido e somado exatamente
  uma vez. O algoritmo é dominado pelo acesso à memória (memory-bound), sem
  operações computacionalmente intensivas.
 
# 2. Ambiente Experimental

 
Os experimentos foram realizados no seguinte ambiente de hardware e software:
 
  Item                          | Descrição
  ------------------------------|-------------------------------------------
  Processador                   | CPU virtual — 2 núcleos físicos (nproc)
  Número de núcleos             | 2 núcleos lógicos disponíveis
  Memória RAM                   | Ambiente virtualizado (container Linux)
  Sistema Operacional           | Ubuntu 24.04 LTS (Linux)
  Linguagem utilizada           | Python 3 (versão padrão do ambiente)
  Biblioteca de paralelização   | multiprocessing (stdlib do Python)
  Compilador / Versão           | CPython 3.x — interpretado
 
# 3. Metodologia de Testes

 
Os experimentos foram conduzidos de forma sistemática para garantir a
reprodutibilidade dos resultados.
 
Como o tempo foi medido?
  O tempo de execução foi medido com time.perf_counter(), que fornece resolução
  de nanosegundos. A medição compreende apenas a fase de soma (após o
  carregamento do arquivo em memória), excluindo o I/O de disco para garantir
  a comparação exclusiva do desempenho computacional.
 
Configurações testadas:
  * 1 processo  (versão serial — baseline)
  * 2 processos paralelos
  * 4 processos paralelos
  * 8 processos paralelos
  * 12 processos paralelos
 
Condições experimentais:
  * 1 execução por configuração (tempo único medido)
  * O arquivo foi carregado em memória antes de iniciar a medição
  * Ambiente de container — possível variação de carga do sistema
  * Sem média estatística aplicada (execução única por configuração)
 
# 4. Resultados Experimentais

 
Tempos de execução obtidos para cada configuração (em segundos):
 
  Nº de Processos | Tempo de Execução (s) | Resultado da Soma
  ------------------|------------------------|------------------
  1  (serial)       | 0,038887               | 5.384
  2                 | 0,984171               | 5.384
  4                 | 0,619764               | 5.384
  8                 | 0,692427               | 5.384
  12                | 0,796300               | 5.384


  
 
# 5. Cálculo de Speedup e Eficiência

 
Fórmulas utilizadas:
 
  Speedup(p)    = T(1) / T(p)
  Eficiência(p) = Speedup(p) / p
 
  Onde:
    T(1) = 0,038887 s  (tempo serial)
    p    = número de processos


 
# 6. Tabela de Resultados

 
  Processos | Tempo (s) | Speedup | Eficiência | Eficiência (%)
  ----------|-----------|---------|------------|---------------
  1         | 0,038887  | 1,000   | 1,000      | 100,0%
  2         | 0,984171  | 0,040   | 0,020      |   2,0%
  4         | 0,619764  | 0,063   | 0,016      |   1,6%
  8         | 0,692427  | 0,056   | 0,007      |   0,7%
  12        | 0,796300  | 0,049   | 0,004      |   0,4%


  
 
# 7. Gráfico de Tempo de Execução

 
O gráfico abaixo ilustra o tempo de execução (em segundos) em função do número
de processos. Observa-se um aumento expressivo ao adicionar paralelismo,
evidenciando o alto custo de overhead do multiprocessing em Python.


 <img width="1440" height="718" alt="image" src="https://github.com/user-attachments/assets/44b22918-ee8f-4860-ac2d-4521eb332ceb" />

 
# 8. Gráfico de Speedup

 
O gráfico compara o speedup obtido com o speedup ideal (linear). Na prática,
o speedup obtido foi inferior a 1,0 em todas as configurações paralelas,
indicando que o overhead superou qualquer ganho.

 
 <img width="1440" height="758" alt="image" src="https://github.com/user-attachments/assets/8e381ccf-cadb-4c57-8402-aa6c4c4a1bb2" />

 
 
# 9. Gráfico de Eficiência

 
O gráfico apresenta a eficiência de paralelização por número de processos.
A eficiência caiu de 2,0% (2 processos) para 0,4% (12 processos).


 
 <img width="1440" height="758" alt="image" src="https://github.com/user-attachments/assets/6c2f3f8c-9a0c-4b11-a8fa-2bc514a468c7" />

  
 
# 10. Análise dos Resultados

 
O speedup obtido foi próximo do ideal?
  Não. O speedup ideal com 2 processos seria 2,0x; o obtido foi 0,04x. Isso
  significa que a versão paralela foi aproximadamente 25 vezes mais lenta que
  a versão serial. O speedup ficou muito abaixo do ideal em todas as
  configurações.
 
A aplicação apresentou escalabilidade?
  Não apresentou escalabilidade positiva. Ao aumentar o número de processos, o
  desempenho não melhorou. Houve uma leve melhora de 2 para 4 processos
  (0,984s → 0,620s), mas ainda muito pior que o serial. A partir de 4
  processos, o desempenho voltou a piorar.
 
Em qual ponto a eficiência começou a cair?
  A eficiência já era criticamente baixa desde a primeira configuração paralela
  (2 processos: 2,0%). Ela caiu continuamente até 0,4% com 12 processos, sem
  nenhum ponto de operação eficiente.
 
O número de processos ultrapassa o número de núcleos físicos?
  Sim. O ambiente possui apenas 2 núcleos físicos (nproc = 2). Executar 4, 8 ou
  12 processos significa que múltiplos processos competem pelo mesmo núcleo,
  causando troca de contexto excessiva (context switching) e degradação.
 
Houve sobrecarga de paralelização?
  Sim, de forma muito significativa. O Python multiprocessing cria processos
  separados (não threads), o que implica:
    (1) criação de subprocessos com cópia do espaço de memória;
    (2) serialização (pickle) do vetor de 10M de inteiros para os filhos;
    (3) deserialização no processo filho;
    (4) retorno dos resultados parciais via serialização novamente.
  Para uma operação simples como soma, esse overhead é ordens de magnitude
  superior ao custo do cálculo em si.
 


# 11. Conclusão

 
O experimento demonstrou que o paralelismo com Python multiprocessing não
trouxe ganho de desempenho para a soma de vetores. Pelo contrário, o overhead
de criação de processos e comunicação (IPC via pickle) foi tão elevado que
tornou todas as configurações paralelas significativamente mais lentas que a
versão serial.
 
O melhor desempenho observado foi da versão serial (0,039 s), que executou
25x mais rápido que a melhor configuração paralela testada (4 processos,
0,620 s).
 
Conclusões principais:
  * A paralelização não é benéfica para tarefas de baixa intensidade
    computacional (memory-bound / I/O-bound).
  * O Python impõe overhead adicional devido ao GIL e ao modelo de
    multiprocessing baseado em processos pesados.
  * O número de núcleos físicos (2) limita qualquer ganho real acima de
    2 processos.
  * Para obter speedup real em Python, seria necessária uma tarefa com
    razão computação/comunicação muito maior (ex: processamento de imagens,
    criptografia, ML).
  * A versão em C com OpenMP para o mesmo problema mostrou speedup positivo
    (até 1,70x), comprovando que o problema não é o algoritmo, mas sim as
    características do runtime do Python.
 
