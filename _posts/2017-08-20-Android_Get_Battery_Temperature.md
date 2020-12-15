---
layout: post
title: "Android 배터리 온도 구하기"
image: '/assets/img/'
description: 'Android 배터리 온도 구하기'
tags:
- Android
categories:
- Anroid
---

최근에 회사에서 Android 배터리 온도를 구해야 하는 일이 있었는데 관련해서 소스 공유 합니다
따로 BroadcastReceiver로부터 배터리 변화에 대한 정보를 가져와서 처리를 하게 됩니다

### Source

```java

@Override
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_main);
    
    Toolbar toolbar = (Toolbar) findViewById(R.id.toolbar);
    setSupportActionBar(toolbar);

    batteryTemperature = (TextView) findViewById(R.id.batteryTemperature);
    intentFilter = new IntentFilter(Intent.ACTION_BATTERY_CHANGED);
    registerReceiver(broadcastReceiver, intentFilter);

}


private BroadcastReceiver broadcastReceiver = new BroadcastReceiver() {
    @Override
    public void onReceive(Context context, Intent intent) {
        BatteryTemp = (float)(intent.getIntExtra(BatteryManager.EXTRA_TEMPERATURE, 0)) / 10;
        batteryTemperature.setText(BatteryTemp + " " + (char) 0x00B0 + "C");
    }
};


```