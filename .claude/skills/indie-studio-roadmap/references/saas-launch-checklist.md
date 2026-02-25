# SaaS Launch Checklist

## T-30 Dias (Un Mes Antes)

### Legal
- [ ] Terminos de servicio redactados (puede ser template adaptado)
- [ ] Politica de privacidad (GDPR-lite: que datos, para que, como borrar)
- [ ] Pagina de pricing clara con que incluye cada plan

### Pagos
- [ ] Stripe (o equivalente) integrado y probado
- [ ] Flujo: registro → trial → pago → activacion
- [ ] Webhooks de Stripe idempotentes (cancelacion, fallo de pago, upgrade)
- [ ] Facturas automaticas configuradas

### Email
- [ ] Dominio verificado para envio (SPF, DKIM, DMARC)
- [ ] Emails transaccionales: verificacion, bienvenida, reset password
- [ ] Template basico con branding consistente

### Monitoreo
- [ ] Error tracking activo (Sentry o similar)
- [ ] Uptime monitoring (UptimeRobot, gratis)
- [ ] Alertas a Slack/email cuando algo falla

### DNS y Dominios
- [ ] Dominio de produccion apuntando
- [ ] SSL activo (Let's Encrypt via Traefik)
- [ ] Redirect www → apex (o viceversa)
- [ ] Subdominio API apuntando

## T-7 Dias (Una Semana Antes)

### QA Final
- [ ] Flujo completo: registro → onboarding → primer check-in → checkout → factura
- [ ] Probado en movil (iOS Safari + Android Chrome minimo)
- [ ] Probado con datos reales (no "Test Hotel" con 1 habitacion)
- [ ] Performance: pagina principal carga en < 3 segundos
- [ ] Errores de consola: 0

### Infraestructura
- [ ] Backup automatico funcionando y probado
- [ ] Rollback probado (revertir a imagen anterior)
- [ ] Disco con al menos 30% libre
- [ ] Logs rotando correctamente

### Contenido
- [ ] Landing page con propuesta de valor clara
- [ ] Documentacion basica / FAQ (5-10 preguntas comunes)
- [ ] Video de demo o screenshots del producto

## Dia de Lanzamiento

### Deploy
- [ ] Build final de produccion
- [ ] Smoke test completo en produccion
- [ ] Monitorear logs los primeros 60 minutos
- [ ] Rollback plan listo y probado

### Comunicacion
- [ ] Notificar a beta testers / clientes piloto
- [ ] Post en redes sociales (si aplica)
- [ ] Email de lanzamiento a lista de espera (si existe)

## T+7 Dias (Primera Semana)

### Monitoreo
- [ ] Revisar Sentry: errores nuevos?
- [ ] Revisar metricas: registros, activaciones, primeros check-ins
- [ ] Revisar feedback directo de usuarios

### Iteracion Rapida
- [ ] Fix criticos desplegados en < 24 horas
- [ ] Lista de "quick wins" basada en feedback
- [ ] Ajustar onboarding si TTFV es alto

## Estrategia Soft Launch para Indies

No lances al mundo entero. Haz soft launch:

1. **3 hoteles piloto** que conoces personalmente.
2. **2 semanas** de uso real con soporte personal.
3. **Iterar** basado en lo que realmente usan (no lo que dicen que quieren).
4. **Abrir** cuando los 3 pilotos estan satisfechos.

Esto te protege de: bugs criticos en publico, mal onboarding, features que nadie usa, y feedback que no puedes manejar.
