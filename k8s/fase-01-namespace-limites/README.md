# Fase 1 - Namespace y límites

## Objetivo

Crear un namespace propio para el proyecto y ponerle un presupuesto de recursos (`ResourceQuota`) y valores por defecto (`LimitRange`). Entender cómo estos límites cambian el comportamiento de los Pods que lleguen después.

## Recursos que se tocan

- **Se crean:** `Namespace`, `ResourceQuota`, `LimitRange`.
- **Se inspeccionan:** `qosClass` y `resources` de Pods descartables.

## Preguntas guía

1. **Si el namespace tiene una `ResourceQuota` de CPU y memoria, ¿qué pasa al crear un Pod que no declara `resources`?**
   - *Cómo verificarlo:* creá un Pod descartable sin `resources` en el namespace y leé el mensaje de error o el estado resultante.

2. **¿Qué `resources` termina teniendo un Pod sin requests/limits cuando existe un `LimitRange` con valores por defecto?**
   - *Cómo verificarlo:* creá el Pod y compará lo que escribiste con lo que muestra `-o yaml` en `spec.containers[].resources`.

3. **¿El `LimitRange` modifica Pods que ya existían cuando lo creaste?**
   - *Cómo verificarlo:* creá un Pod, aplicá después el `LimitRange` y compará sus `resources` con los de un Pod nuevo.

4. **¿Qué `qosClass` recibe un Pod según cómo combines requests y limits?**
   - *Cómo verificarlo:* probá tres combinaciones (sin nada, requests menores que limits, requests iguales a limits) y leé `status.qosClass`.

5. **¿Qué se lleva puesto borrar un `Namespace` y cómo ves cuánta quota consumiste antes de borrarlo?**
   - *Cómo verificarlo:* mirá `describe` de la `ResourceQuota` (usado vs. máximo). Después borrá un namespace descartable con algunos objetos adentro y listá qué queda.

## Lecturas

- [Resource Management - Handbook](https://k8s.info/docs/intermediate/resources): requests vs. limits, QoS, `ResourceQuota` y `LimitRange`.
- [Multi-Tenancy - Handbook](https://k8s.info/docs/advanced/multi-tenancy): cómo se combinan namespace, quota y límites por defecto, y qué NO aísla un namespace.
- [Resource Quotas - Kubernetes](https://kubernetes.io/docs/concepts/policy/resource-quotas/): campos y alcances de la quota.
- [Limit Ranges - Kubernetes](https://kubernetes.io/docs/concepts/policy/limit-range/): tipos de límites y defaults.
- [Namespaces - Kubernetes](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/): qué es y qué no es un namespace.

## Listo cuando

- [ ] Existe el namespace del proyecto y podés usarlo sin repetir `-n` en cada comando.
- [ ] Reprodujiste el rechazo de un Pod por la quota y entendés por qué ocurrió.
- [ ] Verificaste que el `LimitRange` completa los defaults en un Pod nuevo.
- [ ] Dejaste anotado cuánta quota necesitan Postgres, la API y su pico (pensando en las fases 3 y 5).

## Notas y aprendizajes

<!-- completar a medida que itero -->
