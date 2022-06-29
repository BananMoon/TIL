### kotlin3
> 스파르타코딩클럽의『안드로이드 앱개발 기초』강의를 들으며 실습한 내용입니다.

- 기존
`app:layout_constraintEnd_toEndOf="parent/id"` 
-> 자신의 끝이 부모/id의 끝과 동일하다.
`app:layout_constraintTop_toBottomOf="@+id/editTextTextPassword"`
-> 자신의 시작이 부모/id의 끝과 동일하다.
- 위처럼 맞춰주게 되면 해당 layout의 정렬기준이 맞춰짐. (쉽게말해, 4개의 구멍이 채워지면서 각 id/parent에 연결됨)
### 자주 쓰이는 LayerView 
>ConstraintLayout
- 4가지가 있다.<br>
End_toEndOf : 오른쪽<br>
Start_toStartOf: 왼쪽<br>
Bottom_toBottomOf: 아래<br>
Top_toBottomOf: 위<br>

주의) Pixel 화면마다 layout 위치가 달라짐

<h4># LinearLayout, ScrollView</h4>

> LinearLayout
- constraint와 다르게 순서대로 쌓이기 때문에 따로 위치를 설정해줄 필요가 없다.

- `android:orientation="vertical"` -> 수직으로 추가

- `android:gravity="center"` -> 정렬<br>
   왼쪽 정렬하려면, `left_center`

> ScrollView
- view가 화면을 넘어갈 길이가 됐을 때 ScrollView를 이용하여 LinearLayout을 감싸준다.

주의) ScrollView로 감쌀 때는 LinearLayout의 layout_height는 `wrap_content`이어야한다!

> 결론
- LinearLayout은 ConstraintLayout보다 단순하게 view를 추가할 수 있는 layout
- 자주 쓰이는 layout과 ScrollView!
- ScrollView 안에는 LinearLayout을 사용하는 것이 편하다.
---
### 실습 - 성격 유형 조회 화면 뼈대잡기
1. 이미지뷰, 텍스트뷰, 버튼 추가하기
- 화면에 띄울 이미지는 res/drawble 폴더에 넣어줘야한다.

ConstraintLayout을 이용한 XML
```
    <ImageView
        android:id="@+id/introImageView"
        android:layout_width="200dp"
        android:layout_height="200dp"
        android:layout_marginTop="32dp"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent"
        app:srcCompat="@drawable/rtanny" />

    <TextView
        android:id="@+id/introTextView"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginTop="32dp"
        android:gravity="center"
        android:text="성격 유형 조회"
        android:textSize="32sp"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toBottomOf="@+id/introImageView" />

    <Button
        android:id="@+id/buttonIntj"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginTop="100dp"
        android:text="INTJ"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintTop_toTopOf="@+id/introTextView"/>
```
LinearLayout을 이용한 XML
```
    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:gravity="center_horizontal"
        android:orientation="vertical">
        <ImageView
            android:id="@+id/imageView"
            android:layout_width="200dp"
            android:layout_height="200dp"
            android:layout_marginTop="50dp"
            app:srcCompat="@drawable/rtanny" />
        <TextView
            android:layout_width="wrap_content"
            android:layout_height="60dp"
            android:text="성격 유형 조회"
            android:layout_marginTop="50dp"
            android:textSize="32sp" />

        <Button
            android:layout_width="100dp"
            android:layout_height="50dp"
            android:layout_marginTop="100dp"
            android:text="INTJ"/>
    </LinearLayout>
```

### 추가) 1개의 버튼을 16개로 늘려서, 4x4 성격유형 버튼을 띄운다. 버튼 부분에만 scroll을 넣어본다.
- 전체 개요 : ScrollView 안에 LinearView를 추가하고, 그 안에 button을 4개씩 나눠서 LinearView를 생성한다. (vertical과 horizontal 유지를 위해)
```

    <ScrollView
        android:layout_width="match_parent"
        android:layout_height="0dp"
        android:layout_marginTop="32dp"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintTop_toBottomOf="@+id/introTextView">

        <LinearLayout
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:gravity="center"
            android:orientation="vertical">

            <LinearLayout
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:gravity="center"
                android:orientation="horizontal">

                <Button
                    android:layout_width="wrap_content"
                    android:layout_height="100dp"
                    android:layout_margin="4dp"
                    android:text="ISTJ" />

                <Button
                    android:layout_width="wrap_content"
                    android:layout_height="100dp"
                    android:layout_margin="4dp"
                    android:text="ISFJ" />

                <Button
                    android:layout_width="wrap_content"
                    android:layout_height="100dp"
                    android:layout_margin="4dp"
                    android:text="INFJ" />

                <Button
                    android:layout_width="wrap_content"
                    android:layout_height="100dp"
                    android:layout_margin="4dp"
                    android:text="INTJ" />
            </LinearLayout>

            <LinearLayout
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:gravity="center"
                android:orientation="horizontal">

                <Button
                    android:layout_width="wrap_content"
                    android:layout_height="100dp"
                    android:layout_margin="4dp"
                    android:text="ISTP" />

                <Button
                    android:layout_width="wrap_content"
                    android:layout_height="100dp"
                    android:layout_margin="4dp"
                    android:text="ISFP" />

                <Button
                    android:layout_width="wrap_content"
                    android:layout_height="100dp"
                    android:layout_margin="4dp"
                    android:text="INFP" />

                <Button
                    android:layout_width="wrap_content"
                    android:layout_height="100dp"
                    android:layout_margin="4dp"
                    android:text="INTP" />
            </LinearLayout>

            <LinearLayout
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:gravity="center"
                android:orientation="horizontal">

                <Button
                    android:layout_width="wrap_content"
                    android:layout_height="100dp"
                    android:layout_margin="4dp"
                    android:text="ESTP" />

                <Button
                    android:layout_width="wrap_content"
                    android:layout_height="100dp"
                    android:layout_margin="4dp"
                    android:text="ESFP" />

                <Button
                    android:layout_width="wrap_content"
                    android:layout_height="100dp"
                    android:layout_margin="4dp"
                    android:text="ENFP" />

                <Button
                    android:layout_width="wrap_content"
                    android:layout_height="100dp"
                    android:layout_margin="4dp"
                    android:text="ENTP" />
            </LinearLayout>

            <LinearLayout
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:gravity="center"
                android:orientation="horizontal">

                <Button
                    android:layout_width="wrap_content"
                    android:layout_height="100dp"
                    android:layout_margin="4dp"
                    android:text="ESTJ" />

                <Button
                    android:layout_width="wrap_content"
                    android:layout_height="100dp"
                    android:layout_margin="4dp"
                    android:text="ESFJ" />

                <Button
                    android:layout_width="wrap_content"
                    android:layout_height="100dp"
                    android:layout_margin="4dp"
                    android:text="ENFJ" />

                <Button
                    android:layout_width="wrap_content"
                    android:layout_height="100dp"
                    android:layout_margin="4dp"
                    android:text="ENTJ" />
            </LinearLayout>
        </LinearLayout>
    </ScrollView>
```
---

## 새 Activity 추가
> 가벼운 지식

[File]-[New]-[Activity]-[Empty Activity] 선택
- MainActivity.kt과 activity_detail.xaml 파일 2개가 추가된 것을 확인!
- manifests 폴더에 있는 AndroidManifest.xml에도 코드가 추가된 것을 확인!

즉, 내가 추가한 파일들은 manifests에 기록이 되어있어야한다!

> 버튼 클릭시 호출할 함수 생성
- MainActivity.kt 파일에서 onCreate 함수 아래에 적어주면 된다.
- ISTJ 버튼 클릭 시, `Toast`를 이용해서 짧은 피드백을 띄운다.
- actvity_main.xml에서 해당 버튼에 다음과 같이 넣어준다. `android:onClick="clickIsfj"`
- 혹은 XML의 Design 창에서 onclick 옵션을 찾아서 지정해줄 수도 있다.
```
//button 클릭시 함수
fun clickIstj(view: View) {
    Toast.makeText(view.context,
    "청렴결백한 논리주의자", Toast.LENGTH_LONG).show()  //토스트(짧은 피드백) 띄우기
}
fun clickIsfj(view: View){
    Toast.makeText(view.context, "또로롱", Toast.LENGTH_LONG).show()
}
```
