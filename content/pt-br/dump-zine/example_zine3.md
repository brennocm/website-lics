---
title: "0x02_Engenharia_Reversa_de_Malware"
author: "Brenno Miranda"
edition: "dmp 0x1"
translationKey: "zine_rev_malware"
---

## --[ Sumário
- 0 -> [O Fascínio pelo Desconhecido]
- 1 -> [Ferramentas do Ofício]
- 2 -> [Análise Estática vs Dinâmica]
- 3 -> [Dissecando um Dropper]
- 4 -> [Encerramento e Créditos]

### 0 - O Fascínio pelo Desconhecido

Fala, galera!

Engenharia reversa é provavelmente a disciplina mais cinematográfica do hacking. Você pega um binário desconhecido, joga no disassembler e começa a descascar camadas de ofuscação até encontrar a lógica escondida. É como arqueologia digital.

Neste artigo vamos explorar o workflow básico de análise de malware: desde o momento em que você recebe uma amostra suspeita até a extração dos indicadores de comprometimento (IOCs) que permitem criar regras de detecção.

### 1 - Ferramentas do Ofício

O toolkit essencial de todo analista de malware inclui:

- **Ghidra** — Decompilador open-source da NSA. Suporta múltiplas arquiteturas.
- **x64dbg** — Debugger para Windows, substituto moderno do OllyDbg.
- **YARA** — Linguagem de regras para classificação de malware.
- **Cuckoo Sandbox** — Análise dinâmica automatizada.

### 2 - Análise Estática vs Dinâmica

Na análise estática, examinamos o binário sem executá-lo. Procuramos por strings, imports, e padrões conhecidos:

```bash
# Extraindo strings do binário
strings -n 8 sample.exe | grep -i "http\|cmd\|powershell"

# Verificando imports suspeitos
objdump -x sample.exe | grep -i "virtualalloc\|createremotethread"

# Calculando hashes para lookup em bases de threat intel
sha256sum sample.exe
```

Na análise dinâmica, executamos a amostra em ambiente controlado e monitoramos seu comportamento: conexões de rede, alterações no registro, criação de processos filhos e persistência.

_Figura 1. Comandos iniciais de triagem de malware._
