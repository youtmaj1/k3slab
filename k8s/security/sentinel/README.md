# Kyverno Health Sentinel

This folder contains a Kubernetes-native health sentinel for Kyverno bootstrap and runtime availability.

## Detection flow

The health sentinel runs as a controller-style Deployment that watches Kyverno control-plane resources and exposes Prometheus metrics for continuous observability.

- ArgoCD `Application` named `kyverno` exists in namespace `gitops`
- Kubernetes namespace `kyverno` exists
- Kyverno pods `kyverno-admission-controller` and `kyverno-background-controller` are present
- Kyverno CRDs exist: `clusterpolicies.policy.kyverno.io`, `policyexceptions.policy.kyverno.io`
- Kyverno validating webhook configuration exists

The agent reacts to watch events and reconciles on changes, emitting metrics and logging failures immediately.

## Failure mode coverage

| Failure mode | Detection |
|---|---|
| Kyverno app missing | `kubectl get application kyverno -n gitops` |
| Kyverno namespace missing | `kubectl get namespace kyverno` |
| Kyverno pods not running | `kubectl get pod -n kyverno ...` |
| Kyverno CRDs absent | `kubectl get crd ...` |
| Webhook not registered | `kubectl get validatingwebhookconfigurations | grep kyverno` |

## Remediation

A companion controller deployment runs continuously and performs auto-remediation when cluster health checks fail.

- re-syncs the Kyverno Application via ArgoCD annotation refresh
- re-applies Kyverno bootstrap by refreshing the ApplicationSet when Kyverno is missing
- restarts unhealthy Kyverno pods
- includes cooldown logic to avoid remediation storms

## Alerting

The sentinel exposes Prometheus metrics and includes alert rules for:
- Kyverno Application missing
- Kyverno namespace missing
- Kyverno webhook missing
- Kyverno CRDs missing

Alerting is enabled via `PrometheusRule` and can be extended with Alertmanager or webhook receivers.

## GitOps management

The sentinel is managed via ArgoCD by including `k8s/security/sentinel` in the `ApplicationSet` path list.
