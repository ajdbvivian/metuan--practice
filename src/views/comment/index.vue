<template>
  <div class="commentBox" ref="commentBox">
    <div>
      <score
        :delivery="comment.delivery"
        :score="comment.score"
        :packing="comment.packing"
        :flavor="comment.flavor"
      ></score>
      <div class="tag-container">
        <tag :tag="comment.label" :currentType="type" @change="change"></tag>
      </div>
      <list :list="comment.rate"></list>
    </div>
  </div>
</template>

<script>
import score from "./score";
import tag from "./tag";
import list from "./list";
import { getComment } from "@/api/comment";
//1.引入better-scroll组件，设置滚动
import BScroll from "better-scroll";
export default {
  data() {
    return {
      type: 1, //全部
      comment: {},
      commentScroll:null
    };
  },
  components: {
    score,
    tag,
    list
  },
  methods: {
    getData() {
      //获取评论的数据
      return new Promise(resove => {
        getComment({
          id: this.$route.query.id,
          //这是评论的id
          type: this.type
        }).then(res => {
          this.comment = res.data;
          resove()
        });
      });
    },
    change(id) {
      // console.log(id);
      this.type = id;
      this.getData();
    }
  },
  created() {
    this.getData().then(()=>{
      //在vue中$nextTick就是settimeout
      //只有等数据获取结束后，也挂载完成后才要创建better-scroll对象
        this.$nextTick(()=>{
          //使用ref指向需要滚动组件的dom元素
            this.commentScroll = new BScroll(this.$refs.commentBox,{
              //点击事件
                click:true,
              //回弹效果
                bounce:false
            })
        })
    })
  }
};
</script>

<style lang="scss" scoped>
//2.设置最外层的容器，获取Dom
.commentBox {
  //屏幕的高度 - 外面的导航栏
  height: calc(100vh - 44px);
}
.tag-container {
  border-top: 0.2rem solid #f4f4f4;
}
</style>