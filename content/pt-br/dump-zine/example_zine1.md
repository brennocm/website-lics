---
title: "0x00_Introduction_to_the_Void"
author: "Brenno Miranda"
edition: "dmp 0x1"
translationKey: "zine_introduction_to_the"
---

## --[ Sumário
- 0 -> [Título do Tópico Introdutório]
- 1 -> [Conceito e Motivação]
- 2 -> [Análise de Vetores/Possibilidades]
- 3 -> [PoC / Implementação Técnica]
- 4 -> [Encerramento e Créditos]

### 0 - [Título do Tópico Introdutório]

[Saudação inicial aqui]!

Este é um espaço reservado para a sua introdução. Para manter o fluxo do texto original, utilize este primeiro parágrafo para estabelecer uma conexão direta com o leitor. Você pode mencionar o que tem lido ultimamente, qual repositório do GitHub te tirou o sono ou simplesmente comentar sobre o estado atual da cena hacker sob a sua perspectiva.

O objetivo aqui é definir o "tom" da conversa. O hacking é movido por sentimentos de descoberta e pela quebra de paradigmas técnicos. Use este bloco para descrever como o tema deste artigo específico te transformou ou despertou sua curiosidade. É o momento de ser menos "manual técnico" e mais "manifesto", preparando o terreno para o que vem a seguir.

Neste terceiro parágrafo, você pode inserir uma breve anedota ou um relato de experiência. Lembre-se de como era documentar suas primeiras descobertas em canais de IRC ou fóruns antigos. A ideia é emular esse desejo de espalhar o conhecimento de forma bruta e honesta, exatamente como um dump de memória que revela o que estava escondido nos bastidores do sistema.

A proposta deste tópico na DUMP é servir de porta de entrada. É aqui que você valida a essência do compartilhamento de informações da LICS. Finalize este bloco reforçando a importância de manter a cultura viva, seja através do papel ou da tela preta, e convide o leitor a mergulhar nos detalhes técnicos que virão nos próximos tópicos da agenda.

```python
#!/usr/bin/env python3
import sys
import socket
def scan(target, port):
    try:
        s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
        s.settimeout(1)
        result = s.connect_ex((target, port))
        if result == 0:
            print(f"Port {port} is open")
        s.close()
    except Exception as e:
        print(f"Error: {e}")
if __name__ == "__main__":
    if len(sys.argv) != 3:
        print("Usage: python scanner.py <ip> <port>")
        sys.exit(1)
    target_ip = sys.argv[1]
    target_port = int(sys.argv[2])
    scan(target_ip, target_port)
```

_Figura 1. Exemplo de um scanner básico._
