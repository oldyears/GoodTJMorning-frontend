<!--地址-->
<template>
  <view class="container new_address">
    <!-- 地址 -->
    <view class="top" @click="goAddress">
      <!-- 无地址 -->
      <view v-if="!address" class="address_name_disabled">
        请选择收货地址
      </view>
      <!-- end -->
      <!-- 有地址 -->
      <view v-else class="address_name">
        <view class="address">
          <!-- <text class="tag" :class="'tag' + tagLabel">{{ addressLabel }}</text> -->
          <text class="word">{{ address }}</text>
        </view>
        <view class="name">
          <text class="name_1">{{ cryptoName }}</text>
          <text class="name_2">{{ phoneNumber }}</text>
        </view>
        <view v-if="address" class="infoTip"
          >为减少接触，降低风险，推荐使用无接触配送</view
        >
      </view>
      <view class="address_image">
        <view class="to_right"></view>
      </view>
      <!-- end -->
    </view>
  </view>
</template>
<script>
export default {
  // 获取父级传的数据
  props: {
    // 是公司还是家里样式
    tagLabel: {
      type: String,
      default: "",
    },
    // 是公司还是家里
    addressLabel: {
      type: String,
      default: "",
    },
    // 地址
    address: {
      type: String,
      default: "",
    },
    // 名称
    nickName: {
      type: String,
      default: "",
    },
    gender: {
      type: Number,
      default: -1,
    },
    // 电话
    phoneNumber: {
      type: String,
      default: "",
    },
    // 当前选中
    tabIndex: {
      type: Number,
      default: 0,
    },
    // 当前选中的时间样式
    selectValue: {
      type: Number,
      default: 0,
    },
    // 时间选中的左侧数据（今天、明天）
    popleft: {
      type: Array,
      default: () => [],
    },
    // 周几
    weeks: {
      type: Array,
      default: () => [],
    },
    // 时间段
    newDateData: {
      type: Array,
      default: () => [],
    },
  },
  methods: {
    // 地址选择
    goAddress() {
      this.$emit("goAddress");
    },
    //
    change() {
      this.$emit("change");
    },
  },
  computed: {
    // 万先生
    cryptoName() {
      if (this.$store.state.gender === 0) {
        // 男
        return this.nickName.charAt(0) + "先生";
      } else {
        // 女
        return this.nickName.charAt(0) + "女士";
      }
    },
  },
};
</script>
<style src="./../style.scss" lang="scss" scoped></style>