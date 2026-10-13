# Validation

Run `./bootstrap.sh` from the repository root to create the namespace, storage,
configuration, secret, application, and service resources. The script waits for
the Deployment rollout to finish.

## Confirm the app is running

Check that the Deployment is available and its Pods are ready:

```sh
kubectl get deployment,pods -n todoapp
kubectl rollout status deployment/todoapp -n todoapp
```

To make a request from your machine, forward the service port:

```sh
kubectl port-forward -n todoapp service/todoapp-service 8080:80
```

In another terminal, request the health endpoint:

```sh
curl -f http://127.0.0.1:8080/api/health
```

## Confirm ConfigMap files and values

Each ConfigMap key is mounted as a file with the same name under
`/app/configs`. List the files in deterministic (name-sorted) order, then check
the current ConfigMap key and value:

```sh
kubectl exec -n todoapp deployment/todoapp -- sh -c 'find /app/configs -maxdepth 1 -type f -printf "%f\n" | sort'
kubectl exec -n todoapp deployment/todoapp -- cat /app/configs/PYTHONUNBUFFERED
```

The file list should contain `PYTHONUNBUFFERED`, and its contents should be
`1`. The ConfigMap volume is mounted read-only at `/app/configs`.

## Confirm Secret files

Each Secret key is mounted as a file with the same name under `/app/secrets`.
Check that the expected file exists and is non-empty without printing the
secret value:

```sh
kubectl exec -n todoapp deployment/todoapp -- sh -c 'test -s /app/secrets/SECRET_KEY && echo "SECRET_KEY is mounted"'
```

The Secret volume is mounted read-only at `/app/secrets`.
