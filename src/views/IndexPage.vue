<template>
  <a-input-search
    v-model:value="searchParams.text"
    placeholder="input search text"
    enter-button="Search"
    size="large"
    @search="onSearch"
  />
  <!--  {{ JSON.stringify(postList) }}-->
  <a-tabs v-model:activeKey="activeKey" centered @change="onTabChange">
    <a-tab-pane key="post" tab="帖子">
      <!--      把postList数据，传给子组件-->
      <PostList :post-list="postList" />
    </a-tab-pane>
    <a-tab-pane key="user" tab="用户" force-render>
      <UserList :user-list="userList" />
    </a-tab-pane>
    <a-tab-pane key="picture" tab="图片">
      <picture-list />
    </a-tab-pane>
  </a-tabs>
</template>

<script setup lang="ts">
import PostList from "@/components/PostList.vue";
import { ref, watchEffect } from "vue";
import UserList from "@/components/UserList.vue";
import PictureList from "@/components/PictureList.vue";
import { useRoute, useRouter } from "vue-router";
import instance from "@/plugins/myAxios";
import myAxios from "@/plugins/myAxios";

const postList = ref([]);
myAxios.post("post/list/page/vo", {}).then((res: any) => {
  // 把请求的结果赋值给postList
  // console.log(res);
  postList.value = res.records;
});

const userList = ref([]);
myAxios.post("/user/list/page/vo", {}).then((res: any) => {
  // 把请求的结果赋值给userList
  // console.log(res.records);
  userList.value = res.records;
});

const router = useRouter();
const route = useRoute();
const activeKey = route.params.category;
const initSearchParams = {
  text: "",
  pageSize: 10,
  pageNum: 1,
};
const searchParams = ref(initSearchParams);

watchEffect(() => {
  searchParams.value = {
    ...initSearchParams,
    text: route.query.text,
  } as any;
});

const onSearch = (value: string) => {
  alert(value);
  router.push({
    query: searchParams.value,
  });
};

const onTabChange = (key: string) => {
  router.push({
    path: `/${key}`,
    query: searchParams.value,
  });
};
</script>
