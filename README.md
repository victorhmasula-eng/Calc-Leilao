# Leilões Caixa — Painel

Painel mobile (single-page, sem dependências) para acompanhar as análises de due diligence de imóveis Caixa, com calculadora de cenários embutida.

## Como publicar no GitHub Pages

1. Crie um repositório novo no GitHub (ex: `leiloes-painel`), ou use um já existente.
2. Suba o arquivo `index.html` para a raiz do repositório (branch `main`).
3. No GitHub: **Settings → Pages → Build and deployment → Source: Deploy from a branch**, selecione a branch `main` e a pasta `/ (root)`. Salve.
4. Em alguns minutos o site fica disponível em `https://SEU-USUARIO.github.io/NOME-DO-REPO/`.

### Via linha de comando

```bash
mkdir leiloes-painel && cd leiloes-painel
git init
cp /caminho/para/index.html .
git add index.html
git commit -m "Painel de leilões Caixa"
git branch -M main
git remote add origin https://github.com/SEU-USUARIO/leiloes-painel.git
git push -u origin main
```

Depois é só ativar o GitHub Pages como no passo 3 acima.

## Atualizações futuras

O painel é um único arquivo HTML autocontido (HTML+CSS+JS inline, sem build step). Para atualizar: edite `index.html` e faça `git push` de novo — o GitHub Pages republica automaticamente.
