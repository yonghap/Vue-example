# 02_Github Commits  

> **v-bind, v-model, v-for, filters**

## Result
<a target="_blank" href="https://jsfiddle.net/yyx990803/c5g8xnar/">JSFiddle</a>

## HTML
```html
<div id="demo">
    <h2>Latest Vue.js Commits</h2>
    <!-- 등록된 브랜치 길이만큼 input radio 생성-->
    <template v-for="branch in branches">
        <!-- 브랜치명으로 id, value, name 할당 -->
        <input type="radio"
               :id="branch"
               :value="branch"
               name="branch"
               v-model="currentBranch">
        <label :for="branch">{{ branch }}</label>
    </template>
    <!-- 현재 브랜치명 출력 -->
    <p>vuejs/vue@{{ currentBranch }}</p>
    <ul>
        <!-- // commits에 담긴 정보 출력 -->
        <li v-for="record in commits">
            <a :href="record.html_url" target="_blank" class="commit">{{ record.sha.slice(0, 7) }}</a>
            - <span class="message">{{ record.commit.message | truncate }}</span><br>
            by <span class="author"><a :href="record.author.html_url" target="_blank">{{ record.commit.author.name }}</a></span>
            at <span class="date">{{ record.commit.author.date | formatDate }}</span>
        </li>
    </ul>
</div>
```
> **{{ message | filter function }}** - filters에 정의된 함수에 message를 매개변수로 전달
## JavaScript
```javascript
var apiURL = 'https://api.github.com/repos/vuejs/vue/commits?per_page=3&sha='

/**
 * Actual demo
 */

var demo = new Vue({

    el: '#demo',

    data: {
        branches: ['master', 'dev'],
        currentBranch: 'master',
        commits: null
    },

    created: function () {
        this.fetchData()
    },

    // currentBranch 프로퍼티가 변경될때 fetchData 함수 실행
    watch: {
        currentBranch: 'fetchData'
    },

    filters: {
        // 줄바꿈 있을시 줄바꿈까지 글자 자르기 필터
        truncate: function (v) {
            var newline = v.indexOf('\n')
            return newline > 0 ? v.slice(0, newline) : v
        },
        // 날짜 데이터에서 T,Z 문자를 공백으로 변경
        formatDate: function (v) {
            return v.replace(/T|Z/g, ' ')
        }
    },

    methods: {
        // API 호출하여 JSON 형태로 commits에 할당
        fetchData: function () {
            var xhr = new XMLHttpRequest()
            var self = this
            xhr.open('GET', apiURL + self.currentBranch)
            xhr.onload = function () {
                self.commits = JSON.parse(xhr.responseText)
                console.log(self.commits[0].html_url)
            }
            xhr.send()
        }
    }
})
```
> **filters** - 텍스트 형식화를 적용할 수 있는 필터 제공
 
## CSS
```css
#demo {
    font-family: 'Helvetica', Arial, sans-serif;
}
a {
    text-decoration: none;
    color: #f66;
}
li {
    line-height: 1.5em;
    margin-bottom: 20px;
}
.author, .date {
    font-weight: bold;
}
```