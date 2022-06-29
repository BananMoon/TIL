## Geolocation
- 날씨 표시를 위해 weather.js 파일을 생성합니다.
- user의 위치를 준 후, 날씨API로 받아오는 흐름!
- 콜백함수 `navigator의 geolocation.getCurrentPosition()` 만 이용하면 위치를 알 수 있습니다👩
- 참고) https://developer.mozilla.org/ko/docs/Web/API/Geolocation/getCurrentPosition
- 위 링크에도 나와있듯이, 인자로 (실행될 함수, 에러발생 시 실행될 함수) 를 줍니다.
- 실행이 될 때, javascript는 많은 정보를 준다는 건 이전의 event listener에서도 알 수 있었죠?<br> 함수에서 인자(아무거나!)로 position 변수를 적어줬다면, console.log(position)을 통해 **user의 위치 정보**를 알 수 있습니다.<br>
console.log로 찍어보면, `GeolocationPosition`이 뜨는데, 그 아래에는 위도,경도 정보가 있습니다 Perfect
- latitude: 37.1592233
- longitude: 127.10667509999999

#### <코드>
```
function onGeoOk(position){
    const lat = position.coords.latitude;
    const lng = position.coords.longitude;
    console.log("You live in ", lat,lng);
}

function onGeoError() {
    alert("Can't find you. No weather for you.");
}
navigator.geolocation.getCurrentPosition(onGeoOk, onGeoError);
```
---
### step2는 해당 숫자들을 장소로 바꿔줄 서비스를 이용하는 것!

- openweathermap 서버에서 제공하는 api를 이용해봅시다. (회원가입 필수!)
- https://openweathermap.org/에 들어가서 'Current weather data(https://openweathermap.org/current)' 목록에서 우리가 가지고있는 좌표로 접근할 수 있는 '**By geographic coordinates**'로 들어 가면!

- `https://api.openweathermap.org/data/2.5/weather?lat=${lat}&lon=${lon}&appid=${API_KEY}` 
다음과 같이 위도와 경도, 내 API를 이용해서 내 현재 위치의 날씨 정보를 얻어올 수 있습니다.

- 해당 링크를 접속하는 방법은 `fetch()`를 이용하는데, url을 부를 수 있습니다.
- F12- Network 에서 확인해보면 누군가 URL을 요청한 것을 볼 수 있죠? 그것을 클릭해보면 url의 응답결과가 와있는 것을 확인할 수 있는데, **문제는 온도가 모두 화씨 온도로 되어있다는 것!**
- 다시 위의 사이트에서 확인해보면 units 인자로 metric이 있습니다. `&units=metric`을 링크 뒤에 추가해보면? <br>
`"temp_min":24.67,`처럼 뜨네요 ☺

- fetch는 promise. 즉, 당장 일어나지않고 , 시간이 지난 뒤에 일어나는 것. 
- url을 call한 후에 response를 받는데, 이때 **response.json**을 받아야합니다. 
`fetch(url).then(response => response.json)`
- response.json은 'Network'에서 확인할 수 있는 것입니다.
```
{coord: {lon: 127.1067, lat: 37.1592},…}
base: "stations"
clouds: {all: 1}
cod: 200
coord: {lon: 127.1067, lat: 37.1592}
dt: 1625145156
id: 1839652
main: {temp: 25.19, feels_like: 26.09, temp_min: 24.67, temp_max: 25.73, pressure: 1006, humidity: 89}
name: "Osan"
sys: {type: 1, id: 8099, country: "KR", sunrise: 1625084095, sunset: 1625136933}
timezone: 32400
visibility: 10000
weather: [{id: 800, main: "Clear", description: "clear sky", icon: "01n"}]
wind: {speed: 0, deg: 0}
```

- 그렇게 data를 받았으면? <br>
console.log로 찍어보자. json을 확인해보면 알겠지만 **data.name**은 시 이름(ex-Seoul), **data.weather**의 첫번째 요소의 main은 날씨입니다. (temp도 해볼 수 있어요!)
```
fetch(url)
    .then((response) => response.json())
    .then((data) => {
        console.log(data.name, data.weather[0].main)
    });
```
- 이제 html에 날씨와 장소를 띄우도록 태그를 작성해주고 js로 가져와서 innerText에 넣어주면 끝!
---
### 최종 코드<br>
<weather.js>
```
const API_KEY = "{key}"
function onGeoOk(position){
    const lat = position.coords.latitude;
    const lon = position.coords.longitude;
    const url = `https://api.openweathermap.org/data/2.5/weather?lat=${lat}&lon=${lon}&appid=${API_KEY}&units=metric`;
    fetch(url)
    .then((response) => response.json())
    .then((data) => {
        const weather = document.querySelector('#weather span:first-child');
        const city =  document.querySelector('#weather span:last-child');
        weather.innerText = data.name;
        city.innerText = `${data.weather[0].main} / ${data.main.temp}°C`;
    });
}

function onGeoError() {
    alert("Can't find you. No weather for you.");
}
navigator.geolocation.getCurrentPosition(onGeoOk, onGeoError);
```
