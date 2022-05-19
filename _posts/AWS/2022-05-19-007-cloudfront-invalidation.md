---
layout: post
title: 'AWS CloudFront 캐시 갱신'
categories: AWS
tags: [AWS]
---
- CloudFront는 파일의 캐시를 사용하는 AWS의 CDN 서비스이다.
- S3 버킷의 정적 호스팅을 하고 CDN으로 연결한 상태에서 정상적으로 배포가 되지 않았다면 캐시파일이 업데이트 되지 않았을 것이다.
- CloudFront의 기본 캐시 유지시간은 **24시간**이다.
- 하지만 무효화 설정을 통해 캐시파일을 새 파일로 갱신할 수 있다.


### Cloudfront 무효화 설정
`AWS > CloudFront > 배포 > 배포ID > 무효화`

![image](https://user-images.githubusercontent.com/48157259/169175948-74707d3e-b780-4117-89b8-dc9331047229.png)

- `/index.html` 을 경로로 추가