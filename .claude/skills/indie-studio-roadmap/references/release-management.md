# Release Management

## Git Flow para Equipos Micro

Para 1-5 personas, el flow es simple:

```
main (siempre desplegable)
  └── feature/nombre-corto (vida: 1-3 dias maximo)
```

No necesitas `develop`, `release/*`, ni `hotfix/*`. Eso es para equipos de 20+.

Reglas:
- `main` siempre debe pasar CI (typecheck + tests).
- Feature branches viven maximo 3 dias. Si no termino, es muy grande — dividela.
- Commits: `feat(modulo):`, `fix(modulo):`, `refactor(modulo):`, `debt(modulo):`.

## Docker Build Workflow

```
1. Codigo en main
2. docker build -t producto:staging-vNN --build-arg VITE_API_URL=... .
3. docker service update --image producto:staging-vNN servicio_staging
4. Verificar staging (manual o smoke test)
5. docker build -t producto:prod-vNN --build-arg VITE_API_URL=... .
6. docker service update --image producto:prod-vNN servicio_prod
7. Verificar produccion
8. Registrar en releases.md
```

Nunca reusar la misma imagen de staging para produccion si el `VITE_API_URL` cambia — el env se bake en build time con Vite.

## Versionamiento

- **API backend:** Semver (v1.2.3). Major = breaking change, Minor = feature, Patch = fix.
- **Frontend images:** staging-vNN / prod-vNN. Simple y cronologico.
- **Base de datos:** Migraciones con timestamp. Nunca editar una migracion ya ejecutada.

## La Regla Staging-First

Todo pasa por staging antes de produccion. Sin excepciones.

Checklist staging:
- [ ] La app carga sin errores de consola.
- [ ] Login/logout funciona.
- [ ] El flujo principal del modulo nuevo funciona end-to-end.
- [ ] Los endpoints nuevos responden correctamente.
- [ ] No hay queries N+1 en las paginas principales.

## Rollback

Siempre ten a mano el comando de rollback:

```bash
# Ver imagen anterior
docker service inspect --pretty servicio_prod | grep Image

# Rollback
docker service update --image producto:prod-vNN-1 servicio_prod
```

Si el rollback requiere revertir una migracion de DB, eso es un problema. Las migraciones deben ser forward-only y backwards-compatible.

## Release Checklist Template

```markdown
## Release vX.Y.Z — [Nombre]

### Pre-release
- [ ] Todos los tests pasan
- [ ] Staging verificado
- [ ] Changelog escrito
- [ ] modules.md actualizado

### Deploy
- [ ] Build imagen prod
- [ ] Deploy a produccion
- [ ] Smoke test en produccion
- [ ] Monitorear logs 15 minutos

### Post-release
- [ ] Actualizar releases.md
- [ ] Notificar al equipo
- [ ] Cerrar tareas del sprint relacionadas
```
