# Relatório do Laboratório 2 - Chamadas de Sistema

---

## Exercício 1a - Observação printf() vs 1b - write()

### Comandos executados:
```bash
strace -e write ./ex1a_printf
strace -e write ./ex1b_write
```

### Análise

**1. Quantas syscalls write() cada programa gerou?**
- ex1a_printf: 9 syscalls
- ex1b_write: 7 syscalls

**2. Por que há diferença entre printf() e write()?**

```
-printf é uma forma mais simples de executar write no terminal e write é uma implementação mais direta e detalhada do uso de write que evita exessos.
```

**3. Qual implementação você acha que é mais eficiente? Por quê?**

```
Printf é uma forma mais rápido para fazer saída , por ser mais simples e usa buffer, mas existe problema com \n que pode atrasar execução.
```

---

## Exercício 2 - Leitura de Arquivo

### Resultados da execução:
- File descriptor: 3
- Bytes lidos: 127

### Comando strace:
```bash
strace -e open,read,close ./ex2_leitura
```

### Análise

**1. Por que o file descriptor não foi 0, 1 ou 2?**

```
Porque são fd especificados para stdin,standardin,stdout,standardout,stderr,standarderror.
```

**2. Como você sabe que o arquivo foi lido completamente?**

```
Não deu bug ao ler buffer size - 1 , e o próprio texto informa isso.
```

---

## Exercício 3 - Contador com Loop

### Resultados (BUFFER_SIZE = 64):
- Linhas: 25 (esperado: 25)
- Caracteres: 1300
- Chamadas read(): 21
- Tempo: 0.000109 segundos

### Experimentos com buffer:

| Buffer Size | Chamadas read() | Tempo (s) |
|-------------|-----------------|-----------|
| 16          |        83       | 0.000215  |
| 64          |        21       | 0.000119  |
| 256         |         6       | 0.000079  |
| 1024        |         2       | 0.000067  |

### Análise

**1. Como o tamanho do buffer afeta o número de syscalls?**

```
Quando maior o buffer menor é a quantidade de reads,syscall para leitura.
```

**2. Como você detecta o fim do arquivo?**

```
Enquando Read não retornar 0 ou -1 , existe dados no arquivo.
```

---

## Exercício 4 - Cópia de Arquivo

### Resultados:
- Bytes copiados: 1364
- Operações: 6
- Tempo: 0.000184 segundos
- Throughput: 7239.30 KB/s

### Verificação:
```bash
diff dados/origem.txt dados/destino.txt
```
Resultado: [x] Idênticos [ ] Diferentes

### Análise

**1. Por que devemos verificar que bytes_escritos == bytes_lidos?**

```
Para não tar erro na escrita, se o número de bytes escritos é diferente do que foi lido , não foi escrito tudo.
```

**2. Que flags são essenciais no open() do destino?**

```
O_CREAT para criar um arquivo inexistênte,O_TRUNC para truncado o arquivo todas as vezes que o programa entra em processo.
```

---

## Análise Geral

### Conceitos Fundamentais

**1. Como as syscalls demonstram a transição usuário → kernel?**

```
As chamadas de sistema são uma resposta direta do pedido do usuário , os pedidos são transformado para línguagem de máquina e executado.
```

**2. Qual é o seu entendimento sobre a importância dos file descriptors?**

```
fd é um ponteiro responsável por determinar local que um certo função irá atuar. 
```

**3. Discorra sobre a relação entre o tamanho do buffer e performance:**

```
Quando maior for o buffer menos chamdas de read são necessario , mais rápido fica o processo.
```

### Comparação de Performance

```bash
# Teste seu programa vs cp do sistema
time ./ex4_copia
time cp dados/origem.txt dados/destino_cp.txt
```

**Qual foi mais rápido?** cp do sistema,deu resultado diferentes e as vezes iguais,gpt falou que é mais rápido.

**Por que você acha que foi mais rápido?**

```
Tiveram resultados semelhantes , porque em certos partes tiveram uso de funções altamente otimizado como read e open, mas teve uso de printf que é pior que write em velocidade, então creio que cp seja mais rápido
```

---

## Entrega

Certifique-se de ter:
- [ ] Todos os códigos com TODOs completados
- [ ] Traces salvos em `traces/`
- [ ] Este relatório preenchido como `RELATORIO.md`

```bash
strace -e write -o traces/ex1a_trace.txt ./ex1a_printf
strace -e write -o traces/ex1b_trace.txt ./ex1b_write
strace -o traces/ex2_trace.txt ./ex2_leitura
strace -c -o traces/ex3_stats.txt ./ex3_contador
strace -o traces/ex4_trace.txt ./ex4_copia
```