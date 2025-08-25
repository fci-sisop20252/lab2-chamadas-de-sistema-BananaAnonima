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
- Linhas: __24___ (esperado: 25)
- Caracteres: __1299___
- Chamadas read(): __21___
- Tempo: ___0.000086__ segundos

### Experimentos com buffer:

| Buffer Size | Chamadas read() | Tempo (s) |
|-------------|-----------------|-----------|
| 16          |      82         |  0.000204 |
| 64          |       21        |  0.000086 |
| 256         |       6         |  0.000072 |
| 1024        |       2         |  0.000066 |

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
- Bytes copiados: __1364___
- Operações: __6___
- Tempo: __0.000203___ segundos
- Throughput: ___6561.73__ KB/s

### Verificação:
```bash
diff dados/origem.txt dados/destino.txt
```
Resultado: [ ] Idênticos [ ] Diferentes

### Análise

**1. Por que devemos verificar que bytes_escritos == bytes_lidos?**

```
Para garantir que todos os dados lidos do arquivo origem foram escritos no arquivo destino
```

**2. Que flags são essenciais no open() do destino?**

```
As flags O_WRONLY, O_CREAT, O_TRUNC são essenciais para abrir o arquivo apenas para escrita
```

---

## Análise Geral

### Conceitos Fundamentais

**1. Como as syscalls demonstram a transição usuário → kernel?**

```
Syscalls fazem a transição do modo usuário para o modo kernel, com isso, o programa solicita ao SO o acesso seguro aos recursos de hardware.

```

**2. Qual é o seu entendimento sobre a importância dos file descriptors?**

```
Eles são identificadores únicos usados pelo SO para controlar o acesso a arquivos
```

**3. Discorra sobre a relação entre o tamanho do buffer e performance:**

```
Buffers maiores reduzem a quantidade de chamadas de sistema, fazendo ter maior desempenho
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
cp, porque ele é otimizado internamente utilizando buffers maiores
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
