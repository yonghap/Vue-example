# 05_SVG Graph 


- 좌표 이름과 값을 가지는 배열 데이터 필요
- 최상위 컴포넌트 > 폴리곤 컴포넌트 > 라벨 컴포넌트의 구조
- 새로운 좌표 추가와 기존 좌표의 삭제 기능

> **VueJS** - v-model, @click
> **JavaScript** - map(), splice(), MATH(), push(), indexOf(), join()

## Result
<a target="_blank" href="https://jsfiddle.net/yyx990803/mhrckqgq/">JSFiddle</a>

## HTML
```html
<!-- 그래프 템플릿 -->
<script type="text/x-template" id="polygraph-template">
    <g>
        <!-- 받은 좌표리스트를 계산하여 뿌리기 -->
        <polygon :points="points"></polygon>
        <circle cx="100" cy="100" r="80"></circle>
        <!-- 좌표의 갯수만큼 라벨 표시 -->
        <axis-label
            v-for="(stat, index) in stats"
            :stat="stat"
            :index="index"
            :total="stats.length">
        </axis-label>
    </g>
</script>

<!--라벨표시-->
<script type="text/x-template" id="axis-label-template">
    <text :x="point.x" :y="point.y">{{stat.label}}</text>
</script>

<div id="demo">
    <svg width="200" height="200">
        <!-- 폴리곤 모양 정의, 좌표 배열을 전달 -->
        <polygraph :stats="stats"></polygraph>
    </svg>
    <!-- 좌표 설정 컨트롤러 -->
    <div v-for="stat in stats">
        <label>{{stat.label}}</label>
        <input type="range" v-model="stat.value" min="0" max="100">
        <span>{{stat.value}}</span>
        <button @click="remove(stat)" class="remove">X</button>
    </div><br><br>
    <form id="add">
        <input type="newlabel" v-model="newLabel">
        <button @click="add">Add a Stat</button>
    </form>
    <pre id="raw">{{ stats }}</pre>
</div>

<p style="font-size:12px">Input[type=range] requires IE10 or above.</p>
```


## JavaScript
```javascript
// 좌표 리스트
var stats = [
    { label : 'A', value : 100 },
    { label : 'B', value : 100 },
    { label : 'C', value : 100 },
    { label : 'D', value : 100 },
    { label : 'E', value : 100 },
    { label : 'F', value : 100 }
]

// 폴리곤 컴포넌트 정의
Vue.component('polygraph', {
    props : ['stats'], // 좌표 받기
    template : '#polygraph-template',
    computed : {
        // points 좌표 계산 후 할당
        points : function () {
            var total = this.stats.length
            // 좌표리스트를 새로운 배열로 만들어서 텍스트로 받기
            return this.stats.map(function (stat, i) {
                var point = valueToPoint(stat.value, i ,total)
                return point.x + ',' + point.y
            }).join(' ')
        }
    },
    components : {
        // 라벨 컴포넌트 정의
        'axis-label' : {
            props : {
                stat : Object, // 각각의 라벨 객체
                index : Number, // 라벨 인덱스
                total : Number // 라벨 총갯수
            },
            template : '#axis-label-template',
            computed : {
                // 라벨의 위치 값 계산
                point : function () {
                    return valueToPoint(
                        +this.stat.value + 10,
                        this.index,
                        this.total
                    )
                }
            }
        }
    }
})

// 위치 값 함수
function valueToPoint(value, index ,total) {
    var x = 0;
    var y = -value * 0.8;
    var angle = Math.PI * 2 / total * index;
    var cos = Math.cos(angle);
    var sin = Math.sin(angle);
    var tx = x * cos - y * sin + 100;
    var ty = x * sin + y * cos + 100;
    return {
        x : tx,
        y : ty
    }
}

new Vue({
    el : '#demo',
    data : {
        newLabel : '',
        stats : stats
    },
    methods : {
        // 새로운 좌표 추가
        add : function (e) {
            e.preventDefault()
            if (!this.newLabel) return;
            this.stats.push({
                label :this.newLabel,
                value : 100
            })
            this.newLabel = '';
        },
        // 기존 좌표 삭제
        remove : function (stat) {
            if (this.stats.length > 3) {
                this.stats.splice(this.stats.indexOf(stat), 1)
            } else {
                alert('can`t delete more');
            }
        }
    }
})
```
> **map()** - 배열 내 모든 요소에 대하여 함수를 호출한 결과를 새로운 배열로 반환
> **join()** - 배열의 모든 요소를 연결해 텍스트로 생성
> **splice()** - 배열의 요소를 해당 위치부터 삭제
 
## CSS
```css
body {
    font-family: Helvetica Neue, Arial, sans-serif;
}

polygon {
    fill: #42b983;
    opacity: .75;
}

circle {
    fill: transparent;
    stroke: #999;
}

text {
    font-family: Helvetica Neue, Arial, sans-serif;
    font-size: 10px;
    fill: #666;
}

label {
    display: inline-block;
    margin-left: 10px;
    width: 20px;
}

#raw {
    position: absolute;
    top: 50px;
    left: 350px;
}
```