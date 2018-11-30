<template>
    <div>
        <Scroller id="scroll" ref="scroll" :dataList="items" :pullDownRefresh="DOWN_CONFIG" :pullUpLoad="UP_CONFIG"
            :scrollbar="scrollbar" @onPullUp="pullUpHandle" @onPullDown="pullDownHandle" class="wrap">
            <div>
                <div v-for="item in items" :key="item.id" class="item">
                    <p>{{item.appName}}</p>
                    <p>{{item.appDesc}}</p>
                </div>
            </div>
        </Scroller>
    </div>
</template>
<script>
    const BASE_URL = 'https://easy-mock.com/mock/5bfbbb31a8a5da4a1e0f11f8/learn/'
    import Scroller from '../components/BScroll/index'
    export default {
        name: '',
        components: {
            Scroller
        },
        data() {
            return {
                items: [],
                DOWN_CONFIG: {
                    threshold: 80,
                    stop: 50
                },
                UP_CONFIG: {
                    threshold: -50
                },
                scrollbar: true
            }
        },
        created() {
            this.getData('getList', 1)
        },
        methods: {
            getData(url, type) {
                // easy-mock
                let _this = this
                let xhr = new XMLHttpRequest()
                xhr.onreadystatechange = function () {
                    if (this.readyState == 4 && this.status == 200) {
                        let res = xhr.responseText || xhr.responseXML
                        res = JSON.parse(res)
                        console.log(res)
                        console.log(_this.items.unshift(res.items[0]))
                        type == 1 ? _this.items =  _this.items.concat(res.items) : _this.items.unshift(res.items[0])
                    }
                }
                xhr.open('get', BASE_URL + url, true)
                xhr.send()
            },
            pullUpHandle() {
                setTimeout(() => {
                    this.UP_CONFIG = false
                    this.getData('getLoad', 1)
                    this.scrollElement.finish("PullUp");
                }, 1000)
            },
            pullDownHandle() {
                setTimeout(() => {
                    this.getData('getRefresh', 2)
                    this.scrollElement.finish("PullDown");
                }, 1000)
            }
        },
        computed: {
            scrollElement() {
                return this.$refs.scroll
            }
        }
    }
</script>
<style scoped>
    .wrap {
        height: 100vh;
    }

    .item {
        border: 1px solid green;
    }
</style>