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
## 4 · What broke and how I found it

- **Fault 1: Invalid image tag.** The deployment used `0.2.O` (letter O). I saw `ErrImagePull` when I ran `kubectl -n course get pods`. I checked the image name with `kubectl -n course describe pod` and saw the error. I fixed it by changing the tag back to `0.2.0` and committed it.
- **Fault 2: Service selector mismatch.** The Service selected `app.kubernetes.io/name: api`, but the Pods were labeled `app: api`. The port-forward printed nothing, and `curl` failed. I fixed the selector to `app: api` and committed it.
- **Fault 3: Wrong targetPort.** The Service sent traffic to `8080`, but the API listens on `5000`. `curl` returned `Empty reply from server`. I changed `targetPort` to `5000` and committed it.

