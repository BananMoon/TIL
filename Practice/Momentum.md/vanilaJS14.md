### <위로되는 말 10가지>
1) 시 
> 흔들리며 피는 꽃 中 - 도종환
흔들리지 않고 피는 꽃이 어디 있으랴
이 세상 그 어떤 아름다운 꽃들도 다 흔들리면서 피었나니

2) 책 및 드라마 대사
> <#너에게> 中 -완글<br>
그 누구도 나를 응원해주지 않는다면<br>
내가 나를 응원하면 되는 일이다.<br>
내가 나를 가장 잘 아니까, 얼마나 열심이었는지를.

> <예쁜 것은 다 너를 닮았다> 中<br>
지금 이렇게 버티고 나아가고 있는 것만으로도 <br>대단한 일을 하고 있는 걸요.<br>
대신 힘들지만 잘 살아내줘서 고맙다고, <br>대견하다고 말해주세요. 수고했어요 오늘도.

> <아무것도 아닌 지금은 없다> 中<br>
그동안의 노력을 돌아봐 주자. 당신은, 생각보다 <br>먼 길을 달려왔다.<br>
바람도 쉬고 햇살도 쉬고 별들도 쉰다.<br>
당신도 쉬어가길 바란다.

> <눈이부시게> 中
지금 삶이 힘든 당신, 이 세상에 태어난 이상 <br>당신은 모든 걸 매일 누릴 자격이 있어요.<br>
대단하지 않은 하루가 지나고, 또 별 것 아닌 <br>하루가 온다 해도 인생은 살 가치가 있습니다.<br>
후회 가득한 과거와 불안하기만 한 미래 때문에 <br>지금을 망치지 마세요.<br>
오늘을 살아가세요. 눈이 부시게.

> <여자 서른다섯, 그런대로 안녕하다> 中<br>
거저 얻어지는 건 거의 없다.<br>
어두운 곳의 빛이 더 밝고,<br>
혹독한 겨울 뒤에 오는 봄이 유난히 따뜻하다.<br>
깨진 곳에 빛이 머물고, 깨진만큼 더 반짝인다.

> <괜찮아, 사랑이야> 中
오늘 밤엔 고생했던 나에게 먼저 말을 건네봐요.<br>
다른 것보다 나에게 집중해서 <br>
오늘은 어떤 일이 힘들었고<br>
즐거웠던 일은 무엇인지 가만히 들어주세요.

> <오늘처럼 내가 싫었던 날은 없었다> 中
잘하고 싶었는데, 마음처럼 안돼서<br>
내가 싫었던 날이 있나요?<br>
괜찮아요, 잘하고 싶다는 마음만으로도<br>
당신은 좋은 사람이에요.

3) 명언
> 너 지금 멋지게 헤엄치려고 숨 참는 것부터 하고 있다고 생각해<br>
> 조금씩 나아질 거야.<br>
> 누구나 미완성으로 시작하지만 참혹한 결과를 경험하고 여러번 고민에 빠지며 다시 수차례의 실패 끝에 그나마 조금씩 완성이라는 것에 가까워진다.<br>
> 많이 힘들었지? 괜찮아. 온힘을 다했잖아. 다 잘될거야. <br>
> 네가 겪는 숱한 저항과 매서운 바람들은 네 잘못이 아니란다. 흔들리면서도 봉우리를 틔워줘서 고마워. <예쁜 것은 다 너를 닮았다> 中
---
## Quotes
- 이번에는 momentum에서 볼 수 있는 **배경화면과 명언**들을 랜덤으로 띄우는 실습을 해보겠다.
0. html 파일에 div 태그 속, span 태그를 두개 생성해논다. (id는 quote)
1. quotes.js 파일을 생성해서 (각 기능 별로 파일을 생성!)<br>
   string(명언문구, author)을 object형태로 갖고있는 array를 생성한다.
2. quotes.js에서 html에 있는 quote와 author 요소를 가져와서 값을 넣는다.

- 랜덤하게 명언을 화면에 띄우려면? `Math` 사용!<br>
`Math.round()` : .5 이하이면 소수점을 내리고, 이상이면 올린다. <br>
`Math.ceil()` : 무조건 소수점을 올린다.<br>
`Math.floor()` : 무조건 소수점을 내린다.<br>

- 이중 floor을 사용했다.  
- querySelector를 이용해 HTML에서 두개의 span을 가져와서 quote와 author 객체를 생성해주었다.
- 랜덤으로 생성되는 int를 quotes라는 배열의 index로 이용해주면 랜덤으로 명언이 나타난다.
- 그것을 화면에 띄우려면? quote와 author의 innerText에 지정해주면 된다.
---
### <전체 코드>
quotes.js
```
//명언이 꽤 기니 한개만 보여주자면,
const quotes = [
    {
        quote: `흔들리지 않고 피는 꽃이 어디 있으랴
        이 세상 그 어떤 아름다운 꽃들도 다 흔들리면서 피었나니`,
        author: "<흔들리며 피는 꽃> 中 - 도종환"
    },
    {//여기에 계속 추가한다.
    }
];

const quote = document.querySelector("#quote span:first-child");
const author = document.querySelector("#quote span:last-child");

todaysQuote = quotes[Math.floor(Math.random()*quotes.length)];

quote.innerText = todaysQuote.quote;
author.innerText = todaysQuote.author;
```
index.html
```
    <body>
      <form id='login-form' class="hidden">
//..
      </form>
      <h1 id="greeting" class="hidden"></h1>
      <h2 id="clock">00:00</h2>
//추가
      <div id="quote">
        <span></span>
        <span></span>
      </div>
//..
      <script src="js/quotes.js"></script>
    </body>
```
---
## Background (배경 이미지)
- 이번엔 랜덤으로 배경이미지를 선정해줄 건데 명언과 비슷한 방식으로 해주면 된다.
- 우선 img 폴더 생성 후, 그 아래에 image 세개를 저장해주었다.
- js 폴더에 'background.js' 파일을 추가한 뒤, 위 방식과 같이 random으로 생성된 int을 array의 인덱스로 하나씩 뽑아주었다.
> 여태까지는 html에서 가져와서 js로 변형을 해주었지만, 이번에는 **js에서 element를 생성하여 html에 추가**해볼 것이다.

- 랜덤으로 선정된 image를 HTML에 추가해줄 건데, 다음과 같이 추가되야한다.<br>`<img src="img/창문_rain.JPG"/>`

- 여태까지 `document.querySelector()`, `document.getElementbyId()`는 잘 써왔다.<br>
`document.createElement()`를 써보자. 한 문장 만으로 element를 추가할 수 있다.

```
const bgImg = document.createElement('img');
bgImg.src = `img/${choosenImage}`;  //벡틱 사용!
document.body.appendChild(bgImg);  // body 태그 아래에 추가
```
### <전체 코드><br>
background.js
- 1번째 방식 (배열에 이미지들 추가)
```
const images  =[
    "일본_북해도_오타루거리_snow.JPG",
    "Silverton,CO,US_rainbow.jpg",
    "창문_rain.JPG",
    "Clew Bay,Ireland_sunny.jpg",
    "Poon Hill,Nepal_evening.jpg"
];

const choosenImage = images[Math.floor(Math.random() * images.length)];
// 길이가 5개이면, 5를 곱하는데, 이때 5는 나오지 않음. but index는 0~4이므로 괜찮아.

//javascript에서 이미지를 만들고 html 를 생성!
// html에서 <img src = "img/"창문_rain.JPG"></img>
const bgImage = document.createElement('img'); // js에서 HTML element를 생성했다!!

bgImage.src = `img/${choosenImage}`;

//body 에 추가. document 내에 존재하도록 하려면, appendChild() 사용한다. 
// prepend()는 body에서 가장 앞에 위치. html에서도 가장 위에 뜸.
//document.body.prepend(bgImage);
document.body.appendChild(bgImage);

//추가. 만약, clock 위에 사진을 넣고 싶다면 insertBefore()
//const h2 = document.querySelector("#clock")
//document.body.insertBefore(bgImage, h2);
```

- 2번째 방식 (둘다 같은 결과로 작동한다. 단, 배열을 생성해주지 않는 대신, img를 만들어주는 함수 추가!)
```
const createImg  = (number) => {
    // <img> 요소 생성
    const bgImage = document.querySelector('#bgImage');
//    const bgImage = document.createElement('img');
    // <img> src, alt 값을 지정하고 'bgImg' 클래스를 추가
    bgImage.src = `img/img_${number}.jpg`;
    bgImage.alt = 'background images';
    bgImage.classList.add('bgImg');  //css
    // <body>에 <img> 삽입
    document.body.appendChild(bgImage);

}
const getRandom = () => {
    // 이미지 개수. 얼마든지 변경 가능.
    const IMG_NUM = 5;
    // 0 부터 4 까지의 랜덤 숫자 만들기
    let num = Math.floor(Math.random() * IMG_NUM );
    console.log(num);
    // 이미지 생성함수 호출
    createImg(num);
}

getRandom();
```