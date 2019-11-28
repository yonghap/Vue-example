# 07_Elastic Header

- Header 밀고 당기기 효과
- Dynamic.js : 물리 기반 애니메이션 라이브러리 (스피링,중력,바운스 등)

> **VueJS** - slot

## Result
<a target="_blank" href="https://jsfiddle.net/yyx990803/y91wy85p">JSFiddle</a>

## HTML
```html
<script type="text/x-template" id="header-view-template">
    <div class="draggable-header-view"
         @mousedown="startDrag" @touchstart="startDrag"
         @mousemove="onDrag" @touchmove="onDrag"
         @mouseup="stopDrag" @touchend="stopDrag" @mouseleave="stopDrag">
        <svg class="bg" width="320" height="560">
            <!-- 색칠할 좌표 받아오기 -->
            <path :d="headerPath" fill="#3F51B5"></path>
        </svg>
        <div class="header">
            <slot name="header"></slot>
        </div>
        <div class="content" :style="contentPosition">
            <slot name="content"></slot>
        </div>
    </div>
</script>

<div id="app" @touchmove.prevent>
    <draggable-header-view>
        <template slot="header">
            <h1>Elestic Draggable SVG Header</h1>
            <p>Lorem ipsum dolor sit amet.</p>
        </template>
        <template slot="content">
            <p>
                Lorem ipsum dolor sit amet, consectetur adipisicing elit. Animi, voluptatum.
            </p>
        </template>
    </draggable-header-view>
</div>
```
> **slot** - 컨텐츠 배포 통로로 사용되는 태그

<a target="_blank" href="http://dynamicsjs.com/">Dynamic.js</a>


## JavaScript
```javascript
Vue.component('draggable-header-view', {
    template : '#header-view-template',
    data : function () {
        return {
            dragging : false,
            c : {x:160, y:160},
            start : {x:0, y:0}
        }
    },
    computed : {
        // 기본 모양 설정, 드래그시 포물선 처리
        headerPath: function () {
            return 'M0,0 L320,0 320, 160' +
                'Q' + this.c.x + ',' + this.c.y +
                ' 0,160'
        },
        // 드래그 할 때마다 트랜스폼 처리
        contentPosition: function () {
            var dy = this.c.y - 160
            var dampen = dy > 0 ? 2 : 4
            return {
                transform: 'translate3d(0, ' + dy / dampen + 'px,0)'
            }
        }
    },
    methods : {
        // 드래그 시작
        startDrag : function (e) {
            e = e.changedTouches ? e.changedTouches[0] : e
            this.dragging = true // 모션 실행중 체크
            this.start.x = e.pageX // 드래그 시작 X좌표
            this.start.y = e.pageY // 드래그 시작 Y좌표
        },
        // 드래그 중
        onDrag : function (e) {
            e = e.changedTouches ? e.changedTouches[0] : e
            if (this.dragging) {
                this.c.x = 160 + (e.pageX - this.start.x)
                var dy = e.pageY - this.start.y // 드래그 하는 좌표에서 시작좌표를 빼기
                var dampen = dy > 0 ? 1.5 : 4  // 아래로 드래그 할 경우 1.5배, 위로 드래그 할 경우 4의 이동값을 가짐
                this.c.y = 160 + dy / dampen // 아래로 드래그 할 경우 더 크게 튕기게 처리
            }
        },
        // 드래그 멈춤
        stopDrag : function (e) {
            // 드래그를 멈출 경우 원래 위치로
            if (this.dragging) {
                this.dragging = false
                dynamics.animate(this.c, {
                    x : 160,
                    y : 160
                },{
                    type : dynamics.spring,
                    duration : 700,
                    friction : 280
                })
            }
        }
    }
})

new Vue({el:'#app'})
```


## CSS
```css
.draggable-header-view {
    background-color: #fff;
    box-shadow: 0 4px 16px rgba(0,0,0,.15);
    width: 320px;
    height: 560px;
    overflow: hidden;
    margin: 30px auto;
    position: relative;
    font-family: 'Roboto', Helvetica, Arial, sans-serif;
    color: #fff;
    font-size: 14px;
    font-weight: 300;
    -webkit-user-select: none;
    -moz-user-select: none;
    -ms-user-select: none;
    user-select: none;
}
.draggable-header-view .bg {
    position: absolute;
    top: 0;
    left: 0;
    z-index: 0;
}
.draggable-header-view .header, .draggable-header-view .content {
    position: relative;
    z-index: 1;
    padding: 30px;
    box-sizing: border-box;
}
.draggable-header-view .header {
    height: 160px;
}
.draggable-header-view .content {
    color: #333;
    line-height: 1.5em;
}
```
