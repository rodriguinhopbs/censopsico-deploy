# Certificados TLS

Este diretorio e o ponto de montagem de certificados para o Nginx.
Nao comite arquivos de certificado reais.

Arquivos esperados para ativar HTTPS no nginx/default.conf:

- fullchain.pem
- privkey.pem

Sugestao:

- Use um proxy gerenciado (Cloudflare, ALB, Traefik, Caddy) para TLS automatico
- Ou gere certificados com Certbot no host e copie para este diretorio
