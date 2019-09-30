---
title: 使用 docker/docker-compose/k8s/helm 部署 postgres
keywords: postgres,helm,docker,k8s,部署postgres,使用helm部署postgres

date: 2019-10-21 20:37

---

# 使用 helm 部署 postgres

## 前置知识

+ 

## 部署

``` shell
# 使用 helm v2 部署
$ helm install stable/postgresql

# 使用 helm v3 部署
$ helm install postgres stable/postgresql
NAME: postgres
LAST DEPLOYED: 2019-10-21 20:33:59.742081417 +0800 CST m=+2.283315306
NAMESPACE: default
STATUS: deployed
NOTES:
** Please be patient while the chart is being deployed **

PostgreSQL can be accessed via port 5432 on the following DNS name from within your cluster:

    postgres-postgresql.default.svc.cluster.local - Read/Write connection
To get the password for "postgres" run:

    export POSTGRES_PASSWORD=$(kubectl get secret --namespace default postgres-postgresql -o jsonpath="{.data.postgresql-password}" | base64 --decode)

To connect to your database run the following command:

    kubectl run postgres-postgresql-client --rm --tty -i --restart='Never' --namespace default --image docker.io/bitnami/postgresql:11.5.0-debian-9-r60 --env="PGPASSWORD=$POSTGRES_PASSWORD" --command -- psql --host postgres-postgresql -U postgres -p 5432



To connect to your database from outside the cluster execute the following commands:

    kubectl port-forward --namespace default svc/postgres-postgresql 5432:5432 &
    PGPASSWORD="$POSTGRES_PASSWORD" psql --host 127.0.0.1 -U postgres -p 5432
```
