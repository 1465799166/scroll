<template>
  <div class="loading">
    <p class="desc">{{loadingWord}}</p>
    <img src="./loading.gif" alt="" width="24" height="24" v-show="isLoading">
  </div>
</template>

<script>
  export default {
    props: {
      loadingWord: {
        type: String,
        default: '加载更多'
      },
      isLoading: {
        type: Boolean,
        default: true
      }
    }
  }

</script>
<style scoped>
  .loading {
    display: flex;
    justify-content: center;
    align-items: center;
    width: 100%;
    text-align: center;

  }

  .desc {
    line-height: 20px;
    font-size: 14px;
  }

</style>
