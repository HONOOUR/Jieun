---
title: "S3 REST API 요청"
date: "2023-11-05T20:50:10.284Z"
template: "post"
draft: false
slug: "s3-rest-api"
category: "Development"
tags:
  - "S3"
  - "AWS SDK"
description: "S3 호스팅 방식, 객체"
socialImage: ""
---

# 가상 호스팅 방식

가상 호스팅 : 한 웹 서버에 있는 여러 웹 사이트에 서비스를 제공하는 기능

- 형식 (버킷이름이 도메인의 일부로 들어감)

    ```json
    https://bucket-name.s3.region-code.amazonaws.com/key-name
    ```

- **Pre-signed url**
  - 해당 API 를 호출한 서버의 자격증명으로 임시 서명한 URL 을 가져옴
  - 퍼블릭 접근 권한을 열어두지 않더라도 S3 객체를 가져옴
  - 브라우저에서 읽을 수 있는 파일 형태면 바로 읽어지고 읽을 수 없는 파일 형태는 다운로드함 (jpg 는 다운로드함)
  - 만료 시간을 정할 수 있음 → 버킷을 퍼블릭으로 설정하면 만료시간 없음

    ```jsx
    // https://docs.aws.amazon.com/ko_kr/AWSJavaScriptSDK/latest/AWS/S3.html#getSignedUrl-property
    // **Pre-signing a getObject operation (synchronously)**
    var params = {Bucket: 'bucket', Key: 'key'};
    var url = s3.getSignedUrl('getObject', params);
    console.log('The URL is', url);
    ```

    ```java
    // 예시
    public String getFileUrl(String bucketName, String key) {
            GetObjectRequest request = GetObjectRequest.builder()
                    .bucket(bucketName)
                    .key(key)
                    .build();
            GetObjectPresignRequest presignRequest = GetObjectPresignRequest.builder()
                    .signatureDuration(Duration.ofMinutes(1))
                    .getObjectRequest(request).build();
            URL url = s3Presigner.presignGetObject(presignRequest).url();
            return url.toString();
        }
    ```

- 버킷 설정
  - 계정 전체의 public access block off

        ![1](/media/s3_1.jpg)

        ![2](/media/s3_2.jpg)

  - 버킷의 public access block 을 off 로 설정
  - ACLs → enabled → Everyone Read

# 경로 방식

- 형식 (버킷이름이 resource path 일부로 들어감)
- 이후 중단될 서비스

# Reference

<https://docs.aws.amazon.com/ko_kr/AmazonS3/latest/userguide/VirtualHosting.html>
<https://docs.aws.amazon.com/AmazonS3/latest/userguide/RESTAuthentication.html>
<https://docs.aws.amazon.com/ko_kr/AWSJavaScriptSDK/latest/AWS/S3.html#getSignedUrl-property>
<https://docs.aws.amazon.com/AmazonS3/latest/userguide/access-control-block-public-access.html#access-control-block-public-access-options>
