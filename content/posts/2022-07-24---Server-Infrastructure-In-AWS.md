---
title: "AWS에서 서버 클라우드 환경 구성하기"
date: "2022-07-24T22:20:10.284Z"
template: "post"
draft: false
slug: "aws-server-infrastructure"
category: "Development"
tags:
  - "AWS"
  - "EKS"
  - "ALB"
  - "Route53"
  - "CloudFormation"
description: "AWS 에서 서버 구축하기"
socialImage: ""
---

## Network 설정

Key pairs 생성을 제외하고 cloud formation 만들어서 network 환경을 구성한다

cloud formation 을 사용하면 infrastructure 을 코드로 구성, 재사성, 자원을 업데이트하는 것을 가능하게한다

### VPC 설정

### Subnet 설정 (public, private (app, db))

### Internet Gateway, Route Table 설정

### NAT 게이트웨이 설정

- private 영역에서 public 영역으로 통신하기위한 NAT 게이트웨이 설정
- 각 public 영역 서브넷에 설정
- static IP 가 필요하기 때문에  Elastic IP 를 AllocationId 속성에 추가
- NACL ↔ subnet 연결
    - 서브넷의 인바운드 아웃바운드 규칙을 정함 → 자동 생성돼 추가할 필요 없음
- Key pairs 생성
    - EC2 인스턴스에 연결할 때 자격 증명 입증에 사용하는 보안 자격 증명 집합

참고

[https://www.infoq.com/articles/aws-vpc-cloudformation/](https://www.infoq.com/articles/aws-vpc-cloudformation/)

## EC2 Bastion

### Bastion 인스턴스 생성 및 설정

- 보통 az a public subnet에 생성해 private 접근을 가능하게 한다
- bastion에 접근하기 위한 보안 그룹 생성 (eks를 제어하려면 eks를 위한 보안 그룹도 생성해야 함)

### Bastion 에  EKS ****클러스터 관련 내용 설정****

- eks를 제어하기 위해 IAM 생성, eks-mng credential 적용
- AWS CLI
- kubectl
- eksctl
- eks cluster 생성
    - subnet 인자에는 서버가 실행되는 private application subnet을 추가한다

참고

[https://aws.amazon.com/ko/blogs/security/how-to-record-ssh-sessions-established-through-a-bastion-host/](https://aws.amazon.com/ko/blogs/security/how-to-record-ssh-sessions-established-through-a-bastion-host/)

[https://docs.aws.amazon.com/ko_kr/eks/latest/userguide/getting-started-console.html](https://docs.aws.amazon.com/ko_kr/eks/latest/userguide/getting-started-console.html)

## EKS

노드 그룹이 하나 이상 존재해야 Ingress Controller를 적용할 수 있기 때문에 아래를 참고해 auto scaling group(eks node instance) 을 생성한다

[https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-eks-cluster.html](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-eks-cluster.html)

### **Ingress Controller 생성**

선행 준비 사항

- eks cluster
- IAM OIDC
- IAM Policy

IAM Policy 생성

iam_policy.json 다운로드

```
1curl -o iam_policy.json https://raw.githubusercontent.com/kubernetes-sigs/aws-load-balancer-controller/v2.4.2/docs/install/iam_policy.json
```

정책생성

아래 진행사항을 따르고 controller 가 적용 됐는지 확인한다

[amazon-eks-user-guide/aws-load-balancer-controller.md at master · awsdocs/amazon-eks-user-guide](https://github.com/awsdocs/amazon-eks-user-guide/blob/master/doc_source/aws-load-balancer-controller.md)

`load-balancer-role-trust-policy.json` 저장, Role 생성 명령어 실행

### Sub domain 생성

ingress 를 정의해 만들어진 alb를 sub domain에 연결해 정적 end point 를 형성한다

- AWS Certificate Manager 에서 인증서 생성
    
    인증서 유형 → 퍼블릭 인증서 요청 (route53 에 생성된 DNS 로 검증한다)
    
- Route53 에서 레코드가 생성된 것을 확인한다.

### **Ingress 정의 및 생성**

[Application load balancing on Amazon EKS - Amazon EKS](https://docs.aws.amazon.com/eks/latest/userguide/alb-ingress.html) [Annotations - AWS Load Balancer Controller](https://kubernetes-sigs.github.io/aws-load-balancer-controller/v2.4/guide/ingress/annotations/)

expose **HTTP and HTTPS routes** from outside the cluster to **services**

![ingress-1](/media/ingress-1.jpg)

- ingress controller 생성되어 있어야 함
- ingress resource 정의하면 alb 생성되고 sub domain 에 연결해 정적 endpoint 생성 (for API)
- externally-reachable URL (/auth, /shop ..)
- bastion 으로 이동해서 ingress rule을 생성
    - ingress resource includes rules incoming request (service.name, service.port) to ingress
    - example
    
    ```yaml
    apiVersion: networking.k8s.io/v1
    kind: Ingress
    metadata:
      name: simple-fanout-example
    spec:
      rules:
      - host: foo.bar.com
        http:
          paths:
          - path: /foo
            pathType: Prefix
            backend:
              service:
                name: service1
                port:
                  number: 4200
          - path: /bar
            pathType: Prefix
            backend:
              service:
                name: service2
                port:
                  number: 8080
    ```
    
    ![ingress-2](/media/ingress-2.jpg)
    
    참고
    
    [https://kubernetes-sigs.github.io/aws-load-balancer-controller/v2.4/](https://kubernetes-sigs.github.io/aws-load-balancer-controller/v2.4/)
    
    [https://kubernetes.io/docs/concepts/services-networking/ingress/](https://kubernetes.io/docs/concepts/services-networking/ingress/)
    
    ### ECR
    
    아래 링크를 참고해서 dockerfile, buildspec 파일을 만들어 ECR 에 image를 저장하도록 한다
    
    [https://docs.aws.amazon.com/prescriptive-guidance/latest/patterns/deploy-a-sample-java-microservice-on-amazon-eks-by-using-amazon-ecr-and-eksctl.html](https://docs.aws.amazon.com/prescriptive-guidance/latest/patterns/deploy-a-sample-java-microservice-on-amazon-eks-by-using-amazon-ecr-and-eksctl.html)
    
    ### EKS 설정
    
    - cluster 에 노드 연결
    - ECR image 배포
    - 로드 밸런서 연결
    - 파드 설정