<template>
  <div class="msj-down-cell">
    <div
      class="cell-item"
      :class="list.value === item.value ? 'is-actived' : ''"
      v-for="(item, i) in list.options"
      :key="i"
      @click="handleSelect(item, i)"
    >
      <span class="cell-item--label">{{ item.label }}</span>
      <div class="flex items-center">
        <span class="cell-item--suffix mr-2" v-if="item.suffix">
          {{ item.suffix }}
        </span>
        <i class="iconfont text-xl" v-if="list.value === item.value">
          &#xe600;
        </i>
      </div>
    </div>
  </div>
</template>
<script>
export default {
  name: 'dowm-cell',
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
    return {}
  },
  computed: {},
  watch: {},
  mounted() {},
  created() {},
  methods: {
    handleSelect(item, itemIndex) {
      // console.log(item, 'handleSelect')
      this.$forceUpdate()
      this.$emit('success', item.value, item, itemIndex, this.index)
    }
  }
}
</script>
<style lang='scss' scoped>
@import '../common.scss';

.msj-down-cell {
  .cell-item {
    display: flex;
    justify-content: space-between;
    align-items: center;
    // width: 100%;
    min-height: $down-cell-height;
    overflow: hidden;
    font-size: var(--down-font-size);
    color: var(--down-text-color);
    white-space: nowrap;
    border-bottom: 1rpx solid #dedede;
    padding: 0 20rpx;

    &:last-child {
      border-bottom: none;
    }

    &.is-actived {
      color: var(--down-theme-color);
    }

    &--suffix {
      color: #979797;
    }
  }
}
</style>