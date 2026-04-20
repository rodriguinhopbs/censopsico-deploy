# CensoPsico Deploy

Este diretorio contem somente os arquivos necessarios para operacao no cliente.
Ele e sincronizado automaticamente para o repositorio publico de deploy.

## Arquivos

- `docker-compose.yml`: stack de producao (postgres + migrate + web + nginx)
- `.env.example`: template sem segredos
- `nginx/`: configuracao de proxy reverso para internet
- `certs/`: ponto de montagem dos certificados TLS

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
   - `COMPOSE_PROJECT_NAME` (mantenha o mesmo nome do ambiente antigo)
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
   docker compose logs -f nginx
   ```

## Exposicao na internet

- O Nginx e o unico servico exposto externamente nas portas 80/443
- A aplicacao Flask fica somente na rede interna do Compose
- Para HTTPS, ative os blocos TLS em nginx/default.conf e forneca certificados em certs/

## Reutilizacao de dados do ambiente existente

- Mantenha `COMPOSE_PROJECT_NAME` igual ao nome do projeto antigo para reaproveitar volumes
- Nao use `docker compose down -v` em migracoes, pois isso remove volumes

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

## Fase publica temporaria e transicao para o cliente

No repositorio privado de codigo (`Censo_Psico_NR1`), configure no GitHub:

- `Repository variable DEPLOY_REPO`: repo de destino do sync
   - Exemplo inicial (publico): `rodriguinhopbs/censopsico-deploy`
   - Exemplo final (cliente, privado): `ORG_DO_CLIENTE/censopsico-deploy`
- `Repository variable DEPLOY_SYNC_ENABLED`: `true` para sincronizar, `false` para pausar
- `Repository secret DEPLOY_REPO_TOKEN`: token com `Contents: Read and write` no repo de destino

Fluxo recomendado:

1. Fase inicial: publique no repo temporario publico para onboarding rapido.
2. Entrega: crie/receba o repo privado do cliente e transfira os artefatos.
3. No repo privado de codigo, troque `DEPLOY_REPO` para o repo privado do cliente.
4. Gere novo token com acesso apenas ao repo do cliente e atualize `DEPLOY_REPO_TOKEN`.
5. Se quiser congelar a fase publica, defina `DEPLOY_SYNC_ENABLED=false` temporariamente.
