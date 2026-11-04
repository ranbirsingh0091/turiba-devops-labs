# Lab 7 · Mission: run it on a cluster

## 1 · Deploy

- 🔮 Before my first `apply`: I predicted 3 Pods, starting with `api-`.
```text
$ kubectl -n course get deploy,rs,pods
NAME                   READY   STATUS    RESTARTS   AGE
api-675ddbf7b5-2nkx4   1/1     Running   0          14s
api-675ddbf7b5-9cctg   1/1     Running   0          14s
api-675ddbf7b5-pnhk7   1/1     Running   0          14s

$ curl -s localhost:8080/
{"name":"course-api","version":"0.1.0"}

