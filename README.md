# netsolin-config-publico

Configuracion publica consumida por las APIs NETSOLIN desplegadas en clientes.

## ia_models.json

Modelos IA recomendados por nivel (`basico` / `medio` / `avanzado`). Cada API
NETSOLIN consulta este archivo al arrancar y cada 6h (tarea programada
`sync_ia_models`) y actualiza su tabla local `ia_config` para las filas con
`origen='netsolin'`. Las filas con `origen='cliente'` (configuracion local del
contador) no se tocan.

### Cuando OpenRouter deprecia un modelo

1. Editar `ia_models.json`, cambiar `"modelo"` al reemplazo recomendado.
2. Subir el `version` a la fecha del cambio.
3. Commit + push a `main`.
4. Los clientes recogen el cambio en maximo 6h. Si urge, reiniciar el API del
   cliente fuerza el sync inmediato al startup.

### URL consumida

```
https://raw.githubusercontent.com/orlapops/netsolin-config-publico/main/ia_models.json
```

Cada cliente puede sobreescribirla en `config/config.json` con la clave
`"ia_models_registry_url": "..."`.

### Plan B: fallback automatico en 404

Si OpenRouter responde `404 No endpoints found` durante una llamada (modelo
muerto antes del proximo refresh), el codigo aplica un fallback in-process
desde la lista hardcoded `FALLBACKS_FREE` en
`services/configuracion/ia_models_sync_service.py` y reintenta la llamada.
Esto cubre el gap entre el momento de la deprecacion y la siguiente corrida
del sync.
