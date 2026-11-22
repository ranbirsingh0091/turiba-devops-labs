# Lab 10 · Mission: build it from code with OpenTofu

## 1 · The cluster from code

- 🔮 Prediction: how many resources will the first `tofu plan` add? I thought 1. **What happened:** It said `Plan: 1 to add, 0 to change, 0 to destroy.`
- End of first plan:
```text
Plan: 1 to add, 0 to change, 0 to destroy.

Changes to Outputs:
  + kube_context = (known after apply)
``b
- `tofu output`:
```text
kube_context = "kind-course"
```
- **Change:** `tofu apply -var api_replicas=2` showed `~ value = "3" -> "2"`. `helm history course-app -n course` showed:

```text
REVISION  UPDATED                         STATUS      CHART             APP VERSION  DESCRIPTION
1         Thu Nov 19 19:27:33 2026        superseded  course-app-0.1.0  0.2.0        Install complete
2         Fri Nov 20 21:35:48 2026        deployed    course-app-0.1.0  0.2.0        Upgrade complete
```
- `kubectl -n course get deployment api` showed `2/2`.
- **Drift (Secret):** `kubectl -n course delete secret db-secret` then `tofu plan -var api_replicas=2` showed:

```text
Plan: 1 to add, 0 to change, 0 to destroy.
```
OpenTofu sees the Secret is gone and plans to recreate it.
- **Drift (Deployment):** `kubectl -n course scale deployment api --replicas=1` then `tofu plan -var api_replicas=2` showed the **exact same plan** (only the Secret). The Deployment is inside the Helm release, so OpenTofu doesn't see the drift.
- **Bring it back:** `tofu apply` (no `-var`) showed `1 to add, 1 to change`. After it finished, `kubectl -n course get deployment api` showed `3/3`. The Helm upgrade also undid the manual scale.
- **Rebuild from zero:** `tofu destroy -auto-approve` then `tofu apply -auto-approve` took `real 1m50.080s. The todo was gone because the PVC is deleted with the cluster. This is expected: data is not code.

## 3 · What broke and how I found it

- **Fault 1: Wrong provider** | `Inconsistent dependency lock file` and `no version is selected` | `tofu plan` | The teammate changed `source = "tehcyx/kind" to hashicorp/kind`. There is no such provider. I fixed it back to `tehcyx/kind` in `versions.tf`.
- **Fault 2: Wrong chart path** | `Error locating chart` and `Unable to locate chart ./../chart/course-app` | `tofu plan` | The teammate changed `chart = "${path.module}/../charts/course-app"` to `../chart/course-app` (missing the 's'). I fixed it back to `charts`.
- **Fault 3: Wrong namespace** | `Plan: 1 to add, 0 to change, 1 to destroy` with `-/+` and `# forces replacement` | `tofu plan` | The teammate changed `namespace` to "courses". A Helm release can't move namespaces, so OpenTofu would destroy and recreate it. I fixed it back to `kubernetes_namespace_v1.course.metadata[0].name`.
