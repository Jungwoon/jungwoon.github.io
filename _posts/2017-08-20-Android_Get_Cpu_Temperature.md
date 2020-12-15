---
layout: post
title: "Android CPU 온도 구하기"
image: '/assets/img/'
description: 'Android CPU 온도 구하기'
tags:
- Android
categories:
- Anroid
---

최근에 회사에서 Android CPU 온도를 구해야 하는 일이 있었는데 관련해서 소스 공유 합니다
따로 API가 지원되는건 아니고 Linux안에 있는 정보를 가져와서 처리를 하는걸로 보입니다

### Source

```java
public float getCpuTemperature() {
    Process process;

    try {
        process = Runtime.getRuntime().exec("cat sys/class/thermal/thermal_zone0/temp");
        process.waitFor();

        BufferedReader bufferedReader = new BufferedReader(new InputStreamReader(process.getInputStream()));
        String line = bufferedReader.readLine();

        float temperature = Float.parseFloat(line) / 1000.0f;
        return temperature;
    }
    catch (Exception e) {
        e.printStackTrace();
        return 0.0f;
    }
}

```