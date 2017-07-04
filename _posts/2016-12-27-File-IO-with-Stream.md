---
layout: post
title: "Stream을 이용하여 파일 읽고 쓰기"
image: '/assets/img/'
description: 'Stream을 이용하여 파일 읽고 쓰기'
tags:
- Example
- Stream
- File I/O
categories:
- Android
---

## Stream을 이용하여 파일 읽고 쓰기

다음 소스는 Stream을 이용하여 파일을 읽고 쓰는 예제입니다.

```java
@Override
public void Write(String value) {
    try {
        File file = new File(getPath(), "FILE_NAME");

        if (!file.exists()) {
            if (!file.createNewFile()) {
                throw new Exception("createNewFile() Exception");
            }
        }

        FileOutputStream fileOutputStream = new FileOutputStream(file.getAbsolutePath());

        byte[] bytes = value.getBytes();
        fileOutputStream.write(bytes, 0, bytes.length);
        fileOutputStream.close();

    }
    catch (Exception e) {
        e.printStackTrace();
    }
}

@Override
public String Read() {
    try {
        String result = null;
        String readData;

        File file = new File(getPath(), "FILE_NAME");

        BufferedReader bufferedReader = new BufferedReader(new FileReader(file));

        while ((readData = bufferedReader.readLine()) != null) {
            result = readData;
        }

        bufferedReader.close();

        return result;

    }
    catch (Exception e) {
        e.printStackTrace();
        return null;
    }
}

@Override
public String getPath() {
    try {
        File directory = new File(android.os.Environment.getExternalStorageDirectory().toString(), "DIRECTORY_NAME");

        if (!directory.exists()) {
            if (!directory.mkdirs()) {
                throw new Exception("mkdirs() Exception");
            }
        }

        return directory.getAbsolutePath();
    }
    catch (Exception e) {
        e.printStackTrace();
        return null;
    }
}

```
