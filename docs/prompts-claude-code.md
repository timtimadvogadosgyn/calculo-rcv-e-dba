# Prompts para Claude Code

> Cole estes prompts no Claude Code quando quiser que ele faça uma tarefa específica. Edite o que estiver entre `[colchetes]` antes de enviar.

---

## 🎨 Mudanças visuais

### Mudar cor principal do site

```
Quero mudar a cor de destaque do site (atualmente vermelho-bordô #B0312D).
A nova cor deve ser [DESCREVA: ex. azul-marinho similar a um terno].
Atualize a variável --abuse no CSS de todos os arquivos HTML do projeto
(index.html, calculadora/index.html, docs/exemplo-relatorio.html).
Mantenha a coerência visual com as outras cores da paleta.
```

### Adicionar logo do escritório

```
Vou enviar a logo do escritório [Portela e Oliveira / OUTRO NOME].
Substitua o quadradinho preto com letra "R" no canto superior esquerdo
(elemento .brand .mark) por essa logo. Use uma versão SVG inline se possível
para ficar nítida em qualquer tamanho. A logo deve ter no máximo 32px de altura.
```

### Mudar tipografia

```
Quero trocar a fonte serif (Fraunces) por [NOME DA FONTE — ex: Playfair Display, EB Garamond].
Atualize o link do Google Fonts em todos os HTMLs e troque as referências
font-family em todos os arquivos. Mantenha a Inter para o corpo e a JetBrains Mono para números.
```

---

## 🧱 Adicionar nova calculadora

### Crédito pessoal

```
Vou adicionar uma nova calculadora para CRÉDITO PESSOAL não-consignado (PF).
Use a mesma estrutura da calculadora de veículos como base.

Mudanças necessárias:
- Criar pasta calculadora-credito-pessoal/index.html
- Trocar o código da série BACEN de 25471 para 25472 (crédito pessoal PF)
- Ajustar todos os textos da UI mencionando "veículo" para "crédito pessoal"
- Atualizar o tooltip do campo "Valor financiado": no crédito pessoal,
  geralmente é o valor recebido em conta (sem IOF, sem tarifas)
- Adicionar um card novo na landing (index.html) com status "disponível"
- Atualizar o ROADMAP.md marcando como concluído

Mantenha 100% da estrutura de design e padrão de código existente.
```

### Cartão de crédito rotativo

```
Quero adicionar uma calculadora para CARTÃO DE CRÉDITO ROTATIVO.

ATENÇÃO — esta modalidade tem regras DIFERENTES da Tabela Price:
1. Não usa Price. O cálculo é: saldo_final = saldo_inicial × (1 + taxa) - pagamento
2. Há TETO ABSOLUTO da Lei 14.690/2023: juros + encargos não podem exceder 100% do principal
3. Resolução 4.549/2017: rotativo só pode durar 1 ciclo, depois deve ser parcelado
4. Série BACEN: 25481

A calculadora deve:
- Receber: saldo de fatura, valor pago, próximo saldo de fatura, taxa BACEN
- Calcular taxa real do rotativo entre dois meses consecutivos
- Verificar PRIMEIRO o teto da Lei 14.690 (se estourou, é nulidade automática)
- Depois comparar com BACEN (1,5×)
- Verificar se está em rotativo há mais de 1 ciclo (vício adicional)

Antes de implementar, sugira o layout da UI para eu validar.
```

---

## 📝 Conteúdo e textos

### Adicionar página "Sobre"

```
Crie uma página /sobre/index.html com:
- Quem somos (preencho depois)
- Como a ferramenta foi construída
- Por que é gratuita
- Limitações e disclaimer
- Link de contato

Use o mesmo design system do site. Adicione link no menu de navegação
do index.html.
```

### Adicionar FAQ

```
Adicione uma seção FAQ no index.html, antes do CTA final.
Use accordion (perguntas que abrem ao clicar). Inclua estas perguntas:
- A calculadora é confiável para uso profissional?
- De onde vêm os dados das taxas BACEN?
- Por que o resultado pode ser diferente do parecer técnico?
- Como interpretar a "zona cinzenta"?
- Posso usar o resultado direto na petição?
- Por que considerar o "Valor Líquido Financiado" e não o "Total"?

Para cada pergunta, escreva uma resposta de 2-3 frases.
Use o mesmo design system existente.
```

### Adicionar formulário de contato

```
Adicione uma seção de contato no final da landing, antes do footer.
NÃO crie backend — use o serviço Formspree (https://formspree.io)
ou similar que aceite POST direto do HTML estático.

Campos: nome, e-mail, telefone (opcional), mensagem.
Validação client-side básica.
Após envio, mostrar mensagem de sucesso na própria página.

Lembre de adicionar checkbox de aceite para LGPD com link para
política de privacidade (que vai precisar criar também).
```

---

## 🔧 Funcionalidades novas

### Histórico de análises

```
Implemente histórico das últimas 20 análises feitas na calculadora,
salvo no localStorage do navegador (sem backend).

Após cada cálculo, salvar:
- Data da análise
- Data do contrato
- Valores principais (PV, parcela, n)
- Diagnóstico (abusivo / cinzento / regular)

Adicionar um link "📋 Histórico" no canto superior direito da calculadora,
abrindo modal com a lista. Cada item tem botão "carregar" que repreenche
o formulário com os dados.

Botão "limpar histórico" também.
```

### Geração de fundamentação para inicial

```
Adicione um botão "📄 Gerar fundamentação" no resultado da calculadora,
quando o diagnóstico for "abusivo".

Ao clicar, abrir modal com texto pronto para colar em petição inicial,
contendo:
- Tópico de fundamentação numerado
- Citação completa do REsp 1.061.530/RS (Tema 27)
- Cálculo da abusividade com os números do caso
- Pretensão de revisão dos juros
- Pretensão de repetição do indébito (em dobro, CDC art. 42)
- Citação do EAREsp 676.608/RS para a modulação

O texto deve ser editável no modal antes de copiar.
Botão "📋 Copiar texto" no final.
```

---

## 🚀 Deploy e infraestrutura

### Conectar domínio próprio

```
Comprei o domínio [seudominio.com.br]. Quero apontar para o site.

Crie o arquivo CNAME na raiz com o domínio.
Me dê o passo a passo de:
1. Como configurar o DNS no registro.br (registros A e CNAME)
2. Como ativar o domínio em Settings > Pages do GitHub
3. Como ativar HTTPS

Depois faça commit e push.
```

### Otimizar para velocidade

```
Faça uma análise de performance do site e otimize:
- Adicione preload das fontes principais
- Inline o CSS crítico
- Adicione lazy loading nas imagens (se houver)
- Adicione meta tags Open Graph e Twitter Cards
- Sugira melhorias para a meta description e SEO básico

Depois mostre quais mudanças fez e me explique o impacto.
```

---

## 🐛 Manutenção e correções

### Quando algo quebra

```
A calculadora não está mais funcionando: [DESCREVA O QUE NÃO FUNCIONA].

Ao tentar [DESCREVA A AÇÃO], o resultado é [DESCREVA O ERRO].
Espero que aconteça [DESCREVA O COMPORTAMENTO ESPERADO].

Investigue, identifique a causa e proponha correção antes de aplicar.
```

### Atualização de jurisprudência

```
A jurisprudência do STJ atualizou [DESCREVA: ex. um novo Tema mudou
o parâmetro de 1,5× para outro valor].

Localize todas as referências ao parâmetro/jurisprudência antiga no projeto
(busca em todos os arquivos) e atualize. Inclua:
- Calculadora (lógica + textos)
- Landing
- Footer/disclaimer
- CLAUDE.md
- README.md
- docs/series-bacen.md

Liste o que mudou antes de aplicar para eu validar.
```

---

## 💡 Dicas gerais para usar o Claude Code

1. **Seja específico nas instruções** — "mudar a cor" é vago; "trocar --abuse de #B0312D para #003366 e ajustar as outras cores se conflitarem" é claro.

2. **Peça para mostrar antes de aplicar** mudanças grandes — adicione no fim do prompt: "antes de fazer as alterações, me mostre o plano".

3. **Use linguagem natural** — não precisa falar "tecnicamente". "Quero que isso fique mais bonito" funciona, ele pergunta o que mais especificamente.

4. **Lembre-se de testar localmente** antes de subir — peça: "abra o site num servidor local e me dê o link".

5. **Quando der bom**: peça para ele fazer commit + push com mensagem descritiva. Ex: "agora suba para o GitHub com mensagem 'adiciona FAQ na landing'".

6. **Sempre confira o resultado no site público** depois de 2 minutos — é o tempo que o GitHub Pages leva para reconstruir.
