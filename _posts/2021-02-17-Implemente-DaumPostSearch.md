---
layout: post
title: "안드로이드에서 카카오 우편번호 검색 구현하기"
image: '/assets/img/'
description: 'Search Korea address with Kakao API'
tags:
- Android
categories:
- Android
---

우리나라에서 주소 관련 기능을 만들다 보면 우편번호 검색 기능을 사용해야 할때가 많이 있습니다. 보통 카카오에서 제공해주는 우편번호 검색 기능을 많이 사용하는데
해당 기능을 안드로이드에서 구현하는 방법에 대해서 알아보도록 하겠습니다. 

자세한 API 내용은 [여기](http://postcode.map.daum.net/guide) 에서 확인가능합니다.

> 아래 내용은 [DaumPostcodeExample](https://github.com/jolly73-df/DaumPostcodeExample) 를 참고하였습니다.

---

## 안드로이드

안드로이드에서는 웹뷰를 통해서 구현을 하고 보통 startActivityForResult를 통해서 주소를 가진 Activity를 호출하고 해당 호출된 Activity에서
선택한 주소를 받아서 처리하도록 하였습니다.

우선 MainActivity는 아래와 같이 만들었습니다.

```kotlin
import android.content.Intent
import androidx.appcompat.app.AppCompatActivity
import android.os.Bundle
import android.widget.Button
import android.widget.TextView
import com.jungwoon.findkakaoaddress.AddressActivity.Companion.ADDRESS_REQUEST_CODE

class MainActivity : AppCompatActivity() {

    private val button: Button by lazy {
        findViewById(R.id.button)
    }

    private val address: TextView by lazy {
        findViewById(R.id.address)
    }


    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        // 버튼을 누르면 AddressActivity를 띄워주는 부분
        button.setOnClickListener {
            Intent(this, AddressActivity::class.java).apply {
                startActivityForResult(this, ADDRESS_REQUEST_CODE)
            }
        }
    }

    override fun onActivityResult(requestCode: Int, resultCode: Int, data: Intent?) {
        super.onActivityResult(requestCode, resultCode, data)

        when (requestCode) {
            ADDRESS_REQUEST_CODE -> {
                if (resultCode == RESULT_OK) {
                    // 주소를 가져와서 보여주는 부분
                    val addressData = data?.extras?.getString("address")
                    address.text = addressData
                }
            }
        }
    }
}
```

MainActivity의 XML 부분입니다.

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:padding="18dp"
    tools:context=".MainActivity">

    <TextView
        android:id="@+id/address"
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        android:layout_marginBottom="24dp"
        android:gravity="center"
        app:layout_constraintBottom_toTopOf="@+id/button"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        tools:text="서울시 강남구 역삼동 테헤란로" />


    <Button
        android:id="@+id/button"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="우편번호 찾기"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintLeft_toLeftOf="parent"
        app:layout_constraintRight_toRightOf="parent"
        app:layout_constraintTop_toTopOf="parent" />

</androidx.constraintlayout.widget.ConstraintLayout>
```

---

AddressActivity부분은 아래와 같습니다.
(여기서 중요한 점은 HTML파일 주소를 설정할때 https를 사용하면 안된다는 부분입니다.)

```kotlin
import android.annotation.SuppressLint
import android.content.Intent
import androidx.appcompat.app.AppCompatActivity
import android.os.Bundle
import android.webkit.JavascriptInterface
import android.webkit.WebView
import android.webkit.WebViewClient

class AddressActivity : AppCompatActivity() {

    companion object {
        const val ADDRESS_REQUEST_CODE = 2928
    }

    @SuppressLint("SetJavaScriptEnabled")
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_address)

        val webView = findViewById<WebView>(R.id.web_view)

        webView.settings.javaScriptEnabled = true

        webView.addJavascriptInterface(KaKaoJavaScriptInterface(), "Android")
        webView.webViewClient = object : WebViewClient() {
            override fun onPageFinished(view: WebView, url: String) {
                webView.loadUrl("javascript:execKakaoPostcode();")
            }
        }

        // Kakao에서 https를 허용하지 않아서 https -> http로 바꿔야 동작함 (중요!!)
        webView.loadUrl("HTML파일 주소")
    }

    inner class KaKaoJavaScriptInterface {

        @JavascriptInterface
        fun processDATA(address: String?) {
            Intent().apply {
                putExtra("address", address)
                setResult(RESULT_OK, this)
            }
            finish()
        }
    }
}
```

여기는 xml 파일 소스 입니다. 단순히 웹뷰만 있는 구조입니다.

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".AddressActivity">

    <WebView
        android:id="@+id/web_view"
        android:layout_width="0dp"
        android:layout_height="0dp"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent" />

</androidx.constraintlayout.widget.ConstraintLayout>
```


---

## HTML 파일

아래 HTML을 WebView에서 호출해야지 사용할 수 있습니다. 아래 소스는 [DaumPostcodeExample](https://github.com/jolly73-df/DaumPostcodeExample) 를
참고하여 변수명이랑 이름등만 바꾸어서 사용하였습니다.
(문제가 된다면 바로 수정하도록 하겠습니다.)

아래 소스를 참고하여 호스팅하여 해당 주소를 호출하여 사용하면 됩니다.

```html
<!DOCTYPE html>

<html>
<body>
<!-- iOS에서는 position:fixed 버그가 있음, 적용하는 사이트에 맞게 position:absolute 등을 이용하여 top,left값 조정 필요 -->
<div id="layer" style="display:block;position:fixed;overflow:hidden;z-index:1;-webkit-overflow-scrolling:touch;">
</div>

<script src="http://dmaps.daum.net/map_js_init/postcode.v2.js"></script>

<script>
    window.addEventListener("message", onReceivedPostMessage, false);

    function onReceivedPostMessage(event) {
        //..ex deconstruct event into action & params
        let action = event.data.action;
        let params = event.data.params;
        console.log("onReceivedPostMessage " + event);
    }

    function onReceivedActivityMessageViaJavascriptInterface(json) {
        //..ex deconstruct data into action & params
        let data = JSON.parse(json);
        let action = data.action;
        let params = data.params;
        console.log("onReceivedActivityMessageViaJavascriptInterface " + event);
    }

    // 우편번호 찾기 화면을 넣을 element
    var element_layer = document.getElementById('layer');

    function execKakaoPostcode() {
        new daum.Postcode({
            oncomplete: function (data) {
                // 검색결과 항목을 클릭했을때 실행할 코드를 작성하는 부분.
                // 각 주소의 노출 규칙에 따라 주소를 조합한다.
                // 내려오는 변수가 값이 없는 경우엔 공백('')값을 가지므로, 이를 참고하여 분기 한다.
                var fullAddr = data.address; // 최종 주소 변수
                var extraAddr = ''; // 조합형 주소 변수

                // 기본 주소가 도로명 타입일때 조합한다.
                if (data.addressType === 'R') {

                    //법정동명이 있을 경우 추가한다.
                    if (data.bname !== '') {
                        extraAddr += data.bname;
                    }

                    // 건물명이 있을 경우 추가한다.
                    if (data.buildingName !== '') {
                        extraAddr += (extraAddr !== '' ? ', ' + data.buildingName : data.buildingName);
                    }

                    // 조합형주소의 유무에 따라 양쪽에 괄호를 추가하여 최종 주소를 만든다.
                    fullAddr += (extraAddr !== '' ? ' (' + extraAddr + ')' : '');

                }

                let fullRoadAddr = data.roadAddress; // 도로명 주소 변수
                let extraRoadAddr = ''; // 도로명 조합형 주소 변수

                // 법정동명이 있을 경우 추가한다. (법정리는 제외)
                // 법정동의 경우 마지막 문자가 "동/로/가"로 끝난다.
                if (data.bname !== '' && /[동|로|가]$/g.test(data.bname)) {
                    extraRoadAddr += data.bname;
                }

                // 건물명이 있고, 공동주택일 경우 추가한다.
                if (data.buildingName !== '' && data.apartment === 'Y') {
                    extraRoadAddr += (extraRoadAddr !== '' ? ', ' + data.buildingName : data.buildingName);
                }

                // 도로명, 지번 조합형 주소가 있을 경우, 괄호까지 추가한 최종 문자열을 만든다.
                if (extraRoadAddr !== '') {
                    extraRoadAddr = ' (' + extraRoadAddr + ')';
                }

                // 도로명, 지번 주소의 유무에 따라 해당 조합형 주소를 추가한다.
                if (fullRoadAddr !== '') {
                    fullRoadAddr += extraRoadAddr;
                }

                window.Android.processDATA(data.zonecode + ", " + fullRoadAddr);

            },
            width: '100%',
            height: '100%'
        }).embed(element_layer);

        // iframe을 넣은 element를 보이게 한다.
        element_layer.style.display = 'block';

        // iframe을 넣은 element의 위치를 화면의 가운데로 이동시킨다.
        initLayerPosition();
    }


    // 브라우저의 크기 변경에 따라 레이어를 가운데로 이동시키고자 하실때에는
    // resize이벤트나, orientationchange이벤트를 이용하여 값이 변경될때마다 아래 함수를 실행 시켜 주시거나,
    // 직접 element_layer의 top,left값을 수정해 주시면 됩니다.
    function initLayerPosition() {
        let width = (window.innerWidth || document.documentElement.clientWidth); //우편번호서비스가 들어갈 element의 width
        let height = (window.innerHeight || document.documentElement.clientHeight); //우편번호서비스가 들어갈 element의 height
        let borderWidth = 5; //샘플에서 사용하는 border의 두께

        // 위에서 선언한 값들을 실제 element에 넣는다.
        element_layer.style.width = width + 'px';
        element_layer.style.height = height + 'px';
        element_layer.style.border = borderWidth + 'px solid';

        // 실행되는 순간의 화면 너비와 높이 값을 가져와서 중앙에 뜰 수 있도록 위치를 계산한다.
        element_layer.style.left = (((window.innerWidth || document.documentElement.clientWidth) - width) / 2 - borderWidth) + 'px';
        element_layer.style.top = (((window.innerHeight || document.documentElement.clientHeight) - height) / 2 - borderWidth) + 'px';
    }

</script>


</body>
</html>
```