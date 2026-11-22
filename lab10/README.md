# Lab 10 · Mission: build it from code with OpenTofu

## 1 · The cluster from code

- 🔮 Prediction: how many resources will the first `tofu plan` add? I thought 1. **What happened:** It said `Plan: 1 to add, 0 to change, 0 to destroy.`
- End of first plan:
```text
Plan: 1 to add, 0 to change, 0 to destroy.

Changes to Outputs:
  + kube_context = (known after apply)

