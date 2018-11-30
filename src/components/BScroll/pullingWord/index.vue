<template>
    <div class="desc">{{loadingWord}}</div>
</template>
<script>
    export default {
        name: '',
        props: {
            loadingWord: {
                type: String,
                default: ''
            }
        }
    }
</script>
<style  scoped>
    .desc {
        height: 50px;
        text-align: center;
        line-height: 50px;
        font-size: 14px;
    }
</style>