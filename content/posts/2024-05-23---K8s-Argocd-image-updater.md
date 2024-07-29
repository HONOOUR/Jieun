---
title: "K8s Argocd setup"
date: "2024-05-23T20:50:10.284Z"
template: "post"
draft: false
slug: "k8s-argocd"
category: "Development"
tags:
  - "K8s"
  - "EKS"
  - "Argocd"
  - "Argocd-Image-Updater"
description: ""
socialImage: ""
---

# Overview

- Argocd image updater : 컨테이너 이미지를 자동으로 업데이트 하는 도구 (쿠버네스 파드 이미지)
- Argocd에 구성한 애플리케이션을 정기적으로 polling (변경사항 체크)
- 최신 이미지 생성 확인 → 헬름 차트 업데이트(git push) → argocd sync(자동/수동)

# **Installation**

```bash
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj-labs/argocd-image-updater/stable/manifests/install.yaml
```

# Configuration

## 로그레벨

```bash
kubectl edit configmap argocd-image-updater-config -n argocd
```

```yaml
apiVersion: v1
data:
  log.level: debug
kind: ConfigMap
metadata:
  annotations:
    kubectl.kubernetes.io/last-applied-configuration: |
      {"apiVersion":"v1","kind":"ConfigMap","metadata":{"annotations":{},"labels":{"app.kubernetes.io/name":"argocd-image-updater-config","app.kubernetes.io/part-of":"argocd-image-updater"},"name":"argocd-image-updater-config","namespace":"argocd"}}
  creationTimestamp: "2024-07-25T01:33:21Z"
  labels:
    app.kubernetes.io/name: argocd-image-updater-config
    app.kubernetes.io/part-of: argocd-image-updater
  name: argocd-image-updater-config
  namespace: argocd
  resourceVersion: "105030736"
  uid: 32faa020-1d90-4eb7-ac36-a1c344827820
```

## 로컬 계정 설정

- 로그인 필수
- account list 확인

```bash
kubectl edit configmap argocd-cm -n argocd
```

```yaml
apiVersion: v1
data:
  accounts.image-updater: apiKey
kind: ConfigMap
metadata:
  annotations:
    kubectl.kubernetes.io/last-applied-configuration: |
      {"apiVersion":"v1","kind":"ConfigMap","metadata":{"annotations":{},"labels":{"app.kubernetes.io/name":"argocd-cm","app.kubernetes.io/part-of":"argocd"},"name":"argocd-cm","namespace":"argocd"}}
  creationTimestamp: "2024-05-07T06:09:05Z"
  labels:
    app.kubernetes.io/name: argocd-cm
    app.kubernetes.io/part-of: argocd
  name: argocd-cm
  namespace: argocd
  resourceVersion: "104701813"
  uid: 2139e173-8809-4c1b-af3c-d95164d298b8
```

```bash
argocd account list
# login 전
$ argocd account list
FATA[0000] rpc error: code = Unauthenticated desc = invalid session: Token is expired

# login 후
$ argocd login <>.ap-northeast-2.elb.amazonaws.com:80
WARNING: server certificate had error: tls: failed to verify certificate: x509: certificate is valid for localhost, argocd-server, argocd-server.argocd, argocd-server.argocd.svc, argocd-server.argocd.svc.cluster.local, not <>.ap-northeast-2.elb.amazonaws.com. Proceed insecurely (y/n)?  y
Username: admin
Password: nzJVTCDXvD0447rj
'admin:login' logged in successfully
Context '<>.ap-northeast-2.elb.amazonaws.com:443' updated

$ argocd account list
NAME           ENABLED  CAPABILITIES
admin          true     login
image-updater  true     apiKey
```

## 계정 권한 설정

```bash
kubectl edit configmap argocd-rbac-cm -n argocd
```

```yaml
apiVersion: v1
data:
  policy.csv: |
    p, role:image-updater, applications, get, */*, allow
    p, role:image-updater, applications, update, */*, allow
    g, image-updater, role:image-updater
  policy.default: role.readonly
kind: ConfigMap
metadata:
  annotations:
    kubectl.kubernetes.io/last-applied-configuration: |
      {"apiVersion":"v1","kind":"ConfigMap","metadata":{"annotations":{},"labels":{"app.kubernetes.io/name":"argocd-rbac-cm","app.kubernetes.io/part-of":"argocd"},"name":"argocd-rbac-cm","namespace":"argocd"}}
  creationTimestamp: "2024-05-07T06:09:05Z"
  labels:
    app.kubernetes.io/name: argocd-rbac-cm
    app.kubernetes.io/part-of: argocd
  name: argocd-rbac-cm
  namespace: argocd
  resourceVersion: "104224719"
  uid: 9813b59b-ea5e-44f8-bb09-1a9f90cccffd
```

## Argo CD Endpoint 설정 + ECR 연결

```bash
kubectl edit configmap argocd-image-updater-config -n argocd
```

```yaml
apiVersion: v1
data:
  applications_api: argocd
  argocd.grpc_web: "true"
  argocd.insecure: "false"
  argocd.plaintext: "false"
  argocd.server_addr: dev-argocd.<sub-domain>.com
  log.level: debug
  registries.conf: |
    registries:
      - name: AWS ECR HUB
        api_url: https://<>.dkr.ecr.ap-northeast-2.amazonaws.com
        prefix: "<>.dkr.ecr.ap-northeast-2.amazonaws.com"
        ping: yes
        credentials: ext:/app/aws/ecr.sh
        tagsortmode: latest-last
  ecr.sh: |
    #!/bin/sh
    aws ecr --region "ap-northeast-2" get-authorization-token --output text --query 'authorizationData[].authorizationToken' | base64 -d
kind: ConfigMap
metadata:
  annotations:
    kubectl.kubernetes.io/last-applied-configuration: |
      {"apiVersion":"v1","kind":"ConfigMap","metadata":{"annotations":{},"labels":{"app.kubernetes.io/name":"argocd-image-updater-config","app.kubernetes.io/part-of":"argocd-image-updater"},"name":"argocd-image-updater-config","namespace":"argocd"}}
  creationTimestamp: "2024-07-25T01:33:21Z"
  labels:
    app.kubernetes.io/name: argocd-image-updater-config
    app.kubernetes.io/part-of: argocd-image-updater
  name: argocd-image-updater-config
  namespace: argocd
  resourceVersion: "105141645"
  uid: 32faa020-1d90-4eb7-ac36-a1c344827820
```

```bash
kubectl create secret docker-registry aws-ecr-creds \
--docker-server=<>.dkr.ecr.ap-northeast-2.amazonaws.com/<>-dev \
--docker-username=AWS \
--docker-password=$(aws ecr get-login-password) \
-n argocd
```

## Argo CD Access Token Secret 생성

```bash
argocd account generate-token --account image-updater --id image-updater
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJhcmdvY2QiLCJzdWIiOiJpbWFnZS11cGRhdGVyOmFwaUtleSIsIm5iZiI6MTcyMTk2OTQ3MSwiaWF0IjoxNzIxOTY5NDcxLCJqdGkiOiJpbWFnZS11cGRhdGVyIn0.WOGCHEKAhap9773n1E0t8slXPrjENqkfcsAgyv_NiVM
# 토큰 복사

kubectl create secret generic argocd-image-updater-secret --from-literal argocd.token=$YOUR_TOKEN --dry-run -o yaml | kubectl -n argocd apply -f -

kubectl get secrets -n argocd
```

```bash
$ argocd account list
NAME           ENABLED  CAPABILITIES
admin          true     login
image-updater  true     apiKey
$ argocd account generate-token --account image-updater --id image-updater
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.-----------------------------------------.FvCN0K9vd7BJSZHdHYp-0sGoiuEAMg6VO5d1GFLXPOk
$ kubectl create secret generic argocd-image-updater-secret --from-literal argocd.token=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..-----------------------------------------.FvCN0K9vd7BJSZHdHYp-0sGoiuEAMg6VO5d1GFLXPOk --dry-run -o yaml | kubectl -n argocd apply -f -
W0725 02:04:44.506494 2129174 helpers.go:692] --dry-run is deprecated and can be replaced with --dry-run=client.
secret/argocd-image-updater-secret configured
$ kubectl get secrets -n argocd
```

## 볼륨 마운트

- 파드안에 aws 폴더 생성
- [ecr.sh](http://ecr.sh) file 권한 설정
    - defaultMode: 0777

```bash
kubectl edit deployment argocd-image-updater -n argocd
```
아래 volumes 부분 추가
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
        volumeMounts:
        - mountPath: /app/config
          name: image-updater-conf
        - mountPath: /app/config/ssh
          name: ssh-known-hosts
        - mountPath: /app/.ssh
          name: ssh-config
        - mountPath: /tmp
          name: tmp
        - mountPath: /app/ssh-keys/id_rsa
          name: ssh-signing-key
          readOnly: true
          subPath: sshPrivateKey
        - mountPath: /app/aws
          name: aws-scripts
      dnsPolicy: ClusterFirst
      restartPolicy: Always
      schedulerName: default-scheduler
      securityContext: {}
      serviceAccount: argocd-image-updater
      serviceAccountName: argocd-image-updater
      terminationGracePeriodSeconds: 30
      volumes:
      - configMap:
          defaultMode: 420
          items:
          - key: registries.conf
            path: registries.conf
          - key: git.commit-message-template
            path: commit.template
          name: argocd-image-updater-config
          optional: true
        name: image-updater-conf
      - configMap:
          defaultMode: 420
          name: argocd-ssh-known-hosts-cm
          optional: true
        name: ssh-known-hosts
      - configMap:
          defaultMode: 420
          name: argocd-image-updater-ssh-config
          optional: true
        name: ssh-config
      - name: ssh-signing-key
        secret:
          defaultMode: 420
          optional: true
          secretName: ssh-git-creds
      - emptyDir: {}
        name: tmp
      - configMap:
          defaultMode: 0777
          items:
          - key: ecr.sh
            path: ecr.sh
          name: argocd-image-updater-config
        name: aws-scripts
```

## **AWS ECR 인증 파일 ecr.sh 생성 후 적용**

```bash
# access to pod
kubectl -n argocd exec --stdin --tty pod/argocd-image-updater-57b788886d-bsrvr -- /bin/sh
$ cd /app
$ mkdir aws
$ cd aws
$ vi ecr.sh
$ chmod +x /app/aws/ecr.sh
```

```bash
#!/bin/sh
aws ecr --region ap-northeast-2 get-authorization-token --output text --query 'authorizationData[].authorizationToken' | base64 -d
```
## Semantic versioning (semver)

- ecr image tagging
- jenkins 에서 파라미터 입력 받음

## Argocd LB SSL 적용
![1](/media/image-updater_1.png)

## Test

```bash
**argocd-image-updater test \
<>.dkr.ecr.ap-northeast-2.amazonaws.com/<>-dev \
--registries-conf-path /app/config/registries.conf

argocd-image-updater test <>.dkr.ecr.ap-northeast-2.amazonaws.com/<>**
```

```bash
# restart
kubectl rollout restart deployment argocd-image-updater -n argocd

kubectl logs argocd-image-updater-7d5895f966-tjp95 -n argocd
```

## Annotation 추가
- tag(version)을 확인해서 최신으로 업데이트 (semantic versioning을 따름)
- argocd image updater 가 git(bitbucket) 업데이트 하도록 설정
- kubectl annotate 명령어 사용 가능
![2](/media/image-updater_2.png)


## Test
1. Jenkins build
2. argocd image update log 확인
    1. commit, push 발생
3. git commit 확인 (helm)
4. argocd web 확인
    1. sync 가 수동인 경우 outOfSync 발생 (헬름차트 버전이랑 배포 버전이다름)
    2. sync 동작시킴

# Trouble Shooting
## Reauthenticate

```
time="2024-07-29T01:43:11Z" level=error msg="Could not get tags from registry: denied:
Your authorization token has expired. Reauthenticate and try again." alias= application=<>-dev image_name=<>-dev image_tag=cd848d880b27ce2ad30b3cff305c6c4f3f5c9e5e registry=<>.dkr.ecr.ap-northeast-2.amazonaws.com

```

하루가 지날 경우 만료됨, argocd-image-updater를 재실행 시키면 해결되는데 해결 방법 필요

```
kubectl rollout restart deployment argocd-image-updater -n argocd

```

# Reference

https://argocd-image-updater.readthedocs.io/en/v0.3.1/install/start/

https://argocd-image-updater.readthedocs.io/en/latest/basics/authentication/#authentication-in-argo-cd-image-updater

https://deepai.org/gallery-item/d8badd7536a44f1d9fb3ec60a7349c59/choice-travel-logo-my-first-name-is-choi-too-850f5d.jpg.html