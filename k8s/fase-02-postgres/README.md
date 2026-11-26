# Fase 2 - Postgres

## Objetivo

Levantar Postgres como `StatefulSet` con identidad de red estable y almacenamiento persistente, con la credencial guardada en un `Secret`. Entender qué sobrevive y qué no cuando el Pod o los objetos desaparecen.

## Recursos que se tocan

- **Se crean:** `Secret`, `Service` headless, `StatefulSet` con `volumeClaimTemplates` (esto genera el PVC).
- **Solo se inspeccionan:** `StorageClass`, `PersistentVolume`, el PVC generado.
- **Depende de:** el namespace y los límites de la [fase 1](../fase-01-namespace-limites/README.md). Si la quota exige requests/limits, el `StatefulSet` los necesita.

## Preguntas guía

1. **¿Por qué el `StatefulSet` pide un `Service` headless y qué nombre DNS obtiene cada Pod?**
   - *Cómo verificarlo:* desde un Pod efímero resolvé el nombre del Service y el nombre individual del Pod. Compará qué devuelve cada uno.

2. **¿Qué PVC genera `volumeClaimTemplates`, cómo se llama y cómo se enlaza con un PV y una `StorageClass`?**
   - *Cómo verificarlo:* listá PVC y PV, y corré `describe` sobre ambos. Mirá qué `StorageClass` por defecto tiene tu cluster y cómo aprovisiona.

3. **¿Qué datos sobreviven si borrás el Pod? ¿Y si borrás el `StatefulSet`? ¿Y el PVC?**
   - *Cómo verificarlo:* guardá una fila de prueba en Postgres y repetí cada borrado, de a uno, consultando si la fila sigue.

4. **Si cambiás el valor del `Secret` con la contraseña después de que Postgres ya inicializó su volumen, ¿qué contraseña vale?**
   - *Cómo verificarlo:* cambiá el `Secret`, reiniciá el Pod e intentá conectarte con la contraseña vieja y con la nueva.

5. **¿Qué pasa en el primer arranque si montás el volumen justo en el directorio de datos de Postgres?**
   - *Cómo verificarlo:* mirá los logs del primer arranque y el contenido del punto de montaje. Revisá en la referencia de Storage qué se menciona sobre PostgreSQL.

## Referencias

- [StatefulSets - Handbook](https://k8s.info/docs/core/statefulsets): identidad estable, headless Service y comportamiento de los PVC.
- [Storage (PV y PVC) - Handbook](https://k8s.info/docs/core/storage): aprovisionamiento dinámico, `StorageClass`, políticas de reclaim.
- [ConfigMaps y Secrets - Handbook](https://k8s.info/docs/core/config-secrets): cómo se consumen los Secrets y qué significa (y qué no) base64.
- [Service Discovery - Handbook](https://k8s.info/docs/intermediate/service-discovery): DNS de Services y Pods, headless.
- [StatefulSet - Kubernetes](https://kubernetes.io/docs/concepts/workloads/controllers/statefulset/): referencia de campos.
- [Persistent Volumes - Kubernetes](https://kubernetes.io/docs/concepts/storage/persistent-volumes/): ciclo de vida de PV y PVC.

## Listo cuando

- [ ] El Pod de Postgres está `Ready` y aceptás conexiones usando el `Secret`.
- [ ] Identificaste el PVC creado por la plantilla y la `StorageClass` que lo respalda.
- [ ] Comprobaste con una fila de prueba qué sobrevive a cada tipo de borrado.
- [ ] Resolvés por DNS el nombre estable de Postgres desde otro Pod del namespace.

## Notas y aprendizajes

<!-- completar a medida que itero -->
