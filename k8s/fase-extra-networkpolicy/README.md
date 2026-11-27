# Fase extra - NetworkPolicy

## Objetivo

Restringir el acceso a Postgres para que solo la API pueda hablarle, y comprobar que la política realmente se aplica en tu cluster.

## Recursos que se tocan

- **Se crea:** `NetworkPolicy` (solo la API puede hablar con Postgres).
- **Se inspeccionan:** labels de Pods y namespaces, comportamiento del CNI.
- **Depende de:** Postgres ([fase 2](../fase-02-postgres/README.md)) y la API ([fase 3](../fase-03-api-dotnet/README.md)), con sus labels ya definidas.

## Preguntas guía

1. **Antes de escribir ninguna política: ¿tu cluster hace cumplir `NetworkPolicy`?**
   - *Cómo verificarlo:* en un namespace descartable aplicá una política restrictiva sobre un Pod de prueba y verificá si el tráfico realmente se corta.

2. **¿Qué cambia en el comportamiento de un Pod apenas una política lo selecciona?**
   - *Cómo verificarlo:* conectate a Postgres desde un Pod cualquiera antes y después de aplicar la política, y compará.

3. **¿Qué diferencia hay entre poner dos criterios de origen en la misma entrada y ponerlos en entradas separadas?**
   - *Cómo verificarlo:* probá ambas variantes con Pods con y sin las labels correctas, y anotá quién logra conectar en cada caso.

4. **¿Una política de red bloquea `kubectl port-forward` hacia Postgres?**
   - *Cómo verificarlo:* hacé port-forward con la política activa y conectate desde tu máquina. Pensá por qué pasa lo que ves.

## Lecturas

- [Security Policies - Handbook](https://k8s.info/docs/advanced/security): default-deny, DNS y semántica de los selectores en `NetworkPolicy`.
- [Network Policies - Kubernetes](https://kubernetes.io/docs/concepts/services-networking/network-policies/): comportamiento oficial y límites del recurso.
- [Multi-Tenancy - Handbook](https://k8s.info/docs/advanced/multi-tenancy): dónde encaja la red dentro del aislamiento por namespace.

## Listo cuando

- [ ] Comprobaste que tu CNI aplica `NetworkPolicy` (no fue una suposición).
- [ ] La API sigue conectando a Postgres y un Pod cualquiera no.
- [ ] Probaste la diferencia entre criterios en la misma entrada y en entradas separadas.
- [ ] Documentaste qué caminos de acceso a Postgres quedan abiertos (por ejemplo, port-forward) y por qué.

## Notas y aprendizajes

<!-- completar a medida que itero -->
