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
description: ""
socialImage: ""
---

# Architecture

![1](/media/argo_1.jpg)

https://argo-cd.readthedocs.io/en/stable/operator-manual/architecture/

# Argo setup

클러스터에 접근 가능한 곳에 argocd를 설치한다 (bastion server)

## Argo CD 설치

- services
- application resources

```bash
[ec2-user@ip-172-31-65-113 .kube]$ kubectl create namespace argocd
namespace/argocd created
[ec2-user@ip-172-31-65-113 .kube]$ kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
customresourcedefinition.apiextensions.k8s.io/applications.argoproj.io created
customresourcedefinition.apiextensions.k8s.io/applicationsets.argoproj.io created
customresourcedefinition.apiextensions.k8s.io/appprojects.argoproj.io created
serviceaccount/argocd-application-controller created
serviceaccount/argocd-applicationset-controller created
serviceaccount/argocd-dex-server created
serviceaccount/argocd-notifications-controller created
serviceaccount/argocd-redis created
serviceaccount/argocd-repo-server created
serviceaccount/argocd-server created
role.rbac.authorization.k8s.io/argocd-application-controller created
role.rbac.authorization.k8s.io/argocd-applicationset-controller created
role.rbac.authorization.k8s.io/argocd-dex-server created
role.rbac.authorization.k8s.io/argocd-notifications-controller created
role.rbac.authorization.k8s.io/argocd-server created
clusterrole.rbac.authorization.k8s.io/argocd-application-controller created
clusterrole.rbac.authorization.k8s.io/argocd-applicationset-controller created
clusterrole.rbac.authorization.k8s.io/argocd-server created
rolebinding.rbac.authorization.k8s.io/argocd-application-controller created
rolebinding.rbac.authorization.k8s.io/argocd-applicationset-controller created
rolebinding.rbac.authorization.k8s.io/argocd-dex-server created
rolebinding.rbac.authorization.k8s.io/argocd-notifications-controller created
rolebinding.rbac.authorization.k8s.io/argocd-server created
clusterrolebinding.rbac.authorization.k8s.io/argocd-application-controller created
clusterrolebinding.rbac.authorization.k8s.io/argocd-applicationset-controller created
clusterrolebinding.rbac.authorization.k8s.io/argocd-server created
configmap/argocd-cm created
configmap/argocd-cmd-params-cm created
configmap/argocd-gpg-keys-cm created
configmap/argocd-notifications-cm created
configmap/argocd-rbac-cm created
configmap/argocd-ssh-known-hosts-cm created
configmap/argocd-tls-certs-cm created
secret/argocd-notifications-secret created
secret/argocd-secret created
service/argocd-applicationset-controller created
service/argocd-dex-server created
service/argocd-metrics created
service/argocd-notifications-controller-metrics created
service/argocd-redis created
service/argocd-repo-server created
service/argocd-server created
service/argocd-server-metrics created
deployment.apps/argocd-applicationset-controller created
deployment.apps/argocd-dex-server created
deployment.apps/argocd-notifications-controller created
deployment.apps/argocd-redis created
deployment.apps/argocd-repo-server created
deployment.apps/argocd-server created
statefulset.apps/argocd-application-controller created
networkpolicy.networking.k8s.io/argocd-application-controller-network-policy created
networkpolicy.networking.k8s.io/argocd-applicationset-controller-network-policy created
networkpolicy.networking.k8s.io/argocd-dex-server-network-policy created
networkpolicy.networking.k8s.io/argocd-notifications-controller-network-policy created
networkpolicy.networking.k8s.io/argocd-redis-network-policy created
networkpolicy.networking.k8s.io/argocd-repo-server-network-policy created
networkpolicy.networking.k8s.io/argocd-server-network-policy created
```

## **Argo CD CLI 설치**

bastion server - linux 설치

```bash
[ec2-user@ip-172-31-65-113 dev]$ curl -sSL -o ~/bin/argocd https://github.com/argoproj/argo-cd/releases/latest/download/argocd-linux-amd64
[ec2-user@ip-172-31-65-113 dev]$ chmod +x ~/bin/argocd
```

## **Argo CD API Server 노출**

External IP 에서 접근 허용

```bash
[ec2-user@ip-172-31-65-113 dev]$ kubectl patch svc argocd-server -n argocd -p '{"spec": {"type": "LoadBalancer"}}'
service/argocd-server patched
```

![2](/media/argo_2.jpg)

![3](/media/argo_3.jpg)

- external IP address: a64da6f4b23be418cb4f7fb0ef4d895c-1587630269.ap-northeast-2.elb.amazonaws.com

## Login CLI

- id: admin
- pw: nzJVTCDXvD0447rj
    
    ```bash
    [ec2-user@ip-172-31-65-113 dev]$ argocd admin initial-password -n argocd
    nzJVTCDXvD0447rj
    ```
    

### CLI LOGIN

```bash
argocd login a64da6f4b23be418cb4f7fb0ef4d895c-1587630269.ap-northeast-2.elb.amazonaws.com:443
```

![4](/media/argo_4.jpg)

## Register Cluster

- 클러스터에 연결이 안된 이슈가 있어서 직접 클러스터를 선택해서 연결해줌

```bash
# 클러스터 조회
kubectl config get-contexts -o name

# 클러스터 연결
argocd cluster add arn:aws:eks:ap-northeast-2:275573312764:cluster/papa-dev-eks-cluster
```

![5](/media/argo_5.jpg)

### namespace 선택

```bash
kubectl config set-context --current --namespace=argocd

kubectl config set-context --current --namespace=default
```

## Bitbucket  helm repository 연결

1. argocd ip login
2. Settings > Repository > Connect Repo
    1. pw: bitbucket app password
        
        ![6](/media/argo_6.jpg)
        
    2. 연결 확인
        
        ![7](/media/argo_7.jpg)
        
3. project 생성
    1. source repository : bitbucket helm repo
    2. destination : cluster
        
        > kubectl cluster-info 명령어로 cluster server IP 확인
        > 
        
        ![8](/media/argo_8.jpg)
        

## Application 생성

1. Project Name: 위에서 생성한 Project 이름 선택 (default)
2. SOURCE: 위에서 생성한 REPO 선택 
    1. Path: Chart.yaml 있는 디렉토리 이름
3. DESTINATION: 위에서 생성한 Cluster Server 선택
    1. Namespace: default(현재 기준 생성한 ns 없음)
        
        ![9](/media/argo_9.jpg)
        
4. repo 가 헬름으로 확인되면 Helm 챕터가 나오고 values.yaml 을 선택 할 수 있다
    1. 후에 env 에 맞는 vaules.yaml 선택 (values-dev.yaml, values-live.yaml)
    
    ![10](/media/argo_10.jpg)
    

# Trouble shooting

> Unable to create application: application spec for test is invalid: InvalidSpecError: application destination server 
'[https://9B836AE00987FA095DE6F6671EBCDCD7.gr7.ap-northeast-2.eks.amazonaws.com](https://9b836ae00987fa095de6f6671ebcdcd7.gr7.ap-northeast-2.eks.amazonaws.com/)' and namespace 'argocd' do not match any of the allowed destinations in project 'default'
> 

Project 설정을 cluster 서버를 찾아서 연결해줌

# Reference

https://www.sktenterprise.com/bizInsight/blogDetail/dev/2606

https://www.eksworkshop.com/docs/automation/gitops/argocd/cluster_bootstrap

https://argo-cd.readthedocs.io/en/stable/getting_started/

https://argo-cd.readthedocs.io/en/stable/cli_installation/#installation