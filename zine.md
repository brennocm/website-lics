# DUMP Zine — Documentação Técnica

A seção "DUMP Zine" é uma área de conteúdo isolada da identidade padrão da LICS, com estética de terminal hacker.

---

## 1. Arquitetura de Layouts

| Template | Função |
|---|---|
| `baseof.html` | Base exclusiva. Sem Bootstrap, carrega Tailwind CSS via CDN. Fontes: `Courier New`, `VT323`, `Fira Code`. Cores: `#a3e635` (lime), `#000` (fundo). Efeito `glitch-noise` SVG. |
| `list.html` | Index da zine. ASCII art do logo, descrição institucional, listagem automática de artigos (`{{ range .Pages }}`). Link `[ home ]` fixo no canto superior esquerdo. Usa `.RelPermalink` para links relativos (funciona em localhost). |
| `single.html` | Leitor de artigos. Efeito CRT scanlines. Cabeçalho: Título (branco), Autor (`#888`), Edição (`#555`). Nav fixa no canto superior direito: `[ capa ]` (sempre), `[ Próximo > ]` / `[ < Anterior ]` (condicionais via `.PrevInSection`/`.NextInSection`). Rodapé: "2026 DUMP." |

### Integração com o Site Principal
- Menu Principal da LICS (`config/_default/menus.*.yaml`): O link para a zine tem `weight: 4` e aparece antes de "Contato".
  - **pt-br:** "Artigos"
  - **en-us:** "Articles"
  - Ordem visual: `Membros | Sobre | Eventos | Artigos | Contato`
- Index do Site (`themes/org/layouts/home.html`): O botão de acesso rápido à zine exibe o texto **"Artigos"**.

---

## 2. Criação de Artigos

### Archetype (`archetypes/dump-zine.md`)
```yaml
---
title: "Título"
date: ...
draft: true       # Artigo nasce como rascunho
author: ""
edition: ""
translationKey: "" # Para vincular com versão em outro idioma
---

## --[ Sumário
- 0 -> [Tópico 1]
...
### 0 - [Tópico 1]
Conteúdo aqui...
```

### Workflow
```bash
hugo new dump-zine/nome-do-artigo.md    # Cria arquivo com archetype
# Editar front matter (title, author, edition)
# Escrever conteúdo seguindo estrutura do Sumário
# Quando pronto: remover "draft: true"
# Build: hugo --gc --minify
```

O artigo aparece **automaticamente** na listagem do index — não é necessário editar nenhum template.

### Diretórios
- `content/pt-br/dump-zine/` — Artigos em português
- `content/en-us/dump-zine/` — Artigos em inglês
- Vinculação entre idiomas via `translationKey`

---

## 3. Artigos de Teste Atuais (pt-br)

| Arquivo | Título | Edição |
|---|---|---|
| example_zine1 | 0x00_Introduction_to_the_Void | dmp 0x1 |
| example_zine2 | 0x01_Buffer_Overflow_101 | dmp 0x1 |
| example_zine3 | 0x02_Engenharia_Reversa_de_Malware | dmp 0x1 |
| example_zine4 | 0x03_Kubernetes_Attack_Surface | dmp 0x1 |
| example_zine5 | 0x04_Criptografia_Aplicada | dmp 0x2 |
| example_zine6 | 0x05_Linux_Kernel_Exploitation | dmp 0x2 |
| example_zine7 | 0x06_Wi-Fi_Hacking_com_Aircrack | dmp 0x2 |
| example_zine8 | 0x07_SQL_Injection_Avançado | dmp 0x3 |
| example_zine9 | 0x08_OSINT_e_Reconhecimento | dmp 0x3 |
| example_zine10 | 0x09_Redes_Neurais_e_Segurança | dmp 0x3 |

---

## 4. i18n

| Chave | pt-br | en-us |
|---|---|---|
| `dump-zine-a-lics-zine` | `[ Nome / Apelido / Área da Liga ]` | `[ Name / Nickname / League Area ]` |
| `dump-zine-nav-home` | `home` | `home` |
| `dump-zine-footer-copyright` | `2026 DUMP.` | `2026 DUMP.` |


<br>

Navegação prev/next é hardcoded em português no template (`[ Próximo > ]`, `[ < Anterior ]`, `[ capa ]`).

