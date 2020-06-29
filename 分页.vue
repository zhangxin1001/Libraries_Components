<template>
  <div class="pagination" v-if="allpagesNum >= 2">
    <ul>
      <li class="head" @click="goPage(1)" v-if="currentPageNum !== 1">首页</li>
      <li class="head notClick" v-else>首页</li>
      <li :class="['last', currentPageNum === 1 ? 'notClick' : '']" @click="lastpage">上一页</li>
      <li v-for="item in pagesNumArr" :class="{ hover: currentPageNum === item }" :key="item" @click="goPage(item)">
        {{ item }}
      </li>
      <li :class="['next', currentPageNum === allpagesNum ? 'notClick' : '']" @click="nextpage">下一页</li>
      <li class="end" @click="goPage(allpagesNum)" v-if="currentPageNum !== allpagesNum">末页</li>
      <li class="end notClick" v-else>末页</li>
    </ul>
  </div>
</template>
<script>
export default {
  props: ['allpagesNum'],
  data() {
    return {
      currentPageNum: 1
    }
  },
  methods: {
    lastpage() {
      if (this.currentPageNum > 1) {
        this.getData(--this.currentPageNum)
      }
    },
    nextpage() {
      if (this.currentPageNum < this.allpagesNum) {
        this.getData(++this.currentPageNum)
      }
    },
    goPage(i) {
      if (i != this.currentPageNum) {
        this.currentPageNum = i
        this.getData(this.currentPageNum)
      }
    },
    getData(i) {
      this.$emit('getArticle', i)
      this.scrollTop()
    },
    scrollTop() {
      if (window.scrollY > 380) {
        window.scrollTo(0, 0)
      }
    }
  },
  computed: {
    mostShowPage() {
      return this.allPagesNum > 5 ? 5 : this.allPagesNum
    },
    // 页码
    pagesNumArr: function() {
      var left = 1
      var right = this.allpagesNum
      var middle = parseInt(this.mostShowPage / 2)
      var numArr = []
      if (this.allpagesNum >= this.mostShowPage) {
        if (this.currentPageNum > middle + 1) {
          if (this.currentPageNum < this.allpagesNum - middle) {
            left = this.currentPageNum - middle
            right = this.currentPageNum + middle
          } else {
            right = this.allpagesNum
            left = this.allpagesNum - (this.mostShowPage - 1)
          }
        } else {
          left = 1
          right = this.mostShowPage
        }
      }
      while (left <= right) {
        numArr.push(left++)
      }
      return numArr
    }
  }
}
</script>
<style lang="less" scoped>
.pagination {
  position: relative;
  text-align: center;
  ul {
    display: inline-block;
    overflow: hidden;
    li {
      width: 40px;
      height: 40px;
      background: rgba(255, 255, 255, 1);
      border: 1px solid rgba(230, 230, 230, 1);
      border-right: 0;
      float: left;
      line-height: 40px;
      .fontconfig(#666);
      cursor: pointer;
      &:hover {
        color: #ff8100;
      }
    }
    .last,
    .next {
      width: 70px;
    }
    .head,
    .end {
      width: 50px;
    }
    .end {
      border-right: 1px solid rgba(230, 230, 230, 1);
    }
    .hover {
      color: #fff;
      background: #ff8100;
      &:hover {
        color: #fff;
      }
    }
    .notClick {
      color: #999;
      cursor: not-allowed;
      &:hover {
        color: #999;
      }
    }
  }
}
</style>
