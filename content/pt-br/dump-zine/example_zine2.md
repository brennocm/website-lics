---
title: "0x01_Buffer_Overflow_101"
author: "Brenno Miranda"
edition: "dmp 0x1"
translationKey: "zine_buffer_overflow"
---

## --[ Sumário
- 0 -> [Introdução a Stack Overflows]
- 1 -> [Anatomia da Memória]
- 2 -> [Controlando o EIP]
- 3 -> [PoC: Exploração de um Binary Vulnerável]
- 4 -> [Encerramento e Créditos]

### 0 - Introdução a Stack Overflows

E aí, pessoal!

Se tem uma coisa que define a essência do hacking de baixo nível, é entender como a memória funciona — e como quebrá-la. Buffer overflows são uma das vulnerabilidades mais antigas e mais estudadas da computação, mas ainda continuam relevantes. Desde o famoso Morris Worm de 1988 até os CVEs modernos, o princípio permanece o mesmo: escrever além dos limites de um buffer e controlar o fluxo de execução.

A ideia deste artigo é desmistificar o processo. Vamos partir do zero: o que é a stack, como variáveis locais são alocadas, onde fica o endereço de retorno, e como um atacante pode sobrescrever esse endereço para redirecionar a execução para um shellcode ou gadget ROP.

### 1 - Anatomia da Memória

Toda vez que uma função é chamada, o sistema operacional reserva um frame na stack. Esse frame contém as variáveis locais, os parâmetros da função e, crucialmente, o endereço de retorno (EIP/RIP). A disposição na memória segue uma ordem previsível:

```
+---------------------+
|   Variáveis locais  |  <- Buffer vulnerável
+---------------------+
|   EBP salvo         |  <- Base pointer
+---------------------+
|   EIP (retorno)     |  <- ALVO DO ATAQUE
+---------------------+
|   Argumentos        |
+---------------------+
```

Quando um programa usa `strcpy()` ou `gets()` sem validar o tamanho do input, o atacante pode injetar dados que transbordam o buffer e sobrescrevem o EIP.

### 2 - Controlando o EIP

O primeiro passo é descobrir o offset exato entre o início do buffer e o endereço de retorno. Para isso, usamos um "cyclic pattern" — uma sequência de caracteres únicos gerada por ferramentas como `pattern_create` do Metasploit:

```python
#!/usr/bin/env python3
import struct

offset = 76  # Descoberto via cyclic pattern
eip = struct.pack("<I", 0xdeadbeef)  # Endereço alvo
nop_sled = b"\x90" * 100
shellcode = b"\xcc" * 50  # INT3 breakpoints para teste

payload = b"A" * offset + eip + nop_sled + shellcode

with open("payload.bin", "wb") as f:
    f.write(payload)
print(f"[+] Payload gerado: {len(payload)} bytes")
```

_Figura 1. Gerador de payload para stack overflow._
