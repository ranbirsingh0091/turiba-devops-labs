# Lab 8 · Mission: run the whole app

## 1 · Database with storage

- 🔮 Prediction: Before the database Pod starts, will the PVC be `Bound` or `Pending`?
  **My prediction:** I thought it would be `Pending` because nothing was using it yet.
  **What happened:** It turned out to be `Bound` immediately! This is because kind uses the `standard` storage class, which provisions volumes automatically.

- 🔮 Prediction: After deleting the database Pod, will the todo survive?
  **My prediction:** Yes, because the data is on a PersistentVolumeClaim.
  **What happened:** The todo survived perfectly. Here is the output:

```text
$ kubectl -n course get pods -l app=db
NAME                 READY   STATUS    RESTARTS   AGE
db-5dbcccc5f-6nqj8   1/1     Running   0          3m22s
$ kubectl -n course delete pod -l app=db
pod "db-5dbcccc5f-6nqj8" deleted
$ kubectl -n course get pods -l app=db
NAME                 READY   STATUS    RESTARTS   AGE
db-5dbcccc5f-j8t8v   1/1     Running   0          16s
$ curl -s localhost:8080/api/todos
[{"id":1,"title":"survive a restart","done":false,"created_at":"2026-11-15T09:47:37.114Z"}]

