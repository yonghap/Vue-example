# 06_Modal Component

- 컴포넌트의 정의
- prop 전달
- 동적 컨텐츠 삽입
- 트랜지션 효과

> **VueJS** - slot, emit(), transition

## Result
<a target="_blank" href="https://jsfiddle.net/yyx990803/mwLbw11k">JSFiddle</a>

## HTML
```html
<script type="text/x-template" id="modal-template">
    <!-- transtion 모달 템플릿 정의 -->
    <transition name="modal">
        <div class="modal-mask">
            <div class="modal-wrapper">
                <div class="modal-container">

                    <div class="modal-header">
                        <!-- header 라는 이름을 가지는 slot 정의 -->
                        <slot name="header">
                            default header
                        </slot>
                    </div>

                    <div class="modal-body">
                        <!-- body 라는 이름을 가지는 slot 정의 -->
                        <slot name="body">
                            default body
                        </slot>
                    </div>

                    <div class="modal-footer">
                        <!-- footer 라는 이름을 가지는 slot 정의 -->
                        <slot name="footer">
                            default footer
                            <!-- 상위 컴포넌트의 이벤트 호출 -->
                            <button class="modal-default-button" @click="$emit('close')">
                                OK
                            </button>
                        </slot>
                    </div>
                </div>
            </div>
        </div>
    </transition>
</script>

<!-- app -->
<div id="app">
    <button id="show-modal" @click="showModal = true">Show Modal</button>
    <!-- use the modal component, pas sin the prop -->
    <modal v-if="showModal" @close="showModal = false">
        <!-- cuscom content -->
        <!-- header slot에 배포할 컨텐츠 정의 -->
        <h3 slot="header">custom header</h3>
        <!-- body slot에 배포할 컨텐츠 정의 -->
        <p slot="body">
            Lorem ipsum dolor sit amet, consectetur adipisicing elit. Deleniti, odit.
        </p>
    </modal>
</div>
```
> **slot** - 컨텐츠 배포 통로로 사용되는 태그

<a target="_blank" href="https://kr.vuejs.org/v2/guide/components-slots.html">슬롯(slots) 공식가이드</a>


## JavaScript
```javascript
Vue.component('modal',{
    template : '#modal-template'
})

new Vue({
    el : '#app',
    data : {
        showModal : false
    }
})
```


## CSS
```css
.modal-mask {
    position: fixed;
    z-index: 9998;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background-color: rgba(0, 0, 0, .5);
    display: table;
    transition: opacity .3s ease;
}

.modal-wrapper {
    display: table-cell;
    vertical-align: middle;
}

.modal-container {
    width: 300px;
    margin: 0px auto;
    padding: 20px 30px;
    background-color: #fff;
    border-radius: 2px;
    box-shadow: 0 2px 8px rgba(0, 0, 0, .33);
    transition: all .3s ease;
    font-family: Helvetica, Arial, sans-serif;
}

.modal-header h3 {
    margin-top: 0;
    color: #42b983;
}

.modal-body {
    margin: 20px 0;
}

.modal-default-button {
    float: right;
}

/*
 * The following styles are auto-applied to elements with
 * transition="modal" when their visibility is toggled
 * by Vue.js.
 *
 * You can easily play with the modal transition by editing
 * these styles.
 */

.modal-enter {
    opacity: 0;
}

.modal-leave-active {
    opacity: 0;
}

.modal-enter .modal-container,
.modal-leave-active .modal-container {
    -webkit-transform: scale(1.1);
    transform: scale(1.1);
}
```
> **transition** - CSS transition 효과를 적용하는 방법

<a target="_blank" href="https://kr.vuejs.org/v2/guide/transitions.html">CSS Transition 공식가이드</a>