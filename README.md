# A Modo Mio Media Uploader

Microservico para upload de imagens e videos com persistencia em volume de host (`/data`) e entrega publica via Nginx.

## Variaveis de ambiente

- `UPLOAD_API_KEY` (obrigatoria): chave esperada no header `x-api-key`.
- `MEDIA_BASE_URL` (opcional): base publica para retorno da URL final. Default: `https://media.amodomio.com.br`.
- `PORT` (opcional): porta HTTP da API. Default: `3001`.
- `TRUST_PROXY_HOPS` (opcional): numero de proxies confiaveis para obter IP real. Default: `1`.
- `RATE_LIMIT_WINDOW_MS` (opcional): janela de rate limit em ms. Default: `60000`.
- `RATE_LIMIT_MAX` (opcional): maximo de requests por IP na janela (global). Default: `120`.
- `RATE_LIMIT_UPLOAD_MAX` (opcional): maximo de uploads por IP na janela. Default: `20`.
- `REQUEST_TIMEOUT_MS` (opcional): timeout de request para mitigar conexoes lentas. Default: `30000`.
- `HEADERS_TIMEOUT_MS` (opcional): timeout de headers HTTP. Default: `35000`.
- `KEEP_ALIVE_TIMEOUT_MS` (opcional): keep-alive timeout de conexoes. Default: `5000`.

## Endpoint

- `GET /health` -> `{ "ok": true }`
- `POST /upload?kind=image|video&menuItemId=<id>&slot=<nome>` -> `{ ok, kind, menuItemId, slot, url }`

Campo multipart esperado: `file`.

## Exemplo de upload com curl

```bash
curl -X POST "http://localhost:3001/upload?kind=image&menuItemId=margherita&slot=cover" \
  -H "x-api-key: SUA_CHAVE" \
  -F "file=@cover.jpg"
```

```bash
curl -X POST "http://localhost:3001/upload?kind=video&menuItemId=margherita&slot=promo" \
  -H "x-api-key: SUA_CHAVE" \
  -F "file=@promo.mp4"
```

## Estrutura de paths gerada

- Imagem: `/data/images/menu-items/<menuItemId>/<slot>.<ext>`
- Video: `/data/videos/menu-items/<menuItemId>/<slot>.<ext>`
- Temporario de upload: `/data/tmp`

URLs publicas retornadas seguem:

- `https://media.amodomio.com.br/images/menu-items/<menuItemId>/<slot>.<ext>`
- `https://media.amodomio.com.br/videos/menu-items/<menuItemId>/<slot>.<ext>`
