# Kubernetes por práctica: de un namespace a una API con autoscaling

Repositorio de aprendizaje incremental. La idea no es "hacer que funcione", sino entender **por qué** cada recurso existe, qué pasa cuando falla y cómo se diagnostica.

Cada fase agrega una capa sobre la anterior, sobre un mismo sistema que va creciendo: una API .NET que consulta cotizaciones del dólar, decide cuáles son oportunidades y las guarda en Postgres.

> **Aviso:** es un proyecto de práctica de infraestructura. La lógica de "oportunidades" es un ejercicio y no constituye asesoramiento financiero.

---

## Qué se construye

Una API .NET con dos endpoints:

| Endpoint | Qué hace |
|---|---|
| **Consultar** | Hace de proxy a [DolarApi](https://dolarapi.com/docs/argentina/operations/get-dolares.html) (`GET /v1/dolares`) y aplica la lógica de negocio: guarda la oportunidad en Postgres o la descarta |
| **Obtener oportunidades** | Lee de Postgres las oportunidades guardadas |

### Arquitectura objetivo

```
                          Internet
                              │
                  ┌───────────▼───────────┐
                  │ Gateway + HTTPRoute   │  fase 4
                  └───────────┬───────────┘
 ┌────────────────────────────┼─────────────────────────────┐
 │ Namespace (Quota + LimitRange)  fase 1                   │
 │                            │                             │
 │                  ┌─────────▼─────────┐                   │
 │                  │ Service (API)     │                   │
 │                  └─────────┬─────────┘                   │
 │          ┌─────────────────▼─────────────────┐           │
 │          │ Deployment API .NET (HPA 1→2)     │ fases 3,5 │
 │          │ ConfigMap + Secret + probes       │           │
 │          └───────┬───────────────────┬───────┘           │
 │                  │                   │                   │
 │       ┌──────────▼──────────┐        └──► DolarApi       │
 │       │ StatefulSet Postgres│             (externo)      │
 │       │ Secret + PVC        │  fase 2                    │
 │       └─────────────────────┘                            │
 └──────────────────────────────────────────────────────────┘
```

---

## Roadmap

| Fase | Recursos que se crean | Objetivo de aprendizaje |
|---|---|---|
| [0. Setup](k8s/fase-00-setup/README.md) | Ninguno (cluster local y `kubectl`) | Entorno descartable y hábitos de diagnóstico |
| [1. Namespace y límites](k8s/fase-01-namespace-limites/README.md) | `Namespace`, `ResourceQuota`, `LimitRange` | Cómo Kubernetes acepta o rechaza workloads según recursos |
| [2. Postgres](k8s/fase-02-postgres/README.md) | `Secret`, `Service` headless, `StatefulSet` (+ PVC vía `volumeClaimTemplates`) | Estado persistente y almacenamiento dinámico |
| [3. API .NET](k8s/fase-03-api-dotnet/README.md) | `ConfigMap`, `Deployment`, `Service` | Configuración, probes y despliegues sin downtime |
| [4. Gateway API](k8s/fase-04-gateway-api/README.md) | `Gateway`, `HTTPRoute` (+ CRDs y controlador) | Exposición externa y separación de roles |
| [5. Autoscaling](k8s/fase-05-autoscaling/README.md) | `HorizontalPodAutoscaler` | Escalado por métricas y su interacción con la quota |
| [Extra: NetworkPolicy](k8s/fase-extra-networkpolicy/README.md) | `NetworkPolicy` | Aislamiento: solo la API habla con Postgres |

---

## Qué se aprende en cada fase

### Fase 0: Setup
- Crear y destruir un cluster local con un solo comando.
- Leer el estado del cluster: `get`, `describe`, `Events`, `logs`, `exec`.
- Consultar cualquier campo con `kubectl explain` sin salir de la terminal.
- Qué corre en `kube-system` y para qué.

### Fase 1: Namespace y límites
- Diferencia entre `requests` y `limits` para el scheduler y para el kernel.
- Por qué una quota de cómputo obliga a declarar recursos.
- Qué completa un `LimitRange` (defaults, mínimos, máximos) y cuándo rechaza un pod.
- QoS classes y admission control.

### Fase 2: Postgres
- Cómo se enlazan `StatefulSet`, `volumeClaimTemplates`, PVC, PV y `StorageClass`.
- Por qué un StatefulSet y no un Deployment para una base de datos.
- Trampas conocidas: `PGDATA`, `lost+found`, permisos con `fsGroup`.
- Qué pasa con los datos al borrar el pod, el StatefulSet o el PVC (`persistentVolumeClaimRetentionPolicy`).
- DNS estable de los pods mediante el Service headless.

### Fase 3: API .NET
- Inyectar configuración como variables de entorno y como volumen, y compararlas.
- Mapeo de `ConnectionStrings__Default` a `IConfiguration`.
- Liveness, readiness y startup probes, y por qué la readiness no debería depender de un servicio externo.
- Estrategia `RollingUpdate` (`maxSurge`, `maxUnavailable`) y qué pasa al cambiar un ConfigMap.
- Salida a internet desde un pod (egress hacia DolarApi).

### Fase 4: Gateway API
- Instalar CRDs y un controlador.
- Roles: quién administra la infraestructura (`Gateway`) y quién define rutas (`HTTPRoute`).
- Relación entre `parentRefs` y `backendRefs`.
- Diferencias conceptuales con Ingress.

### Fase 5: Autoscaling
- metrics-server y `kubectl top`.
- Contra qué se calcula el porcentaje de utilización del HPA.
- Observar el escalado y el desescalado bajo carga.
- Qué pasa cuando la quota de la fase 1 impide crear el segundo pod.

### Extra: NetworkPolicy
- Modelo "todo permitido por defecto" y cómo se pasa a "denegar y permitir".
- Verificar que un CNI realmente hace cumplir las políticas.
- Decidir qué se permite de egress en la API.

---

## Estructura del repositorio

```
.
├── README.md
└── k8s/
    ├── README.md
    ├── fase-00-setup/README.md
    ├── fase-01-namespace-limites/README.md
    ├── fase-02-postgres/README.md
    ├── fase-03-api-dotnet/README.md
    ├── fase-04-gateway-api/README.md
    ├── fase-05-autoscaling/README.md
    └── fase-extra-networkpolicy/README.md
```

Cada carpeta de fase contiene su `README.md` con: objetivo, recursos que se tocan, preguntas guía, referencias, criterios de "listo cuando" y un espacio de notas. Los manifiestos se agregan a medida que se resuelve cada fase.

---

## Cómo trabajar cada fase

1. **Leer** las preguntas guía de la fase antes de escribir nada.
2. **Predecir** el resultado de cada experimento antes de ejecutarlo.
3. **Escribir** los manifiestos a mano, sin copiar soluciones completas.
4. **Provocar fallos a propósito** (rechazos de quota, pods sin recursos, borrado de pods, caída del proveedor externo) y diagnosticarlos con `describe` y `Events`.
5. **Verificar** el checklist "Listo cuando".
6. **Anotar** qué se aprendió y qué sorprendió en la sección de notas.

El hábito central: ante cualquier problema, `kubectl describe` y la sección `Events` primero.

---

## Prerrequisitos

- Docker (o el runtime que use el cluster local elegido).
- Un cluster local: kind, k3d o minikube.
- `kubectl`.
- SDK de .NET para construir la imagen de la API.
- Conocimientos previos de contenedores y de desarrollo de APIs HTTP.

**Antes de cada sesión:** verificá el contexto activo para no aplicar manifiestos sobre un cluster real.

```bash
kubectl config current-context
```

---

## Cómo evoluciona la quota

| Fase | Se suma a la `ResourceQuota` |
|---|---|
| 1 | CPU, memoria y cantidad de `pods` |
| 2 | `requests.storage` y `persistentvolumeclaims` |
| 3 | `configmaps`, `secrets`, `services` (opcional) |
| 5 | Dimensionarla para que el HPA falle a propósito, y luego ajustarla |

---

## Convenciones

- Un solo namespace para todo el sistema, creado en la fase 1.
- Manifiestos declarativos aplicados con `kubectl apply`.
- Los nombres de `StorageClass` dependen del cluster local (por ejemplo `standard` en kind y minikube, `local-path` en k3d), así que no se hardcodean valores de proveedores cloud.
- Los secretos de práctica no se versionan con valores reales.

---

## Referencias

- [The Kubernetes Visual Handbook](https://k8s.info/docs/intro): guía interactiva usada como material de consulta.
- [Documentación oficial de Kubernetes](https://kubernetes.io/docs/)
- [Gateway API](https://gateway-api.sigs.k8s.io/)
- [DolarApi: endpoint de dólares](https://dolarapi.com/docs/argentina/operations/get-dolares.html)
