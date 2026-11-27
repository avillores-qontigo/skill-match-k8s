# Fase 5 - Autoscaling

## Objetivo

Escalar la API de 1 a 2 réplicas según CPU con un `HorizontalPodAutoscaler` (autoscaling/v2), y entender qué necesita para funcionar y cómo interactúa con el resto.

## Recursos que se tocan

- **Se crea:** `HorizontalPodAutoscaler` (autoscaling/v2), con mínimo 1 y máximo 2 réplicas.
- **Se instala o verifica:** metrics-server.
- **Depende de:**
  - la [fase 1](../fase-01-namespace-limites/README.md): la quota debe tener lugar para la segunda réplica (y para el surge de un rollout).
  - la [fase 3](../fase-03-api-dotnet/README.md): los `resources` y la readiness del `Deployment` condicionan el cálculo y el reparto de tráfico.

## Preguntas guía

1. **¿Qué ves en el HPA si falta metrics-server, o si los Pods no declaran requests de CPU?**
   - *Cómo verificarlo:* corré `describe` del HPA y mirá las condiciones y la columna de targets. Probá `kubectl top pods`.

2. **El porcentaje de CPU del target, ¿se calcula sobre el request o sobre el limit?**
   - *Cómo verificarlo:* generá carga sobre la API y comparaste el valor del HPA con el consumo de `kubectl top` y con los `resources` del Pod.

3. **Si el HPA quiere una segunda réplica pero la quota no alcanza, ¿dónde se ve el problema?**
   - *Cómo verificarlo:* reducí temporalmente la quota, subí la carga y seguí los Events del `ReplicaSet` y del HPA.

4. **¿Por qué el escalado hacia abajo es más lento que hacia arriba?**
   - *Cómo verificarlo:* cortá la carga y cronometrá cuánto tarda en volver a 1 réplica. Revisá el campo `behavior` con `kubectl explain`.

5. **¿Qué pasa con las réplicas si re-aplicás el manifiesto del `Deployment` mientras el HPA está activo?**
   - *Cómo verificarlo:* dejá el HPA en 2 réplicas, re-aplicá el `Deployment` tal como lo tenés y observá el número de Pods.

## Lecturas

- [Resource Management (HPA) - Handbook](https://k8s.info/docs/intermediate/resources): requests, limits, la fórmula del HPA y metrics-server.
- [Horizontal Pod Autoscaling - Kubernetes](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/): algoritmo y `behavior` oficial.
- [metrics-server](https://github.com/kubernetes-sigs/metrics-server): instalación y requisitos.

## Listo cuando

- [ ] `kubectl top` devuelve métricas y el HPA muestra un target numérico (no desconocido).
- [ ] Con carga, la API sube a 2 réplicas; sin carga, vuelve a 1.
- [ ] Reprodujiste y entendiste el bloqueo por quota.
- [ ] Sabés qué campo del `Deployment` pelea con el HPA y cómo evitarlo.

## Notas y aprendizajes

<!-- completar a medida que itero -->
