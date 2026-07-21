# publicar-limpo

Publica qualquer entregável HTML (relatório, catálogo, apresentação, landing) num **link limpo**: uma URL neutra, sem o seu username, pronta para enviar a cliente final.

```bash
publicar-limpo relatorio.html meu-relatorio
# ✅ publicado: https://<org>.github.io/meu-relatorio/
```

## O problema

GitHub Pages pessoal publica em `https://SEU-USERNAME.github.io/...` — funciona, mas o seu username no meio da URL não fica profissional num link que o cliente vai encaminhar. Hospedagens com URL neutra ou exigem conta/token extra (Netlify, Cloudflare Pages) ou verificação de identidade (GitLab CI).

## A solução

Uma **organização gratuita do GitHub** com nome neutro (ex.: `catalogoview`, `entregas-digitais`). Repos públicos da org publicam em `https://<org>.github.io/<repo>/` — sem username, grátis, sem verificação nova, usando o `gh` que você já tem autenticado.

O script automatiza o resto: cria o repo por slug, publica o conteúdo, habilita o Pages, injeta `noindex` (o link é para pessoas, não para o Google) e espera o build. Republicar o mesmo slug atualiza a mesma URL.

## Instalação

1. Crie uma organização Free em https://github.com/organizations/plan com um nome neutro em minúsculas (ele aparece na URL).
2. Instale e configure:

```bash
curl -so ~/bin/publicar-limpo https://raw.githubusercontent.com/usernameazevedo/publicar-limpo/main/publicar-limpo
chmod +x ~/bin/publicar-limpo
mkdir -p ~/.config/publicar-limpo
echo NOME-DA-ORG > ~/.config/publicar-limpo/org
```

Requisitos: [gh](https://cli.github.com) autenticado, git, curl.

## Uso

```bash
# um arquivo HTML
publicar-limpo relatorio.html meu-relatorio

# uma pasta inteira (precisa ter index.html; assets relativos vão junto)
publicar-limpo pasta-do-site/ meu-catalogo
```

- **Atualizar**: rode de novo com o mesmo slug — mesma URL, conteúdo novo.
- **Vários clientes**: um slug por entregável; cada um vira um repo na org.
- **Despublicar**: apague o repo (`gh repo delete <org>/<slug>`).

## Dicas

- O nome da org e dos slugs aparecem na URL — escolha nomes que façam sentido para quem recebe o link.
- Limites do GitHub Pages: 1 GB por site, 100 GB de banda/mês — de sobra para documentos e catálogos.
- Se você tem automações que varrem seus repos pessoais (badges, blocos de contato), elas não tocam na org: `gh repo list SEU-USER` não lista repos de organizações.

## Licença

MIT
