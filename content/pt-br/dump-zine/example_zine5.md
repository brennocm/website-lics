---
title: "0x04_Criptografia_Aplicada"
author: "Brenno Miranda"
edition: "dmp 0x2"
translationKey: "zine_crypto_aplicada"
---

## --[ Sumário
- 0 -> [Por que Criptografia Importa]
- 1 -> [Simétrica vs Assimétrica]
- 2 -> [Implementação Segura com libsodium]
- 3 -> [Ataques a Implementações Fracas]
- 4 -> [Encerramento e Créditos]

### 0 - Por que Criptografia Importa

E aí!

Criptografia é a fundação de tudo que fazemos na segurança digital. De HTTPS a carteiras de Bitcoin, de mensageiros end-to-end a assinaturas de firmware — sem crypto, não existe confiança digital.

O problema é que a maioria dos desenvolvedores trata criptografia como uma caixa preta: importa uma lib, chama `encrypt()` e torce para que esteja seguro. Neste artigo, vamos abrir essa caixa e entender o que realmente acontece por baixo.

### 1 - Simétrica vs Assimétrica

Na criptografia simétrica, a mesma chave cifra e decifra. AES-256-GCM é o padrão ouro atual. Na assimétrica, temos um par de chaves (pública/privada) — RSA e Curve25519 são os mais usados.

### 2 - Implementação Segura com libsodium

```python
#!/usr/bin/env python3
from nacl.secret import SecretBox
from nacl.utils import random

# Gera uma chave de 256 bits
key = random(SecretBox.KEY_SIZE)
box = SecretBox(key)

# Cifra a mensagem (nonce gerado automaticamente)
message = b"dados_sensíveis_da_operacao"
encrypted = box.encrypt(message)

# Decifra
decrypted = box.decrypt(encrypted)
assert decrypted == message
print("[+] Criptografia simétrica OK")
print(f"[+] Tamanho cifrado: {len(encrypted)} bytes")
```

A vantagem do `libsodium` (PyNaCl em Python) é que ele elimina decisões perigosas: não precisa escolher modo de operação, padding, ou gerar nonce manualmente. A API é "misuse-resistant" por design.

_Figura 1. Criptografia simétrica com PyNaCl/libsodium._
