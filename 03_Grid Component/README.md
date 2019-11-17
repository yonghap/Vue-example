# 03_Grid Component 

> **JavaScript** - filter(), keys(), some(), toLowerCase(), indexOf(), slide(). sort(). charAt(), toUpperCase()<br>
> **VueJS** - @click, :class, v-for, filters, props, template

## Result
<a target="_blank" href="https://jsfiddle.net/Tertia/vbyon64p/6/">JSFiddle</a>

## HTML
```html
<!-- 테이블 템플릿 생성 -->
<script type="text/x-template" id="grid-template">
    <table>
        <thead>
        <tr>
            <!-- 정렬제목 영역 반복 출력 -->
            <th v-for="key in columns"
                @click="sortBy(key)"
                :class="{ active: sortKey == key }"> <!-- 현재 정렬일 경우 클래스 추가 -->
                {{ key | capitalize }} <!-- 첫 문자 대문자 처리 capitalize -->
                <span class="arrow" :class="sortOrders[key] > 0 ? 'asc' : 'dsc'">
                <!-- 현재 정렬 칼럼의 오름,내림차순에 따른 클래스 추가 -->
          </span>
            </th>
        </tr>
        </thead>
        <tbody>
        <!-- 필터나 정렬기능으로 리턴되어 온 배열 길이만큼 tr 생성 -->
        <tr v-for="entry in filteredHeroes">
            <!-- 이름, 파워순 정렬이므로 항목갯수만큼 반복 -->
            <td v-for="key in columns">
                <!-- name, power 출력 -->
                {{entry[key]}}
            </td>
        </tr>
        </tbody>
    </table>
</script>

<!-- demo root element -->
<div id="demo">
    <form id="search">
        Search <input name="query" v-model="searchQuery">
    </form>
    <!-- 상단에서 만든 템플릿으로 props 형태로 전달 (데이터, 정렬항목, 검색어) -->
    <demo-grid
            :heroes="gridData"
            :columns="gridColumns"
            :filter-key="searchQuery">
    </demo-grid>
</div>
```
> **{{ key | filter function }}** - filters에 정의된 함수에 key를 매개변수로 전달

## JavaScript
```javascript
// register the grid component
Vue.component('demo-grid', {
    template: '#grid-template',
    // 데이터, 정렬항목, 검색어 받기
    props: {
        heroes: Array, // 데이터
        columns: Array, // 정렬항목
        filterKey: String // 검색어
    },
    data: function () {
        var sortOrders = {} // 기본 정렬 차순 1로 설정, 정렬시 -1,1이 스위칭
        this.columns.forEach(function (key) {
            sortOrders[key] = 1
        })
        return {
            sortKey: '', // 정렬기준
            sortOrders: sortOrders // 정렬기준별 차순
        }
    },
    computed: {
        filteredHeroes: function () {
            var sortKey = this.sortKey // 정렬기준
            var filterKey = this.filterKey && this.filterKey.toLowerCase() // 검색어 소문자로 변환
            var order = this.sortOrders[sortKey] || 1 // 오름, 내림차순 변수
            var heroes = this.heroes // 데이터 가져오기
            if (filterKey) {
                heroes = heroes.filter(function (row) {
                    // 검색어를 포함한 데이터일 경우 true를 리턴하여 새로운 배열로 반환
                   return Object.keys(row).some(function (key) {
                        // name을 소문자 변환후 검색어로 검색해서 있을 경우 true 반환
                        return String(row[key]).toLowerCase().indexOf(filterKey) > -1
                    })
                })
            }
            if (sortKey) {
                heroes = heroes.slice().sort(function (a, b) {
                    a = a[sortKey]
                    b = b[sortKey]
                    // 오름, 내림차순으로 정렬
                    return (a === b ? 0 : a > b ? 1 : -1) * order
                })
            }
            return heroes
        }
    },
    filters: {
        // 앞문자 대문자 처리 함수
        capitalize: function (str) {
            return str.charAt(0).toUpperCase() + str.slice(1)
        }
    },
    methods: {
        // 정렬 클릭 이벤트 선언
        sortBy: function (key) {
            this.sortKey = key // 정렬 칼럼 선언
            this.sortOrders[key] = this.sortOrders[key] * -1 // 정렬 화살표 클래스 추가 + 오름, 내림차순 처리
        }
    }
})

// bootstrap the demo
var demo = new Vue({
    el: '#demo',
    // 기본 데이터 선언
    data: {
        searchQuery: '',
        gridColumns: ['name', 'power'],
        gridData: [
            { name: 'Chuck Norris', power: Infinity },
            { name: 'Bruce Lee', power: 9000 },
            { name: 'Jackie Chan', power: 7000 },
            { name: 'Jet Li', power: 8000 }
        ]
    }
})
```
> **filter()** - 주어진 함수의 테스트를 통과하는 모든 요소를 모아 새로운 배열로 반환<br>
> **Object.keys()** - 개체 고유 속성의 이름을 배열로 반환<br>
> **some()** - 배열 안의 한 요소라도 함수를 통과하는지 테스트하여 boolean 반환<br>
> **toLowerCase()** - 소문자 변환<br>
> **toUpperCase()** - 대문자 변환<br>
> **indexOf()** - 문자열 값과 일치하는 첫번째 위치 반환. 값이 없으면 -1 반환<br>
> **slice()** - 문자열을 잘라서 새로운 문자열로 반환<br>
> **sort()** - 배열을 정렬하여 새로운 배열로 반환<br>
> **filters** - 텍스트 형식화를 적용할 수 있는 필터 제공
 
## CSS
```css
    body {
        font-family: Helvetica Neue, Arial, sans-serif;
        font-size: 14px;
        color: #444;
    }

    table {
        border: 2px solid #42b983;
        border-radius: 3px;
        background-color: #fff;
    }

    th {
        background-color: #42b983;
        color: rgba(255,255,255,0.66);
        cursor: pointer;
        -webkit-user-select: none;
        -moz-user-select: none;
        -ms-user-select: none;
        user-select: none;
    }

    td {
        background-color: #f9f9f9;
    }

    th, td {
        min-width: 120px;
        padding: 10px 20px;
    }

    th.active {
        color: #fff;
    }

    th.active .arrow {
        opacity: 1;
    }

    .arrow {
        display: inline-block;
        vertical-align: middle;
        width: 0;
        height: 0;
        margin-left: 5px;
        opacity: 0.66;
    }

    .arrow.asc {
        border-left: 4px solid transparent;
        border-right: 4px solid transparent;
        border-bottom: 4px solid #fff;
    }

    .arrow.dsc {
        border-left: 4px solid transparent;
        border-right: 4px solid transparent;
        border-top: 4px solid #fff;
    }
```