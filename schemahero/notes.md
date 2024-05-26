kubectl krew install schemahero
kubectl schemahero version
kubectl schemahero install
kubectl get pods -n schemahero-system

kubectl create ns schemahero-tutorial
kubectl apply -n schemahero-tutorial -f https://raw.githubusercontent.com/schemahero/schemahero/main/examples/tutorial/postgresql/postgresql-11.8.0.yaml

# kubectl exec -it -n schemahero-tutorial postgresql-0 -- psql -U airlinedb-user -d airlinedb

## Adminer
helm upgrade --install adminer-release cetic/adminer
k apply -f ./adminer.yaml
## https://adminer.127.0.0.1.nip.io/
# system: PostgreSQL
# server: postgresql.schemahero-tutorial.svc.cluster.local
# username: "airlinedb-user"
# password: "password"
# database: "airlinedb"



kubectl apply -f airline-db.yaml
kubectl get databases -n schemahero-tutorial

kubectl apply -f airline-table.yaml
kubectl schemahero describe migration eaa36ef -n schemahero-tutorial

## apply the migration
kubectl schemahero -n schemahero-tutorial approve migration eaa36ef
kubectl schemahero describe migration eaa36ef -n schemahero-tutorial
kubectl schemahero get migrations -n schemahero-tutorial

kubectl apply -f ./schedule-table.yaml -n schemahero-tutorial
kubectl schemahero get migrations -n schemahero-tutorial



