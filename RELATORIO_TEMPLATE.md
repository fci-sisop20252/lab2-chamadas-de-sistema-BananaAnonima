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
- ex1a_printf: ___1__ syscalls
- ex1b_write: ___3 (para cada msg)__ syscalls

**2. Por que há diferença entre printf() e write()?**

```
Printf usa e gera menos chamadas ao sistema, agrupando dados para melhorar o desempenho, enquanto o write envia os dados direto para o kernel a cada chamada.
```

**3. Qual implementação você acha que é mais eficiente? Por quê?**

```
Printf(), pois por ele agrupar dados gerando menos chamadas, ele consegue melhorar o desemprenho.
```

---

## Exercício 2 - Leitura de Arquivo

### Resultados da execução:
- File descriptor: ___3__
- Bytes lidos: __127___

### Comando strace:
```bash
strace -e open,read,close ./ex2_leitura
```

### Análise

**1. Por que o file descriptor não foi 0, 1 ou 2?**

```
Porque quando quando o SO procura o menor file descriptor disponível ele considera a partir do 3, pois o 0 1 e 2 são reservados. 0 = entrada; 1 = saida; 2 = saida de erro 
```

**2. Como você sabe que o arquivo foi lido completamente?**

```
Porque o read retornou menos bytes que o tamanho do buffer, isso significa que o arquivo terminou
```

---

## Exercício 3 - Contador com Loop

### Resultados (BUFFER_SIZE = 64):
- Linhas: __25___ (esperado: 25)
- Caracteres: __1683___
- Chamadas read(): __27___
- Tempo: ___0.000035__ segundos

### Experimentos com buffer:

| Buffer Size | Chamadas read() | Tempo (s) |
|-------------|-----------------|-----------|
| 16          |      100        |  0.00012  |
| 64          |       25        |  0.00006  |
| 256         |       7         |  0.00003  |
| 1024        |       2         |  0.00002  |

### Análise

**1. Como o tamanho do buffer afeta o número de syscalls?**

```
Quanto maior os buffers mais chamadas reads ele consegue ler por conta que cada syscall le mais dados por vez
```

**2. Como você detecta o fim do arquivo?**

```
pois quando o arquivo termina a chamada read retorna 0, indicando que o arquivo chegou ao fim q encontrou \n
```

---

## Exercício 4 - Cópia de Arquivo

### Resultados:
- Bytes copiados: _____
- Operações: _____
- Tempo: _____ segundos
- Throughput: _____ KB/s

### Verificação:
```bash
diff dados/origem.txt dados/destino.txt
```
Resultado: [ ] Idênticos [ ] Diferentes

### Análise

**1. Por que devemos verificar que bytes_escritos == bytes_lidos?**

```
[Sua análise aqui]
```

**2. Que flags são essenciais no open() do destino?**

```
[Sua análise aqui]
```

---

## Análise Geral

### Conceitos Fundamentais

**1. Como as syscalls demonstram a transição usuário → kernel?**

```
[Sua análise aqui]
```

**2. Qual é o seu entendimento sobre a importância dos file descriptors?**

```
[Sua análise aqui]
```

**3. Discorra sobre a relação entre o tamanho do buffer e performance:**

```
[Sua análise aqui]
```

### Comparação de Performance

```bash
# Teste seu programa vs cp do sistema
time ./ex4_copia
time cp dados/origem.txt dados/destino_cp.txt
```

**Qual foi mais rápido?** _____

**Por que você acha que foi mais rápido?**

```
[Sua análise aqui]
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
