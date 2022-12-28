<template>
	<div class="container">
		<div class="plugins-tips">
			wangEditor：轻量级 web 富文本编辑器，配置方便，使用简单。 访问地址：
			<a href="https://www.wangeditor.com/doc/" target="_blank">wangEditor</a>
		</div>
    <div>
      文章语言
      <el-input v-model="article.language"></el-input>
      文章标题
      <el-input size="large" v-model="article.title" ></el-input>
      文章作者
      <el-input size="large" v-model="article.author" ></el-input>

      文章类型
      <el-input size="large" v-model="article.tag" ></el-input>

      封面图片地址
      <el-input size="large" v-model="article.imgUrl" ></el-input>

      Meta Description
      <el-input v-model="article.desc"></el-input>

      Related Articles
      <el-input v-model="article.relatedArts"></el-input>
    </div>

    文章内容
		<div class="mgb20" ref="editor"></div>
		<el-button type="primary" @click="syncHTML">提交</el-button>
	</div>
</template>

<script setup lang="ts" name="editor">
import WangEditor from 'wangeditor';
import { ref, reactive, onMounted, onBeforeUnmount } from 'vue';
import axios from "axios";


// 创建前后端交互体 request
const request = axios.create({
  baseURL: 'http://127.0.0.1:8000/api/v1/manager/',
  timeout: 1000,
})

const editor = ref(null);
const article = reactive({
	content: '',
	title: '',
  author: '',
  tag: '',
  imgUrl: '',
  language: "",
  desc: "",
  relatedArts: ''
});
let instance: any;
onMounted(() => {
	instance = new WangEditor(editor.value);
	instance.config.zIndex = 1;
	instance.create();
});
onBeforeUnmount(() => {
	instance.destroy();
	instance = null;
});
const syncHTML = () => {
  article.content = instance.txt.html();
  console.log(article.title)
	console.log(article.content);
  request.post("articles", {
    title: article.title,
    author: article.author,
    tag: article.tag,
    imgUrl: article.imgUrl,
    content: article.content,
    language: article.language,
    desc: article.desc,
    relatedArts: article.relatedArts

  })
      .then(function (res){
        console.log(res)
      })
      .catch(function (err){
        console.log(err)
      })
};
</script>

<style></style>
