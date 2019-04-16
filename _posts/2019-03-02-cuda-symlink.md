---
title: cuDNN- something is not a symbolic link
date: 2019-03-02 14:00:00 +0900
categories: troubleshooting cuda cudnn
---

cuDNN 등을 설치할 떄 종종 다음과 같은 오류를 만나곤 합니다.

```bash
/usr/local/cuda-10.1/targets/x86_64-linux/lib/libcudnn.so.7 is not a symbolic link
```

말 그대로 쟤가 symbolic link가 아니기 때문에 나타나는 문제인데요, 실제로 cuda의 library를 들어가보면 다음과 같이 *상위* 버전이 *하위* 버전의 파일을 symlink 형태로 가리키고 있음을 알 수 있습니다. 방금 추가해 넣은 `libcudnn.so*`들만 빼고 말이죠.

```bash
❯❯❯ ls /usr/local/cuda-10.1/targets/x86_64-linux/lib/ -l
lrwxrwxrwx   14 root 27 Feb 15:36 libOpenCL.so -> /usr/local/cuda-10.1/targets/x86_64-linux/lib/libOpenCL.so.1
lrwxrwxrwx   16 root 27 Feb 15:36 libOpenCL.so.1 -> /usr/local/cuda-10.1/targets/x86_64-linux/lib/libOpenCL.so.1.1
.rwxr-xr-x  27k root 27 Feb 15:36 libOpenCL.so.1.1
lrwxrwxrwx   19 root 27 Feb 15:36 libaccinj64.so -> /usr/local/cuda-10.1/targets/x86_64-linux/lib/libaccinj64.so.10.1
lrwxrwxrwx   23 root 27 Feb 15:36 libaccinj64.so.10.1 -> /usr/local/cuda-10.1/targets/x86_64-linux/lib/libaccinj64.so.10.1.105
.rwxr-xr-x 7.4M root 27 Feb 15:36 libaccinj64.so.10.1.105
.rw-r--r-- 717k root 27 Feb 15:36 libcudadevrt.a
lrwxrwxrwx   17 root 27 Feb 15:36 libcudart.so -> /usr/local/cuda-10.1/targets/x86_64-linux/lib/libcudart.so.10.1
lrwxrwxrwx   21 root 27 Feb 15:36 libcudart.so.10.1 -> /usr/local/cuda-10.1/targets/x86_64-linux/lib/libcudart.so.10.1.105
.rwxr-xr-x 504k root 27 Feb 15:36 libcudart.so.10.1.105
.rw-r--r-- 888k root 27 Feb 15:36 libcudart_static.a
.rwxr-xr-x 400M root 27 Feb 17:20 libcudnn.so
.rwxr-xr-x 400M root 27 Feb 17:20 libcudnn.so.7
.rwxr-xr-x 400M root 27 Feb 17:20 libcudnn.so.7.5.0
```

사실 얘네들, 내용은 완전 같은 파일인데 중복되어 있는 녀석들이라서,

```bash
❯❯❯ md5sum /usr/local/cuda-10.1/targets/x86_64-linux/lib/libcudnn.so
e55a1e48b671d37c05fae8c35499b618  libcudnn.so
❯❯❯ md5sum /usr/local/cuda-10.1/targets/x86_64-linux/lib/libcudnn.so.7
e55a1e48b671d37c05fae8c35499b618  libcudnn.so.7
❯❯❯ md5sum /usr/local/cuda-10.1/targets/x86_64-linux/lib/libcudnn.so.7.5.0
e55a1e48b671d37c05fae8c35499b618  libcudnn.so.7.5.0
```

그냥 manually 날려버리고 직접 symlink를 달아주면 해결이 됩니다.

```bash
❯❯❯ sudo rm /usr/local/cuda-10.1/targets/x86_64-linux/lib/libcudnn.so /usr/local/cuda-10.1/targets/x86_64-linux/lib/libcudnn.so.7 -f

❯❯❯ sudo ln -s /usr/local/cuda-10.1/targets/x86_64-linux/lib/libcudnn.so.7.5.0 /usr/local/cuda-10.1/targets/x86_64-linux/lib/libcudnn.so.7

❯❯❯ sudo ln -s /usr/local/cuda-10.1/targets/x86_64-linux/lib/libcudnn.so.7 /usr/local/cuda-10.1/targets/x86_64-linux/lib/libcudnn.so

❯❯❯ ls /usr/local/cuda-10.1/targets/x86_64-linux/lib -l | grep cudnn
lrwxrwxrwx   13 root 27 Feb 17:32 libcudnn.so -> ./libcudnn.so.7
lrwxrwxrwx   17 root 27 Feb 17:32 libcudnn.so.7 -> ./libcudnn.so.7.5.0
.rwxr-xr-x 400M root 27 Feb 17:20 libcudnn.so.7.5.0
.rw-r--r-- 367M root 27 Feb 17:20 libcudnn_static.a
```

저는 [Tar file로부터 설치](https://docs.nvidia.com/deeplearning/sdk/cudnn-install/index.html#installlinux-tar)하는 과정에서 이 문제를 몇 번 마주쳤는데, 아마도 tar 파일을 풀면서 있던 symlink가, directory를 복사하는 과정에서 내용물이 가끔 hard copy되어 생기는 문제로 예상이 됩니다.

이 문제의 교훈은 **에러 메시지를 잘 읽자** 정도가 되겠네요. 금방 찾을 수 있는 내용이긴 한데 하도 자주 까먹어서 굳이 기록해둡니다.

---

20190328 Update - 뒤늦게 깨달은 사실: `cp -a` 옵션을 주면 symlink가 고대로 잘 복사됩니다.