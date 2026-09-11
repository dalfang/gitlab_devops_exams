# Kubernetes manifests

These manifests were generated from `docker-compose.yml` with
[`kompose`](https://kompose.io) (`kompose convert`) and then hand-adapted for
a real cluster:

- dropped the dev-only bind mounts and `--reload` uvicorn flag (baked into the
  image instead, see each service's `Dockerfile`)
- added a `Service` for `users` and `orders` (kompose skips services with no
  `ports:` in the compose file, but the gateway needs to reach them by DNS
  name on port 8000, exactly like the compose network does)
- `gateway` is exposed with a `NodePort` Service (it's the only entrypoint,
  matching the compose file's `8001:8000` published port); `users` and
  `orders` stay `ClusterIP`-only, internal-only, matching the app's design
  ("only the gateway can reach the internal services")

These raw manifests are kept here as the intermediate deliverable asked for
in the exercise. The pipeline itself deploys via the Helm chart in
[`../helm/examen`](../helm/examen), which is templated from these same
Deployments/Services.

Apply manually to any namespace with:

```bash
kubectl apply -n <dev|qa|staging|prod> -f k8s/
```
