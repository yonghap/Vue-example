# 08_Default  Slider

- VueJS를 이용한 기본적인 슬라이더

> **VueJS** - transition, v-for

## Result
<a target="_blank" href="https://jsfiddle.net/box252/qwt56zxv/2">JSFiddle</a>

## HTML
```html
<div id="app">
    <div class="main">
        <transition-group name="fade" tag="div" mode="in-out">
            <div v-for="i in [currentIndex]" :key="i">
                <div class="color" :style="{ backgroundColor : currentColor }"></div>
            </div>
        </transition-group>
        <a class="prev" @click="prev" href="#">&#10094;</a>
        <a class="next" @click="next" href="#">&#10095;</a>
    </div>
</div>
```



## JavaScript
```javascript
new  Vue({
    el : '#app',
    data() {
        return {
            // 슬라이더 이미지 배열
            color : [
				"green",
	            "red",
	            "blue",
	            "orange"
            ],
            // 타이머
            timer : null,
            // 현재 활성화된 인덱스
            currentIndex : 0
        }
    },
    // 마운트시 실행 함수
    mounted() {
        this.startSlide();
    },
    methods : {
        // 슬라이드 시작
        startSlide : function() {
            this.timer = setInterval(this.next, 3000);

        },
        // 다음 슬라이드
        next : function () {
            this.currentIndex += 1;

        },
        // 이전 슬라이드
        prev : function () {
            this.currentIndex -= 1;
        }
    },
    computed : {
        // 활성화된 이미지 주소
        currentColor : function() {
            return this.color[Math.abs(this.currentIndex) % this.color.length];
        }
    }
})
```
## CSS
```css
.fade-enter-active,
.fade-leave-active {
    transition: all .5s ease;
    opacity: 1;
}
.fade-enter,
.fade-leave-to {
    opacity: 0;
}
.fade-leave-to {
	display:none;
}
.color {
	width:300px;
	height:600px;
}
.prev, .next {
    cursor: pointer;
    position: absolute;
    top: 50%;
    width: auto;
    margin-top : -25px;
    padding: 0 10px;
    color: white;
    line-height:50px;
    font-weight: bold;
    font-size: 18px;
    transition: 0.7s ease;
    border-radius: 0 4px 4px 0;
    text-decoration: none;
    user-select: none;
}
.next {
    right: 0;
}
.prev {
    left: 0;
}
.prev:hover, .next:hover {
    background-color: rgba(0,0,0,0.9);
}
#app {overflow:hidden;position:relative;max-width:300px;height:600px;}
```
