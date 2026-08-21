# alba-captacao-videos

Vídeos de referência que aparecem na **Preparação de captação** da galeria da alba
(`galeriamayaraalba.com/captacao/{id}`, seção *Referências e Direcionamentos*, aba **Vídeos**).

Todos são públicos e servidos pelo próprio domínio, através do proxy
`/api/vid/{arquivo}.mp4`, que devolve `video/mp4` com suporte a streaming por range
(206) — é o que o `<video>` precisa pra tocar inline, principalmente no iPhone.

## Onde cada vídeo mora

| Origem | Arquivos | Observação |
|---|---|---|
| Assets do release `v1` | `a1`–`a3`, `b1`–`b2`, `c1`–`c2`, `d1`–`d2`, `e1`–`e2`, `f1`, `v1`–`v3` | O CDN do GitHub já responde 206 sozinho. |
| Pasta `videos/` deste repo | `n01`–`n10` | O `raw` do GitHub ignora Range, então o proxy fatia e monta o 206. |

O proxy tenta o release primeiro e cai pra `videos/` quando não encontra.

## Padrão de conversão

Os originais vêm da pasta do Drive *EXEMPLOS DE VÍDEOS E EDIÇÕES* em 4K, com 50–300 MB
cada. No site eles aparecem num card de 156 px, então são convertidos para um perfil leve
— o que mantém a página rápida no celular:

```
ffmpeg -i ORIGINAL \
  -vf "scale=540:960:force_original_aspect_ratio=decrease,pad=540:960:(ow-iw)/2:(oh-ih)/2,fps=30" \
  -c:v libx264 -profile:v main -level 3.1 -crf 28 -preset faster -pix_fmt yuv420p \
  -maxrate 1200k -bufsize 2400k \
  -c:a aac -b:a 80k -ac 1 -ar 44100 \
  -movflags +faststart videos/nNN.mp4
```

`-movflags +faststart` é obrigatório: sem ele o índice do MP4 fica no fim do arquivo e o
vídeo só começa a tocar depois de baixar tudo.

## Catálogo de `videos/`

| Arquivo | Referência | Duração |
|---|---|---|
| `n01.mp4` | Gastronomia: o prato apresentado pelo chef | 44s |
| `n02.mp4` | Música: performance em traje social | 66s |
| `n03.mp4` | Marca: fala direta com legenda em destaque | 50s |
| `n04.mp4` | Beleza: lifestyle com joias e close | 39s |
| `n05.mp4` | Esporte: corrida em plano aberto | 80s |
| `n06.mp4` | Estética: depoimento sobre cuidados com a pele | 47s |
| `n07.mp4` | Preto e branco: fala institucional | 58s |
| `n08.mp4` | Bastidores: closet e preparação | 57s |
| `n09.mp4` | Negócios: fala com número em destaque | 24s |
| `n10.mp4` | Clínica: detalhe do equipamento | 39s |

## Como fazer esses vídeos aparecerem pro cliente

Subir o arquivo aqui **não** publica sozinho. A lista que o cliente vê fica no painel:
**Acessos → Vídeos de exemplo da captação**, uma linha por vídeo no formato `Nome | link`.
As linhas correspondentes ao catálogo acima estão em [`LISTA-ADMIN.txt`](LISTA-ADMIN.txt).
