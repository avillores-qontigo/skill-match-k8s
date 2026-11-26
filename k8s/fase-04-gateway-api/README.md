# Fase 4 - Gateway API

## Objetivo

Exponer la API fuera del cluster usando Gateway API: instalar los CRDs y un controlador, y enrutar tráfico HTTP hacia el `Service` de la fase 3.

## Recursos que se tocan

- **Se crean:** `Gateway`, `HTTPRoute`.
- **Se instalan:** CRDs de Gateway API y un controlador de Gateway.
- **Solo se inspecciona:** `GatewayClass`.
- **Depende de:** el `Service` ClusterIP de la [fase 3](../fase-03-api-dotnet/README.md) como backend de la ruta.

## Preguntas guía

1. **¿Quién provee cada pieza (`GatewayClass`, controlador, `Gateway`) y qué condiciones del `Gateway` te dicen si está listo?**
   - *Cómo verificarlo:* inspeccioná la `GatewayClass` y leé las condiciones en el `status` de tu `Gateway` con `describe`.

2. **¿Qué pasa si intentás crear un `Gateway` antes de instalar los CRDs, y cómo sabés qué versión de CRDs tenés?**
   - *Cómo verificarlo:* probá aplicar el recurso sin los CRDs y leé el error. Después de instalarlos, mirá los CRDs y sus anotaciones.

3. **¿Cómo sabés que tu `HTTPRoute` fue aceptada y que su backend se resolvió?**
   - *Cómo verificarlo:* mirá el `status` de la ruta (los padres y la condición de referencias resueltas). Rompé a propósito el nombre del Service y observá cómo cambia.

4. **¿Cómo se comparan los paths de las rutas con los de tus dos endpoints, y qué pasa con paths parecidos?**
   - *Cómo verificarlo:* hacé requests a los dos endpoints y a paths con prefijos similares. Revisá cuáles llegan a la API y cuáles no.

5. **¿Cómo se alcanza el `Gateway` desde tu máquina en un cluster local?**
   - *Cómo verificarlo:* mirá el `Service` que crea el controlador (tipo y dirección externa) y probá la forma de llegar desde el host en tu Rancher.

## Referencias

- [Gateway API - Handbook](https://k8s.info/docs/advanced/gateway-api): roles, listeners, `parentRefs`, `backendRefs` y cómo leer el `status`.
- [Ingress - Handbook](https://k8s.info/docs/intermediate/ingress): sirve para entender qué problema resuelve Gateway API frente a Ingress.
- [Gateway API - Kubernetes](https://kubernetes.io/docs/concepts/services-networking/gateway/): concepto oficial y componentes.
- [Documentación del proyecto Gateway API](https://gateway-api.sigs.k8s.io/): instalación de CRDs y lista de implementaciones.

## Listo cuando

- [ ] Los CRDs y el controlador están instalados y la `GatewayClass` figura aceptada.
- [ ] El `Gateway` está programado y la `HTTPRoute` figura aceptada con referencias resueltas.
- [ ] Llegás a "Consultar" y "Obtener oportunidades" desde fuera del cluster.
- [ ] Sabés diagnosticar una ruta que no recibe tráfico mirando solo el `status`.

## Notas y aprendizajes

<!-- completar a medida que itero -->
