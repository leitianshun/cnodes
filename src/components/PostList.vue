<template>
<div class="PostList">
  <!--在数据未返回的时候，显示这个正在加载的gif-->
  <div class="loading" v-if="isLoading">
    <img src="../assets/loading.gif" >
  </div>

  <!--代表主题帖子列表-->
  <div class="posts" v-else>
    <ul>
      <li>
        <div class="toobar">
          <span>全部</span>
          <span>精华</span>
          <span>分享</span>
          <span>问答</span>
          <span>招聘</span>
        </div>
      </li>
      <li v-for="post in posts" >
        <!--头像-->
        <img :src="post.author.avatar_url" alt="">
        <!--回复/浏览-->
        <span class="allcount">
          <span class="reply_count">{{post.reply_count}}</span>
          /{{post.visit_count}}
        </span>
        <!--帖子的分类-->
        <span :class="[{put_good:(post.good  == true),put_top:(post.top  == true),
        'topiclist-tab':(post.good  != true && post.top  != true)}]">
          <span>
            {{post | tabFormatter}}
          </span>
        </span>
        <!--标题-->
        <router-link :to="{
        name:'post_content',
        params:{
          id:post.id,
          name:post.author.loginname
        }
        }">
          <span>
          {{post.title}}
         </span>
        </router-link>
        <!--最終回复时间-->
        <span class="last_reply">
          {{post.last_reply_at | formatDate}} 
        </span>
      </li>
      <li>
        <!--分页-->
        <pagination @handleList="renderList"></pagination>
      </li>
    </ul>
  </div>
</div>
</template>

<script>
  import pagination from './Pagination'
    export default {
        name: "PostList",
      data(){
          return {
            isLoading:false,
            posts:[],   //代表页面的列表数组
            postpage:1
          }
      },
      components:{
        pagination
      },
      methods:{
          getData(){
            this.$http.get('https://cnodejs.org/api/v1/topics',{    //获取数据
              params:{        //参数个数
                page:this.postpage,
                limit:20      //20条
              }
            })
              .then(res=>{              
                this.isLoading = false; //加载成功之后，去除动画
                this.posts = res.data.data;      //此时的this是vue，把返回的结果给data里的posts
              })
              .catch(function (err) {     //处理返回失败后的问题
                console.log(err)
              })
          },
        renderList(value){
          this.postpage = value;
          this.getData();
        }
      },
      beforeMount(){
        this.isLoading = true;    //加载成功之前显示加载动画
        this.getData();           //在页面加载之前获取数据
      }
    }
</script>

<style scoped>
  .PostList{
    background-color: #e1e1e1;
  }
  .posts {
    margin-top: 5px;
  }

  .PostList img {
    height: 30px;
    width: 30px;
    vertical-align: middle;
  }

  ul {
    
    list-style: none;
    width: 100%;
    max-width: 1300px;
    margin: 0 auto;
  }

  ul li:not(:first-child) {
    padding: 9px;
    font-size: 15px;
    font-family: "Helvetica Neue", "Luxi Sans", "DejaVu Sans", Tahoma, "Hiragino Sans GB", STHeiti, sans-serif !important;
    font-weight: 400;
    background-color: white;
    color: #333;
    border-top: 1px solid #f0f0f0;
  }

  li:not(:first-child):hover {
    background: #f5f5f5;;
  }

  li:last-child:hover {
    background: white;
  }

  li span {
    line-height: 30px;
  }

  .allcount {
    width: 70px;
    display: inline-block;
    text-align: center;
    font-size: 12px;
  }

  .reply_count {
    color: #9e78c0;
    font-size: 14px;
  }

  .put_good, .put_top {
    background: #80bd01;
    padding: 2px 4px;
    border-radius: 3px;
    -webkit-border-radius: 3px;
    -moz-border-radius: 3px;
    -o-border-radius: 3px;
    color: #fff;
    font-size: 12px;
    margin-right: 10px;
  }

  .topiclist-tab {
    background-color: #e5e5e5;
    color: #999;
    padding: 2px 4px;
    border-radius: 3px;
    -webkit-border-radius: 3px;
    -moz-border-radius: 3px;
    -o-border-radius: 3px;
    font-size: 12px;
    margin-right: 10px;
  }

  .last_reply {
    text-align: right;
    min-width: 50px;
    display: inline-block;
    white-space: nowrap;
    float: right;
    color: #778087;
    font-size: 12px;
  }

  .toobar {
    height: 50px;
    background-color: #f5f5f5;
  }

  .toobar span {
    font-size: 14px;
    text-align: center;
    color: #4224ee;
    line-height: 40px;
    display: inline-block;
    /* margin: 0 auto; */
    margin-left: 20px;
    padding-top: 8px;
    cursor: pointer;
  }

  .toobar span:hover {
    color: #9e78c0;
  }

  a {
    text-decoration: none;
    color: black;
  }

  a:hover {
    text-decoration: underline;
  }

  .loading {
    text-align: center;
   padding-top: 400px;
   

    
  }
</style>
