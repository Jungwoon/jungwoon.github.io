---
layout: post
title: "정규 표현식(Regular Expressions) 정리하기"
image: '/assets/img/'
description: '정규 표현식(Regular Expressions) 정리하기'
tags:
- Regex
categories:
- Regex
---

이번에는 정규 표현식을 한번 정리해보도록 하겠습니다.
정규 표현식은 텍스트에서 특정 패턴만 골라서 가져올 수 있는 기술로
파싱이나 크롤링때 많이 이용합니다

표현이 힘들어서, 키노트로 만들어서 해당 키노트를 캡처해서 올렸습니다.
해당 자료는 [슬라이드 쉐어의 김민태님의 자료](https://www.slideshare.net/ibare/ss-39274621)를 `엄청` 많이 참고하였습니다.
위의 자료를 제가 공부하면서 정리한 자료를 공유 드립니다.

포스팅한 PDF 다운로드는 아래 공유 드리도록 하겠습니다.

> 다운로드 : [PDF 다운로드](https://www.dropbox.com/s/du6hrakweehua9l/Regex_wide.pdf?dl=0)

> 정규 표현식 연습 사이트 : [연습 사이트](https://regexr.com)


---

![](https://cdn-images-1.medium.com/max/2000/1*hmwnjCP6-AZS0_HVG_Ypgw.png)

---

![](https://cdn-images-1.medium.com/max/2000/1*2YZ6oziwa50BNILGEO4Gzg.png)

---

![](https://cdn-images-1.medium.com/max/2000/1*acarjGXxgx-GwlkwpSUAxw.png)

---

![](https://cdn-images-1.medium.com/max/2000/1*v8Bbj9sIaSNXWSdsZVOsDA.png)

---

![](https://cdn-images-1.medium.com/max/2000/1*bEkCLHJqVVdQH7eE7I8ZuA.png)

---

![](https://cdn-images-1.medium.com/max/2000/1*2dA4dUm5BA6H3ihNbNctiQ.png)

---

![](https://cdn-images-1.medium.com/max/2000/1*lKz51x5evPUmn-hoal55Rw.png)

---

![](https://cdn-images-1.medium.com/max/2000/1*_2v45N3IhVetfxPJ_oL-FQ.png)

---

![](https://cdn-images-1.medium.com/max/2000/1*O52q8DRrxMJZXczyIPLmgQ.png)

---

![](https://cdn-images-1.medium.com/max/2000/1*tOq3Yg0RJR6lhQoXo5bvgw.png)

---

![](https://cdn-images-1.medium.com/max/2000/1*ecGkhw9aaJiNTnqsk0Y6lA.png)

---

![](https://cdn-images-1.medium.com/max/2000/1*Eb7nm5BhEF6p_UCbDxcMJg.png)

---

![](https://cdn-images-1.medium.com/max/2000/1*BKcLZKRBRstCbspRHr_LJg.png)

---

![](https://cdn-images-1.medium.com/max/2000/1*vMpS4bajMa61om8sR6wTWQ.png)

---

![](https://cdn-images-1.medium.com/max/2000/1*iLkOVmIBKNqFlIhU5E9eUQ.png)

---

![](https://cdn-images-1.medium.com/max/2000/1*SEMaahntQKDHcpLKHCvNsg.png)

---

![](https://cdn-images-1.medium.com/max/2000/1*CWEu6caIG0fYUaPGCrFUqA.png)

---

![](https://cdn-images-1.medium.com/max/2000/1*m-obaiC8tL4Hsz9jEyOrmA.png)

---

![](https://cdn-images-1.medium.com/max/2000/1*PQ3Z2RHjgU2yXxAP0T80xw.png)

---

![](https://cdn-images-1.medium.com/max/2000/1*x-R_NFMHSDvph1RfLReFWA.png)

---

![](https://cdn-images-1.medium.com/max/2000/1*SgE4JDkXM0K2jhLa51XcCQ.png)

---

![](https://cdn-images-1.medium.com/max/2000/1*IzI2TnilTU1w1P3BIpNWNw.png)

---

![](https://cdn-images-1.medium.com/max/2000/1*b18DvDm-tB-KMBgapavbrg.png)

---

![](https://cdn-images-1.medium.com/max/2000/1*PZazvbDxrbt1QnyOmmlClQ.png)

---

![](https://cdn-images-1.medium.com/max/2000/1*DJXb-mi3ik7jh6-I_zPXCg.png)

---

![](https://cdn-images-1.medium.com/max/2000/1*Z0S54d0HsXm-yc2suaty0Q.png)

---

![](https://cdn-images-1.medium.com/max/2000/1*a3gX8qQlhaDY37N3nx4ccQ.png)

---

![](https://cdn-images-1.medium.com/max/2000/1*eld5WR6zOQ2pBbN9fJL5NA.png)

---

![](https://cdn-images-1.medium.com/max/2000/1*KAJ_n-O1KYMGVOHIGS4tNg.png)

---

![](https://cdn-images-1.medium.com/max/2000/1*ifHPZLA8uiUzGgqOUdtUKA.png)

---

![](https://cdn-images-1.medium.com/max/2000/1*RgQ3_33-39kKRwbne8pOag.png)

---

![](https://cdn-images-1.medium.com/max/2000/1*PP_1kQDu_N21JGeUhHmoyg.png)

