<h3> Kotlin3 </h3>

> 스파르타코딩클럽의 『안드로이드 앱개발 기초』강의를 들으며 실습한 내용입니다.

<h4># TextView</h4>
- 텍스트를 비활성화하려면 <br>
  오른쪽 메뉴의 'Attributes'에서 'enabled'를 검색하면 true, false를 지정할 수 있음
  - Switch도 가능!
<br><br>
<h4># Switch</h4>
- 처음부터 체크되어있도록하려면<br>
  오른쪽 메뉴의 'Attributes'에서 'checked'를 검색하면 true, false를 지정할 수 있음
<br><br>
<h4># SeekBar</h4>
- 처음부터 100%로 채워놓으려면<br>
  'Attributes'에서 'progress'를 검색 - 100 입력<br>


+) 우측 메뉴에서 아래로 내려가보면 <All attribute>가 있는데
여기서 해당 아이템에서 쓸 수 있는 속성들을 볼 수 있음

<h4># 실습</h4>

<b><로그인 페이지></b>
- Text에 있는 'E-mail'과 'Password' 아이템을 이용
- 'Attributes'의 '`hint`' 옵션을 통해 나타낼 문구를 적을 수 있음 (회색으로 나타남)
  - 입력과 동시에 hint로 적어놓은 텍스트가 사라짐
- 비밀번호 입력 시 ***와 같은 보안기능이 자동으로 동작함<br><br>

<b><자기소개 앱></b>
- 화면에 Image를 추가하려면, res/drawer 폴더에 사진 추가
- 앱 아이콘을 변경하려면, res/mipmap 폴더에 사진 변경
- 앱 이름을 변경하려면, res/values/strings.xml에서 변경