---
layout: post
title: Blog post를 위한 markdown 사용법
comments: true
categories: [Blog]
tags: [blog, jekyll, markdown]
---
Gihub.io 를 통해 blogging을 하다보니 Markdown에 대한 정리가 필요하다.

# Markdown?
>마크다운(Markdown)은 일반 텍스트 기반의 경량 마크업 언어다. 일반 텍스트로 서식이 있는 문서를 작성하는 데 사용되며, 일반 마크업 언어에 비해 문법이 쉽고 간단한 것이 특징이다. HTML과 리치 텍스트(RTF) 등 서식 문서로 쉽게 변환되기 때문에 응용 소프트웨어와 함께 배포되는 README 파일이나 온라인 게시물 등에 많이 사용된다.
>
>> -- <cite>[Link 위키백과][1]</cite>   
>> -- <cite>[New tab Link 위키백과](https://ko.wikipedia.org/wiki/%EB%A7%88%ED%81%AC%EB%8B%A4%EC%9A%B4){:target="_blank"}</cite>

[1]: https://ko.wikipedia.org/wiki/%EB%A7%88%ED%81%AC%EB%8B%A4%EC%9A%B4

---
# 사용법
---
## 제목  
제목의 범위는 1 - 6 까지 사용 할 수 있다.  

---
``` 
# 제목1
## 제목2
### 제목3
#### 제목4
##### 제목5
###### 제목6
```
---
### 제목3
#### 제목4
##### 제목5
###### 제목6
---
## 강조
---
```
*기울기 기울기 기울기 기울기 기울기*
_기울기 기울기 기울기 기울기 기울기_

**굵게 굵게 굵게 굵게 굵게**
__굵게 굵게 굵게 굵게 굵게__

*기울기 **기울기 굵게** 기울기*

~~취소선~~
```

---
*기울기 기울기 기울기 기울기 기울기*
_기울기 기울기 기울기 기울기 기울기_   

**굵게 굵게 굵게 굵게 굵게**  
__굵게 굵게 굵게 굵게 굵게__   

*기울기 **기울기 굵게** 기울기*   

~~취소선~~

---
## 목록
---
```
Unordered
- 내용 1
- 내용 2
    - 내용 2-1
    - 내용 2-2
* 내용 1
* 내용 2
    * 내용 2-1
    * 내용 2-2
+ 내용 1
+ 내용 2
    + 내용 2-1
    + 내용 2-2
Ordered
1. 내용 1
2. 내용 2
    1. 내용 2-1
    2. 내용 2-2
```
---
**Unordered**
- 내용 1
- 내용 2
    - 내용 2-1
    - 내용 2-2
* 내용 1
* 내용 2
    * 내용 2-1
    * 내용 2-2
+ 내용 1
+ 내용 2
    + 내용 2-1
    + 내용 2-2

**Ordered**
1. 내용 1
2. 내용 2
    1. 내용 2-1
    2. 내용 2-2

---
## 이미지
---
```
# 첫 번째
![Image name](/image/address) 

# 두 번째
<a href="#"><img src="https://image_address" width="300px" height="300px" title="px 설정" alt="image"></a> 

# 세 번째
<a href="#이미지"><img src="https://image_address" width="40%" height="30%" title="px 설정2" alt="image"></a> 
```
---
![Alt name](/assets/img/favicons/android-chrome-192x192.png) 

<a href="#"><img src="https://won-u.github.io/assets/img/favicons/android-chrome-192x192.png" width="192px" height="300px" title="px 설정" alt="ant_image"></a>

<a href="#이미지"><img src="https://won-u.github.io/assets/img/favicons/android-chrome-192x192.png" width="40%" height="30%" title="px 설정2" alt="ant_image2"></a>

---
## 링크

---
```
# 첫 번째
[첫 번째 링크][id_1]

[id_1]: https://first_link_uri

# 두 번째
[두 번째 링크](https://second_link_uri)

# 세 번째 (새 탭 링크)
[세 번째 링크][id_2]{:target="_blank"}

[id_2]: https://third_link_uri

# 네 번째 (새 탭 링크)
[네 번째 링크](https://fourth_link_uri "Hinting"){:target="_blank"}

# 다섯 번째 (이미지 링크)
<a href="https://image_address"><img src="https://image_address" width="300px" alt="image"></a> 

# 여섯 번째
* 외부링크: <http://example.com>
```

---

[Google][id_1]

[id_1]: https://www.google.com/  

[Amazon](https://www.amazon.com/)

[Apple(new tab)][id_2]{:target="_blank"}

[id_2]: https://www.apple.com

[GitHub(new tab)](https://www.github.com "깃 허브"){:target="_blank"}

<a href="https://www.github.com"><img src="https://won-u.github.io/assets/img/favicons/android-chrome-192x192.png" width="300px" alt="ant_image"></a> 

* 외부링크: <http://example.com>

---
## 코드 블록
---
```
    ```cpp
    #include <iostream>
    int32_t main() {
        return 0;
    }
    ```

    This is a normal paragraph:

    #include <iostream>
    int32_t main() {
        return 0;
    }
    
    end code block.
```
---
```cpp
#include <iostream>

int32_t main() {
    return 0;
}
```
This is a normal paragraph:

    #include <iostream>
    int32_t main() {
        return 0;
    }
    
end code block.

---
## 인라인 코드
---
```
Blah blah `std::cout << "출력" << std::endl;` blah blah~
```
---
Blah blah `std::cout << "출력" << std::endl;` blah blah~
---
## 인용구
---
```
Blah blah ~
> Block quotes
> 
> Block quotes 2
> 
> -- Block quotes 3
>> Block quotes 4  
>> -- Block quotes 5  
```
---
Blah blah ~
> Block quotes
> 
> Block quotes 2
> 
> -- Block quotes 3
>> Block quotes 4  
>> -- Block quotes 5  

---
## 테이블
---
```
| Name | Name |
| --- | --- |
| column a-2 | column b-2 |
| column a-3 | column b-3 |
```
---

| Name First | Name Second |
| --- | --- |
| column a-2 | column b-2 |
| column a-3 | column b-3 |

---
## 체크박스
---
- [x] Complete 1
- [ ] Incomplete 1
- [ ] Incomplete 2
- [x] Complete2

---
## 수평선 <hr/>
---
```
* * *

***

*****

- - -

---------------------------------------
```
---
* * *

***

*****

- - -

---------------------------------------

# 참고
>-- <https://post.naver.com/viewer/postView.nhn?volumeNo=24627214&memberNo=42458017>   
>-- <https://gist.github.com/ihoneymon/652be052a0727ad59601>