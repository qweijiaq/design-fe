<template>
  <div class="homepage-container">
    <div class="banner">
      <img
        class="banner-img"
        src="../assets/background.jpeg"
        style="background-color: rgb(0, 0, 0)"
      />
      <div class="banner-text">
        <h2 class="text-headline" style="color: rgb(255, 255, 255)">
          海量精彩设计 一键生成
        </h2>
        <a-input-search
          v-model:value="searchText"
          placeholder="搜索一下，快速找模版"
          enter-button
          @search="onSearch"
        />
      </div>
    </div>
    <div class="welcome-container">
      <div class="welcome-container-inner">
        <a-row>
          <a-col :span="8" class="feature-item">
            <img src="../assets/Vue.svg" alt="" />
            <h3>Vue3框架</h3>
            <p>渐进式JavaScript框架</p>
          </a-col>
          <a-col :span="8" class="feature-item">
            <img src="../assets/typescript.svg" alt="" />
            <h3>TypeScript语言</h3>
            <p>具有类型语法的JavaScript</p>
          </a-col>
          <a-col :span="8" class="feature-item">
            <img src="../assets/node-js.svg" alt="" />
            <h3>Node.js环境</h3>
            <p>开源跨平台的JavaScript运行时环境</p>
          </a-col>
        </a-row>
      </div>
    </div>
    <div class="content-container">
      <a-row class="content-title" type="flex" align="middle">
        <h2 v-if="currentSearchText">{{ currentSearchText }}的结果</h2>
        <a-button
          shape="circle"
          size="small"
          v-if="currentSearchText"
          :style="{ marginLeft: '10px' }"
          @click="clearSearch"
        >
          ×
        </a-button>
        <div class="hot-title" v-else>
          <h2 class="hot-template">热门海报</h2>
          <p>只需替换文字和图片，一键自动生成H5</p>
        </div>
      </a-row>
      <a-row :gutter="16">
        <a-empty class="empty" v-if="templates.length === 0 && !loading">
          <template v-slot:description>
            <span> 没找到任何海报 换个关键词试试 </span>
          </template>
        </a-empty>
        <template-list :list="templates" type="template"></template-list>
      </a-row>
      <a-row type="flex" justify="center">
        <a-button
          type="primary"
          size="large"
          @click="loadMorePage"
          v-if="!isLastPage"
          :loading="loading"
        >
          加载更多
        </a-button>
      </a-row>
      <div class="my-works" v-if="isLogin && works.length > 0">
        <a-row
          type="flex"
          justify="space-between"
          align="middle"
          class="content-title"
        >
          <h2>我的作品</h2>
          <router-link to="/mywork">查看我的所有作品</router-link>
        </a-row>
        <a-row :gutter="16">
          <template-list :list="works"></template-list>
        </a-row>
      </div>
    </div>
  </div>
</template>

<script lang="ts" setup>
import { onMounted, computed, ref } from "vue"
import { useStore } from "vuex"
import { GlobalDataProps } from "../store/index"
import TemplateList from "../components/TemplateList.vue"
import useLoadMore from "../hooks/useLoadMore"

const store = useStore<GlobalDataProps>()
const searchText = ref("")
const isLogin = computed(() => store.state.user.isLogin)
const works = computed(() => store.state.works.works)
const templates = computed(() => store.state.works.templates)
const total = computed(() => store.state.works.totalTemplates)
const loading = computed(() => store.state.status.loading)
const currentSearchText = computed(() => store.state.works.searchText)
const { loadMorePage, isLastPage } = useLoadMore(
  "fetchTemplates",
  total,
  { pageIndex: 0, pageSize: 8, title: searchText.value },
  8
)
const onSearch = () => {
  const title = searchText.value.trim()
  if (title !== "" || currentSearchText.value !== "") {
    store.dispatch("fetchTemplates", { title, pageIndex: 0, pageSize: 8 })
  }
}
const clearSearch = () => {
  store.dispatch("fetchTemplates", { title: "", pageIndex: 0, pageSize: 8 })
}
onMounted(() => {
  if (isLogin.value) {
    store.dispatch("fetchWorks", { pageIndex: 0, pageSize: 4 })
  }
  store.dispatch("fetchTemplates", { pageIndex: 0, pageSize: 8 })
})
</script>

<style>
.banner {
  display: flex;
  position: relative;
  height: 450px;
  width: 100%;
  overflow: hidden;
}
.banner img {
  width: 100%;
  height: 100%;
  object-fit: cover;
}
.banner-text {
  position: absolute;
  width: 100%;
  height: 100%;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  box-sizing: border-box;
}
.banner-text .ant-input-search {
  width: 40%;
}
.banner-text .ant-input {
  height: 40px;
  font-size: 17px;
  padding: 7px 15px;
  padding-right: 30px;
  border: 2px solid #3b01c4;
  border-right-width: 0px;
}
.banner-text .ant-input-search-button {
  height: 40px;
  border: 2px solid #3b01c4;
  border-left-width: 0px;
  display: flex;
  align-items: center;
}
.banner-text .ant-input-search-button svg {
  font-size: 25px;
}
.text-headline {
  text-shadow: 0 0 1px rgba(68, 92, 116, 0.02), 0 2px 8px rgba(57, 76, 96, 0.15);
  font-size: 2rem;
}
.text-link {
  color: #ffffff;
}
.text-link:hover {
  color: #ffffff;
  text-decoration: underline;
}
.feature-item {
  text-align: center;
  padding: 20px 0;
}
.feature-item .anticon {
  font-size: 60px;
}
.feature-item p {
  color: #666;
}
.feature-item h3 {
  margin: 5px 0;
  font-size: 19px;
  color: #333;
}
.welcome-container {
  background: #f2f2f2;
}
.welcome-container-inner {
  max-width: 1200px;
  margin: 0 auto;
}
.welcome-container-inner img {
  width: 60px;
  height: 60px;
}

.hot-title {
  margin: 0 auto;
  padding: 20px 0;
}
.hot-title p {
  text-align: center;
  font-size: 16px;
  color: #666;
}
.hot-template {
  font-size: 22px;
  color: #333;
}
.empty {
  margin: 0 auto;
}
.hot-template::before,
.hot-template::after {
  content: "";
  display: inline-block;
  width: 57px;
  height: 1px;
  margin: 0 26px;
  background-color: #d8d8d8;
  vertical-align: middle;
}
.content-container {
  background: #fff;
  padding: 0 24px 24px 30px;
  min-height: 85vh;
  max-width: 1200px;
  margin: 0 auto;
  width: 100%;
}
.poster-item {
  position: relative;
  margin-bottom: 20px;
}
.content-title {
  min-height: 70px;
}
.content-title h2 {
  margin-bottom: 0px;
}
</style>
