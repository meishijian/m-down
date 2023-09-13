<template>
  <div class="msj-down-filter">
    <div
      class="down-filter-row"
      v-for="(item, index) in list.options"
      :key="index"
    >
      <div class="down-filter-row-title font-bold" v-if="item.title">
        {{ item.title }}
      </div>
      <div class="down-filter-row-content flex flex-wrap">
        <template v-if="item.type === 'radio'">
          <div
            class="content-item"
            :class="[item.value === radioItem.value ? 'is-actived' : '']"
            v-for="(radioItem, radioIndex) in item.options"
            :key="radioIndex"
            @click="radioClick(item, index, radioItem, radioIndex)"
          >
            {{ radioItem.label }}
          </div>
        </template>
        <template v-else-if="item.type === 'checkbox'">
          <div
            class="content-item"
            :class="[
              filtersAll(item.value, checkboxItem.value) ? 'is-actived' : ''
            ]"
            v-for="(checkboxItem, checkboxIndex) in item.options"
            :key="checkboxIndex"
            @click="checkboxClick(item, index, checkboxItem, checkboxIndex)"
          >
            {{ checkboxItem.label }}
          </div>
        </template>
      </div>

      <!-- {{ item }} -->
    </div>
  </div>
</template>
<script>
export default {
  name: 'MsjFilter',
  components: {},
  mixins: [],
  props: {
    /** 项 */
    list: {
      type: Object,
      default: {}
    },
    /** 索引 */
    index: {
      type: Number,
      default: 0
    }
  },
  filters: {},
  data() {
    return {
      filtersAllList: []
    }
  },
  computed: {},
  watch: {},
  mounted() {},
  created() {},
  methods: {
    radioClick(item, index, radioItem, radioIndex) {
      // console.log(item, index, radioItem, radioIndex)
      item.value = radioItem.value
      this.$forceUpdate()
      // this.index
      this.$emit('changeItem', item, index, this.index)
    },
    checkboxClick(item, index, radioItem, radioIndex) {
      // console.log(this.list, item, index, radioItem, radioIndex)
      // item.value = radioItem.value
      if (item.isAll && radioItem.value === 'all') {
        item.value = [item.options[0].value]
      } else {
        if (item.value.length > 0 && item.value[0] === 'all') {
          item.value = [radioItem.value]
        } else {
          item.value.push(radioItem.value)
        }
      }
      this.$forceUpdate()
      this.$emit('changeItem', item, index, this.index)
    },
    filtersAll(itemValue, value) {
      const isValueInArray = itemValue.some(item => item === value)
      return isValueInArray
    }
  }
}
</script>
<style lang='scss' scoped>
@import '../common.scss';
.msj-down-filter {
  .down-filter {
    &-row {
      // padding: 0 16rpx;
      &-title {
        font-size: var(--down-font-size);
      }
      &-content {
        margin-top: 16rpx;
        .content-item {
          display: flex;
          align-items: center;
          justify-content: center;
          padding: 14rpx 40rpx;
          margin-right: 20rpx;
          margin-bottom: 20rpx;
          overflow: hidden;
          font-size: 28rpx;
          color: var(--down-text-color);
          background-color: #f1f1f1;
          border-radius: 10rpx;
          border: 1px solid transparent;

          &.is-actived {
            color: var(--down-theme-color);
            border: 1px solid var(--down-theme-color);
          }
        }
      }
    }
  }
}
</style>