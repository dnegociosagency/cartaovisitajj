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

O hub (`index.html`, menu "Escolha sua Cidade") é servido na raiz de
qualquer domínio que não tenha regra própria.

## Arquivos

    index.html         hub com o menu de cidades
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

- Telefones e WhatsApp são placeholders (`5591999999999`, `5594999999999`)
- Links entre cidades são relativos: navegar a partir de um subdomínio
  mantém o subdomínio de origem na URL
