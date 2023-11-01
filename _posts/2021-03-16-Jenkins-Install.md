---
date: 2021-03-16
layout: post
title:  "ubuntu jenkins 설치"
description: "jenkins 설치"
image: /assets/img/posts/image/jenkins.png
optimized_image: /assets/img/posts/image/jenkins.png
category: Jenkins
tags:
  - Jenkins
  - ubuntu
author: chanos
---
📌Jenkins 설치 전에 Java가 설치 되어 있어야 한다.

#### 환경 ✔ 
`Windows Hyper-V`      
`Ubuntu 18.04.3 LTS`

```shell
wget -q -O - https://pkg.jenkins.io/debian/jenkins-ci.org.key | sudo apt-key add -
sh -c 'echo deb http://pkg.jenkins.io/debian-stable binary/ > /etc/apt/sources.list.d/jenkins.list'
apt-get update
apt-get install jenkins

service jenkins start

# default port:8080
# url : localhost:8080 or IP:8080
```

![install1.png](/assets/img/posts/2021-03-16/install1.png)

- 사이트에 접속하면 Password를 입력하라고 합니다.
	- sudo cat /var/lib/jenkins/secrets/initialAdminPassword
- 터미널에서 Password 확인 후 넣어주고 Continue 클릭

![install2.png](/assets/img/posts/2021-03-16/install2.png)

- Install suggested plugins 클릭하여 기본 플러그인 설치 (설치 후에 추가 플러그인 설치 가능합니다.)

![install3.png](/assets/img/posts/2021-03-16/install3.png)

![install4.png](/assets/img/posts/2021-03-16/install4.png)

- Plugin 설치가 끝나면 계정 생성 창으로 넘어옵니다. 

![install5.png](/assets/img/posts/2021-03-16/install5.png)

- 계정 생성까지 완료하게 되면 Jenkins Dashboard로 넘어가게 됩니다.