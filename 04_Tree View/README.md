# 04_Tree View 


- 트리 형태의 데이터
- 컴포넌트의 재귀 호출
- 메뉴 추가시 children 속성 추가

> **VueJS** - @click, #dblclick, v-if, Custom Event, $emit, set()

## Result
<a target="_blank" href="https://jsfiddle.net/chrisvfritz/pnqzspoe/">JSFiddle</a>

## HTML
```html
<!-- 테이블 템플릿 생성 -->
<script type="text/x-template" id="item-template">
    <li>
        <!-- 클릭, 더블클릭 이벤트 선언 -->
        <!-- 하위 메뉴 존재 여부에 따라 클래스 추가와 블릿 표시 -->
        <div
            :class="{bold : isFolder}"
            @click="toggle"
            @dblclick="makeFolder">
            {{ item.name }}
            <span v-if="isFolder">[{{ isOpen ? '-' : '+' }}]</span>
        </div>
        <!-- 하위 메뉴가 존재할시 표시하지만 숨김처리 -->
        <ul v-show="isOpen" v-if="isFolder">
            <!-- 템플릿 재귀 호출-->
            <!-- 호출할 커스텀 이벤트 선언 -->
            <tree-item
                class="item"
                v-for="(child,index) in item.children"
                :key="index"
                :item="child"
                @make-folder="$emit('make-folder', $event)"
                @add-item="$emit('add-item', $event)"
            >
            </tree-item>
            <!-- 아이템 추가시 emit으로 상위 이벤트 호출 -->
            <li class="add" @click="$emit('add-item', item)">+</li>
        </ul>
    </li>
</script>
<p>(You can double click on an item to turn it into a folder.)</p>

<!-- 템플릿 호출, 커스텀 이벤트 정의 -->
<ul id="demo">
    <tree-item
        class="item"
        :item="treeData"
        @make-folder="makeFolder"
        @add-item="addItem"
    >
    </tree-item>
</ul>
```
> **@even-name** - 커스텀 이벤트, $emit으로 호출하기 위해 사용
> **$emit()** - 상위 컴포넌트의 이벤트 호출

## JavaScript
```javascript
// 트리 데이터
var treeData = {
    name : 'My Tree',
    children : [
        { name : 'hello' },
        { name : 'wat' },
        {
            name: 'child folder',
            children: [
                {
                    name : 'child folder',
                    children : [
                        { name : 'hello' },
                        { name : 'wat' }
                    ]
                },
                { name : 'hello' },
                { name : 'wat' },
                {
                    name : 'child folder',
                    children : [
                        { name : 'hello' },
                        { name : 'wat' }
                    ]
                }
            ]
        }
    ]
}

// 컴포넌트 설정
Vue.component('tree-item', {
    template : '#item-template',
    props : {
        item : Object
    },
    data : function () {
        return {
            isOpen : false
        }
    },
    computed : {
        // 하위 메뉴 여부 체크
        isFolder : function () {
            return this.item.children &&
                    this.item.children.length
        }
    },
    methods : {
        // 하위 메뉴 표시 여부 체크
        toggle : function () {
            if (this.isFolder) {
                this.isOpen = !this.isOpen;
            }
        },
        makeFolder : function () {
            // 하위 메뉴가 없을 경우에만 실행
            if (!this.isFolder) {
                // 상위 컴포넌트의 이벤트 호출
                this.$emit('make-folder', this.item)
                this.isOpen = true
            }
        }
    }
})

var demo = new Vue({
    el : '#demo',
    data : {
        treeData : treeData
    },
    methods : {
        makeFolder : function (item) {
            // 동적으로 속성 추가
            Vue.set(item, 'children', []);
            this.addItem(item);
        },
        addItem : function (item) {
            // 메뉴 추가시 새로우 이름 설정
            item.children.push({
                name : 'new stuff'
            })
        }
    }
})
```
> **$emit()** - 상위 컴포넌트의 이벤트 호출
> **set()** - 동적으로 속성 추가
> **pust()** - 배열의 요소 추가
 
## CSS
```css
body {
    font-family: Menlo, Consolas, monospace;
    color: #444;
    max-width:1000px;margin:0 auto
}
.item {
    cursor: pointer;
}
.bold {
    font-weight: bold;
}
ul {
    padding-left: 1em;
    line-height: 1.5em;
    list-style-type: dot;
}
```