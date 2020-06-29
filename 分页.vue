<template>
  <div class="numroll">
    <ul>
      <li
        v-for="(item,index) in newNum"
        :key="index"
        :class= "[item === ',' ? 'hover' : '']"
      >   
        <span v-if="item === ','">,</span>
        <span v-else> <i ref="numberItem">0123456789</i></span>
      </li>
    </ul>
  </div>
</template>
<script>
export default {
  props: {
    num:{
      required:true,
      type:[String,Number]
    },
    length:{
      type:[String,Number],
      default:'9'
    }
  },    
  data() {
    return {
      orderNum: this.num,
      len: this.length 
    }
  },
  computed: {
    tempNum: function() {
      return ('000000000' + this.orderNum).slice(-this.len)
    },
    newNum: function() {
      return this.tempNum.replace(/\d{1,3}(?=(\d{3})+$)/g, '$&,')
    }
  },
  mounted() {
    setTimeout(() => {
      this.setNumberTransform()
    })
  },
  methods: {
    setNumberTransform() {
      var numberItems = this.$refs.numberItem
      for (let index = 0; index < this.tempNum.length; index++) {
        var elem = numberItems[index]
        elem.style.transform = `translate(0, -${this.tempNum[index] * 10}%)`
      }
    }
  }
}
</script>
<style lang="less" scoped>
@wWidth: 1920;
@wHeight: 1080;

.vw(@name, @px) {
  @{name}: @px / @wWidth * 100vw;
}
.vh(@name, @px) {
  @{name}: @px / @wHeight * 100vh;
}

.numroll {
  ul {
    width: 100%;
    display: flex;
    justify-content: sapce-between;
    .hover {
      background: #0000;
    }
    li {
      color: #f00;
      font-size: 68px;
      .vh(height, 100);
      .vw(margin-left, 10);
      .vw(margin-right, 10);
      text-align: center;
      list-style: none;
      overflow: hidden;
      background: #000;
      span {
        width: 100%;
        display: inline-block;
      }
      i {
        width: 100%;
        display: inline-block;
        text-align: center;
        writing-mode: vertical-lr;
        text-orientation: upright;
        transform: translate(0, 0);
        transition: transform 1s ease-in-out;
      }
    }
  }
}
</style>

