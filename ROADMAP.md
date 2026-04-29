# ROADMAP

> Funcionalidades planejadas para o projeto, em ordem de prioridade. Marque como concluído conforme forem implementadas.

---

## 🟢 Concluído

- [x] Calculadora de financiamento de veículos (PF) — série BACEN 25471
- [x] Busca automática da taxa BACEN via API pública
- [x] Diagnóstico visual com cores semáforo (abusivo / fronteira / regular)
- [x] Tabela de amortização revisada
- [x] Cálculo de cobrança indevida + devolução em dobro
- [x] Botão "imprimir / salvar PDF" com layout otimizado
- [x] Botão "copiar resumo" (clipboard)

---

## 🟡 Próximas features (prioridade alta)

### 1. Landing page mais completa
- [ ] Texto explicativo "como funciona" para o cliente final
- [ ] Seção sobre o critério do STJ
- [ ] FAQ
- [ ] Logo/identidade do escritório (se aplicável)
- [ ] Footer com contato

### 2. Outras modalidades de crédito
- [ ] **Crédito pessoal não-consignado** (série BACEN 25472) — Tabela Price padrão
- [ ] **Cartão de crédito rotativo** (série 25481) — atenção: regime ROTATIVO, não Price
- [ ] **Cartão de crédito parcelado** (série 25478)
- [ ] **Cheque especial** (verificar série) — atenção: regime rotativo + teto Res. 4.765/19
- [ ] **Crédito consignado INSS** (série 25469) — atenção: tetos do CNPS

> **Importante:** rotativo (cartão e cheque especial) tem matemática diferente. NÃO usar Tabela Price. Ver instrução no `CLAUDE.md`.

### 3. Geração automática de fundamentação
- [ ] Botão "Gerar fundamentação para inicial" que produz texto pronto baseado no diagnóstico
- [ ] Inclui citação automática do REsp 1.061.530/RS, número exato, valores do caso
- [ ] Editável antes de copiar
- [ ] Templates separados para: petição inicial, manifestação, recurso

---

## 🔵 Médio prazo

### 4. Histórico de análises
- [ ] Salvar análises feitas no `localStorage` do navegador
- [ ] Lista das últimas 20 análises com nome do cliente (opcional)
- [ ] Exportar histórico como CSV

### 5. Modo "lote"
- [ ] Upload de planilha CSV com múltiplos contratos
- [ ] Análise em massa
- [ ] Relatório consolidado com casos viáveis vs inviáveis

### 6. Importação de CCB em PDF
- [ ] Upload de PDF da CCB
- [ ] Extração automática dos campos (data, valor financiado, parcela, n)
- [ ] Pré-preenchimento do formulário
- [ ] Tecnologias possíveis: PDF.js + heurísticas regex, ou serviço de OCR

### 7. Calculadora de devolução do indébito
- [ ] Tabela com todos os pagamentos feitos pelo cliente
- [ ] Atualização monetária parcela a parcela (INPC ou SELIC)
- [ ] Aplicação de juros de mora desde cada pagamento indevido
- [ ] Devolução simples vs dobro (com base em data de modulação do EAREsp 676.608)

---

## 🟣 Longo prazo (após validação do uso)

### 8. Login e área restrita
- [ ] Apenas após validar que há demanda real do escritório
- [ ] Implicará mudança de arquitetura (precisa backend) — discutir antes
- [ ] Possíveis stacks: Cloudflare Workers + KV, Supabase, Firebase

### 9. Painel administrativo
- [ ] Estatísticas de uso
- [ ] Gestão de clientes/casos
- [ ] Geração de relatório consolidado para gestão do escritório

### 10. API pública da calculadora
- [ ] Permitir integração com outros sistemas do escritório
- [ ] Endpoint REST para diagnóstico programático

### 11. Versão mobile dedicada / PWA
- [ ] Instalável como app no celular
- [ ] Funciona offline (cache da última taxa BACEN)
- [ ] Notificações quando taxa BACEN do mês é atualizada

---

## ⚫ Ideias soltas (sem prioridade definida)

- Conversor de CET ↔ taxa de juros pura (educativo)
- Simulador "qual deveria ser a taxa para ser justo"
- Comparador de bancos (ranking por taxa praticada)
- Glossário interativo de termos jurídico-financeiros
- Modo escuro (dark mode)
- Internacionalização (i18n) — começar por espanhol se houver demanda em outros países latino-americanos

---

## 🚫 O que NÃO está no roadmap (decisão consciente)

- **Migração para framework JS** (React, Vue, Next, etc.) — manter vanilla é estratégico
- **Build step / bundler** — adiciona complexidade sem ganho proporcional
- **Coleta de dados de usuários** — questão de LGPD; só com decisão deliberada
- **Pagamentos / SaaS** — a ferramenta é gratuita por enquanto
- **IA generativa para parecer** — pode comprometer a tese se errar; gerar TEMPLATES sim, gerar pareceres autônomos não
