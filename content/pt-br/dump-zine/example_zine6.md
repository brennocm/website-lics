---
title: "0x05_Linux_Kernel_Exploitation"
author: "Brenno Miranda"
edition: "dmp 0x2"
translationKey: "zine_kernel_exploit"
---

## --[ Sumário
- 0 -> [O Ring 0]
- 1 -> [Superfície de Ataque do Kernel]
- 2 -> [Use-After-Free no Kernel]
- 3 -> [PoC: Privilege Escalation via ioctl]
- 4 -> [Encerramento e Créditos]

### 0 - O Ring 0

Fala, hacker!

Quando você pensa em exploração, provavelmente imagina exploits para aplicações userland — browsers, servidores web, clientes de e-mail. Mas o verdadeiro poder está no kernel. Comprometer o kernel significa comprometer tudo: todos os processos, todos os usuários, toda a memória.

O kernel Linux tem milhões de linhas de código, centenas de syscalls e uma superfície de ataque gigantesca. E o melhor: é open-source, então podemos estudar cada linha.

### 1 - Superfície de Ataque do Kernel

Os vetores mais explorados incluem:

- **Syscalls** — Interface direta entre userland e kernel.
- **Drivers** — Código de terceiros rodando em ring 0 com qualidade variável.
- **ioctl handlers** — Frequentemente complexos e mal auditados.
- **Filesystems** — Parsing de estruturas controladas pelo atacante.

### 2 - Use-After-Free no Kernel

O conceito é o mesmo do userland, mas as consequências são ampliadas:

```c
// Exemplo simplificado de UAF no kernel
struct vulnerable_struct {
    void (*callback)(void);
    char data[64];
};

// Módulo vulnerável
static long vuln_ioctl(struct file *f, unsigned int cmd, unsigned long arg) {
    struct vulnerable_struct *obj;
    
    switch(cmd) {
        case ALLOC_OBJ:
            obj = kmalloc(sizeof(*obj), GFP_KERNEL);
            obj->callback = default_handler;
            break;
        case FREE_OBJ:
            kfree(obj);
            // BUG: obj não é setado para NULL
            break;
        case USE_OBJ:
            obj->callback();  // UAF: chama ponteiro já liberado
            break;
    }
    return 0;
}
```

O atacante pode: (1) alocar o objeto, (2) liberá-lo, (3) ocupar a memória liberada com dados controlados via heap spray, e (4) triggerar o uso do ponteiro dangling para desviar a execução.

_Figura 1. Exemplo simplificado de Use-After-Free em módulo kernel._
