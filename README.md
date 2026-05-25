# 📚 mkbook

> Pipeline completo pra transformar uma ideia em ebook publicado em **30 dias**, usando Claude Code como motor de escrita, edição, consistência e build.

Baseado no framework dos 7 prompts (Idea → Outline → Chapter → Edit → Launch → Publishing → Monetization), mas operando como **pipeline real de arquivos versionados** — não prompts soltos no chat.

🌐 **Curso completo (GitHub Pages):** [inematds.github.io/mkbook](https://inematds.github.io/mkbook/)
📖 **Repositório:** [github.com/inematds/mkbook](https://github.com/inematds/mkbook)

---

## ⚡ TL;DR — 5 comandos pra ter um livro

```bash
git clone https://github.com/inematds/mkbook.git && cd mkbook
./scripts/setup.sh                         # 🧬 wizard interativo (preenche bíblia em 20 min)
./scripts/write-chapter.sh 1               # Claude gera cap 1
./scripts/edit-chapter.sh 1                # Claude edita
./scripts/build-epub.sh                    # EPUB/PDF/MOBI prontos pro KDP
```

## 🧬 Duas formas de preencher a bíblia

| Modo | Como rodar | Quando usar |
|---|---|---|
| **Web (visual)** | Acesse [inematds.github.io/mkbook/setup/](https://inematds.github.io/mkbook/setup/) → preencha 12 perguntas → baixe `bible.md` → coloque na raiz do projeto | Quer ver progresso visual, prefere formulário com previews |
| **CLI (terminal)** | `./scripts/setup.sh` | Já tá no terminal, prefere fluxo rápido sem trocar de janela |
| **Manual** | `./scripts/new-book.sh "Título"` + edita `bible.md` direto | Já fez antes, sabe o que quer preencher |

Os 3 modos geram o mesmo `bible.md` — só muda a experiência.

Repete `write-chapter.sh N` + `edit-chapter.sh N` pra cada capítulo (1 por dia recomendado). Em 30 dias, ebook na Amazon.

---

## 🎯 Por que existe

Os 7 prompts virais funcionam em chat curto, mas falham em livro real:

| Problema do chat | Solução do mkbook |
|---|---|
| Contexto perdido no cap 3 | Cada capítulo é arquivo separado, alimentado por bíblia + outline + stories |
| Sem persistência | Git versiona tudo |
| Edição manual copy/paste | Skill `ebook-writer` automatiza |
| Inconsistência entre capítulos | Agente verificador cruza glossário, voz, promessas |
| Texto solto no chat | `build-epub.sh` gera EPUB + PDF + MOBI |

---

## 📂 Estrutura

```
mkbook/
├── templates/                  # Templates iniciais
│   ├── bible.md                # Voz, audiência, promessa, glossário
│   ├── outline.md              # Estrutura dos 10-12 capítulos
│   └── stories.md              # Banco de histórias pessoais
├── chapters/                   # Cada capítulo em arquivo próprio (gitignored)
│   ├── 01-draft.md
│   ├── 01-edited.md
│   └── ...
├── scripts/                    # Automação shell
│   ├── new-book.sh             # Bootstrap em 1 comando
│   ├── write-chapter.sh        # Gera cap N
│   ├── edit-chapter.sh         # Editor cruel
│   ├── consistency-check.sh    # Verificação cruzada
│   └── build-epub.sh           # EPUB + PDF + MOBI
├── build/                      # Saídas (gitignored)
│   ├── manuscript.md
│   ├── book.epub
│   ├── book.pdf
│   └── book.mobi
├── .claude/skills/ebook-writer/  # Skill custom que orquestra os 7 prompts
└── docs/                       # Curso GitHub Pages (formato INEMA.CLUB)
```

---

## 🛠️ Pré-requisitos

```bash
# obrigatório
brew install pandoc git                    # macOS
# ou: sudo apt install pandoc git           # Ubuntu/Debian

# Claude Code instalado e autenticado
npm install -g @anthropic-ai/claude-code
claude --version

# opcional — PDF de alta qualidade
brew install --cask basictex               # macOS
# ou: sudo apt install texlive-xetex        # Ubuntu

# opcional — geração de MOBI
brew install --cask calibre                # macOS
# ou: sudo apt install calibre              # Ubuntu
```

---

## 📖 Como criar um livro novo (passo a passo)

### Dia 1 — Setup (30 minutos)

```bash
cd ~/projetos/mkbook
./scripts/new-book.sh "Título Provisório"
```

O script:
- Copia `templates/bible.md`, `outline.md`, `stories.md` pra raiz
- Faz `git init` e primeiro commit
- Substitui o título placeholder pelo que você passou

---

### Dia 1-3 — Preencher a bíblia (3-4h totais, dividido em 3 dias)

A bíblia é a **lei** do livro. Tudo daqui pra frente lê dela.

Abra `bible.md` e preencha:

| Seção | O que colocar |
|---|---|
| **Identidade** | Título, subtítulo, gênero, categorias Amazon, keywords |
| **Promessa central** | 1 frase: "Ao terminar, [avatar] terá [transformação] mesmo [objeção]" |
| **Público-alvo (avatar)** | 1 pessoa real ou semi-fictícia: idade, profissão, dor, desejo, onde está hoje |
| **Voz e tom** | 3 adjetivos + autor de referência + **amostras suas** (cola 2-5 mil palavras suas: posts longos, e-mails, threads) |
| **Estrutura por capítulo** | Promessa específica de cada cap (você preenche depois do outline) |
| **Glossário controlado** | 5-10 termos com definição + variações proibidas |
| **Linhas vermelhas** | O que o livro NUNCA faz |
| **Modelo de negócio pós-livro** | Curso? Consultoria? Palestras? Comunidade? |

> **⚠️ Não pule "amostras de voz".** Sem 2k+ palavras suas coladas, o agente vai gerar prosa LLM-padrão. Isso é a diferença entre "parece que você escreveu" e "parece IA".

---

### Dia 4 — Gerar ideia + outline (~2h)

```bash
claude
> /ebook-writer Gere 5 ideias de livro baseadas no inventário em bible.md.
                Salve em ideas.md.
```

Escolha a vencedora (marque ✓ em `ideas.md`).

```bash
> /ebook-writer Monte o outline completo do livro escolhido em outline.md.
                12 capítulos, ~4000 palavras cada, com promessa por capítulo.
```

Revise `outline.md` à mão. Cap por cap. Ajuste promessas que não convencem.

---

### Dia 5 — Banco de histórias (~2h)

Abra `stories.md` e cole **todas** suas anedotas relevantes (cruas, sem polir). Tagueia:

```markdown
### A vez que perdi US$ 30k por não ter mailing list
**Tags:** #cap1 #lançamento #arrependimento
**Ano:** 2022
**O que aconteceu:** Lancei pra "todo mundo" via Instagram. 12 vendas...
**Lição:** Audiência > oferta
**Onde encaixa:** Cap 1, abertura
```

Isso é o que separa seu livro de LLM-genérico.

---

### Dia 6-17 — 1 capítulo por dia (~2h cada)

```bash
./scripts/write-chapter.sh 1
# Claude lê bible.md + outline.md + stories.md
# → chapters/01-draft.md

# revisa rapido o draft (lê todo, anota o que mudar)
$EDITOR chapters/01-draft.md

./scripts/edit-chapter.sh 1
# editor cruel: corta gordura, melhora aberturas, elimina repetição
# → chapters/01-edited.md

git add chapters/ && git commit -m "cap 1"
```

Pode automatizar com `/loop` do Claude Code:

```bash
claude
> /loop 1d Execute: ./scripts/write-chapter.sh $((dia_atual)) && ./scripts/edit-chapter.sh $((dia_atual))
```

---

### Dia 18-21 — Verificação de consistência

```bash
./scripts/consistency-check.sh
```

Relatório:
- Termos do glossário usados de forma inconsistente?
- Capítulos destoam da voz da bíblia?
- Histórias repetidas?
- Promessas do outline cumpridas?
- Cliffhangers funcionando entre capítulos?
- Linhas vermelhas respeitadas?

Corrija o que aparecer.

---

### Dia 22-25 — Build

```bash
./scripts/build-epub.sh
```

Gera em `build/`:
- `manuscript.md` (Markdown consolidado)
- `book.epub` (pra KDP, Kobo, Apple Books, Google Play)
- `book.pdf` (alta qualidade via xelatex, se instalado)
- `book.mobi` (Kindle antigo, se Calibre instalado)

---

### Dia 26-30 — Publicação no Amazon KDP

Siga a **Trilha 3** do curso (`docs/curso/trilha3/`):

1. Conta KDP + W8-BEN (pra não perder 30% de imposto)
2. Capa via Midjourney/DALL-E + composição no Canva
3. Subir EPUB
4. 3 categorias estratégicas (2 nichos pequenos + 1 amplo)
5. 7 keywords long-tail
6. Descrição com HTML básico (bullets, bold)
7. Preço estratégico (US$ 2.99 / 5.99 / 9.99 são sweet spots)
8. KDP Select sim/não

Vai pro ar em ~12h após submit.

---

## 🎬 Plano de lançamento (paralelo à escrita)

Veja **Trilha 4** do curso pra plano de 30 dias com pré-launch, dia do bestseller e 7 streams de monetização atrás do livro.

---

## 🔧 Comandos de referência rápida

```bash
# Bootstrap
./scripts/new-book.sh "Título"

# Pipeline diária de capítulos
./scripts/write-chapter.sh N            # gera cap N
./scripts/edit-chapter.sh N             # edita cap N

# Verificação cruzada (rodar quando todos os caps estiverem prontos)
./scripts/consistency-check.sh

# Build final
./scripts/build-epub.sh                 # → build/book.{epub,pdf,mobi}

# Ver o curso local
python3 -m http.server -d docs 8000     # abre em localhost:8000
```

---

## 🆘 Troubleshooting

| Problema | Solução |
|---|---|
| `command not found: pandoc` | `brew install pandoc` ou `apt install pandoc` |
| `xelatex ausente — PDF pulado` | `brew install --cask basictex` (macOS) |
| Voz do livro tá genérica | Falta amostras suas em `bible.md`. Adicione mais 2k palavras |
| Capítulo curto demais | Outline pediu 4k palavras mas saiu 2k — reforce a quantidade no prompt |
| Sobrescrever capítulo por engano | `git log chapters/03-edited.md` + `git checkout <hash> -- chapters/03-edited.md` |
| Quero recomeçar do zero | `rm bible.md outline.md ideas.md && ./scripts/new-book.sh "Novo"` |
| EPUB não abre no Kindle | Use o Send to Kindle do Amazon ou converta pra MOBI com Calibre |

---

## 🌐 Curso (GitHub Pages)

O treinamento completo em 4 trilhas (16 módulos · 96 tópicos) está em `docs/`:

| Trilha | Foco | Cor |
|---|---|---|
| **T1 Fundamentos** | Ideia validada + bíblia + outline | 🟢 Emerald |
| **T2 Escrita** | Pipeline de capítulos + edição + consistência | 🔵 Blue |
| **T3 Publicação** | EPUB, KDP, capa, formatação | 🟣 Purple |
| **T4 Lançamento** | 30 dias, bestseller, 7 streams de receita | 🟡 Amber |

Deploy automático via `.github/workflows/pages.yml`. Após push, vai pro ar em ~2 min.

URL: **[inematds.github.io/mkbook](https://inematds.github.io/mkbook/)**

---

## 📜 Licença

MIT — use, modifique, publique seu livro e fique com 100% da grana.

---

## 🙏 Créditos

Framework dos 7 prompts originais: domínio público (viralizou em 2024). Implementação como pipeline em arquivos versionados: [INEMA.CLUB](https://inema.club).
