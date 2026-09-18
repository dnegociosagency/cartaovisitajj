# Cartão de Visita Digital — JJ Moto Peças

Site estático com um hub e uma página por cidade, hospedado na Vercel
e publicado automaticamente a cada push na branch `main`.

## Subdomínio → página

| Subdomínio | Serve | Status |
|---|---|---|
| `jjmaraba.agenciadnegocios.com` | `maraba.html` | ativo |
| `jjananindeua.agenciadnegocios.com` | `ananindeua.html` | pendente |
| `jjparauapebas.agenciadnegocios.com` | `parauapebas.html` | pendente |

O mapeamento é feito por `rewrites` em `vercel.json`, baseado no host da
requisição. A URL não muda: o visitante vê o domínio limpo, sem `.html`.

O hub (`hub.html`, menu "Escolha sua Cidade") é servido na raiz de
qualquer domínio que não tenha regra própria.

## Arquivos

    hub.html           hub com o menu de cidades (NAO pode se chamar index.html)
    maraba.html        \
    ananindeua.html     >  mesmo template, dados de loja diferentes
    parauapebas.html   /
    style.css          estilos de TODAS as páginas (as 3 usam as mesmas 33 classes)
    vercel.json        regras de subdomínio
    jj_moto_pecas_*.png  logo compartilhada

Repositório único de propósito: as três cidades compartilham 100% do CSS e
a logo. Separar em repositórios distintos triplicaria esses arquivos e faria
as lojas divergirem visualmente com o tempo.

## Publicar uma alteração

    git add -A
    git commit -m "descrição"
    git push

A Vercel detecta o push na `main` e publica em produção sozinha.

## Adicionar um novo subdomínio

1. Adicione o domínio na Vercel em *Settings → Domains*
2. Anote o CNAME e o TXT que ela exibir
3. Na Hostinger, em **Domínios → DNS / Nameservers** (não em *Subdomínios*,
   que é do painel de hospedagem e não cria registro DNS):
   - `CNAME` · nome = o subdomínio · destino = o valor da Vercel, **sem o ponto final**
   - `TXT` · nome = `_vercel` · valor = o `vc-domain-verify=...` completo
4. Acrescente a regra correspondente em `vercel.json` e dê push

### Atenção: o editor de DNS da Hostinger substitui TXT de mesmo nome

Ao salvar um `TXT` em `_vercel`, a Hostinger **apaga os outros registros TXT
com esse mesmo nome**, mesmo exibindo erro de conflito. Verifique um
subdomínio por vez: a verificação é única, então perder o token de um
domínio já verificado não o derruba — mas nunca configure dois em paralelo.

## Pendências

- Links entre cidades são relativos: navegar a partir de um subdomínio
  mantém o subdomínio de origem na URL

### Por que o hub se chama `hub.html` e não `index.html`

Na Vercel, `rewrites` são avaliados **depois** do sistema de arquivos. Se
existir um `index.html`, ele responde em `/` e a regra de rewrite nunca
chega a ser consultada — todos os subdomínios servem o hub.

Por isso o hub se chama `hub.html` e `/` é resolvido por uma regra de
fallback no fim da lista. **Não recrie um `index.html` na raiz**: isso
quebra silenciosamente os três subdomínios de uma vez.

Pelo mesmo motivo, o botão "Voltar ao Menu Principal" aponta para
`/hub.html` e não para `/` — num subdomínio de cidade, `/` cairia na
própria cidade.

### URLs sem `.html`

`cleanUrls: true` no `vercel.json` serve as páginas sem extensão: `/maraba`
em vez de `/maraba.html`. Links antigos com `.html` continuam funcionando —
a Vercel responde 308 para a versão limpa.

Por isso os `destination` dos rewrites **não levam `.html`**. Se levassem,
o redirect 308 dispararia dentro do rewrite e o visitante de
`jjmaraba.agenciadnegocios.com/` seria jogado para `/maraba`, perdendo a
URL limpa na raiz — o oposto do objetivo.
