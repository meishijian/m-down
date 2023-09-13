<template>
  <view>
    <msj-down
      :menuList="menuList"
      :isGrow="true"
      @dianji="open"
      @ces="open"
      @downSolt="downSolt"
      @noDownSolt="downSolt"
      @downConfirm="downConfirmNoSolt"
      @filterSolt="filterSolt"
      @filterEmitConfim="filterEmitConfim"
      fixedTop
    >
      <!-- fixedTop -->
      <template #header>
        <div>头部插槽-header</div>
      </template>
      <template>
        <div>内容插槽-无</div>
      </template>
      <template #down>
        <div>第一个具名插槽</div>
      </template>
      <template #popupFilter>
        <div>插槽内容</div>
      </template>
    </msj-down>

    <cascade
      :data="cascadeData"
      @itemClick="itemClick"
      :dataNum="3"
      @toLast="toLast"
      @tagActive="tagActive"
      @refresh="handleOnRefresh"
      @loadMore="loadMore"
    ></cascade>

    <div class="oo">
      <div class="item" v-for="i in 10" :key="i">充数内容</div>
      <div class="item">充数内容</div>
      <!-- 自行复制上面的充数内容(让容器溢出即可) -->
    </div>

    <div class="ce">
      <div class="item" v-for="i in 10" :key="i">充数内容</div>
      <div class="item">充数内容</div>
      <!-- 自行复制上面的充数内容(让容器溢出即可) -->
    </div>
    <div class="content">
      <image class="logo" src="/static/logo.png"></image>
      <view class="text-area">
        <div class="title" v-for="(item, index) in 100" :key="index">
          {{ title }}
        </div>
      </view>
    </div>
  </view>
</template>

<script>
import cascade from './cascade'
export default {
  components: { cascade },
  data() {
    return {
      title: 'Hello',
      menuList: [
        {
          title: '点击',
          type: 'click',
          icon: 'icon-001', // 单独有
          class: 'click',
          emit: 'dianji' // 点击菜单时 默认 click
          // slot: 'click' // text
        },
        {
          title: '排序',
          type: 'sort',
          class: 'page',
          emit: 'ces', // 点击菜单时 默认 sort
          value: 'asc' // desc
          // slot: 'page', // text
        },
        {
          title: '下拉插槽',
          type: 'down',
          class: 'down',
          popupSlot: 'down', // 弹框内容
          emit: 'downSolt' // 默认 down 插槽默认为 down ， 不是插槽 downConfirm 下面为例
          // slot: 'page', // text
        },
        {
          title: '下拉列表',
          type: 'down',
          class: 'downss',
          value: 'name', // 默认 all
          isAll: true, // 是否存在全部，不是插槽的情况下有效
          options: [
            {
              label: '姓名',
              value: 'name',
              suffix: '副标题'
            },
            {
              label: '手机号',
              value: 'phone'
            }
          ],
          emit: 'noDownSolt' // 默认的时候 downConfirm ，并不是插槽
          // slot: 'page', // text
        },
        {
          title: '筛选',
          type: 'filter',
          class: 'filter',
          options: [
            {
              type: 'radio',
              title: '单选',
              value: 'all',
              isAll: true, // 是否存在全部，不是插槽的情况下有效
              options: [
                {
                  label: '血压',
                  value: 'xueya'
                },
                {
                  label: '脑卒',
                  value: 'naozu'
                },
                {
                  label: '冠心',
                  value: 'guanxin'
                }
              ]
            },
            {
              type: 'checkbox',
              title: '多选',
              value: ['all'],
              isAll: true, // 是否存在全部，不是插槽的情况下有效
              options: [
                {
                  label: '血压',
                  value: 'xueya'
                },
                {
                  label: '脑卒',
                  value: 'naozu'
                },
                {
                  label: '冠心',
                  value: 'guanxin'
                },
                {
                  label: '慢阻',
                  value: 'manzu'
                },
                {
                  label: '慢性肾',
                  value: 'manxingshen'
                },
                {
                  label: '痛风',
                  value: 'tongfeng'
                }
              ]
            }
          ],
          emit: 'filterSolt', // 默认的时候 filterConfirm ，并不是插槽
          emitConfim: 'filterEmitConfim'
        },
        {
          title: '筛选插槽', // 用 slot , title 失效
          type: 'filter',
          class: 'filter',
          emit: 'filterSolt', // 点击文字时  有 slot 失效 默认的时候 filterConfirm ，并不是插槽
          emitConfim: 'filterEmitConfim', // 确实按钮
          // slot: 'filterTitle', // text
          popupSlot: 'popupFilter' // 弹框内容
        }
      ],
      cascadeData: []
    }
  },
  onLoad() {
    const originalArray = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12]
    const chunkedArrays = this.chunkArray(originalArray, 3)

    console.log(chunkedArrays)
    this.one()
  },

  methods: {
    chunkArray(array, chunkSize) {
      const chunks = []
      for (let i = 0; i < array.length; i += chunkSize) {
        chunks.push(array.slice(i, i + chunkSize))
      }
      return chunks
    },
    open(item, index) {
      console.log(111, item, index)
    },
    downSolt(item, index) {
      console.log(item, index, 'downSolt')
    },
    downConfirmNoSolt(value, item, itemIndex, index) {
      console.log(value, item, itemIndex, index, 'downConfirmNoSolt')
    },
    filterSolt(item, index) {
      console.log(item, index, 'filterSolt')
    },
    filterEmitConfim(value, item, index) {
      console.log(value, item, index, 'filterEmitConfim')
    },
    one() {
      let data = [
        {
          name: 'one-1',
          id: '101'
        },
        {
          name: 'one-2',
          id: '102'
        }
      ]
      this.cascadeData = data
    },
    two() {
      let data = [
        {
          name: 'two-1',
          id: '201'
        },
        {
          name: 'two-2',
          id: '202'
        }
      ]
      this.cascadeData = data
    },
    three() {
      let data = [
        {
          name: 'three-1',
          id: '301'
        },
        {
          name: 'three-2',
          id: '302'
        }
      ]
      this.cascadeData = data
    },
    itemClick(item, index, activeNum) {
      this.activeSelect(activeNum)
    },
    toLast(activeList, item, index, activeNum) {
      console.log(activeList, item, index, activeNum, 'item, index, activeNum')
    },
    tagActive(item, index, activeNum) {
      this.itemClick(item, index, activeNum)
    },

    activeSelect(activeNum) {
      if (activeNum === 0) {
        this.one()
      } else if (activeNum === 1) {
        this.two()
      } else if (activeNum === 2) {
        this.three()
      }
    },
    loadMore(activeList, activeNum) {},
    handleOnRefresh(pageSize, activeList, activeNum) {
      this.cascadeData = []
      this.$nextTick(() => {
        this.activeSelect(activeNum)
      })
    }
  }
}
</script>


<style lang="scss" scoped>
.oo {
  display: flex;
  width: 100%;
  overflow: hidden;
  overflow-x: auto;
  white-space: nowrap;
  background: rgb(219, 0, 0);
  .item {
    display: inline-block;
    background: #fff;
    margin: 10px;
    width: 100px;
  }
}
.ce {
  background: rgb(196, 196, 196);
  width: 100%;
  overflow: hidden;
  overflow-x: auto;
  white-space: nowrap;
  .item {
    display: inline-block;
    background: #fff;
    margin: 10px;
    width: 100px;
    height: 100px;
  }
}
</style>
<style>
.content {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
}

.logo {
  height: 200rpx;
  width: 200rpx;
  margin-top: 200rpx;
  margin-left: auto;
  margin-right: auto;
  margin-bottom: 50rpx;
}

.text-area {
  display: flex;
  justify-content: center;
  flex-direction: column;
}

.title {
  font-size: 36rpx;
  color: #8f8f94;
}
</style>
