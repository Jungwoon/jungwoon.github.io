---
layout: post
title: "Usage Stats 권한 체크하기"
image: '/assets/img/'
description: 'Usage Stats 권한있는지 체크하기'
tags:
- Example
- Usage Stats
categories:
- Android
---

## Usage Stats 권한을 가지고 있는지 확인

다음 소스는 해당 프로젝트의 Usage 권한이 있는지 확인하는 예제입니다.

```java
@Override
public boolean checkUsageStatsPermissions() {
    try {
        PackageManager packageManager = getPackageManager();
        ApplicationInfo applicationInfo = packageManager.getApplicationInfo(getPackageName(), 0);
        AppOpsManager appOpsManager = (AppOpsManager) getSystemService(Context.APP_OPS_SERVICE);

        int mode = appOpsManager.checkOpNoThrow(AppOpsManager.OPSTR_GET_USAGE_STATS, applicationInfo.uid, applicationInfo.packageName);
        return (mode == AppOpsManager.MODE_ALLOWED);
    }
    catch (PackageManager.NameNotFoundException e) {
        return false;
    }
}
```
