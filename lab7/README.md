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
```

## 2 · Heal and scale

- 🔮 After deleting one Pod, two seconds later I expected to see **3** Pods because Kubernetes recreates them to match the desired state.
```text
$ kubectl -n course get pods  (after the delete)
NAME                   READY   STATUS    RESTARTS   AGE
api-675ddbf7b5-9cctg   1/1     Running   0          11m
api-675ddbf7b5-dmjn8   1/1     Running   0          6s
api-675ddbf7b5-pnhk7   1/1     Running   0          11m

$ kubectl -n course get pods  (with replicas: 5)
NAME                   READY   STATUS    RESTARTS   AGE
api-675ddbf7b5-9cctg   1/1     Running   0          13m
api-675ddbf7b5-dmjn8   1/1     Running   0          98s
api-675ddbf7b5-gsnrx   1/1     Running   0          7s
api-675ddbf7b5-ln9vr   1/1     Running   0          7s
api-675ddbf7b5-pnhk7   1/1     Running   0          13m
```

## 3 · Roll and roll back

- 3a: I rolled from the tag `0.2.0` to the tag `873def9`.
```text
$ kubectl -n course rollout status deployment/api
Waiting for deployment "api" rollout to finish: 1 out of 3 new replicas have been updated...
```
- 3b: 🔮 With the tag 9.9.9, I predicted that **3** old Pods keep running, because the new ones would fail to pull.
```text
$ kubectl -n course get pods  (during the stall)
NAME                   READY   STATUS         RESTARTS   AGE
api-675ddbf7b5-9cctg   1/1     Running        0          29m
api-675ddbf7b5-dmjn8   1/1     Running        0          18m
api-675ddbf7b5-pnhk7   1/1     Running        0          29m
api-754f4f675d-nf4nf   0/1     ErrImagePull   0          10s

$ kubectl -n course rollout undo deployment/api
deployment.apps/api rolled back
```
The warning means the cluster rolled back to the previous version, but the YAML file on my laptop still had the broken tag. I fixed this by running `git restore k8s/` and applying it again so Git and the cluster agreed.

## 4 · What broke and how I found it

- **Fault 1: Invalid image tag.** The deployment used `0.2.O` (letter O). I saw `ErrImagePull` when I ran `kubectl -n course get pods`. I checked the image name with `kubectl -n course describe pod` and saw the error. I fixed it by changing the tag back to `0.2.0` and committed it.
- **Fault 2: Service selector mismatch.** The Service selected `app.kubernetes.io/name: api`, but the Pods were labeled `app: api`. The port-forward printed nothing, and `curl` failed. I fixed the selector to `app: api` and committed it.
- **Fault 3: Wrong targetPort.** The Service sent traffic to `8080`, but the API listens on `5000`. `curl` returned `Empty reply from server`. I changed `targetPort` to `5000` and committed it.
