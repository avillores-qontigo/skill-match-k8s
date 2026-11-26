# Fase 0 - Setup

## Objetivo

Tener un cluster local de Rancher funcionando y saber inspeccionarlo con `kubectl`, `kubectl explain`, `describe` y Events. Sin esto, el resto de las fases se vuelve adivinar.

## Recursos que se tocan

- Ninguno se crea como parte del roadmap.
- Solo se inspeccionan: contexto de `kubeconfig`, nodos, namespaces del sistema y recursos disponibles en la API.
- Para los experimentos podés usar Pods descartables con `kubectl run` y borrarlos después.

## Preguntas guía

1. **¿Contra qué cluster está hablando tu `kubectl` ahora mismo y cómo te protegés de ejecutar algo en el cluster equivocado?**
   - *Cómo verificarlo:* mirá los contextos de tu `kubeconfig`, cambiá de contexto (si tenés más de uno) y observá qué cambia en la salida de `kubectl get nodes`.

2. **¿Qué diferencia hay entre `spec` y `status` en un objeto, y qué campos de un Pod son obligatorios?**
   - *Cómo verificarlo:* usá `kubectl explain` sobre `pod` y sus subcampos, y comparalo con el YAML que devuelve un Pod real (`-o yaml`) creado con `kubectl run`.

3. **¿Cómo llega al cluster una imagen que construiste en tu máquina?**
   - *Cómo verificarlo:* creá un Pod descartable con una imagen local y jugá con la política de pull de imagen. Mirá cómo cambia el estado del Pod. Fijate qué runtime de contenedores usa tu Rancher.

4. **Cuando un Pod no arranca, ¿qué Events aparecen, en qué orden y cuánto tiempo se conservan?**
   - *Cómo verificarlo:* lanzá un Pod con una imagen que no existe, corré `describe` y listá los Events del namespace ordenados por tiempo.

5. **¿Qué trae tu cluster "de fábrica" que vas a necesitar más adelante (API de métricas, CRDs de Gateway API, controladores de ingreso)?**
   - *Cómo verificarlo:* listá los recursos que expone la API, los CRDs instalados y probá si `kubectl top nodes` responde. Anotá qué falta.

## Referencias

- [Setting Up Your Lab - Handbook](https://k8s.info/docs/foundations/setup): compara herramientas de cluster local, contextos y la trampa de las imágenes locales.
- [Troubleshooting Guide - Handbook](https://k8s.info/docs/intermediate/troubleshooting): método de diagnóstico de arriba hacia abajo (estado, Events, logs, exec).
- [kubectl quick reference](https://kubernetes.io/docs/reference/kubectl/quick-reference/): para encontrar flags de salida y de orden.
- [Rancher Desktop docs](https://docs.rancherdesktop.io/): detalles del runtime y del Kubernetes que trae tu instalación.

## Listo cuando

- [ ] Sabés decir en qué contexto estás y podés cambiarlo a propósito.
- [ ] Usaste `kubectl explain` para encontrar al menos un campo sin buscar en internet.
- [ ] Provocaste una falla de arranque y la diagnosticaste con `describe` y Events.
- [ ] Tenés anotado el inventario de capacidades del cluster (métricas, CRDs, StorageClass por defecto).

## Notas y aprendizajes

<!-- completar a medida que itero -->
