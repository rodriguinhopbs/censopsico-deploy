# CensoPsico Deploy

Este diretorio contem somente os arquivos necessarios para operacao no cliente.
Ele e sincronizado automaticamente para o repositorio publico de deploy.

## Arquivos

- `docker-compose.yml`: stack de producao (postgres + migrate + web)
- `.env.example`: template sem segredos

## Pre-requisitos

- Docker 24+
- Docker Compose v2+
- Acesso ao GHCR para puxar a imagem (`ghcr.io/...`)

## Setup rapido

1. Copie o template de ambiente:

   ```bash
   cp .env.example .env
   ```

2. Edite o arquivo `.env` e troque obrigatoriamente:
   - `POSTGRES_PASSWORD`
   - `SECRET_KEY`
   - `APP_IMAGE` e `APP_IMAGE_TAG` (se necessario)

3. Suba a stack:

   ```bash
   docker compose --env-file .env up -d
   ```

4. Verifique status:

   ```bash
   docker compose ps
   docker compose logs -f web
   ```

## Atualizacao de versao

Para atualizar para uma nova imagem:

```bash
docker compose --env-file .env pull
docker compose --env-file .env up -d
```

## Boas praticas de seguranca

- Nunca comitar `.env` com valores reais
- Usar senha forte para banco e `SECRET_KEY` unica por cliente
- Expor a aplicacao atras de proxy com HTTPS
- Restringir acesso de rede ao banco de dados
