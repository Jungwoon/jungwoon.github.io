---
layout: post
title: "ADB 명령어"
image: '/assets/img/'
description: '터미널을 이용하여, ADB 명령어를 이용 방법'
tags:
- Android
- ADB
categories:
- Android
---

## ADB 명령어

`adb devices (디바이스 검색)`


: adb 서버와 연결된 폰의 디바이스와 에뮬레이터 목록을 보여줍니다. 연결된 devices의 TCP/IP 포트 번호를 알아 낼때도 사용합니다.

`adb shell (타겟 시스템의 쉘 명령)`


: 해당 시스템의 쉘에 연결하고 # 프롬프트를 띄어 해당 시스템을 탐색하고 수정할 수 있습니다.

예) 여러 단말기중 하나를 선택해서 접속 adb -s emulator-5554 shell

`adb shell push Phone.apk /system/app (파일 복사)`


:  Phone.apk 파일을 해당 기기의 /system/apk 폴더에 Phone.apk 넣습니다.

`adb shell chmod 644 system/app/Phone.apk (파일 권한)`


: 해당 기기의 /system/apk 폴더에 있는 Phone.apk 파일 권한을 설정합니다.

`adb pull /system/app/Phone.apk (파일 추출)`


: 해당 기기의 /system/apk 폴더에 있는 Phone.apk 파일을 추출합니다.

`adb install [-l][-r] file_spec (app을 설치 또는 재설치 할 때 사용)`


-l : 다른 장치로 복사돼는것을 막습니다. -r : 이미 존재하는 app 데이터를 지우지 않은채 재설치 합니다. file_apec : 설치할 app의 .apk 파일입니다.

`adb uninstall [-k] package (패키지 이름을 가진 app를 제거)`

-k : app 의 데이터를 보존합니다. package : 패키지의 전체 경로, apk 확장자는 빼야합니다.

예) 패키지 삭제 adb unstall com.falinux.android.hello

`adb push local remote (local 파일을 remote 이름으로 복사)`


: 개발자 컴퓨터에 있는 local 이란 이름을 가진 파일을...타겟 시스템 remote 란 이름으로 복사합니다.
예) com.falinux.android.rose.apk 파일을 안드로이드 기기 /data/app 폴더 안으로 집어넣습니다. adb push c:\com.falinux.android.rose.apk /data/app/

`adb pull remote local (remote 파일을 local 이름으로 복사)`


: 해당 시스템에 있는 remote 라는 파일을 개발자 컴퓨터에 local 이란 이름으로 복사합니다.

예) 안드로이드 기기 /data/app/com.falinux.android.rose.apk 파일을 C 드라이버로 가져옵니다. adb pull /data/app/com.falinux.android.rose.apk c:\com.falinux.android.rose.apk

`adb reboot (시스템 재부팅)`


: 안드로이드 시스템을 재부팅합니다.

`adb kill-server (adb 종료)`


: adb에 문제가 있을 경우, adb를 종료 시킵니다.

`adb start-server (adb 실행)`


: 종료된 adb를 실행 시킵니다.

adb 명령어가 너무 많으므로 자주 쓰이는것 이외엔 [Link](http://developer.android.com/guide/developing/tools/adb.html) 에서 확인하시면 됩니다.
