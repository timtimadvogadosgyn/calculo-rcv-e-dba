# CLAUDE.md — Contexto do Projeto

> Este arquivo é lido pelo Claude Code automaticamente. Ele contém o contexto técnico, decisões de arquitetura, convenções e roadmap. Mantenha-o atualizado conforme o projeto evolui.

---

## 🎯 Sobre o projeto

**Nome:** Calculadora Revisional Bancária
**Domínio:** Direito Bancário · Ações Revisionais · Análise de abusividade de juros
**Audiência:** Advogados e clientes finais (pessoas leigas)
**Tipo:** Site estático multi-página, hospedado em GitHub Pages
**Stack:** HTML5 + CSS3 + JavaScript Vanilla (sem frameworks, sem build step)

### Por que stack vanilla?

Decisão deliberada. O projeto é mantido por um advogado não-desenvolvedor que precisa:
- Editar arquivos diretamente pelo GitHub web
- Subir alterações sem instalar Node, build tools ou rodar comandos
- Que qualquer pessoa do escritório consiga fazer ajustes simples

**NÃO migre para React, Vue, Next.js ou qualquer framework sem antes confirmar com o usuário.** Se a complexidade futura justificar, sugira mas não execute autonomamente.

---

## 🧱 Arquitetura

### Estrutura de arquivos

```
/
├── index.html              # Landing — apresenta a ferramenta, links para calculadoras
├── calculadora/
│   └── index.html          # Calculadora de financiamento de veículos (módulo atual)
├── assets/
│   ├── css/
│   │   └── tokens.css      # (a criar) variáveis de design compartilhadas
│   └── js/
│       └── shared.js       # (a criar) funções comuns (Newton-Raphson, fmt BRL, fetch BACEN)
├── docs/
│   └── exemplo-relatorio.html  # Modelo de relatório (referência de design)
├── README.md               # Para o usuário humano
├── CLAUDE.md               # Este arquivo
├── ROADMAP.md              # Próximas features
└── .github/workflows/      # CI/CD opcional
```

### Decisão pendente: extração para arquivos compartilhados

Hoje, a calculadora tem **CSS e JS embutidos** no próprio HTML (single-file). Isso facilita o entendimento isolado mas duplica código quando houver mais módulos.

**Quando expandir para outras modalidades:**
1. Extraia CSS comum para `assets/css/tokens.css` (variáveis, tipografia, cores)
2. Extraia funções comuns para `assets/js/shared.js`:
   - `parseNum()`, `fmtBRL()`, `fmtPct()`
   - `findRate()` (Newton-Raphson)
   - `pricePMT()`, `tabelaPrice()`
   - `buscarTaxaBacen(serie, data)` — generalize para aceitar código da série
   - `diagnosticar(multiplo)` — devolve `{classe, titulo, eyebrow, detalhe}`
3. Mantenha cada calculadora em sua pasta com o HTML específico mais leve

Mas: **só faça essa refatoração se houver pelo menos 2 calculadoras no projeto.** Antes disso, single-file é mais didático.

---

## 🎨 Sistema de design

A identidade visual está estabelecida nos dois arquivos HTML existentes (`calculadora/index.html` e `docs/exemplo-relatorio.html`). Mantenha consistência total ao adicionar páginas novas.

### Tokens (variáveis CSS já em uso)

```css
--bg: #F7F4ED              /* fundo principal — creme */
--bg-soft: #EFEAE0         /* fundo secundário */
--bg-card: #FFFFFF         /* cards */
--ink: #16181D             /* texto principal — quase preto */
--ink-soft: #4F5562        /* texto secundário */
--ink-faint: #8A91A0       /* texto terciário */
--rule: #DBD3C2            /* linhas/bordas */
--rule-soft: #EDE7DA       /* bordas suaves */

--abuse: #B0312D           /* vermelho — abusividade caracterizada */
--abuse-deep: #7A1D1A      /* vermelho escuro — gradientes */
--abuse-bg: #FBEDEB        /* fundo vermelho claro */

--warn: #C77B2A            /* laranja — zona cinzenta (1×–1,5×) */
--warn-bg: #FBF1E0

--fair: #2F6B3F            /* verde — dentro do parâmetro */
--fair-bg: #E9F1EA

--neutral: #2D5074         /* azul — informacional */
--neutral-bg: #E7EEF6

--gold: #9C7B3F            /* dourado — labels e destaques */
```

### Tipografia (Google Fonts)

```html
<link href="https://fonts.googleapis.com/css2?family=Fraunces:opsz,wght@9..144,400;9..144,500;9..144,600;9..144,700&family=Inter:wght@400;500;600;700&family=JetBrains+Mono:wght@400;500;700&display=swap" rel="stylesheet">
```

- **Fraunces** (serif moderna): títulos, h1, h2, valores grandes — itálico para ênfase emocional
- **Inter**: corpo de texto, labels de UI, botões
- **JetBrains Mono**: números tabulares, datas, taxas, valores monetários (sempre com `font-feature-settings: "tnum"` ou `font-variant-numeric: tabular-nums`)

### Princípios visuais

- **Editorial profissional** — paleta sóbria, generoso whitespace, hierarquia clara
- **Cores semáforo apenas para diagnóstico** — vermelho/laranja/verde reservados para o resultado da análise
- **Tabular nums obrigatório** em números — alinhamento vertical perfeito é assinatura da identidade
- **Sem ícones genéricos do Material/Heroicons** — usar caracteres Unicode minimalistas (✓ ⚠ ↻ ↳) ou nada
- **Cards têm shadow-md sutil**, nunca borrado nem extremo

---

## 🧮 Lógica de negócio (núcleo)

### Newton-Raphson — encontrar a taxa efetiva

```javascript
function findRate(pv, pmt, n) {
  let i = 0.02; // chute inicial
  for (let iter = 0; iter < 200; iter++) {
    const pow = Math.pow(1 + i, n);
    const f = pv * i * pow / (pow - 1) - pmt;
    const h = 1e-7;
    const pow2 = Math.pow(1 + i + h, n);
    const f2 = pv * (i + h) * pow2 / (pow2 - 1) - pmt;
    const df = (f2 - f) / h;
    if (Math.abs(df) < 1e-12) break;
    const newI = i - f / df;
    if (Math.abs(newI - i) < 1e-12) return newI;
    if (newI <= 0) i = i / 2;
    else i = newI;
  }
  return i;
}
```

Esta função é estável e converge em poucas iterações. **Não substitua por bisseção ou outra heurística sem motivo claro.**

### Diagnóstico de abusividade — gatilhos

```
multiplo = taxa_contratada / taxa_bacen

multiplo > 1.5   → ABUSIVO (gatilho objetivo do STJ)
multiplo > 1.2   → ZONA CINZENTA (acima da média mas abaixo do gatilho)
multiplo ≤ 1.2   → REGULAR (em linha com o mercado)
```

Esses thresholds vêm do **REsp 1.061.530/RS (Tema 27 do STJ)**. Se mudar, atualize o disclaimer no rodapé e o `CLAUDE.md`.

### Códigos das séries BACEN (mapa para expansão)

| Modalidade | Código SGS | Frequência | Descrição |
|---|---|---|---|
| Aquisição de veículos PF | **25471** | mensal | Já implementada |
| Aquisição de veículos PF | 20749 | anual | Equivalente anual |
| Crédito pessoal não-consignado PF | 25472 | mensal | Próxima |
| Crédito pessoal não-consignado PF | 20750 | anual | |
| Cartão de crédito rotativo PF | 25481 | mensal | |
| Cartão de crédito parcelado PF | 25478 | mensal | |
| Cheque especial PF | 25478 | mensal | (verificar código exato) |
| Crédito consignado INSS | 25469 | mensal | |
| Crédito consignado público | 25470 | mensal | |

**Endpoint da API BACEN:**
```
https://api.bcb.gov.br/dados/serie/bcdata.sgs.{CODIGO}/dados?formato=json&dataInicial={DD/MM/AAAA}&dataFinal={DD/MM/AAAA}
```

Retorna JSON `[{ "data": "01/12/2025", "valor": "1.97" }]`.

A API tem **CORS aberto** — pode ser chamada direto do navegador. Mas tenha sempre fallback manual caso a API falhe.

---

## 🗣️ Convenções de código e idioma

- **Idioma da UI:** português brasileiro
- **Idioma do código:** inglês para nomes de funções/variáveis técnicas (`findRate`, `pricePMT`); português para nomes de domínio jurídico (`taxaBacen`, `multiplo`, `abusivo`)
- **Comentários no código:** português, voltados para o futuro mantenedor (advogado)
- **Mensagens ao usuário final:** sempre em português, tom profissional sem jargão jurídico desnecessário
- **Citações de jurisprudência:** sempre o número exato + tribunal (ex: `REsp 1.061.530/RS`, `Tema 27 do STJ`)
- **Formatação numérica:** padrão brasileiro — `R$ 1.234,56` e `1,97% a.m.`
- **Datas:** padrão brasileiro `DD/MM/AAAA`

---

## ⚖️ Restrições importantes (não quebre)

1. **Nunca invente jurisprudência.** Se precisar fundamentar algo novo, use apenas o que já está no projeto ou peça ao usuário para confirmar.
2. **Nunca remova o disclaimer** do rodapé sobre limitações da calculadora — é proteção contra responsabilização.
3. **Nunca colete dados pessoais** (CPF, nome, e-mail) sem antes adicionar política de privacidade e LGPD compliance — assunto sensível em escritório de advocacia.
4. **Nunca substitua a API do BACEN** por dados embutidos sem consultar o usuário — a fonte oficial é parte do valor da ferramenta.
5. **Mantenha 100% client-side.** Sem backend, sem banco de dados, sem login — esses passos exigem decisão de arquitetura separada.
6. **Não use bibliotecas externas via NPM.** Se precisar de algo, prefira CDN com SRI hash ou implemente vanilla.

---

## 📋 Como adicionar uma nova calculadora (modalidade)

Quando o usuário pedir uma nova modalidade (cartão, cheque especial, consignado), siga este checklist:

1. **Confirme o código da série BACEN** consultando a tabela acima ou o catálogo do BCB
2. **Crie a pasta** `calculadora-{modalidade}/` (ex: `calculadora-cartao/`)
3. **Copie o `calculadora/index.html`** como base
4. **Ajuste:**
   - Título da página e textos da UI
   - Código da série BACEN no fetch (`bcdata.sgs.XXXXX`)
   - Tooltips dos campos (cada modalidade tem peculiaridades — cheque especial é rotativo, cartão tem outras nuances)
   - Tetos regulatórios específicos se aplicável (cheque especial: Res. CMN 4.765/19; cartão: Lei 14.690/23)
5. **Atualize o `index.html`** (landing) adicionando o card do novo módulo
6. **Atualize o `ROADMAP.md`** marcando como entregue
7. **Atualize o `CLAUDE.md`** se houver convenção nova

### Modalidades especiais — cuidados

- **Rotativo (cartão, cheque especial):** Tabela Price não se aplica. Use cálculo de saldo evolutivo (juros sobre saldo). A função `findRate()` precisa ser adaptada — não copie cegamente.
- **Consignado:** há tetos regulatórios do CNPS (Conselho Nacional de Previdência Social) que devem ser verificados ANTES da comparação BACEN. Se a taxa já viola o teto, é nulidade automática (não precisa do parâmetro do STJ).

---

## 🚀 Deploy

Site estático servido pelo GitHub Pages a partir da branch `main`, raiz do repositório. Não há build step.

### Workflow recomendado para o usuário

1. Edita arquivo no GitHub web ou via Claude Code
2. Commit + push
3. GitHub Pages reconstrói em ~1 minuto

### Se algum dia precisar de build

(não precisa hoje, mas para futura referência)

```yaml
# .github/workflows/deploy.yml — exemplo se algum dia houver build
name: Deploy
on:
  push:
    branches: [main]
jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/configure-pages@v4
      - uses: actions/upload-pages-artifact@v3
        with:
          path: '.'
      - uses: actions/deploy-pages@v4
```

---

## 🧪 Como testar localmente

Como é HTML puro, basta abrir os arquivos diretamente no navegador (`file://`). MAS:

**⚠️ A API do BACEN pode dar erro de CORS quando o HTML é aberto via `file://`.** Para testar a busca automática, sirva via servidor local:

```bash
# Python (já vem em qualquer Linux/Mac)
python3 -m http.server 8000

# Ou Node, se instalado
npx serve .
```

E acesse `http://localhost:8000`.

Para testes rápidos no GitHub Pages, basta dar push — em 1 minuto está no ar.

---

## 📝 Histórico de decisões importantes

> Adicione aqui decisões arquiteturais relevantes conforme forem tomadas.

- **2026-04-29 · Stack vanilla** — Decidimos não usar framework para manter o projeto editável por não-desenvolvedor.
- **2026-04-29 · Single-file por enquanto** — CSS e JS embutidos em cada HTML. Refatorar quando houver 2+ calculadoras.
- **2026-04-29 · API BACEN em tempo real** — Não embutimos histórico de taxas. Sempre consulta a fonte oficial.

---

## 🔗 Referências externas relevantes

- [STJ · REsp 1.061.530/RS](https://processo.stj.jus.br/) — Tema 27, parâmetro objetivo de abusividade
- [STJ · REsp 2.009.614/SC](https://processo.stj.jus.br/) — exigência de prova concreta de risco e custo de captação
- [BACEN · SGS](https://www3.bcb.gov.br/sgspub/) — Sistema Gerenciador de Séries Temporais
- [BACEN · Catálogo de séries](https://dadosabertos.bcb.gov.br/) — busca de códigos
- [Súmula 380 STJ](https://scon.stj.jus.br/SCON/sumulas/) — depósito do incontroverso afasta mora
- [CDC art. 42, parágrafo único](http://www.planalto.gov.br/ccivil_03/leis/l8078.htm) — devolução em dobro
- [EAREsp 676.608/RS](https://processo.stj.jus.br/) — modulação da repetição em dobro (30/03/2021)
