<!--
 * @Author: your name
 * @Date: 2021-01-31 15:10:13
 * @LastEditTime: 2021-01-31 16:11:54
 * @LastEditors: Please set LastEditors
 * @Description: In User Settings Edit
 * @FilePath: \jenny-app\src\pages\index\index.vue
-->
<template>
  <view class="content">
    <image class="logo" src="../../static/logo.png"></image>
    <view>
      <view class="fa fa-check-circle"></view>
      <text class="title">{{ title }}</text>
    </view>
  </view>
</template>

<script lang="ts">
import Vue from "vue";
export default Vue.extend({
  data() {
    return {
      title: "Hello",
    };
  },
  onLoad() {
    uni.login({
      success(res) {
        console.log(res);
        uni.request({
          url: "http://127.0.0.1:8000/api/login/",
          method: "POST",
          data: {
            code: res["code"],
          },
          success(res) {
            console.log("服务器返回结果", res);
          },
        });
      },
      fail(err) {
        console.log(err);
      },
    });
  },
  methods: {},
});
</script>

<style>
.content {
  @apply flex flex-col items-center justify-center;
}

.logo {
  @apply h-40 w-40 mt-20 mb-20;
}

.text-area {
  @apply flex justify-center;
}
.title {
  @apply text-2xl text-gray-500;
}
</style>
