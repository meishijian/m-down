<template>
  <view class="msj-down" :style="downStyle">
    <div class="msj-down-header">
      <!-- 头部 -->
      <slot name="header" v-if="!fixedTop"></slot>
      <div
        :class="[
          'down',
          fixedTop ? 'is-fixed' : isFixedTopBall ? 'is-fixed' : ''
        ]"
      >
        <slot name="header" v-if="fixedTop"></slot>
        <!-- 菜单 -->
        <div class="down-menu">
          <div
            class="down-menu-item"
            :class="[
              isGrow ? '' : 'each',
              border ? 'border' : '',
              item.class ? 'down-menu-' + item.class : '',
              getMenuList.length > 1 ? 'justify-center' : '',
              currentIndex === index ? 'is-actived' : ''
            ]"
            v-for="(item, index) in getMenuList"
            :key="index"
            @click="menuClick(item, index)"
          >
            <div class="down-menu-item-click" v-if="item.type === 'click'">
              <slot :name="item.slot" v-if="item.slot"> </slot>
              <template v-else>
                <text> {{ item.title }}</text>
                <slot v-if="item.slotIcon" :name="item.slotIcon"></slot>
                <i v-else-if="item.icon" :class="['click-icon', item.icon]"></i>
              </template>
            </div>
            <div
              v-else-if="item.type === 'sort'"
              class="down-menu-item-sort flex items-center"
            >
              <slot :name="item.slot" v-if="item.slot"> </slot>
              <template v-else>
                <text> {{ item.title }}</text>
                <div
                  class="sort-icon flex flex-col"
                  :class="[
                    item.value === 'asc'
                      ? 'is-actived-sort-icon-up'
                      : 'is-actived-sort-icon-down'
                  ]"
                >
                  <i class="iconfont up-fill" name="arrow-up-fill">
                    &#xeb6e;
                  </i>
                  <i class="iconfont down-fill" name="arrow-down-fill">
                    &#xeb6d;
                  </i>
                </div>
              </template>
            </div>
            <div class="down-menu-item-down" v-else-if="item.type === 'down'">
              <slot :name="item.slot" v-if="item.slot"> </slot>
              <template v-else>
                <text> {{ item.title }}</text>
                <i v-if="item.icon" :class="['down-icon', item.icon]"></i>
                <div v-else class="down-icon">
                  <i class="iconfont up-fill" v-if="currentIndex === index">
                    &#xeb6f;
                  </i>
                  <i class="iconfont" v-else> &#xeb70; </i>
                </div>
              </template>
            </div>
            <div
              class="down-menu-item-filter"
              v-else-if="item.type === 'filter'"
            >
              <slot :name="item.slot" v-if="item.slot"> </slot>
              <template v-else>
                <text> {{ item.title }}</text>
                <i v-if="item.icon" :class="['filter-icon', item.icon]"></i>
                <div v-else class="filter-icon">
                  <i class="iconfont up-fill" v-if="currentIndex === index">
                    &#xeb6f;
                  </i>
                  <i class="iconfont" v-else> &#xeb70; </i>
                </div>
              </template>
            </div>
          </div>
        </div>
        <!-- 弹出 -->
        <div
          class="down-ball"
          :class="{
            'is-show': isShow,
            'is-visible': isVisible,
            'is-show-ball': isFixedTopBall
          }"
        >
          <div class="down-ball-popup">
            <div
              class="down-ball-popup-content"
              :class="[
                menuItem.type === 'down' ? 'down-ball-popup-content-down' : ''
              ]"
            >
              <template v-for="(item, index) in getMenuList">
                <template v-if="item.type === 'down' && index === currentIndex">
                  <slot v-if="item.popupSlot" :name="popupName"></slot>
                  <down
                    v-else
                    :key="index"
                    :list="item"
                    :index="index"
                    @success="downSuccess"
                  />
                </template>
                <template
                  v-else-if="item.type === 'filter' && index === currentIndex"
                >
                  <slot v-if="item.popupSlot" :name="popupName"></slot>
                  <msj-filter
                    v-else
                    :key="index"
                    :list="item"
                    :index="index"
                    @changeItem="changeItem"
                  >
                  </msj-filter>
                </template>
              </template>
            </div>
            <template v-if="menuItem.type !== 'down'">
              <div
                class="down-ball-popup-btn flex justify-between"
                v-if="isBtn"
              >
                <button
                  class="flex-1 resetting"
                  :hair-line="false"
                  ripple
                  @click="handleResetting('resetMenu', true)"
                >
                  重 置
                </button>
                <button
                  class="flex-1 confirm"
                  type="primary"
                  ripple
                  @click="confirm"
                >
                  确 定
                </button>
              </div>
            </template>
          </div>
          <div class="down-ball-mask" @tap="handlePopupHide"></div>
        </div>
      </div>
    </div>

    <!-- 内容 -->
    <div class="msj-down-content" :class="fixedTop ? 'margin' : ''">
      <slot></slot>
    </div>
  </view>
</template>
<script>
import { deepClone } from './utils'
import down from './components/down'
import MsjFilter from './components/filter'
export default {
  name: 'msj-down',
  components: { down, MsjFilter },
  mixins: [],
  props: {
    /**
     * 导航菜单数据
     */
    menuList: {
      type: Array,
      default: () => []
    },
    /**
     * 激活颜色
     */
    themeColor: {
      type: String,
      default: '#007aff'
    },
    /**
     * 常规颜色
     */
    textColor: {
      type: String,
      default: '#000000'
    },
    /**
     * 是否固定在顶部
     */
    fixedTop: {
      type: Boolean,
      default: false
    },
    /**
     * 弹窗过渡时间
     */
    duration: {
      type: [Number, String],
      default: 300
    },
    /**
     * 高度
     */
    height: {
      type: [Number, String],
      default: 80
    },
    /**
     * 是否显示边框
     */
    border: {
      type: Boolean,
      default: false
    },
    /**
     * 是否自定义样式
     */
    isGrow: {
      type: Boolean,
      default: false
    },
    /**
     * 是否显示确认 重置 按钮
     */
    isBtn: {
      type: Boolean,
      default: true
    },
    /**
     * 字体大小
     */
    fontSize: {
      type: String,
      default: '14'
    }
  },
  filters: {},
  data() {
    return {
      createMargin: null,
      currentIndex: -1,
      popupName: '',
      isShow: false,
      isVisible: false,
      menuItem: {},
      isFixedTopBall: false, // 弹框弹出后 固定
      marginFix: null,
      resetMenu: null, // 重置数组 copy
      newMenuCopy: null, //修改后的数据 备份
      isNewMenu: false // 判断是否 修改成 上一次的数据 单项修改
    }
  },
  computed: {
    getMenuList() {
      const allItem = {
        label: '全部',
        value: 'all'
      }
      const newMenu = JSON.parse(JSON.stringify(this.menuList))
      // click 点击 只需要点击
      // 数据
      // {
      //   title: '类型筛选', // 标题
      //   type: 'click', // 类型
      // }

      // sort 排序
      // value : asc desc

      newMenu.forEach(item => {
        if (item.type === 'sort') {
          if (!item.value) {
            item.value = 'asc'
          }
        } else if (item.type === 'down') {
          // 是否是插槽 默认没有插槽
          if (!item.popupSlot) {
            if (!item.value) {
              item.value = 'all'
            }
            // 没有插槽的情况 看是否有options
            if (!item.options) {
              item.options = []
            }
            if (item.isAll) {
              item.options.unshift(allItem)
            }
          }
        } else if (item.type === 'filter') {
          // 是否是插槽 默认没有插槽
          if (!item.popupSlot) {
            if (!item.value) {
              item.value = ['all']
            }
            // 没有插槽的情况 看是否有options
            if (!item.options) {
              item.options = []
            }
            item.options.forEach(filterItem => {
              if (filterItem.isAll) {
                filterItem.options.unshift(allItem)
              }
            })
          }
        }
        // console.log(el, 'el')
      })
      this.resetMenu = JSON.parse(JSON.stringify(newMenu))
      this.newMenuCopy = JSON.parse(JSON.stringify(newMenu))
      return newMenu
    },
    /**
     * 样式
     */
    downStyle() {
      this.marginTopHanld()
      return `
        --down-theme-color: ${this.themeColor};
        --down-text-color: ${this.textColor};
        --down-popup-duration: ${this.duration / 1000}s;
        --down-menu-height: ${this.height / 2 || 0}px;
        --down-margin-top: ${this.createMargin}px;
        --down-margin-top-fix: ${this.marginFix}px;
        --down-font-size: ${this.fontSize}px;
      `
    }
  },
  watch: {},
  mounted() {},
  created() {},
  methods: {
    /**
     * 获取系统信息高度
     */
    marginTopHanld() {
      return this.$nextTick(() => {
        const select = uni.createSelectorQuery().in(this)
        select
          .select('.msj-down .down')
          .boundingClientRect(data => {
            // 在这里可以获取到元素的信息，例如高度、宽度等
            // console.log('元素的高度：', data)
            if (data) {
              // 在这里可以获取到元素的高度
              this.marginFix = data.height
            }
          })
          .exec()
        if (this.fixedTop) {
          select
            .select('.msj-down .down')
            .boundingClientRect(data => {
              // 在这里可以获取到元素的信息，例如高度、宽度等
              // console.log('元素的高度：', data)
              if (data) {
                // 在这里可以获取到元素的高度
                this.createMargin = data.height
              }
            })
            .exec()
        } else {
          select
            .select('.msj-down .msj-down-header')
            .boundingClientRect(data => {
              // 在这里可以获取到元素的信息，例如高度、宽度等
              // console.log('元素的高度：', data)
              if (data) {
                // 在这里可以获取到元素的高度
                this.createMargin = data.height
              }
            })
            .exec()
        }

        return this.createMargin || 0
      })
    },
    /**
     * 点击菜单
     */
    menuClick(item, index) {
      this.menuItem = item
      // 点击
      if (item.type === 'click') {
        this.handlePopupHide(1)
        this.$emit(item.emit || 'click', item, index)
        return true
      } else if (item.type === 'sort') {
        // 排序
        this.handlePopupHide(1)
        item.value = item.value === 'asc' ? 'desc' : 'asc'
        this.$forceUpdate()
        this.$emit(item.emit || 'sort', item, index)
        return true
      } else if (item.type === 'down') {
        // console.log(item, 'item')
        // 下拉
        if (item.popupSlot) {
          this.popupName = item.popupSlot
          this.$emit(item.emit || 'down', item, index)
        } else {
          this.popupName = 'popupDown'
          this.$emit(item.emit || 'down', item, index)
        }
        if (!item.emitConfim) {
          item.emitConfim = 'downConfirm'
        }
        // console.log(this.popupName)
        // $forceUpdate()
      } else if (item.type === 'filter') {
        if (item.popupSlot) {
          this.popupName = item.popupSlot
          this.$emit(item.emit || item.type, item, index)
        } else {
          this.popupName = 'popupFilter'
          this.$emit(item.emit || item.type, item, index)
        }
        if (!item.emitConfim) {
          item.emitConfim = 'filterConfirm'
        }
      }
      this.handlePopupShow(index)
      // console.log(2222, item)
    },

    /**
     * 确定
     */
    confirm() {
      this.newMenuCopy[this.currentIndex] = JSON.parse(
        JSON.stringify(this.getMenuList[this.currentIndex])
      )
      this.isNewMenu = true // 不重置
      this.handlePopupHide()

      this.$emit('confirm', this.menuItem, this.currentIndex)
      if (this.menuItem.type === 'filter' && !this.menuItem.popupSlot) {
        let data = {}
        this.menuItem.options.forEach((item, index) => {
          data[index] = item.value
        })
        this.$emit(
          this.menuItem.emitConfim,
          data,
          this.menuItem,
          this.currentIndex
        )
      } else {
        this.$emit(
          this.menuItem.emitConfim,
          '',
          this.menuItem,
          this.currentIndex
        )
      }
    },

    /**
     * 重置
     */
    handleResetting(name, isReset) {
      // console.log(name, isReset)
      this.getMenuList[this.currentIndex] = JSON.parse(
        JSON.stringify(this[name][this.currentIndex])
      )
      if (!!isReset) {
        this.newMenuCopy[this.currentIndex] = JSON.parse(
          JSON.stringify(this.getMenuList[this.currentIndex])
        )
        this.$emit('resetting')
      }
      this.$forceUpdate()
    },
    /**
     * 打开弹窗
     * @param index 当前激活索引
     */
    handlePopupShow(index) {
      this.isShow = true
      this.isVisible = true
      this.currentIndex = index
      this.isFixedTopBall = true
      this.$emit('open', this.currentIndex)
    },
    /**
     * 关闭弹窗
     */
    handlePopupHide(duration) {
      this.isShow = false
      this.isFixedTopBall = false

      // 延迟移除下拉弹窗
      setTimeout(
        () => {
          if (!this.isNewMenu) {
            this.handleResetting('newMenuCopy', false)
          }
          this.currentIndex = -1
          this.isVisible = false
          this.isNewMenu = false
          // this.getMenuList.forEach(item => {
          //   item.isActived = false
          // })
        },
        typeof duration === 'number' ? duration : this.duration
      )
      this.$emit('close', this.currentIndex)
    },
    /**
     * 菜单项-下拉列表回调
     * @param value 操作返回的数据
     * @param item 下拉列表项数据
     * @param itemIndex 下拉列表项索引
     * @param index 菜单索引
     */
    downSuccess(value, item, itemIndex, index) {
      let itemList = this.getMenuList[index]
      itemList.value = value // 更改值
      this.newMenuCopy[index] = this.getMenuList[index]
      this.isNewMenu = true // 不重置
      this.$forceUpdate()
      this.$emit(
        item.emitConfim || 'downConfirm',
        value,
        item,
        itemIndex,
        index
      )
      this.handlePopupHide()
    },
    /**
     *
     * @param {*} item 当前项
     * @param {*} index 索引
     * @param {*} menuIndex 类型索引
     */
    changeItem(item, index, menuIndex) {
      this.getMenuList[menuIndex].options[index] = item
    }
  }
}
</script>
<style lang='scss' scoped>
@import './common.scss';

.msj-down {
  --icon-mr-left: 10rpx;
  position: relative;
  z-index: 900;
  width: 100%;
  background-color: #fff;
  .down {
    &.is-fixed {
      position: fixed;
      top: var(--window-top, 0px);
      right: 0;
      left: 0;
      z-index: 910;
      display: block;
      height: auto;
      background-color: #fff;
    }

    &-menu {
      position: relative;
      z-index: 920;
      height: var(--down-menu-height, 0px);
      display: flex;
      align-items: center;
      box-shadow: 0 1rpx 0 0 #ececec;
      color: var(--down-text-color);
      overflow: hidden;
      overflow-x: auto;
      white-space: nowrap;
      &::-webkit-scrollbar {
        // background-color: #fafafa;
        width: 0px;
        height: 0px;
      }
      &-item {
        display: flex;
        align-items: center;
        height: 100%;
        padding: 0 20rpx;
        &.is-actived {
          color: var(--down-theme-color);
        }
        &.each {
          flex-grow: 1;
        }
        &.border {
          border-right: 1px solid #ececec;
        }
        &-click,
        &-down,
        &-filter {
          display: flex;
          align-items: center;
          // width: 100%;
          // height: 100%;
          font-size: var(--down-font-size);
          .click-icon,
          .down-icon,
          .filter-icon {
            margin-left: var(--icon-mr-left);
          }
        }
        &-sort {
          font-size: var(--down-font-size);
          .sort-icon {
            margin-left: var(--icon-mr-left);
            u-icon {
              display: flex;
            }
          }
          .is-actived-sort-icon {
            &-up,
            &-down {
              .up-fill,
              .down-fill {
                color: $tips-color;
              }
            }
            &-up {
              .up-fill {
                color: var(--down-theme-color);
              }
            }
            &-down {
              .down-fill {
                color: var(--down-theme-color);
              }
            }
          }
        }
        &-down {
          .down-icon {
            margin-left: var(--icon-mr-left);
            u-icon {
              display: flex;
              color: $tips-color;
            }
            .up-fill {
              color: var(--down-theme-color);
            }
          }
        }
      }
    }

    &-ball {
      position: absolute;
      top: var(--down-margin-top);
      bottom: 0;
      left: 0;
      z-index: -1;
      width: 100%;
      z-index: 930;
      overflow: hidden;
      // opacity: 0;
      visibility: hidden;
      height: calc(100vh - var(--down-menu-height, 0px));
      z-index: 901;

      &.is-show {
        .down-ball-popup {
          transform: translateY(0);
        }
      }

      &.is-show-ball {
        top: var(--down-margin-top-fix);
      }
      &.is-visible {
        // opacity: 1;
        visibility: visible;
      }
      &-popup {
        position: relative;
        z-index: 10;
        max-height: 100%;
        overflow: auto;
        transform: translateY(-120%);
        transition: transform var(--down-popup-duration) linear,
          -webkit-transform var(--down-popup-duration) linear;
        &-content {
          padding: 20rpx;
          background-color: #fff;
          &-down {
            padding: 0;
          }
        }
        &-btn {
          background-color: #fff;
          padding: 20rpx 20rpx;
          // :deep(button) {
          //   // border-radius: 5px;
          // }
          .resetting,
          .confirm {
            margin-right: 30rpx;
            padding: 14rpx 0;
            line-height: inherit;
            border-radius: 35rpx;
          }
          .confirm {
            color: #fff;
            background-color: var(--down-theme-color);
          }

          .resetting {
            background-color: #ffffff;
            border: 1px solid var(--down-theme-color);
            color: var(--down-theme-color);
          }
        }
      }

      &-mask {
        position: absolute;
        top: 0;
        bottom: 0;
        left: 0;
        z-index: 9;
        width: 100%;
        background-color: rgba(0, 0, 0, 0.6);
      }
    }
  }
  &-content {
    &.margin {
      margin-top: calc(var(--down-margin-top));
    }
  }
}
</style>
