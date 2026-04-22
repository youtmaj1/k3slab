# k3s Lab GitOps Platform

A minimal GitOps homelab setup for k3s with ArgoCD, Traefik, Sealed Secrets, monitoring, and PostgreSQL.

```
GitHub → ArgoCD → k3s Cluster
├── apps/
├── monitoring/
└── database
```

- GitOps flow: push changes to GitHub and ArgoCD syncs them into the cluster.
- Traefik handles ingress routing for app and platform traffic.
- Sealed Secrets keeps credentials encrypted in GitHub.
- Monitoring stack collects metrics, logs, and dashboards for observability.
- PostgreSQL provides the database for application data.

Deployment:

`git push → ArgoCD sync → cluster updates automatically`

- Example sealed secrets in this repo:
  - `k8s/database/postgresql-sealedsecret.yaml`
  - `k8s/apps/redis/redis-sealedsecret.yaml`
  - `k8s/monitoring/grafana-sealedsecret.yaml`
- The Sealed Secrets controller manifest is tracked under `k8s/gitops/sealed-secrets-controller.yaml`.
- No plaintext passwords are committed to YAML manifests or Helm values.
- CI avoids hardcoded secret values by using GitHub Actions secrets like `POSTGRES_PASSWORD`.

For production readiness, ensure the controller private key remains secure and rotate sealed secrets if the private key changes.

---

## 🧭 Troubleshooting

```bash
kubectl get pods -A
kubectl describe pod <pod-name> -n <namespace>
kubectl get pvc
kubectl logs <pod-name> -n <namespace>
```

---

## 📚 References
- [k3s](https://docs.k3s.io/)
- [ArgoCD](https://argo-cd.readthedocs.io/)
- [Ansible](https://docs.ansible.com/)
- [Loki](https://grafana.com/oss/loki/)
- [Prometheus](https://prometheus.io/)
- [Grafana](https://grafana.com/)
- [Longhorn](https://longhorn.io/)

---

**MIT License** — portfolio-ready documentation for platform engineering.
