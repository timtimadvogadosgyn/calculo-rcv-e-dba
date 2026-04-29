# Calculadora Revisional Bancária

> Ferramenta web para análise de abusividade em contratos de financiamento de veículos pelo critério objetivo do STJ (REsp 1.061.530/RS · Tema 27).

---

## 📖 O que é este projeto

Site composto por:

- **Página inicial** (`index.html`) — apresentação da ferramenta
- **Calculadora de abusividade** (`calculadora/index.html`) — cálculo automático para financiamento de veículos
- **Documentação técnica** (`docs/`) — exemplo de relatório gerado e referências

A calculadora consulta a API pública do Banco Central (série SGS nº 25471) em tempo real para obter a taxa média de mercado, calcula a taxa efetiva do contrato pelo método de Newton-Raphson, e diagnostica abusividade comparando com o gatilho de 1,5× fixado pelo STJ.

---

## 🚀 Como colocar no ar (passo a passo, do zero)

Você não precisa saber programar. Siga as etapas abaixo na ordem.

### Etapa 1 · Criar conta no GitHub

Se ainda não tem, acesse [github.com](https://github.com) e crie uma conta gratuita. Anote seu nome de usuário (ex: `joaosilva`).

### Etapa 2 · Criar o repositório

1. Estando logado, clique no `+` no canto superior direito → **New repository**
2. Em "Repository name", escolha um nome — sugestão: `revisional-bancaria`
3. Marque como **Public**
4. **NÃO** marque "Add a README file" (já temos um)
5. Clique em **Create repository**

### Etapa 3 · Subir os arquivos

A forma mais simples (sem usar terminal):

1. Na tela do repositório recém-criado, clique em **uploading an existing file** (link no meio da página)
2. Arraste **TODOS os arquivos e pastas** deste projeto para a janela do navegador
3. Role para baixo, em "Commit changes" deixe a mensagem padrão e clique em **Commit changes**

> **Importante:** preserve a estrutura de pastas. A pasta `calculadora/` deve continuar como pasta, não solta.

### Etapa 4 · Ativar o GitHub Pages

1. No repositório, clique em **Settings** (no menu superior do repo)
2. No menu lateral esquerdo, clique em **Pages**
3. Em "Source", selecione **Deploy from a branch**
4. Em "Branch", escolha `main` e pasta `/ (root)`
5. Clique em **Save**

Aguarde 1-2 minutos. O endereço do seu site vai aparecer no topo dessa mesma página, no formato:

```
https://SEU-USUARIO.github.io/revisional-bancaria/
```

Esse é o link público. Pode compartilhar com qualquer pessoa.

### Etapa 5 · Testar

Acesse o link e verifique:

- [ ] A página inicial carrega
- [ ] O botão "Abrir calculadora" funciona
- [ ] A calculadora consegue buscar a taxa BACEN ao preencher uma data
- [ ] O botão "Carregar exemplo" preenche os campos
- [ ] O cálculo gera o resultado completo

Se algo não funcionar, veja a seção [Solução de problemas](#solução-de-problemas) abaixo.

---

## ✏️ Como editar e melhorar o site

### Edições simples (sem Claude Code)

Para mudanças pequenas, basta abrir o arquivo no GitHub:

1. Clique no arquivo que quer editar (ex: `index.html`)
2. Clique no ícone de lápis ✏️ ("Edit this file")
3. Faça a alteração
4. Role para baixo, escreva o que mudou em "Commit message"
5. Clique em **Commit changes**

O site atualiza automaticamente em 1-2 minutos.

### Edições maiores (com Claude Code)

Para evoluir o site (adicionar páginas, novas modalidades, mudar layout):

1. **Instale o Claude Code** seguindo as instruções em [docs.claude.com/en/docs/agents-and-tools/claude-code](https://docs.claude.com/en/docs/agents-and-tools/claude-code)
2. **Clone o repositório** (uma vez só):
   ```bash
   git clone https://github.com/SEU-USUARIO/revisional-bancaria.git
   cd revisional-bancaria
   ```
3. **Inicie o Claude Code** dentro da pasta:
   ```bash
   claude
   ```
4. **Peça as alterações em linguagem natural**, por exemplo:
   - "Adicione uma calculadora para cartão de crédito rotativo, similar à de veículos, mas usando a série BACEN 20741"
   - "Crie uma página de contato com formulário"
   - "Mude as cores do site para um tom mais sóbrio"
5. **Após as mudanças**, peça ao Claude Code:
   > "Suba as alterações para o GitHub"

   Ele vai fazer commit e push automaticamente.

O Claude Code já tem contexto completo do projeto através do arquivo `CLAUDE.md` na raiz — não precisa explicar tudo de novo a cada sessão.

---

## 🧱 Estrutura do projeto

```
revisional-bancaria/
├── index.html                 # Landing page
├── calculadora/
│   └── index.html             # Calculadora de abusividade (veículos)
├── assets/
│   ├── css/                   # CSS compartilhado (futuro)
│   └── js/                    # JS compartilhado (futuro)
├── docs/
│   └── exemplo-relatorio.html # Modelo de relatório gerado
├── .github/
│   └── workflows/
│       └── deploy.yml         # Deploy automático (opcional)
├── README.md                  # Este arquivo
├── CLAUDE.md                  # Contexto técnico para o Claude Code
├── ROADMAP.md                 # Próximas funcionalidades planejadas
└── .gitignore                 # Arquivos ignorados pelo Git
```

---

## 🌐 Domínio próprio (opcional)

Quando quiser mudar de `seuusuario.github.io/revisional-bancaria` para algo como `revisional.seuescritorio.com.br`:

1. **Compre um domínio** em [registro.br](https://registro.br) (R$ 40/ano para `.com.br`) ou [namecheap.com](https://namecheap.com)
2. **Configure DNS** apontando para o GitHub Pages — instruções em [docs.github.com/pages/custom-domain](https://docs.github.com/en/pages/configuring-a-custom-domain-for-your-github-pages-site)
3. **Adicione o domínio** em Settings → Pages → Custom domain do repositório
4. Pronto — em até 24h o site responde no domínio novo (e o anterior continua funcionando)

---

## 🔧 Solução de problemas

**O site não carrega após o deploy**
→ Vá em Settings → Pages e confira se a fonte está em `main` / `/ (root)`. Espere 5 minutos e tente em aba anônima.

**A calculadora abre mas não busca a taxa BACEN**
→ A API do BACEN raramente fica fora do ar, mas pode acontecer. A calculadora detecta a falha e permite preencher manualmente. Verifique também se o navegador não está bloqueando JavaScript.

**Erros ao subir arquivos no GitHub via interface web**
→ A interface tem limite de 100 arquivos por upload. Se acontecer, suba em partes (primeiro a raiz, depois cada pasta).

**Quero apagar tudo e começar de novo**
→ Vá em Settings → role até o final → Delete this repository. Depois recrie do zero.

---

## ⚖️ Licença e disclaimer

Este software é distribuído "como está", sem garantias. Os cálculos seguem a Tabela Price padrão e o parâmetro objetivo do STJ (REsp 1.061.530/RS), mas **não substituem parecer técnico contábil ou perícia judicial**. A configuração definitiva de abusividade depende ainda de prova concreta sobre o perfil de risco da operação e custo de captação do banco (REsp 2.009.614/SC).

Para uso profissional em ações revisionais, sempre conferir os dados diretamente nas fontes oficiais do Banco Central: [www3.bcb.gov.br/sgspub](https://www3.bcb.gov.br/sgspub/).

---

## 📞 Próximos passos

Veja o arquivo [`ROADMAP.md`](./ROADMAP.md) para a lista de funcionalidades planejadas.
