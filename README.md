# solomon585858_platform
solomon585858 Platform repository

## Homework 1
 - В рамках первого ДЗ был подготовлен образ docker на базе nginx для web сервера
 - Добавлены манифесты для web сервера на базе Docker образа
 - Добавлены манифесты для frontend компонента приложения Hipster Shop
 - Все файлы можно найти в папке kubernetes-intro

## Homework 2
 - Установлен локальный кластер kind
 - Подготовлен манифест Replicaset frontend-replicaset.yaml
 - Подготовлен Docker образ с тэгами v0.0.1/v0.0.2 для сервиса paymentservice
 - Подготовлен манифест Replicaset payment-replicaset.yaml
 - Подготовлен манифест Deployment payment-deployment.yaml
 - Выполнен отказ на предыдущую версию Deployment для сервиса paymentservice
 - С использованием параметров maxSurge и maxUnavailable реализованы дваr сценария - blue-green, Reverse Rolling Update
 - Подготовлен манифест Deployment frontend-deployment для сервиса frontend c Readiness Probe
 - Подготовлен манифест DaemonSet/Namespace для развертывания Node Exporter для Prometheus на всех узлах кластера Kubernetes

## Homework 3
 - Подготовлены манифесты для Service Accounts, Namespaces, Rolebindings, Clusterrolebindings для трех разных задач

## Homework 4
 - Проведена работа с тестовым веб-приложением
 - Настроен доступ к приложению извне кластера

## Homework 5
 - Подготовлены манифесы для StatefulSet, HeadlessService

## Homework 6
 - Развернут кластер GKE
 - Подготовлены HELM чарты для сервисов hipster-shop
 - Подготовлены манифесты с помощью kubecfg/kustomize

## Homework 7
 - Подготовлены CR, CRD, Operator для MySQL

```
$ kubectl get jobs
NAME                         COMPLETIONS   DURATION   AGE
backup-mysql-instance-job    1/1           4s         2m21s
restore-mysql-instance-job   1/1           20s        89s
```
```
$ export MYSQLPOD=$(kubectl get pods -l app=mysql-instance -o jsonpath="{.items[*].metadata.name}")
$ kubectl exec -it $MYSQLPOD -- mysql -potuspassword -e "select * from test;" otus-database
+----+-------------+
| id | name        |
+----+-------------+
|  1 | some data   |
|  2 | some data-2 |
+----+-------------+
```

## Homework 8
 - Подготовлены Deployment, Service, ServiceMonitor и СonfigMap для Nginx
 - Использован Nginx Exporter для передачи метрик в Prometheus
 - Развернут Prometheus на кластере Kubernetes с помощью Kubernetes Operator
```
Deploy Prometheus:
$ kubectl create namespace monitoring
$ helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
$ helm repo update 
$ helm upgrade --namespace monitoring --install kube-stack-prometheus prometheus-community/kube-prometheus-stack --set prometheus-node-exporter.hostRootFsMount.enabled=false
```
```
Connect to Prometheus:
$ kubectl port-forward --namespace monitoring svc/kube-stack-prometheus-kube-prometheus 9090:9090
```
```
Connect to Grafana:
$ kubectl get secret --namespace monitoring kube-stack-prometheus-grafana -o jsonpath='{.data.admin-user}' | base64 -d
$ kubectl get secret --namespace monitoring kube-stack-prometheus-grafana -o jsonpath='{.data.admin-password}' | base64 -d
$ kubectl port-forward --namespace monitoring svc/kube-stack-prometheus-grafana 8080:80
```
[![Nginx-Prometheus-Exporter-Dashboard.png](https://i.postimg.cc/nrjrkXvK/Nginx-Prometheus-Exporter-Dashboard.png)](https://postimg.cc/K3bmcvp4)


## Homework 9
 - Проведено ознакомление с инструментами Flagger, Flux, Istio


```
$ kubectl describe pod -l app=frontend -n microservices-demo
Name:         frontend-7bf54b569-qkklb
Namespace:    microservices-demo
...
Events:
  Type    Reason     Age   From               Message
  ----    ------     ----  ----               -------
  Normal  Scheduled  20s   default-scheduler  Successfully assigned microservices-demo/frontend-7bf54b569-qkklb to gke-mycluster-default-pool-be1086e0-2nml
  Normal  Pulling    17s   kubelet            Pulling image "solomon5858558/frontend:v0.0.2"
  Normal  Pulled     15s   kubelet            Successfully pulled image "solomon5858558/frontend:v0.0.2" in 2.630767651s
  Normal  Created    15s   kubelet            Created container server
  Normal  Started    15s   kubelet            Started container server
```
  
```$ helm history frontend -n microservices-demo
REVISION        UPDATED                         STATUS          CHART           APP VERSION     DESCRIPTION
1               Wed Aug 24 10:42:00 2022        superseded      frontend-0.21.0 1.16.0          Install complete
2               Wed Aug 24 10:56:51 2022        deployed        frontend-0.21.0 1.16.0          Upgrade complete
```
```
Events:
  Type     Reason  Age                From     Message
  ----     ------  ----               ----     -------
...
  Normal   Synced  5m30s              flagger  New revision detected! Scaling up frontend.microservices-demo
  Normal   Synced  4m30s              flagger  Starting canary analysis for frontend.microservices-demo
  Normal   Synced  4m30s              flagger  Advance frontend.microservices-demo canary weight 5
  Normal   Synced  3m30s              flagger  Advance frontend.microservices-demo canary weight 10
  Normal   Synced  2m20s               flagger  Advance frontend.microservices-demo canary weight 15
  ...
```

## Homework 10
 - Проведено ознакомление с инструментами Vault, Consul
```
$ helm status vault
NAME: vault
LAST DEPLOYED: Thu Aug 25 13:41:34 2022
NAMESPACE: default
STATUS: deployed
REVISION: 1
NOTES:
```
```
$ kubectl exec -it vault-0 -- vault operator init --key-shares=1 --key-threshold=1
Unseal Key 1: uyzZBn7sdI1HFx4vKuEtAt8omKrOQEIv3hbVVCJjsXs=
Initial Root Token: hvs.SmNalCBB5K763ii6lROU7DVK
```
```
$ kubectl exec -it vault-0 -- vault status
Key             Value
---             -----
Seal Type       shamir
Initialized     true
Sealed          false
Total Shares    1
Threshold       1
Version         1.11.2
Build Date      2022-07-29T09:48:47Z
Storage Type    consul
Cluster Name    vault-cluster-1e61b8e7
Cluster ID      827b343a-b481-21a3-e7e7-fb8d68013abc
HA Enabled      true
HA Cluster      https://vault-0.vault-internal:8201
HA Mode         active
Active Since    2022-08-25T10:48:38.628280837Z
```
```
$ kubectl exec -it vault-1 -- vault status
Key                    Value
---                    -----
Seal Type              shamir
Initialized            true
Sealed                 false
Total Shares           1
Threshold              1
Version                1.11.2
Build Date             2022-07-29T09:48:47Z
Storage Type           consul
Cluster Name           vault-cluster-1e61b8e7
Cluster ID             827b343a-b481-21a3-e7e7-fb8d68013abc
HA Enabled             true
HA Cluster             https://vault-0.vault-internal:8201
HA Mode                standby
Active Node Address    http://10.84.3.8:8200

$ kubectl exec -it vault-2 -- vault status
Key                    Value
---                    -----
Seal Type              shamir
Initialized            true
Sealed                 false
Total Shares           1
Threshold              1
Version                1.11.2
Build Date             2022-07-29T09:48:47Z
Storage Type           consul
Cluster Name           vault-cluster-1e61b8e7
Cluster ID             827b343a-b481-21a3-e7e7-fb8d68013abc
HA Enabled             true
HA Cluster             https://vault-0.vault-internal:8201
HA Mode                standby
Active Node Address    http://10.84.3.8:8200
```
```
$ kubectl exec -it vault-0 -- vault login
Token (will be hidden):
Success! You are now authenticated. The token information displayed below
is already stored in the token helper. You do NOT need to run "vault login"
again. Future Vault requests will automatically use this token.

Key                  Value
---                  -----
token                hvs.SmNalCBB5K763ii6lROU7DVK
token_accessor       bW4KDdS6UrWlMR6GQQNKpyi0
token_duration       ∞
token_renewable      false
token_policies       ["root"]
identity_policies    []
policies             ["root"]

$ kubectl exec -it vault-0 -- vault auth list
Path      Type     Accessor               Description
----      ----     --------               -----------
token/    token    auth_token_f681aa88    token based credentials

$ kubectl exec -it vault-0 -- vault read otus/otus-ro/config
Key                 Value
---                 -----
refresh_interval    768h
password            asajkjkahs
username            otus

$ kubectl exec -it vault-0 -- vault kv get otus/otus-rw/config
====== Data ======
Key         Value
---         -----
password    asajkjkahs
username    otus

$ kubectl exec -it vault-0 -- vault auth list
Path           Type          Accessor                    Description
----           ----          --------                    -----------
kubernetes/    kubernetes    auth_kubernetes_90ebe3b6    n/a
token/         token         auth_token_f681aa88         token based credentials
```
```
$ kubectl exec -it vault-0 -- vault write pki_int/issue/example-dot-ru common_name="gitlab.example.ru" ttl="24h"
Key                 Value
---                 -----
ca_chain            [-----BEGIN CERTIFICATE-----
MIIDnDCCAoSgAwIBAgIUXNGodZgzZwxCGG9LB1qYfxPO80owDQYJKoZIhvcNAQEL
BQAwFTETMBEGA1UEAxMKZXhtYXBsZS5ydTAeFw0yMjA4MjUxMjI1MzhaFw0yNzA4
MjQxMjI2MDhaMCwxKjAoBgNVBAMTIWV4YW1wbGUucnUgSW50ZXJtZWRpYXRlIEF1
dGhvcml0eTCCASIwDQYJKoZIhvcNAQEBBQADggEPADCCAQoCggEBALT0Aty5v5dX
JGOEBuD3kVlc8LHxhcXCewRZLj5NkbBFPMDfxrsmRSamQjlFHEnbIwLng0Rm7Yhc
1oi0XbMVRKD7vpigVY/nYJU8tfXVS5Hmqmtd7qq/X1G8PrdQp6d3kOxRNpPh9eLW
rORSAPNHFGZhhEJOU6/LCN5MtAy87YvJ4YHZr4gsDlS9nbSu3LzJJbFZnY51nz21
wlwokv5yml4cANTOv4EjZmbVyoizDg/mIuAUOozvS4seQWXGFT4sZ2wnlmSK1bCP
TKsJcY1YvNCdu1SpP1BkyYtCwJ4kzKDFx4b5UojSGR2C8ulezDZqzw9IKUdKUP/4
11NPHbECtjUCAwEAAaOBzDCByTAOBgNVHQ8BAf8EBAMCAQYwDwYDVR0TAQH/BAUw
AwEB/zAdBgNVHQ4EFgQUBXWgncJXzo1lD5wotTN8k46+TEswHwYDVR0jBBgwFoAU
E6PcjoAptUWiXvJ+hOIOyNFo2eUwNwYIKwYBBQUHAQEEKzApMCcGCCsGAQUFBzAC
hhtodHRwOi8vdmF1bHQ6ODIwMC92MS9wa2kvY2EwLQYDVR0fBCYwJDAioCCgHoYc
aHR0cDovL3ZhdWx0OjgyMDAvdjEvcGtpL2NybDANBgkqhkiG9w0BAQsFAAOCAQEA
PCeX5jyNrfjBAyB97cDwpyMUAPzTui+LsgHez+AcrpV9pc8UCI3ipxQvuTO7t47p
2JdoK5kAApgp+oa2K9JCebCDZzct9Wm/nR+hTGPlSwEoeis1g5XtJ6VCe8OADk3v
QC6e6yghl3VN1riqSSDcOjCGgp3SuDKpAoFxyQciiV6wza6NnTZiJwJZBKfBuHiU
2W+kPkczP+ghh61g7adnMkPP0D4LFuuxIC2SzD8RcCC0uf28hdBrMtyw6XLTpVPQ
uU0ohMUb4i0gatHSIF1jyVsT44puGuZN0h9INwDvo0FRG8GFQaOIXgmt5l0qF3T5
4ZLwG4kjLa2YTaE3i3BUgQ==
-----END CERTIFICATE----- -----BEGIN CERTIFICATE-----
MIIDMjCCAhqgAwIBAgIUW8rWZtHBErrEypPyheonDHFdlg4wDQYJKoZIhvcNAQEL
BQAwFTETMBEGA1UEAxMKZXhtYXBsZS5ydTAeFw0yMjA4MjUxMjIwNDRaFw0zMjA4
MjIxMjIxMTRaMBUxEzARBgNVBAMTCmV4bWFwbGUucnUwggEiMA0GCSqGSIb3DQEB
AQUAA4IBDwAwggEKAoIBAQDaVeUfBTORAyLmk6C2plyBoske8c5tGCc7ws60VVi9
Za8LIIOeVgJitt41svua4Y3P18YLs2U8b7VpwugLqK1+8dKLGR7nIx0NCBUbeMb8
4CbPcY4UQ4/K+45a3QPCKxYIakXIf/nBz4KLap+QpUaaP8gxDEWINa8cYWAHl1dq
0CToWt2hUvTvkfWAnUfrTUInpZtz1AxSp8c7kLokuH82hUA4QC3RG3sjCeEkk1+r
FSbrjLGnQuxlVkja0oNHt3SvUqIW5sI9CdUo5f8Ri+DEZ96smSv8sIn8H/q983KH
8sTddKTk1LLjFukgSCYeShoKliidmndNoWNGnRRg1W17AgMBAAGjejB4MA4GA1Ud
DwEB/wQEAwIBBjAPBgNVHRMBAf8EBTADAQH/MB0GA1UdDgQWBBQTo9yOgCm1RaJe
8n6E4g7I0WjZ5TAfBgNVHSMEGDAWgBQTo9yOgCm1RaJe8n6E4g7I0WjZ5TAVBgNV
HREEDjAMggpleG1hcGxlLnJ1MA0GCSqGSIb3DQEBCwUAA4IBAQAXItJ1Y5epcGvg
vTWCU9Xl18EEwfXwU5eJbN/5iyOV3XCF2EjB1i9DNCTSQwsaSok0x4QJMd0aJN8O
CzGOGD+Hm4mO+CGYr/SNx5EPeLyn9jj1k26tey3ken2x41U66oGnjxU8RQFhCTJH
7wFOm3eBB/S+n5MN80PJ2XfkF0WOreVWsePuQg8JFj0YZAw30az0tqQgIUM5gOUK
E43pn79FHdECm0OSBE2+wg/KmSYtIlI5C3AYmFXX0Akab3NwYest3h55GJilXzxu
lf/8ibAj9SI8M8sgDvzcJNV0nDX5lacR6UB0RMXKXBX4QodGZBn1tuLrry9x1HF1
3hZUqGVB
-----END CERTIFICATE-----]
certificate         -----BEGIN CERTIFICATE-----
MIIDZzCCAk+gAwIBAgIUUfq7iexeP+6/W74KKUEDUqMy35kwDQYJKoZIhvcNAQEL
BQAwLDEqMCgGA1UEAxMhZXhhbXBsZS5ydSBJbnRlcm1lZGlhdGUgQXV0aG9yaXR5
MB4XDTIyMDgyNTEyMjk1MVoXDTIyMDgyNjEyMzAyMVowHDEaMBgGA1UEAxMRZ2l0
bGFiLmV4YW1wbGUucnUwggEiMA0GCSqGSIb3DQEBAQUAA4IBDwAwggEKAoIBAQDq
gcSLdmdQelkQVFTQdzGuawWDKSV243tN/i2jj39y/5kosqnJRnFCVe1zvc3htV0O
O7UbKL5kX4k5rF3hXBcT4YqUJ23PjCkf4WECDV7POIzSpmSzs1mukyhNsdLePurF
YUevnp600XplansB5InxalJTcqvRj5mbwFqLfWs3ng+99YdUcG4xG0m9EMfIT4h3
HObM1jLKVzDOJ/sFOgn7kUYbjaICNWBWUX1IXrXzyy6W0nOgJX5GtxGfmA+Detce
Y+iQEcYMPtmuqiU0kCEBTJkCQsU1VQKSyqO+n7j+/dkWs3xY1P3chfTVjVFeBUj1
ObQOhtYcVW9ugQkKqmxnAgMBAAGjgZAwgY0wDgYDVR0PAQH/BAQDAgOoMB0GA1Ud
JQQWMBQGCCsGAQUFBwMBBggrBgEFBQcDAjAdBgNVHQ4EFgQUXYBoP/wVyDWx/A+S
W7wJIfEuO/4wHwYDVR0jBBgwFoAUBXWgncJXzo1lD5wotTN8k46+TEswHAYDVR0R
BBUwE4IRZ2l0bGFiLmV4YW1wbGUucnUwDQYJKoZIhvcNAQELBQADggEBAJzn6FBg
Lm6GdKjDEfZEbsbc/r20HEHXvstngav/qIVqo35+iGsHmx+l1VRiJJZvpR0gif5W
TC+wFf6O6Pi2fQoHc2he8tsWI9bpzzEVI8uRNzkQyxYD1xQMnkUYGiY3P9JpdJ8P
azpkWA60ALXxyWh9r1wuGqPpqOewTBALcY07NmKTu7wrP4gvQ72hMAJga2JZUiDH
zHF29DSGFhjiquc6SPYIfReUC3VU2Rs6ZAMB7x10T0g1hCuqDMPkcxsT0Gj5Fw0e
ApfymdICDQdBj5HPO/2tmEllkEqXFLWjdz0WtxHgV+NWwkyTpUVNXwjfeh3txmst
AX5L2xWuBUKIWDM=
-----END CERTIFICATE-----
expiration          1661517021
issuing_ca          -----BEGIN CERTIFICATE-----
MIIDnDCCAoSgAwIBAgIUXNGodZgzZwxCGG9LB1qYfxPO80owDQYJKoZIhvcNAQEL
BQAwFTETMBEGA1UEAxMKZXhtYXBsZS5ydTAeFw0yMjA4MjUxMjI1MzhaFw0yNzA4
MjQxMjI2MDhaMCwxKjAoBgNVBAMTIWV4YW1wbGUucnUgSW50ZXJtZWRpYXRlIEF1
dGhvcml0eTCCASIwDQYJKoZIhvcNAQEBBQADggEPADCCAQoCggEBALT0Aty5v5dX
JGOEBuD3kVlc8LHxhcXCewRZLj5NkbBFPMDfxrsmRSamQjlFHEnbIwLng0Rm7Yhc
1oi0XbMVRKD7vpigVY/nYJU8tfXVS5Hmqmtd7qq/X1G8PrdQp6d3kOxRNpPh9eLW
rORSAPNHFGZhhEJOU6/LCN5MtAy87YvJ4YHZr4gsDlS9nbSu3LzJJbFZnY51nz21
wlwokv5yml4cANTOv4EjZmbVyoizDg/mIuAUOozvS4seQWXGFT4sZ2wnlmSK1bCP
TKsJcY1YvNCdu1SpP1BkyYtCwJ4kzKDFx4b5UojSGR2C8ulezDZqzw9IKUdKUP/4
11NPHbECtjUCAwEAAaOBzDCByTAOBgNVHQ8BAf8EBAMCAQYwDwYDVR0TAQH/BAUw
AwEB/zAdBgNVHQ4EFgQUBXWgncJXzo1lD5wotTN8k46+TEswHwYDVR0jBBgwFoAU
E6PcjoAptUWiXvJ+hOIOyNFo2eUwNwYIKwYBBQUHAQEEKzApMCcGCCsGAQUFBzAC
hhtodHRwOi8vdmF1bHQ6ODIwMC92MS9wa2kvY2EwLQYDVR0fBCYwJDAioCCgHoYc
aHR0cDovL3ZhdWx0OjgyMDAvdjEvcGtpL2NybDANBgkqhkiG9w0BAQsFAAOCAQEA
PCeX5jyNrfjBAyB97cDwpyMUAPzTui+LsgHez+AcrpV9pc8UCI3ipxQvuTO7t47p
2JdoK5kAApgp+oa2K9JCebCDZzct9Wm/nR+hTGPlSwEoeis1g5XtJ6VCe8OADk3v
QC6e6yghl3VN1riqSSDcOjCGgp3SuDKpAoFxyQciiV6wza6NnTZiJwJZBKfBuHiU
2W+kPkczP+ghh61g7adnMkPP0D4LFuuxIC2SzD8RcCC0uf28hdBrMtyw6XLTpVPQ
uU0ohMUb4i0gatHSIF1jyVsT44puGuZN0h9INwDvo0FRG8GFQaOIXgmt5l0qF3T5
4ZLwG4kjLa2YTaE3i3BUgQ==
-----END CERTIFICATE-----
private_key         -----BEGIN RSA PRIVATE KEY-----
MIIEpQIBAAKCAQEA6oHEi3ZnUHpZEFRU0HcxrmsFgyklduN7Tf4to49/cv+ZKLKp
yUZxQlXtc73N4bVdDju1Gyi+ZF+JOaxd4VwXE+GKlCdtz4wpH+FhAg1ezziM0qZk
s7NZrpMoTbHS3j7qxWFHr56etNF6ZWp7AeSJ8WpSU3Kr0Y+Zm8Bai31rN54PvfWH
VHBuMRtJvRDHyE+IdxzmzNYyylcwzif7BToJ+5FGG42iAjVgVlF9SF6188sultJz
oCV+RrcRn5gPg3rXHmPokBHGDD7ZrqolNJAhAUyZAkLFNVUCksqjvp+4/v3ZFrN8
WNT93IX01Y1RXgVI9Tm0DobWHFVvboEJCqpsZwIDAQABAoIBAEpH1pDybWFRj9qb
S85IoyUDY1ibdPGGtu0KgTxY3N75VXNNvHgz5vZDL5bE3W0MPLCgewpiSoSSum3Z
SldzCyBSBcpN/vuKAxDFHCxBPfHXkMu8xxMg0Vx54n9Rv2CEf2sMGF/pl43g+Ksd
Il+EG6mrqu4h2YjkupxaPpQGoyqXPEMw6gmGPPFxEsxuEUIQ7V/zSi57HWQAwXBo
UiimVlX3kKfLz5OvPvoNdpkK+fiv6573ufBc+sC3ucnDRRr+X3JHI+6uRMUUN1wG
BLpLT5ssk0Z/o7jHMNYopW2NTjMruOwAk7Wj75uQG5JyXDkAm09OgnYSMloO63r8
DFolF8kCgYEA7B+rhik8tfIS+UaWz/qb0Q/BK0YpCjdm3SoxmVeZLtL0rS4hZZTU
dpkV+GuOWyUspbAeZc2eN5k/3/JM6GyeViP0Oi73I7pAPn29LqhSwWW71icrkYpU
OaA+Gj3ja4TOvPY5Eh1742DjblWVivjDsOxUNUb8Ic8YXN5cLOSLZt0CgYEA/j9B
qlJHW9vbuy1/T8Qa/dIrgB0DHTb6LK6CXhC44QZhGaHPPr1UQJPUG24XEPR0hzaM
e1RWCHEVDLyPU1uhigJ1B9mlmQrWzjF8/dgxKoAGn/e6AZUjtsQJ/uOEpsFNuqVT
Hc7IEFCyxv1JV8kMg1rk5/oCMjh6/0bo+LtrUhMCgYEAqk1dRPJRG1lxXeEPvQmf
gHXAeRmtV/UFd9aK13JaxW7Zkl6HL+DcLH3VXssUbaCuRwJzqjFXP/qEG12DIs/U
k4RzDdP55AbddzTxUcmNhnd9tsOhpOMERFvS7tJcSJwNEDT2jJ4/UIv8/QM1THqH
if+p7JM6Hphjlfe6u5ZLBPECgYEArqIWYX3oY9ENCXFDzC8/9u3gUTwcQnnbtssa
4q+LvYybL3b0Gf0DnOOtQjYJA+1VCPbP4XpZKa06ryfpLnF28IulhQHa+9b1GmXl
m5pekvmjE0ed7K5a/JOE5h2ypLOLx1L9VzEMPCCh24poUTLYNHU/W5Ne5M3ktekn
5okdOx8CgYEArRNW9lBK4+gkL7zrrj6zNJJVZ+W0xuALmu+GFZL0gRPdGKRhggps
j0+45qjlZHDclvLSQ2NspowenvuPYJsEq6Vy0tEzal9MwE5PkWBJiSqU93IKFqNQ
QX4y0yDLxxSE4ryM4eCqnJIxO+/ID43r5wfZk13kjHHkKpcOaw9NqT0=
-----END RSA PRIVATE KEY-----
private_key_type    rsa
serial_number       51:fa:bb:89:ec:5e:3f:ee:bf:5b:be:0a:29:41:03:52:a3:32:df:99
```
```
$ kubectl exec -it vault-0 -- vault write pki_int/revoke serial_number="51:fa:bb:89:ec:5e:3f:ee:bf:5b:be:0a:29:41:03:52:a3:32:df:99"
Key                        Value
---                        -----
revocation_time            1661430684
revocation_time_rfc3339    2022-08-25T12:31:24.587858956Z
```

## Homework 12
 - Установлен CSI Driver, развернуты StorageClass, PV/PVC, Pod и протестирована работа снапшотов
```
$ kubectl get sc
NAME                 PROVISIONER             RECLAIMPOLICY   VOLUMEBINDINGMODE      ALLOWVOLUMEEXPANSION   AGE
csi-hostpath-sc      hostpath.csi.k8s.io     Delete          WaitForFirstConsumer   false                  6s
```
```
$ kubectl get pvc
NAME          STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS      AGE
storage-pvc   Bound    pvc-de948d78-3400-42d6-8dc0-94774d7e29d8   1Gi        RWO            csi-hostpath-sc   7s
```
```
$ kubectl get pv
NAME                                       CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM                 STORAGECLASS      REASON   AGE
pvc-de948d78-3400-42d6-8dc0-94774d7e29d8   1Gi        RWO            Delete           Bound    default/storage-pvc   csi-hostpath-sc            10s
```
```
$ kubectl get po
NAME                   READY   STATUS    RESTARTS   AGE
...
storage-pod            1/1     Running   0          6s
```
```
```
$ kubectl get volumesnapshot
NAME           READYTOUSE   SOURCEPVC     SOURCESNAPSHOTCONTENT   RESTORESIZE   SNAPSHOTCLASS            SNAPSHOTCONTENT                                    CREATIONTIME   AGE
csi-snapshot   true         storage-pvc                           1Gi           csi-hostpath-snapclass   snapcontent-0aaba314-d0e6-43f7-bee1-482652520c3c   78s            78s
```
