# Séries BACEN — Mapa de Referência

> Códigos das séries do Sistema Gerenciador de Séries Temporais (SGS) do Banco Central, para uso nas calculadoras de cada modalidade de crédito.

---

## 📊 Endpoint padrão da API

```
https://api.bcb.gov.br/dados/serie/bcdata.sgs.{CODIGO}/dados?formato=json&dataInicial={DD/MM/AAAA}&dataFinal={DD/MM/AAAA}
```

**Resposta:** `[{"data": "01/12/2025", "valor": "1.97"}]`

A API tem CORS aberto e pode ser chamada direto do navegador.

---

## 🚗 Veículos · Pessoa Física

| Código | Descrição | Frequência |
|---|---|---|
| **25471** | Taxa média de juros mensal | mensal |
| **20749** | Taxa média de juros anual | anual |

Use o **25471** para cálculos comparativos (a calculadora atual usa este).

---

## 💵 Crédito pessoal não-consignado · Pessoa Física

| Código | Descrição | Frequência |
|---|---|---|
| **25472** | Taxa média de juros mensal | mensal |
| **20750** | Taxa média de juros anual | anual |

---

## 💳 Cartão de crédito · Pessoa Física

| Código | Descrição | Frequência |
|---|---|---|
| **25481** | Cartão rotativo · taxa mensal | mensal |
| **25478** | Cartão parcelado da fatura · taxa mensal | mensal |

⚠️ **Atenção regulatória — Lei 14.690/2023:** desde 03/01/2024, juros e encargos cobrados sobre o saldo do cartão de crédito (rotativo ou parcelado) **não podem exceder 100% do principal**. Nulidade automática se ultrapassar — não precisa do parâmetro do STJ.

⚠️ **Resolução 4.549/2017:** rotativo do cartão só pode permanecer por **um único ciclo**. Após isso, o banco é obrigado a oferecer parcelamento. Manter no rotativo por 2+ ciclos é vício automático.

---

## 🏦 Cheque especial · Pessoa Física

⚠️ **Resolução CMN 4.765/2019** (vigência 06/01/2020): teto absoluto de **8% ao mês** (ou 151,82% a.a.). Acima disso, **nulidade automática** independente da média BACEN.

| Código | Descrição |
|---|---|
| **20739** | Taxa média de juros · cheque especial PF |

A regra é: primeiro testar contra o teto regulatório (8%); se não violar, então comparar com a média BACEN pelo critério do STJ.

---

## 👴 Crédito consignado

### INSS (aposentados e pensionistas)

| Código | Descrição |
|---|---|
| **25469** | Consignado INSS · taxa mensal |

⚠️ **Tetos do CNPS** (Conselho Nacional de Previdência Social) — variam conforme resolução vigente. Verificar:
- **Empréstimo consignado:** teto histórico ~1,80% a 1,85% a.m. (consultar resolução vigente)
- **Cartão consignado RMC:** teto ~2,46% a.m.
- **Cartão benefício RCC:** teto similar ao RMC

### Servidores públicos

| Código | Descrição |
|---|---|
| **25470** | Consignado público · taxa mensal |

---

## 🔍 Como descobrir códigos novos

1. Acesse [dadosabertos.bcb.gov.br](https://dadosabertos.bcb.gov.br/)
2. Pesquise pela modalidade (ex: "consignado", "veículos")
3. Clique no conjunto de dados desejado
4. O número antes do nome é o código da série

Alternativamente, use o sistema gráfico:
[www3.bcb.gov.br/sgspub](https://www3.bcb.gov.br/sgspub/)

---

## ✅ Checklist antes de adicionar uma calculadora nova

- [ ] Confirmei o código exato da série no SGS
- [ ] Verifiquei se a taxa é mensal (% a.m.) ou anual (% a.a.) — preferir mensal para cálculo direto
- [ ] Identifiquei se há **teto regulatório** específico da modalidade (cheque especial, consignado, cartão)
- [ ] Identifiquei se a modalidade é **Price** (parcelas fixas) ou **rotativa** (saldo evolutivo) — matemática é diferente
- [ ] Documentei jurisprudência específica que se aplica (Lei 14.690/23, Res. 4.549, 4.765, etc.)
- [ ] Atualizei o `CLAUDE.md` e o `ROADMAP.md`

---

## 📚 Jurisprudência por modalidade (referência rápida)

| Tema | Ementa / Lei | Aplicável a |
|---|---|---|
| REsp 1.061.530/RS | Tema 27 — abusividade pelo critério de 1,5× | Todas as modalidades |
| REsp 2.009.614/SC | Necessidade de prova concreta de risco | Modulação do Tema 27 |
| Tema 958 | TAC e TEC — possibilidade de cobrança | Empréstimo / financiamento |
| Tema 972 | Seguro embutido — venda casada | Financiamento |
| Súmulas 539 e 541 | Capitalização mensal — possibilidade | Contratos pós-31/03/2000 |
| Resolução 4.549/2017 | Rotativo do cartão — limite 1 ciclo | Cartão de crédito |
| Resolução CMN 4.765/2019 | Teto cheque especial 8% a.m. | Cheque especial |
| Lei 14.690/2023 | Limite de 100% do principal | Cartão de crédito (Desenrola) |
| EAREsp 676.608/RS | Modulação repetição em dobro 30/03/2021 | Todas (devolução do indébito) |
| Súmula 380 STJ | Depósito do incontroverso afasta mora | Tutela de urgência em revisional |
