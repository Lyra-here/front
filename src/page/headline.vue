<template>
  <div class="fillcontain">
    <head-top></head-top>
    <article v-loading="loading" @click="popModalBox($event)">
      <h1 class="article-title">{{ content.ArticleTitle }}</h1>
      <div class="author-container">
        <p class="author-name">文章来源：{{ content.AuthorName }}</p>
        <img class="author-head-image" :src="content.AuthorHeadImageUrl" />
      </div>
      <img class="article-cover-image" :src="content.ArticleCoverImageUrl" />
      <div class="divider"></div>
      <div
        class="article-content"
        v-html="content.ArticleContentWithTags"
      ></div>
    </article>
    <el-pagination
      small
      layout="prev, pager, next"
      :page-size="pagesize"
      :page-count="pagecount"
      :current-page="pageindex"
      @current-change="getInfo"
      @prev-click="getInfo"
      @next-click="getInfo"
    >
    </el-pagination>
  </div>
</template>
<script>
import headTop from "../components/headTop";
import { getHeadLine } from "../api/getData.js";
export default {
  data() {
    return {
      pagesize: 1, //条目数每页
      pagecount: 0, //总页数，Ajax请求接收后被改变
      pageindex: 1, //页数，从一开始
      resReciver: new Map(),
      content: {}, //初始为空对象，Ajax请求接收后被改变
      loading: true,
      Modal:[]
    };
  },
  created() {
    this.getInfo(this.pageindex);
    this.loading = false;//取消正在加载动画
  },
  components: {
    headTop,
  },
  methods: {
    popModalBox($event){
      console.log($event.target.innerText,'($event.target.innerText)',$event.target.innerHTML,'$event.target.innerHTML')
      let str = $event.target.innerText;
        if(!this.isImg($event.target.className)&&!this.isImg($event.target.innerHTML))
          this.openModal($event.target.innerText);
      
    },
    getInfo(pageI) {//Ajax获取内容，并且将data-src换成src
      this.pageindex = pageI;
      getHeadLine().then((res) => {
        this.resReciver = res;
        this.content = this.resReciver.data.Data[this.pageindex - 1]; //Ajax获取数组内容，数组下标从0开始，所以减一
        this.content.ArticleContentWithTags = this.datasrckiller(this.content.ArticleContentWithTags);//将标签中的datasrc属性换成data，否则图片显示不出来
        this.pagecount = this.resReciver.data.Data.length;//总页数显示
      });
    },
    datasrckiller(str){//把data-src换成src
      return str.replace("data-src", "src")
    },
    isImg(str){//返回布尔：是否图片
      return /image/.test(str)||/img/.test(str)||/<br>/.test(str)
    },
      openModal(val) {//打开模态框 
        this.$alert(val, "Title", {
          confirmButtonText: "确定",
        });
      },
  },
};
</script>
<style lang="less">
body,
div,
span,
header,
footer,
nav,
section,
aside,
article,
ul,
dl,
dt,
dd,
li,
a,
p,
h1,
h2,
h3,
h4,
h5,
h6,
i,
b,
textarea,
button,
input,
select,
figure,
figcaption {
  padding: 0;
  margin: 0;
  list-style: none;
  font-style: normal;
  text-decoration: none;
  border: none;
  font-family: "Microsoft Yahei";
}
.el-pagination {
  text-align: center;
}
p {
  display: block;
  margin-block-start: 1em;
  margin-block-end: 1em;
  margin-inline-start: 0px;
  margin-inline-end: 0px;
}
.rich_media_content {
  visibility: visible !important;
}
h1 {
  font-weight: 400;
  text-align: center;
  font-size: 36px;
  margin-bottom: 16px;
  line-height: 1.35;
}
article {
  display: flex;
  flex-direction: column;
  padding: 40px 90px 40px 90px;
}
::-webkit-scrollbar {
  width: auto;
  height: auto;
  color: grey;
  background-color: #f5f5f5;
}
title {
  display: block;
  font-size: 62px;
}
.divider {
  width: 100%;
  height: 1px;
  border-bottom: 1px #222f3a solid;
  margin: 20px 0 20px 0;
}
body,
html {
  width: 100%;
  height: 100%;
  font: 12px/20px Hiragino Sans GB, Microsoft Yahei, Arial;
}
.author-container {
  text-align: right;
}
span {
  font-size: 16px;
  word-break: break-word;
}
span:hover {
  cursor: pointer;
}
</style>
