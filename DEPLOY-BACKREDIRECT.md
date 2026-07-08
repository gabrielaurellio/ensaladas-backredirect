# Deploy: back-redirect → GitHub Pages

**URL final:** `http://backredirect.labuenavida.online/back-redirect.html`

> **Nota sobre domínio:** GitHub Pages não suporta subpastas de domínio em repositórios separados. O correto é usar o subdomínio `backredirect.labuenavida.online`. O arquivo ficará acessível em `/back-redirect.html` dentro desse domínio.

---

## Pré-requisitos

- Git instalado (`git --version`)
- GitHub CLI instalado (`gh --version`) — se não tiver: `brew install gh`
- Autenticado no GitHub CLI: `gh auth login`

---

## Passo a passo

### 1. Entrar na pasta do projeto

```bash
cd /Users/gabrielfernandes/paginas-dr-claude
```

### 2. Criar o arquivo CNAME (domínio customizado)

```bash
echo "backredirect.labuenavida.online" > CNAME
```

### 3. Inicializar o repositório Git e fazer o primeiro commit

```bash
git init
git add .
git commit -m "chore: deploy inicial back-redirect page"
```

### 4. Criar o repositório no GitHub e fazer o push

```bash
gh repo create ensaladas-backredirect \
  --public \
  --source=. \
  --remote=origin \
  --push
```

> O nome `ensaladas-backredirect` é sugestão — pode trocar. O importante é que seja público para o GitHub Pages funcionar.

### 5. Ativar o GitHub Pages

```bash
gh api \
  --method POST \
  -H "Accept: application/vnd.github+json" \
  "/repos/$(gh api user --jq .login)/ensaladas-backredirect/pages" \
  -f source[branch]="main" \
  -f source[path]="/"
```

### 6. Verificar que o Pages foi ativado

```bash
gh api "/repos/$(gh api user --jq .login)/ensaladas-backredirect/pages" --jq '.status, .html_url'
```

Deve retornar `built` e a URL padrão do GitHub Pages.

---

## Configuração de DNS (fazer no painel do provedor do domínio)

Adicionar um registro **CNAME** no painel DNS do `labuenavida.online`:

| Tipo  | Nome           | Valor                        | TTL  |
|-------|----------------|------------------------------|------|
| CNAME | backredirect   | `[seu-usuario].github.io`    | 3600 |

Substitua `[seu-usuario]` pelo seu username do GitHub.

> Após salvar o DNS, aguardar até 30 minutos para propagação.

---

## Atualizar o link do Hotmart na página

Antes do deploy (ou logo depois), substituir o placeholder no arquivo:

```bash
sed -i '' 's|LINK_HOTMART_3.90|https://pay.hotmart.com/SEU_LINK_AQUI|g' back-redirect.html

git add back-redirect.html
git commit -m "fix: link hotmart $3.90 inserido"
git push
```

---

## Verificação final

Após propagação do DNS, acessar:

```
http://backredirect.labuenavida.online/back-redirect.html
```

A página deve carregar com a animação de seleção (1 em 50) e o cupom de $3.

---

## Atualizar a página no futuro

Para qualquer alteração futura:

```bash
cd /Users/gabrielfernandes/paginas-dr-claude
git add back-redirect.html
git commit -m "update: nova versão da página"
git push
```

O GitHub Pages atualiza em 1-2 minutos após o push.
