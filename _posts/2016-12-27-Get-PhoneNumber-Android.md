---
layout: post
title: "안드로이드 핸드폰 번호 가져오기"
image: '/assets/img/'
description: 'Telephony Manager를 이용하여 핸드폰 번호 가져오기'
tags:
- Example
- TelephonyManager
categories:
- Android
---

## Telephony를 통한 핸드폰 번호 가져오기

다음 소스는 TelephonyManager를 통한 핸드폰 번호 가져오기

```java
@Override
public String getPhoneNumber() {
    TelephonyManager telephony = (TelephonyManager) getSystemService(Context.TELEPHONY_SERVICE);
    String phoneNumber ="";

    try {
        if (telephony.getLine1Number() != null) {
            phoneNumber = telephony.getLine1Number();
        }
        else {
            if (telephony.getSimSerialNumber() != null) {
                phoneNumber = telephony.getSimSerialNumber();
            }
        }
    }
    catch(Exception e) {
        e.printStackTrace();
    }

    return phoneNumber;
}
```
