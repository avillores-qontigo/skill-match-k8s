# Roadmap de aprendizaje de Kubernetes

Cluster local (Rancher). Se construye de forma incremental una API .NET con dos endpoints:

- **Consultar**: proxy a <https://dolarapi.com/v1/dolares>.
- **Obtener oportunidades**: lee de Postgres lo que la lógica de negocio decidió guardar.

Cada fase tiene objetivo, preguntas guía (máximo 5), referencias y un checklist de "listo cuando". Los manifiestos los escribís vos: acá solo hay preguntas y criterios de éxito.

## Fases

| Fase | Recursos | Carpeta |
|------|----------|---------|
| 0 - Setup | Ninguno (cluster local, `kubectl`, `explain`, `describe`, Events) | [fase-00-setup](./fase-00-setup/README.md) |
| 1 - Namespace y límites | `Namespace`, `ResourceQuota`, `LimitRange` | [fase-01-namespace-limites](./fase-01-namespace-limites/README.md) |
| 2 - Postgres | `Secret`, `Service` headless, `StatefulSet` (+ PVC vía `volumeClaimTemplates`); se inspeccionan `StorageClass` y `PersistentVolume` | [fase-02-postgres](./fase-02-postgres/README.md) |
| 3 - API .NET | `ConfigMap`, `Deployment` (RollingUpdate), `Service` ClusterIP, probes; reutiliza `Secret` | [fase-03-api-dotnet](./fase-03-api-dotnet/README.md) |
| 4 - Gateway API | `Gateway`, `HTTPRoute` (+ CRDs y controlador); se inspecciona `GatewayClass` | [fase-04-gateway-api](./fase-04-gateway-api/README.md) |
| 5 - Autoscaling | `HorizontalPodAutoscaler` (autoscaling/v2, 1 a 2 réplicas) con metrics-server | [fase-05-autoscaling](./fase-05-autoscaling/README.md) |
| Extra - NetworkPolicy | `NetworkPolicy` (solo la API habla con Postgres) | [fase-extra-networkpolicy](./fase-extra-networkpolicy/README.md) |

## Índice rápido

- **Fase 0**: conocer tu cluster y sus herramientas antes de crear nada.
- **Fase 1**: ponerle fronteras y presupuesto de recursos al namespace del proyecto.
- **Fase 2**: levantar Postgres con identidad y almacenamiento estables.
- **Fase 3**: desplegar la API con configuración externa, probes y actualizaciones sin corte.
- **Fase 4**: exponer la API fuera del cluster con Gateway API.
- **Fase 5**: escalar la API de 1 a 2 réplicas según CPU.
- **Extra**: cerrar el acceso a Postgres para que solo lo alcance la API.

## Fuentes

- [The Kubernetes Visual Handbook](https://k8s.info/docs/intro)
- [Documentación oficial de Kubernetes](https://kubernetes.io/docs/)
