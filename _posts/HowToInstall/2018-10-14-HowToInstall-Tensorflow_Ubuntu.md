---
title: "[HowToInstall]텐서플로우 우분투에 설치하기"
excerpt: "Linux Ubuntu에서 Tensorflow CUDA 환경 구축하기"
search: true
categories:
  - HowToInstall
tags:
  - Tensorflow
toc: true
sidebar_main: true
---

# Ubuntu16.04에서 Tensorflow 설치

## 설치 환경
- Linux Ubuntu 16.04
- GTX 1060 3GB (CUDA)
- python 3.5
- Virtualenv

## 설치 순서
1) CUDA 설치 (CUDA 9.0)
2) cuDNN 설치 (cuDNN 7.1)
3) Virtualenv(python 가상 환경) 설치
4) Tensorflow 설치

## CUDA 설치

### NVIDIA 드라이버 설치 확인

~~~
$ nvidia-smi
~~~

### 주의 사항
- 현재 설치하는 tensorflow 버전은 1.8이며, 이 버전은 CUDA 9.0과 호환된다. (CUDA 버전이 9.0이 아닐시 밑과 같은 오류 메시지가 뜬다.)

![tensorflow_error](https://user-images.githubusercontent.com/34755287/46916462-11a91c00-cff6-11e8-81a9-13f8f09bbf36.JPG)

- CUDA 이전 버전 삭제하기 (CUDA 8.0)

~~~
$ sudo apt-get --purge remove 'cuda*'
$ sudo apt-get autoremove --purge 'cuda*'
~~~

![cuda8 0_remove1](https://user-images.githubusercontent.com/34755287/46916450-0f46c200-cff6-11e8-927d-5057c427205f.JPG)

![cuda8 0_remove2](https://user-images.githubusercontent.com/34755287/46916451-0fdf5880-cff6-11e8-8e59-432d79b0e694.JPG)

~~~
$ sudo rm -rf /usr/local/cuda-8.0
$ sudo rm -rf /usr/local/cuda
~~~

![cuda8 0_remove3](https://user-images.githubusercontent.com/34755287/46916452-0fdf5880-cff6-11e8-9747-4d4281a8a979.JPG)

### CUDA 9.0 Toolkit 설치
- [https://developer.nvidia.com/cuda-90-download-archive](https://developer.nvidia.com/cuda-90-download-archive) (Nvidia, 설치 파일)
- 위 사이트에 들어가서 아래와 같이 다운받는다.
- Patch 파일은 가장 최신인 Patch 2만 설치하면 된다.

![cuda9 0_install1](https://user-images.githubusercontent.com/34755287/46916455-1077ef00-cff6-11e8-8544-36e103d599b6.JPG)

![cuda9 0_install2](https://user-images.githubusercontent.com/34755287/46916456-1077ef00-cff6-11e8-8e7a-3605ab2c5329.JPG)

- Base Installer

~~~
$ cd Download(다운로드)
$ sudo dpkg -i cuda-repo-ubuntu1604-9-0-local_9.0.176-1_amd64.deb
$ sudo apt-key add /var/cuda-repo-9-0-local/7fa2af80.pub
$ sudo apt-get update
$ sudo apt-get install cuda
~~~

- Patch 2 (Released Mar 5, 2018)

~~~
$ cd Download
$ sudo dpkg -i cuda-repo-ubuntu1604-9-0-local-cublas-performance-update-2_1.0-1_amd64.deb
$ sudo apt-get update
$ sudo apt-get upgrade cuda
~~~

![cuda9 0_install3](https://user-images.githubusercontent.com/34755287/46916457-1077ef00-cff6-11e8-8136-1502bd163ac3.JPG)

![cuda9 0_install4](https://user-images.githubusercontent.com/34755287/46916458-11108580-cff6-11e8-8f4d-efd282997f28.JPG)

![cuda9 0_install5](https://user-images.githubusercontent.com/34755287/46916459-11108580-cff6-11e8-9629-4d009510a693.JPG)

## cuDNN 설치

### cuDNN 7.1.4 설치
- [https://developer.nvidia.com/rdp/cudnn-download](https://developer.nvidia.com/rdp/cudnn-download) (Nvidia, 설치 파일)
- 로그인을 해야 설치 파일을 다운 받을 수 있다.
- 아래 그림에서 cuDNN v7.1.4 Library for Linux 를 다운 받는다.
![cudnn_install1](https://user-images.githubusercontent.com/34755287/46916460-11108580-cff6-11e8-9655-e2128da3fb3a.JPG)

- 압축 풀기

~~~
$ cd Download
$ tar xvzf cudnn*
~~~

- cuDNN 라이브러리를 CUDA가 설치된 곳에 복사하기

~~~
$ sudo cp cuda/include/cudnn.h /usr/local/cuda/include
$ sudo cp cuda/lib64/libcudnn* /usr/local/cuda/lib64
$ sudo chmod a+r /usr/local/cuda/include/cudnn.h /usr/local/cuda/lib64/libcudnn*
$ sudo mv ~/Download/cuda/* /usr/local/cuda-9.0
~~~

- cuDNN 라이브러리를 /usr/lib/x86_64-linux-gnu 폴더에 복사하면 R 언어에서 정상적으로 사용가능하다.

~~~
$ sudo cp cuda/lib64/libcudnn* /usr/lib/x86_64-linux-gnu
~~~

![cudnn_install2](https://user-images.githubusercontent.com/34755287/46916461-11a91c00-cff6-11e8-94f3-a04b2a9701c4.JPG)

## Virtualenv 가상환경 설치

### Virtualenv 를 사용하는 이유
- [https://www.tensorflow.org/install/install_linux](https://www.tensorflow.org/install/install_linux)
- 위의 tensorflow 공식 홈페이지에 따르면, Virtualenv 가상환경이 tensorflow를 설치하기 간단하여 추천하고 있다.

### Virtualenv 설치
- python 3.5 버전, GPU 환경에 맞춰서 설치한다.
- 다른 환경 설치와 자세한 사항은 참고 문헌 부분을 참고하길 바란다.

1. pip와 virtualenv 패키지 설치

~~~
$ sudo apt-get install python3-pip python3-dev python-virtualenv
~~~

2. Virtualenv 환경 생성

~~~
$ virtualenv --system-site-packages -p python3 targetDirectory
~~~

3. 가상 환경 활성화

~~~
$ source ~/tensorflow/bin/activate # bash, sh, ksh, or zsh
$ source ~/tensorflow/bin/activate.csh # csh or tcsh
~~~

- 위의 명령어를 실행하면 가상 환경이 밑에와 같이 활성화되는 모습을 볼 수 있다.

~~~
(tensorflow)$
~~~

4. 원활한 진행을 위해 pip 8.1버전 이상이 설치되어 있어야 한다. 만약을 위해 pip 업그레이드를 한다.

~~~
$ easy_install -U pip
~~~

![virtualenv_install1](https://user-images.githubusercontent.com/34755287/46916467-1241b280-cff6-11e8-960c-b011df04d342.JPG)

![virtualenv_install2](https://user-images.githubusercontent.com/34755287/46916468-12da4900-cff6-11e8-9c91-3a22fec5ad6e.JPG)

5. Tensorflow 설치

~~~
(tensorflow)$ pip3 install --upgrade tensorflow-gpu
~~~

![tensorflow_install](https://user-images.githubusercontent.com/34755287/46916464-11a91c00-cff6-11e8-9dc6-31290c263589.JPG)

6. Virtualenv 가상 환경 비활성화

~~~
(tensorflow)$ deactivate
~~~

### Tensorflow 테스트

~~~
$ source ~/tensorflow/bin/activate
(tensorflow)$ python
import tensorflow as tf
hello = tf.constant('Hello, Tensorflow!')
sess = tf.Session()
print(sess.run(hello))
(tensorflow)$ deactivate
$
~~~

![tensorflow_test](https://user-images.githubusercontent.com/34755287/46916465-1241b280-cff6-11e8-89ab-8964a32cb97f.JPG)

### Tensorflow 버전 확인

~~~
(tensorflow)$ python
import tensorflow as tf
tf.__version__
~~~

![tensorflow_version](https://user-images.githubusercontent.com/34755287/46916466-1241b280-cff6-11e8-9d8a-3ad17b0294f3.JPG)

## 참고 문헌
- CUDA 8.0 지우고 CUDA 9.0 설치하기
  - [https://park-ju-hyeong.github.io/2018/04/05/CUDA-8.0-to-9.0/](https://park-ju-hyeong.github.io/2018/04/05/CUDA-8.0-to-9.0/)
- cuDNN 설치
  - [http://igotit.tistory.com/entry/cuDNN-Ubuntu-%EC%97%90-%EC%84%A4%EC%B9%98](http://igotit.tistory.com/entry/cuDNN-Ubuntu-%EC%97%90-%EC%84%A4%EC%B9%98)
- Virtualenv를 활용한 tensorflow 설치
  - [http://webnautes.tistory.com/1046](http://webnautes.tistory.com/1046)
  - [https://www.tensorflow.org/install/install_linux](https://www.tensorflow.org/install/install_linux) (tensorflow 공식 홈페이지)
