---
title: "0x08_OSINT_e_Reconhecimento"
author: "Brenno Miranda"
edition: "dmp 0x3"
translationKey: "zine_osint_recon"
---

## --[ Sumário
- 0 -> [Informação é Poder]
- 1 -> [Fontes Abertas e Técnicas]
- 2 -> [Automatizando Recon com Python]
- 3 -> [Construindo o Perfil do Alvo]
- 4 -> [Encerramento e Créditos]

### 0 - Informação é Poder

Fala, pessoal!

Antes de disparar qualquer exploit, o primeiro passo de qualquer operação é reconhecimento. OSINT (Open Source Intelligence) é a arte de coletar informações de fontes publicamente disponíveis — e você ficaria surpreso com o quanto está exposto por aí.

De registros DNS a perfis de LinkedIn, de commits no GitHub a metadados em PDFs, cada fragmento de informação é uma peça do quebra-cabeça que revela a postura de segurança do alvo.

### 1 - Fontes Abertas e Técnicas

As principais fontes de OSINT incluem:

- **DNS/WHOIS** — Registros de domínio, subdomínios, nameservers.
- **Shodan/Censys** — Banners de serviços expostos na internet.
- **Google Dorks** — Queries avançadas para encontrar arquivos sensíveis.
- **GitHub** — Credenciais hardcoded, configs expostas, histórico de commits.
- **Wayback Machine** — Versões antigas de sites que podem conter dados removidos.

### 2 - Automatizando Recon com Python

```python
#!/usr/bin/env python3
import dns.resolver
import requests

def enum_subdomains(domain, wordlist_path):
    """Enumeração de subdomínios via bruteforce DNS"""
    found = []
    with open(wordlist_path) as f:
        for sub in f:
            sub = sub.strip()
            target = f"{sub}.{domain}"
            try:
                answers = dns.resolver.resolve(target, 'A')
                ips = [r.address for r in answers]
                print(f"[+] {target} -> {', '.join(ips)}")
                found.append((target, ips))
            except dns.resolver.NXDOMAIN:
                pass
            except Exception:
                pass
    return found

if __name__ == "__main__":
    results = enum_subdomains("target.com", "subdomains.txt")
    print(f"\n[*] Total encontrado: {len(results)} subdomínios")
```

_Figura 1. Enumerador de subdomínios via DNS bruteforce._
