# Fase 3 - API .NET

## Objetivo

Desplegar la API con su configuración externa, probes bien pensados y actualizaciones sin corte. Conectarla a Postgres reutilizando el `Secret` de la fase 2.

## Recursos que se tocan

- **Se crean:** `ConfigMap`, `Deployment` (estrategia RollingUpdate), `Service` ClusterIP, probes (liveness, readiness, startup).
- **Se reutiliza:** el `Secret` de la [fase 2](../fase-02-postgres/README.md).
- **Depende de:** la quota y el `LimitRange` de la [fase 1](../fase-01-namespace-limites/README.md). Durante un rolling update hay Pods extra y la quota los cuenta.

## Preguntas guía

1. **¿Cómo llega la configuración a la API (variables de entorno o volumen) y qué pasa en los Pods en ejecución si editás el `ConfigMap`?**
   - *Cómo verificarlo:* consumí el mismo `ConfigMap` de las dos formas, editá un valor y compará qué ve cada Pod sin reiniciar.

2. **¿Qué diferencia observable hay entre que falle la readiness y que falle la liveness?**
   - *Cómo verificarlo:* forzá cada falla por separado y observá reinicios del Pod y los endpoints del `Service`. Probá también un `targetPort` equivocado.

3. **Dado que "Obtener oportunidades" depende de Postgres y "Consultar" de dolarapi.com, ¿qué debería (y qué no debería) chequear cada probe?**
   - *Cómo verificarlo:* bajá Postgres a cero réplicas y observá qué hace tu API según cómo diseñaste los probes. Mirá reinicios y Ready.

4. **¿Qué hace la `startupProbe` con la liveness y la readiness mientras todavía no pasó?**
   - *Cómo verificarlo:* simulá un arranque lento en la app y seguí los Events del Pod con y sin `startupProbe`.

5. **En un RollingUpdate, ¿cuántos Pods viejos y nuevos hay en cada instante y qué puede frenar el proceso?**
   - *Cómo verificarlo:* cambiá la imagen (tag) y observá los Pods en vivo, junto con el estado del rollout. Probá distintos valores de `maxSurge`/`maxUnavailable` y revisá si la quota interviene.

## Lecturas

- [Deployments - Handbook](https://k8s.info/docs/core/deployments): RollingUpdate, `maxSurge`/`maxUnavailable`, rollback.
- [Services - Handbook](https://k8s.info/docs/core/services): ClusterIP, `port` vs. `targetPort`, selectores y endpoints.
- [ConfigMaps y Secrets - Handbook](https://k8s.info/docs/core/config-secrets): formas de consumo y cuándo se refrescan.
- [Health Checks - Handbook](https://k8s.info/docs/intermediate/health-checks): los tres probes, mecanismos y errores típicos.
- [Probes - Kubernetes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/): campos de timing y semántica oficial.
- [Deployment - Kubernetes](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/): referencia de estrategia y rollout.

## Listo cuando

- [ ] La API responde "Consultar" y "Obtener oportunidades" dentro del cluster (probado desde otro Pod).
- [ ] El `Service` tiene endpoints solo cuando el Pod está Ready.
- [ ] Un cambio de imagen se despliega sin que el endpoint deje de responder.
- [ ] Sabés explicar qué chequea cada probe y por qué.

## Notas y aprendizajes

<!-- completar a medida que itero -->
