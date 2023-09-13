<template>
  <div class="cascade">
    <div class="cascade-tabs">
      <div
        v-for="(item, index) in activeList"
        :key="index"
        class="cascade-tabs-item cl-mr-20"
        :class="{ active: activeNum === index }"
        @click="activeClick(item, index)"
      >
        {{ item.name }}
        <span v-if="index !== activeList.length - 1">></span>
      </div>
    </div>
    <scroll-view
      scroll-y="true"
      class="main"
      refresher-enabled
      :refresher-triggered="triggered"
      @refresherrefresh="handleOnRefresh"
      @refresherpulling="onPulling"
      @scrolltolower="loadMore"
    >
      <ul v-if="dataList.length > 0">
        <li
          :title="item.name"
          v-for="(item, index) in dataList"
          :key="index"
          :showArrow="true"
          clickable
          @click="listClick(item, index)"
        >
          {{ item.name }}
        </li>
      </ul>
      <div v-if="dataList.length === 0" description="暂无数据">暂无数据</div>
    </scroll-view>
  </div>
</template>
<script>
export default {
  name: 'cascade',
  components: {},
  mixins: [],
  props: {
    data: {
      type: Array,
      default: []
    },
    /**
     * 最后点击 比如 医院 科室 医生 那么就 点击医生时就返回
     */
    dataNum: {
      type: [String, Number],
      default: 0
    }
  },
  filters: {},
  data() {
    return {
      activeNum: 0,
      activeList: [
        {
          name: '请选择',
          orgId: null
        }
      ],
      defaultActive: {
        name: '请选择',
        orgId: null
      },
      dataList: [],
      Isfreshing: false,
      triggered: false
    }
  },
  computed: {},
  watch: {
    data: {
      handler(value) {
        this.dataList = value
      },
      deep: true,
      immediate: true
    }
  },
  mounted() {},
  created() {},
  methods: {
    loadMore() {
      this.$emit('loadMore', this.activeList, this.activeNum)
    },
    onPulling() {
      if (!this.triggered) {
        //下拉刷新，先变true再变false才能关闭
        this.triggered = true
        //关掉圈圈，需要先执行完刷新操作
        setTimeout(() => {
          this.triggered = false
        }, 1000)
      }
    },
    handleOnRefresh() {
      this.dataList = []

      this.$emit('refresh', 1, this.activeList, this.activeNum)
    },
    activeClick(item, index) {
      console.log('🚀 ~ file: cascade.vue:81 ~ item, index:', item, index)
      this.activeNum = index
      console.log(this.activeNum, index, '  this.activeNum ,index')
      // 切割数组
      this.activeList = this.activeList.slice(0, index)
      this.activeList[this.activeNum] = { ...this.defaultActive }
      console.log(
        '🚀 ~ file: cascade.vue:85 ~ activeClick ~ this.activeList:',
        this.activeList
      )

      this.dataList = []
      this.$emit('tagActive', item, index, this.activeNum)
    },
    listClick(item, index) {
      if (this.dataNum === this.activeNum + 1) {
        const aList = [...this.activeList]
        aList[this.activeNum] = item
        this.$emit('toLast', aList, item, index, this.activeNum + 1)
        return true
      }
      // if(this.activeList.length)
      // activeList
      this.activeList[this.activeList.length - 1] = item
      this.activeList[++this.activeNum] = { ...this.defaultActive }

      this.dataList = []
      this.$forceUpdate()
      this.$emit('itemClick', item, index, this.activeNum)
    }
  }
}
</script>
<style lang='scss' scoped>
.cascade {
  &-tabs {
    // padding: 0 10px;
    // display: flex;
    overflow: hidden;
    overflow-x: auto;
    white-space: nowrap;
    // border-bottom: 1px solid #cecece;
    &::after {
      content: '';
      display: block;
      width: 100%;
      height: 1px;
      background-color: #cecece;
    }
    &-item {
      display: inline-block;
      padding: 10px 10px;
      margin-top: -1px;
      span {
        margin-left: 5px;
      }
    }

    .active {
      border-bottom: 2px solid #558bfc;
      color: #558bfc;
      margin-bottom: -1px;
    }
  }
  scroll-view {
    // margin-top: 181rpx;
    height: calc(100vh - 56px - 45px);
    // height: calc(100vh - 56px - 179px);
    // background-color: #f5f4f9;
    ::v-deep .uni-list-item__container {
      padding: 18px 15px;
      padding-right: 0;
    }
  }
}
</style>