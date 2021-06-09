## 용어 정의
New Project 창에서 보이는 용어 의미를 간단히 짚어보면,
- <u>package name</u>은 각 앱을 구분하는 <b>고유 아이디</b>라고 생각하면 된다. 그러니까 겹치지않게 하도록 주의해야하고!<br>
- <u>Minimum SDK</u>는 최소 지원하는 SDK 버전인데, 너무 낮은 버전을 쓸 경우, 다양한 기능을 사용하지 못한다고 한다.
다양한 기능들이 추가된 버전은 API26이라고 한다.(이것도 나중에는 너무 낮은 버전이 되겠지..?) <br>
ps: 그래서 API 26: Android 8.0 (Oreo)로 진행! 현재 현업에서는 21 Lollipop을 사용한다고 하네용<br>

## 파일 정의
이제 처음 project를 생성하고나면 Emulator로 볼 수 있는 기본화면을 구성하는 파일들을 살펴보겠다.<br>
크게 manifests, java, res, Gradle scripts가 보인다.
1. manifests<br>
앱의 사용설명서. AndroidManifests.xml에는 앱의 기본 구성 요소들이 적혀있는 것을 볼 수 있음
2. java<br>
java에는 package명 폴더들이 있다.
여기에는 기본 화면을 작동시키는 파일 'MainActivity.kt'가 있다.
3. res<br> 
Resource의 줄임말로, 이 폴더에는 앱의 Resource인 drawble, layout, maipmap, values가 있다. <br>
나머지는 차차 알아가고, 가장 중요한 layout폴더!는 어떤 화면을 보여줄지에 대한 정보를 담고 있다.
특히 activity_main.xml에는 레이아웃의 사용자 인터페이스가 정의되어있음.
4. Gradle Scripts
여기에는 build.gradle파일이 있는데, 앱을 apk로 만들기 위한 정보가 들어있다 생각하면 된다.<br>
project gradle은 전체 project에 대해, module gradle은 은 한 앱에 대한 설명이라고 생각하면 된다. 

의미적으로 정리는 해봤는데, 사실 이런 걸 머릿속에 넣기보다 개발하면서 익히는게 더 효율적이라 생각한다.ㅎㅎ

## 간단한 실습
- 앱 이름 수정 : res/values/strings.xml 파일에서 app 이름을 수정
- 앱 아이콘 수정 : res/mipmap/에 사진을 넣어주면 app icon을 수정
- 기본화면 수정: 
  - res/layout/activity_main.xml에서는 layout editor를 이용해서도 디자인할 수 있음
  - Design view(보여질 화면)와 Blueprint(레이아웃의 윤곽선)으로 화면을 구성할 수 있다.
  - 이미지 추가 :
    1. res/drawble에 추가할 이미지를 (소문자로) 넣어준다.
    2. activity_main.xml에서 ImageView로 이미지를 추가한다.
    3. 이미지 박스에 있는 빈 원을 각 화면테두리에 땡겨 맞춘다. (지금만 수동으로 해보는겁니다! 그럼 왜 해줘야하냐?<br>
    android는 device 해상도가 정~~말 다양하기 때문에 반응형으로 device를 지원하기 위해! 즉 기준점을 잡아준 것이라 생각하자.)

