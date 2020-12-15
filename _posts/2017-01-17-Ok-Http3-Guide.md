---
layout: post
title: "OkHttp3를 간단 사용법"
image: '/assets/img/'
description: 'OkHttp3를 이용한 네트워크 통신'
tags:
- OkHttp3
- Guide
categories:
- Android
---


Square사의 오픈소스 라이브러리인 OkHttp3는 Retrofit에서도 이용되고 있는 아주 유용한, 라이브러리 입니다. Restful한 개발이 필요하면 Retrofit을 이용하고, 만약에 Rest를 사용하지 않으면, OkHttp3로 충분하다고 생각이 듭니다. 물론 네트워크 처리는 MainThread와 구분하여 처리해주어야 합니다.

#### Gradle에 설정하기

```
compile 'com.squareup.okhttp3:okhttp:3.5.0'
```

#### 응답 메시지를 받기 위한 선언부

```java
OkHttpClient client = new OkHttpClient();

// OkHttp3를 활용하여 구현함
public String httpRun(String url) throws IOException {
    Request request = new Request.Builder().url(url).build();

    try (Response response = client.newCall(request).execute()) {
        return response.body().string();
    }
}
```

#### 네트워크에서 응답 메시지 받아서 변수에 저장하기

```java
String response = httpRun(url);
```

#### 파일 다운로드

```java
@Override
public void downloadFile() {
    new Thread(new Runnable() {
        @Override
        public void run() {
            // 파일이 저장될 경로
            String savePath = android.os.Environment.getExternalStorageDirectory().toString() + "/" + fileName;

            Response connection = null;
            InputStream inputStream = null;
            ByteArrayOutputStream outputStream = null;
            FileOutputStream fileOutputStream = null;

            try {
                URL url = new URL("Download URL");

                connection = client.newCall(new Request.Builder().url(url).build()).execute(); // OkHttp
                inputStream = new BufferedInputStream(connection.body().byteStream());
                outputStream = new ByteArrayOutputStream();

                byte[] buffer = new byte[4096];
                int readLength;

                while ((readLength = inputStream.read(buffer)) != -1) {
                    outputStream.write(buffer, 0, readLength);
                }

                byte[] readByte = outputStream.toByteArray();

                fileOutputStream = new FileOutputStream(savePath);
                fileOutputStream.write(readByte);
            }
            catch (Exception e) {
                FirebaseCrash.report(e);
                e.printStackTrace();
            }
            finally {
                try {
                    if (fileOutputStream != null) fileOutputStream.close();
                    if (outputStream != null) outputStream.close();
                    if (inputStream != null) inputStream.close();
                    if (connection != null) connection.close();
                }
                catch (Exception e) {
                    FirebaseCrash.report(e);
                    e.printStackTrace();
                }
            }
        }
    }).start();
}
```

> 자세한 내용은 [OkHttp](http://square.github.io/okhttp/)를 참고하시기 바랍니다.
