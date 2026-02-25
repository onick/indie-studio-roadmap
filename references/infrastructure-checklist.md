# Infrastructure Checklist

## VPS Single-Node (Tu Setup Actual)

### Esenciales (Debe tener)

- [ ] **SSH solo con keys.** Password auth deshabilitado.
- [ ] **UFW activo.** Solo puertos necesarios abiertos (22, 80, 443, puertos GPS si aplica).
- [ ] **Docker Swarm.** Incluso en single-node, Swarm te da rolling updates y service management.
- [ ] **Traefik como reverse proxy.** TLS automatico con Let's Encrypt.
- [ ] **Unattended upgrades.** Security patches se aplican automaticamente.

### Backups (No negociable)

- [ ] **MySQL dump diario.** `mysqldump` via cron dentro del container, copiado fuera.
- [ ] **Backup a remoto.** Al menos copiar a un segundo lugar (S3, Backblaze, otro VPS).
- [ ] **Test de restauracion.** Un backup que nunca probaste restaurar NO es un backup.
- [ ] **Retencion.** 7 dias de backups diarios, 4 semanales, 3 mensuales.

Script base para backup MySQL en Docker:
```bash
#!/bin/bash
CONTAINER=$(docker ps -q -f name=mysql)
DATE=$(date +%Y%m%d_%H%M)
docker exec $CONTAINER mysqldump -u root -p$MYSQL_ROOT_PASSWORD \
  --all-databases --single-transaction > /root/backups/db_$DATE.sql
gzip /root/backups/db_$DATE.sql
# Limpiar backups > 7 dias
find /root/backups -name "*.sql.gz" -mtime +7 -delete
```

### Monitoreo Basico

- [ ] **Disk usage.** Alerta si supera 80%. Docker images se acumulan.
- [ ] **Memory.** Alerta si swap > 50% de uso sostenido.
- [ ] **Docker service health.** `docker service ls` debe mostrar todas las replicas up.

Comando rapido de salud:
```bash
echo "=== DISK ===" && df -h / | tail -1
echo "=== MEMORY ===" && free -h | grep Mem
echo "=== SWAP ===" && free -h | grep Swap
echo "=== SERVICES ===" && docker service ls
echo "=== IMAGES ===" && docker images | wc -l
```

### Limpieza Docker

Las imagenes se acumulan con cada deploy. Limpia periodicamente:
```bash
# Imagenes sin usar (dangling)
docker image prune -f
# Imagenes antiguas de staging (mantener ultimas 5)
docker images | grep staging | sort -t: -k2 -n | head -n -5 | awk '{print $3}' | xargs docker rmi
```

## Cuando Escalar de 1 VPS a 2

La respuesta: mas tarde de lo que piensas. Un VPS de 4GB RAM con Docker Swarm puede manejar:
- 1 API (Laravel/PHP-FPM)
- 1 Frontend (Nginx static)
- 1 MySQL
- 1 Redis (si lo necesitas)
- Traefik
- Hasta ~100 usuarios concurrentes

Escala cuando:
- CPU sostenido > 80% por horas.
- No puedes hacer deploy sin afectar produccion.
- Necesitas base de datos dedicada (DB en otro servidor).

NO escala porque: "se ve profesional tener 3 servers." Eso es gasto sin retorno.

## Seguridad Basica

- [ ] **fail2ban** instalado y configurado para SSH.
- [ ] **Secrets fuera de git.** Usar `.env` no commiteado + Docker secrets.
- [ ] **CORS estricto.** Solo dominios conocidos en `CORS_ALLOWED_ORIGINS`.
- [ ] **Rate limiting.** En endpoints de auth y APIs publicas.
- [ ] **Log rotation.** Docker logs no deben llenar el disco.
