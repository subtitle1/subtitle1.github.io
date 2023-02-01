---
layout: single
title: "[Python] python 가상환경 패키지 설치"
excerpt: ''
categories: python
tag: python, git bash, requests
---

파이썬 및 Git bash가 이미 설치된 환경(window 기준)

# 1. 터미널 환경 변경
>
**1-1. vscode에서 터미널 -> 플러스 버튼 오른쪽에 설정창에서 기본 프로필 선택**
<br>
![image](https://user-images.githubusercontent.com/87356533/215966932-0f2457df-f083-4480-a929-b9039442de16.png)
<br><br>
**1-2. 상단에서 Git bash 선택**
<br>
![image](https://user-images.githubusercontent.com/87356533/215967935-2ed70276-f1a8-4cea-bb45-96eeb2fddb65.png) 
<br><br>
**1-3. 변경된 터미널 환경 확인**
<br>
![image](https://user-images.githubusercontent.com/87356533/215968291-fc025427-9772-4c68-8c7d-b54ea4d681f0.png)
{: .notice}

-------------------------------

# 2. 가상환경 설치
가상환경(virtual environment)는 같은 시스템에서 실행되는 다른 파이썬 응용 프로그램들의 동작에 영향을 주지 않기 위해 파이썬 배포 패키지들을 설치하거나 업그레이드 하는 것을 가능하게 하는 **격리된 실행환경**이다.

## 2-1) 라이브러리 전용 폴더 생성
```
    python -m venv venv
```
>
**- 터미널에 명령어 친 후 바로 폴더 생성이 된 것을 확인할 수 있음**<br>
![image](https://user-images.githubusercontent.com/87356533/215969127-365626a1-c1ce-4fb6-8899-de2353dce239.png)
<br>
**- 그 후 인터프리터 변경**<br>
![image](https://user-images.githubusercontent.com/87356533/215969781-a7365c3c-852d-48ce-bc09-d5e5d1ccde02.png)
<br>
**- 터미널 껐다 키면 끝**<br>
![image](https://user-images.githubusercontent.com/87356533/215969984-20609c38-483a-4072-b562-5007155ca883.png)
<br><br>
**앞으로는 venv 폴더에서 라이브러리를 가져다 쓸 거라는 의미**
{: .notice}


## 2-2) venv 폴더에 라이브러리 다운로드
- ctrl + `로 터미널창 열기
- pip(python install package) 사용해서 패키지(외부 라이브러리) 설치

```
    pip install requests
```

![image](https://user-images.githubusercontent.com/87356533/215971181-2cecb964-3bc4-4d2f-a096-302a38c8f5f5.png)

끗
