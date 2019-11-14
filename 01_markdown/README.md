# 01_Markdown

- marked.js - 마크다운 문법을 HTML로 컴파일
- lodash.js - 자바스크립트 함수형 라이브러리
- v-value, v-on:input(@input), v-html

## Result
<a target="_blank" href="https://jsfiddle.net/chrisvfritz/0dzvcf4d">JSFiddle</a>

## HTML
```html
<script src="https://unpkg.com/marked@0.3.6"></script>
<script src="https://unpkg.com/lodash@4.16.0"></script>
<div id="editor">
    // textarea의 기본값으로 input 변수값 사용
    // 텍스트가 입력될 때마다 update 함수 실행
    <textarea :value="input" @input="update"></textarea>
    <div v-html="compiledMarkdown"></div>
</div>
```
> v-html : compliledMarkdown 함수의 리턴값을 HTML으로 출력
## JavaScript
```javascript
new Vue({
    el: '#editor',
    data: {
        input: '# hello'
    },
    computed: {
        // input 값이 바뀔 때마다 실행
        compiledMarkdown: function () {
            return marked(this.input, { sanitize: true })
        }
    },
    methods: {
        // textarea 값이 바뀔 때마다 실행
        update: _.debounce(function (e) {
            this.input = e.target.value
            console.log(e.target.value);
        }, 300)
    }
})
```
> _.dobounce : lodashJS 함수 (지정한 시간에 한번만 실행)
## CSS
```css
html, body, #editor {
    margin: 0;
    height: 100%;
    font-family: 'Helvetica Neue', Arial, sans-serif;
    color: #333;
}

textarea, #editor div {
    display: inline-block;
    width: 49%;
    height: 100%;
    vertical-align: top;
    box-sizing: border-box;
    padding: 0 20px;
}

textarea {
    border: none;
    border-right: 1px solid #ccc;
    resize: none;
    outline: none;
    background-color: #f6f6f6;
    font-size: 14px;
    font-family: 'Monaco', courier, monospace;
    padding: 20px;
}

code {
    color: #f66;
}
```