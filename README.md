# bcs-lke

Infrastructure (k8s on Linode Cloud) related code for BCS (bettercallshao) website.

## Set up CockroachDB

Reference: https://www.cockroachlabs.com/docs/stable/orchestrate-cockroachdb-with-kubernetes.html#manual

* Set up kubectl
* Create the pods
```
kubectl create ns cockroachdb
kubectl -n cockroachdb create -f cockroachdb-statefulset-secure.yaml
```
* Approve the CSRs
```
kubectl -n cockroachdb get csr
kubectl -n cockroachdb certificate approve node.cockroachdb-0
...
```
* Join the clusters with a Job
```
kubectl -n cockroachdb create -f cluster-init-secure.yaml
```
* Create a permanent pod for queries
```
kubectl -n cockroachdb create -f client-secure.yaml
```
* Get a SQL shell
```
kubectl exec -it cockroachdb-client-secure -- ./cockroach sql --certs-dir=/cockroach-certs --host=cockroachdb-public
```
* Create users (one for admin access, one as service account)
```
CREATE USER <user> WITH PASSWORD '<passwd>';
CREATE DATABASE <db>;
GRANT ALL ON DATABASE <db> TO <user>;
```
* Port forward for local access
```
kubectl -n cockroachdb port-forward svc/cockroachdb 8080:8080
```
* Install DBeaver which has support for CockroachDB
