---
layout: post
title: "자바에서 직렬화를 이용한 소켓 통신 정리"
image: '/assets/img/'
description: 'Java Serialize with Socket'
tags:
- Java
categories:
- Java
---

최근 업무에서 자바 직렬화와 소켓을 이용하여 통신을 해야하는 일이 있었는데 관련된 부분들 정리를 하려고 합니다.

설명은 주석을 통해서 하도록 하겠습니다.

---

### 자바 직렬화란?

```

객체 -> 직렬화 -> 바이트 -> 역직렬화 -> 객체 

```

자바 시스템간 객체나 데이터를 전달하기 위해 바이트로 바꿨다가 다시 객체로 바꾸는 기술입니다.

---

### 직렬화 클래스

우선 자바 직렬화 클래스를 만들어야 하는데, 
`java.io.Serializable` 인터페이스를 구현하는 `concrete 클래스`를 만듭니다.


```java
package com.test; // 반드시 서버와 동일한 위치에 있어야 함

import java.io.Serializable;

public class Unit implements Serializable {
    // serialVersionUID는 통신하는 자바 시스템간 동일해야 합니다.
    private static final long serialVersionUID = 1L;

    private int code;
    private int size;
    private byte[] data;

    public int getCode() {
        return code;
    }

    public void setCode(int code) {
        this.code = code;
    }

    public byte[] getData() {
        return data;
    }

    public void setData(byte[] data) {
        this.data = data;
    }

    public int getSize() {
        return size;
    }

    public void setSize(int size) {
        this.size = size;
    }

}

```

주의해야 할 점은 `serialVersionUID`는 자바 시스템간에 동일해야 하는데,
만약 따로 설정을 하지 않으면, 클래스의 기본 해쉬값을 사용합니다.

`** 직접 세팅하지 않아도 자동으로 생성이 된다.`

설정을 해주지 않게 되면 나중에 위의 클래스의 멤버변수의 변경이 일어나면 에러가 발생하기 때문에
`직접 설정을 해주는 것이 좋습니다.`

---

### 서버 클래스

`서버 클래스`에서 중요한 점은 `ObjectInputStream`객체를 이용해서 역직렬화를 하는 부분과
`Client`에 응답을 하는  `printWriter` 부분입니다.  

```java
package com.test;

import java.io.*;
import java.net.ServerSocket;
import java.net.Socket;

public class Server {
    private final static int SERVER_PORT = 4000;

    public static void main(String args[]){
        ServerSocket serverSocket; // Client의 접속을 기다리는 역활을 함
        Socket socket; // Client와 데이터 송수신 역활을 함

        ObjectInputStream objectInputStream; // Class의 객체를 읽어올때 사용
        PrintWriter printWriter; // 값을 전달할때 사용

        try {
            //서버 소켓 생성
            serverSocket = new ServerSocket(SERVER_PORT);
            System.out.println("Server Running");

            while (true) {

                System.out.println("before socket connect");
                // 서버는 정지상태가 되며, 클라이언트의 접속을 기다립니다.
                // 클라이언트의 접속이 되면 그 다음으로 넘어갑니다.
                socket = serverSocket.accept();

                System.out.println("after socket connect");
                printWriter = new PrintWriter(new BufferedWriter(new OutputStreamWriter(socket.getOutputStream())));

                // Client 로부터 객체를 읽어오는 역활을 하는 객체를 생성
                objectInputStream = new ObjectInputStream(socket.getInputStream());
                Order order = (Order)objectInputStream.readObject(); // readObject는 object 객체로 불러오기 때문에 형변화해야 합니다.
                System.out.println(order.toString());


                printWriter.write("ok");
                printWriter.close(); // close() or flush()를 해줘야지 전해진다
                socket.close(); // 여기서 socket 접속이 끊어져야 클라이언트가 종료가 됩니다.

            }
        }
        catch (Exception e){
            e.printStackTrace();
        }
    }
}
```

---

### 클라이언트 클래스

`클라이언트 클래스`에서 중요한 점은 `ObjectOutputStream`을 이용하여 직렬화 하는 부분과
`BufferedReader`를 이용하여 서버로부터 데이터를 받는 부분입니다.

```java
package com.test;

import java.io.*;
import java.net.*;

public class Client {
    public static void main(String[] args) {

        try {
            // 입력받은 인수가 2개가 아니면 예외 발생 (IP와 PORT)
            if (args.length != 2) throw new IllegalArgumentException("Arguments something wrong");

            String ip = args[0]; // ip
            int port = Integer.parseInt(args[1]); // port

            System.out.println("Server Information : " + ip + " : " + port);

            //서버로 접속 시도를 할 때 서버는 먼저 작동되고 있어야 합니다.
            Socket socket = new Socket(ip, port);

            // ObjectOutputStream()을 이용하여 직렬화를 합니다.
            // socket 통신을 하기 때문에 socket의 stream을 가져옵니다.
            ObjectOutputStream objectOutputStream = new ObjectOutputStream(socket.getOutputStream());

            // Server로부터의 응답을 받기 위한 부분
            // socket 통신을 하기 때문에 socket의 stream을 가져옵니다.
            BufferedReader bufferReader = new BufferedReader(new InputStreamReader(socket.getInputStream()));

            Order order = new Order("Jungwoon", 3, "Chicken");
            objectOutputStream.writeObject(order); // 데이터 직렬화
            objectOutputStream.flush(); // 직렬화된 데이터 전달

            // 서버로부터 메시지 받기 위한 부분
            String returnMessage = bufferReader.readLine();
            System.out.println("returnMessage : " + returnMessage);

            objectOutputStream.close();
            socket.close();
        }
        catch (Exception e) {
            System.err.println("Usage: java Client <hostname> <port:4000>");
            e.printStackTrace();
        }

    }
}
```

### 주의할 점

1. 직렬화 클래스의 `serialVersionUID의 값은 동일` 해야 한다
2. 서버와 클라잉너트의 직렬화 클래스의 `패키지 위치가 동일`해야 한다. (여기예제에서는 `com.test`)  

---

### 마무리

사실 주의할 점 부분을 정리하기 위해서였는데, 패키지 위치가 동일하지 않으면,
통신이 되질 않습니다, 이 부분 주의해주세요

(이것때문에 며칠간 삽질을 했는지.....)