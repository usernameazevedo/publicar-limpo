# publicar-limpo

Publica qualquer entregável HTML (relatório, catálogo, apresentação, landing page) num **link limpo**: uma URL no seu próprio domínio, sem username no meio, pronta para enviar a cliente.

```bash
publicar-limpo relatorio.html balanco-agosto
# ✅ publicado: https://entregas.seudominio.com/balanco-agosto/
```

## O problema

Você termina um HTML para um cliente e precisa mandar um link. As saídas comuns têm defeitos:

- Anexar o arquivo: quebra em celular, e cada revisão vira um anexo novo.
- Hospedagem gratuita com subdomínio genérico: a URL entrega a ferramenta que você usou.
- Criar um projeto de hospedagem por entregável: você bate no teto da conta em poucos meses.

## A solução

**Um único projeto no Cloudflare Pages** hospeda todos os entregáveis, cada um numa subpasta, servido no seu domínio:

```
https://entregas.seudominio.com/cliente-a-catalogo/
https://entregas.seudominio.com/cliente-b-relatorio/
```

Como o projeto é sempre o mesmo, você nunca cria projeto novo e nunca esbarra no limite de 100 projetos por conta da Cloudflare (que é igual no plano gratuito e no pago).

O script cuida do resto: monta o diretório, injeta `noindex` (o link é para pessoas, não para o Google), sobe por `wrangler`, **espera a URL responder 200 antes de dizer que publicou**, e mantém um catálogo local do que está no ar. Republicar o mesmo slug atualiza a mesma URL, então o link que o cliente já tem continua valendo.

## Instalação

Requisitos: `bash`, `curl`, `python3`, `node` (para o `npx wrangler`) e uma conta Cloudflare.

```bash
mkdir -p ~/bin ~/.config/publicar-limpo
curl -so ~/bin/publicar-limpo https://raw.githubusercontent.com/usernameazevedo/publicar-limpo/main/publicar-limpo
chmod +x ~/bin/publicar-limpo
```

Crie o token da Cloudflare com escopo **Account · Cloudflare Pages · Edit** e salve:

```bash
printf '%s' 'SEU_TOKEN' > ~/.config/publicar-limpo/cf-token
chmod 600 ~/.config/publicar-limpo/cf-token
```

Copie o `config.example` para `~/.config/publicar-limpo/config` e preencha com o seu account ID, o nome do projeto e o seu domínio. **Nenhum valor pessoal fica dentro do script**, justamente para que ele possa ser versionado e compartilhado sem vazar nada.

Por fim, aponte um subdomínio para o projeto em Cloudflare Pages · Custom domains.

## Uso

```bash
publicar-limpo <arquivo.html|pasta> <slug> ["descrição"] [grupo]
```

- **arquivo.html**: vira o `index.html` do endereço.
- **pasta**: sobe a pasta inteira, que precisa conter um `index.html`. Use quando a página depende de imagens ou vídeos externos.
- **slug**: identifica o entregável na URL. Republicar com o mesmo slug atualiza a mesma URL.

Modos alternativos:

| Flag | Resultado |
|---|---|
| _(sem flag)_ | `https://seu-dominio/<slug>/` · um projeto para tudo. Recomendado. |
| `--isolado` | `https://<slug>.pages.dev` · um projeto Cloudflare por slug. |
| `--netlify` | `https://<slug>.netlify.app` · via API da Netlify, exige `netlify-token`. |

## Sobre GitHub Pages

**Este script não usa GitHub Pages, e isso é deliberado.**

Uma versão anterior publicava criando um repositório por entregável dentro de uma organização do GitHub e ligando o Pages em cada um. Funcionava, mas é um mau uso da plataforma: os Termos de Serviço do GitHub dizem que o Pages não se destina a servir como hospedagem de sites comerciais, e uma organização recém-criada que só recebe HTML gerado, sem código, tem exatamente a assinatura que os sistemas antiabuso procuram.

Na prática, essa versão antiga levou a conta que a usava a ser sinalizada, o que derrubou todos os sites de uma vez. O repositório foi reescrito para Cloudflare Pages, que é feito para hospedar sites, e a abordagem antiga foi removida por completo.

Se você chegou aqui procurando como usar o GitHub Pages para entregas de cliente: não use. Use uma hospedagem de verdade. Este script faz isso com um comando.

## Licença

MIT. Veja [LICENSE](LICENSE).

## Autor

Luis Azevedo · username.azevedo@gmail.com
