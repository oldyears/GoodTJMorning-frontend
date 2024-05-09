<!--新增收获地址-->
<template>
  <view class="customer-box">
    <uni-nav-bar
      @clickLeft="goBack"
      left-icon="back"
      leftIcon="arrowleft"
      :title="delId ? '编辑收货地址' : '新增收货地址'"
      statusBar="true"
      fixed="true"
      color="#ffffff"
      backgroundColor="#333333"
    ></uni-nav-bar>
    <view
      class="add_edit"
      :style="{ height: `calc(100% - ${statusBarHeight} - 44px)` }"
    >
      <form class="form_address">
        <view class="uni-form-item uni-column form_item">
          <view class="title">联系人:</view>
          <uni-easyinput
            class="uni-input"
            v-model="form.name"
            placeholder-class="uni-place"
            placeholder="请填写收货人的姓名"
            minlength="2"
            maxlength="12"
          />
          <view class="radio">
            <view
              class="radio-item"
              v-for="(item, index) in items"
              :key="item.value"
              @click="sexChangeHandle(item.value)"
            >
              <image
                v-if="item.value != form.sex"
                class="radio-img"
                src="../../static/icon-radio.png"
              ></image>
              <image
                v-else
                class="radio-img"
                src="../../static/icon-radio-selected.png"
              ></image>
              <text class="radio-label">{{ item.name }}</text>
            </view>
          </view>
        </view>
        <view class="uni-form-item uni-column form_item">
          <view class="title">手机号:</view>
          <uni-easyinput
            class="uni-input"
            v-model="form.phone"
            type="number"
            placeholder-class="uni-place"
            placeholder="请填写收货人手机号码"
            maxlength="11"
          />
        </view>
        <view class="uni-form-item uni-column form_item pad">
          <view class="title">收货地址:</view>
          <!-- 联动省市县 -->
          <view class="update-input">
            <view class="update-adress" @click="openAddres">
              <text
                v-if="showInput"
                class="uni-input"
                :class="address !== '' ? '' : 'uni-place'"
                >{{ address !== "" ? address : "校区/楼宇" }}</text
              >
              <text class="addressIcon">
                <text class="icon" :class="showClass ? 'iconOn' : ''"></text>
              </text>
            </view>

            <!-- 详细地址 -->
            <textarea
              class="detail"
              :class="{ 'detail-ios': platform == 'ios' }"
              placeholder-class="uni-place"
              v-model="form.detail"
              placeholder="详细地址（精确到门牌号）"
            ></textarea>
          </view>
        </view>
        <view class="uni-form-item uni-column form_item tag-box">
          <view class="title">标签:</view>
          <text
            :class="{ active: form.type === item.type }"
            class="tag_text"
            v-for="item in options"
            :key="item.type"
            @click="getTextOption(item)"
            >{{ item.name }}</text
          >
        </view>
      </form>

      <view class="add_address">
        <button
          class="add_btn"
          type="primary"
          plain="true"
          @click="addAddressFun()"
        >
          保存地址
        </button>
        <button
          v-if="showDel"
          class="del_btn"
          type="default"
          plain="true"
          @click="deleteAddressFun()"
        >
          删除地址
        </button>
      </view>
    </view>
    <simple-address
      ref="simpleAddress"
      :pickerValueDefault="cityPickerValueDefault"
      @onConfirm="onConfirm"
      @isClass="isClass"
      themeColor="#F58C21"
    ></simple-address>
  </view>
</template>

<script>
import simpleAddress from "../common/simple-address/simple-address.nvue"
import {
  addAddressBook,
  delAddressBook,
  queryAddressBookById,
  editAddressBook,
  getCampusInfo,
  getAddressInfo,
} from "../api/api.js"
import { mapState } from "vuex";

export default {
  components: {
    simpleAddress,
  },
  data () {
    return {
      platform: "ios",
      showDel: false,
      showInput: true,
      valueMan: true,
      valueWoman: true,
      showClass: false,
      items: [
        {
          value: "0",
          name: "先生",
        },
        {
          value: "1",
          name: "女士",
        },
      ],
      current: 0,
      options: [
        {
          name: "同学",
          type: 1,
        },
        {
          name: "老师",
          type: 2,
        },
        {
          name: "自己",
          type: 3,
        },
      ],
      // type: 1,
      form: {
        name: "",
        phone: "",
        type: 1,
        sex: "0",
		campusCode: "11",
		campusName: "",
		dormCode: "1101",
		dormName: "",
		is_default: 1,
		id: 0,
		addressId: 0,
        detail: "",
      },
      // 联动省市县
      // 弹框的初始值
      cityPickerValueDefault: [0, 0],
      pickerText: "",
      // 初始值
      address: "",
      // 保存将要删除的
      delId: "",
    }
  },
  onLoad (options) {
    this.init()
    if (options && options.type === "编辑") {
      this.delId = ""
      this.showDel = true
      uni.setNavigationBarTitle({
        title: "编辑收货地址",
      })
      // 保存将要删除的id
      this.delId = options.id
      // 查询详情的接口
      this.queryAddressBookById(options.id)
    } else {
      this.showDel = false
    }
  },
  onUnload () {
    uni.removeStorage({
      key: "edit",
    })
  },
  computed: {
    statusBarHeight () {
      return uni.getSystemInfoSync().statusBarHeight + "px"
    },
	...mapState(['addressDataMap']),
  },
  created () {  },
  methods: {
    init () {
      const res = uni.getSystemInfoSync()
      this.platform = res.platform
    },
    goBack () {
      uni.redirectTo({
        url: "/pages/address/address",
      })
    },
	
	
    // 查询地址详情接口
    queryAddressBookById (id) {
      queryAddressBookById({ id }).then((res) => {
        if (res.code === 1) {
          this.form = {
            phone: res.data.phone,
            name: res.data.consignee,
            sex: res.data.sex,
            detail: res.data.detail,
            addressId: res.data.addressId,
			detail: res.data.domitory,
			id: res.data.id,
			is_default: res.data.is_default,
          }
          if (
            this.addressDataMap[res.data.addressId].campusName &&
            this.addressDataMap[res.data.addressId].addressName
          ) {
            this.address =
              this.addressDataMap[res.data.addressId].campusName +
              "/" +
              this.addressDataMap[res.data.addressId].addressName
          }
        }
      })
    },
    isClass (val) {
      this.showClass = val
    },
    openAddres () {
      this.$refs.simpleAddress.open()

      uni.hideKeyboard()
    },
    onConfirm (e) {
      // this.form.provinceCode = e.provinceCode
      // this.form.cityCode = e.cityCode
      // this.form.districtCode = e.areaCode
	  // this.form.campusCode = e.campusCode
	  // this.form.dormCode = e.dormCode
      // 把选择的地址回显到input框中
	  this.form.addressId = e.id
      this.address = e.label
    },
    bindTextAreaBlur: function (e) {
    },
    radioChange (e) {
      if (e.detail.value === "man") {
        this.form.radio = 0
      } else {
        this.form.radio = 1
      }
    },
    sexChangeHandle (val) {
      this.form.sex = val
    },
    // 保存地址
    addAddressFun () {
      if (this.form.name === "") {
        return uni.showToast({
          title: "联系人不能为空",
          duration: 1000,
          icon: "none",
        })
      } else if (this.form.phone === "") {
        return uni.showToast({
          title: "手机号不能为空",
          duration: 1000,
          icon: "none",
        })
      } else if (this.form.type === "") {
        return uni.showToast({
          title: "所属标签不能为空",
          duration: 1000,
          icon: "none",
        })
      } else if (this.address === "") {
        return uni.showToast({
          title: "所在地区不能为空",
          duration: 1000,
          icon: "none",
        })
      } else if (this.form.detail === "") {
        return uni.showToast({
          title: "详细地址不能为空不能为空",
          duration: 1000,
          icon: "none",
        })
      }

      if (this.form.phone) {
        const reg =
          /^(13[0-9]|14[01456879]|15[0-35-9]|16[2567]|17[0-8]|18[0-9]|19[0-35-9])\d{8}$/
        if (!reg.test(this.form.phone)) {
          return uni.showToast({
            title: "手机号输入有误",
            duration: 1000,
            icon: "none",
          })
        }
      }
      if (this.form.name) {
        const reg = /^[\u0391-\uFFE5A-Za-z0-9]{2,12}$/
        if (!reg.test(this.form.name)) {
          return uni.showToast({
            title: "请输入合法的2-12个字符",
            duration: 1000,
            icon: "none",
          })
        }
      }

      const params = {
		id: this.form.id,
		userId: 1,
        consignee: this.form.name,
		sex: this.form.sex,
		phone: this.form.phone,
		domitory: this.form.detail,
		is_default: this.form.is_default,
		addressId: this.form.addressId,
      }
      // 编辑
      if (this.showDel) {
        editAddressBook(params).then((res) => {
          if (res.code === 1) {
            uni.redirectTo({
              url: "/pages/address/address",
            })
          }
        })
      } else {
        delete params.id
        addAddressBook(params).then((res) => {
          if (res.code === 1) {
            uni.redirectTo({
              url: "/pages/address/address",
            })
          }
        })
      }
    },
    // 删除地址
    deleteAddressFun () {
      delAddressBook(this.delId).then((res) => {
        if (res.code === 1) {
          uni.redirectTo({
            url: "/pages/address/address",
          })
          uni.showToast({
            title: "地址删除成功",
            duration: 1000,
            icon: "none",
          })
          this.form.name = ""
          this.form.phone = ""
          this.form.address = ""
		  this.form.addressId = ""
		  this.form.sex = 1
        }
      })
    },
    // 标签的事件
    getTextOption (item) {
      this.form.type = item.type
    },
  },
};
</script>

<style lang="scss" scoped>
.add_edit {
  width: 750rpx;
  height: 100%;
  background-color: #fff;
  .form_address {
    .form_item {
      margin: 0 22rpx;
      padding: 36rpx 0;
      border-bottom: 1px solid #efefef;
      display: flex;
      align-items: center;
      &.pad {
        padding-bottom: 10rpx;
        align-items: baseline;
      }
      .title {
        width: 140rpx;
        opacity: 1;
        font-size: 28rpx;
        font-family: PingFangSC, PingFangSC-Medium;
        font-weight: 600;
        text-align: left;
        color: #333333;
        letter-spacing: 0px;
        line-height: 44rpx;
      }
      /deep/ .is-input-border {
        border: 0 none;
        border-radius: none;
        min-height: auto;
        line-height: 16rpx;
        .uni-easyinput__content-input {
          padding-left: 0 !important;
          font-size: 26rpx;
        }
        .uni-easyinput__placeholder-class {
          font-size: 26rpx;
        }
        .uni-easyinput__content-textarea {
          padding: 18rpx 0 0;
          width: 100%;
          min-height: 60rpx;
          box-sizing: border-box;
          overflow: visible;
          height: auto;
          font-size: 26rpx;
        }
      }
      .uni-input {
        flex: 1;
      }
      /deep/ .uni-place {
        font-size: 26rpx;
        font-family: PingFangSC, PingFangSC-Regular;
        font-weight: 400;
        color: #999999 !important;
      }
      .radio {
        opacity: 1;
        font-size: 26rpx;
        font-family: PingFangSC, PingFangSC-Regular;
        font-weight: 400;
        text-align: left;
        color: #333333;
        letter-spacing: 0px;
        height: 40rpx;
        display: flex;
        padding-right: 20rpx;
        margin-left: 20rpx;
        .radio-item {
          display: flex;
          align-items: center;
          &:first-child {
            margin-right: 54rpx;
          }
        }
        .radio-img {
          width: 32rpx;
          height: 32rpx;
          margin-right: 10rpx;
        }
      }

      // 标签
      .tag_text {
        width: 68rpx;
        height: 44rpx;
        line-height: 40rpx;
        border: 1px solid #e5e4e4;
        display: inline-block;
        border-radius: 6rpx;
        text-align: center;
        box-sizing: border-box;
        color: #333333;
        font-size: 24rpx;
        &:nth-child(3) {
          margin: 0 20rpx;
        }
        &:nth-child(3) {
          &.active {
            background: #fef8e7;
            border: 1px solid #fef8e7;
          }
        }
        &:nth-child(4) {
          &.active {
            background: #e7fef8;
            border: 1px solid #e7fef8;
          }
        }
      }

      .active {
        background: #e1f1fe;
        border: 1px solid #e1f1fe;
      }
      .addressIcon {
        width: 24px;
        height: 24px;
        display: inline-block;
        position: absolute;
        right: 20rpx;
        top: 0;
        .icon {
          width: 16px;
          height: 16px;
          display: inline-block;
          background: url(../../static/toRight.png) no-repeat 6rpx 50%;
          background-size: contain;
          transform: rotate(90deg);
          margin-left: 10rpx;
        }
        .iconOn {
          transform: rotate(-90deg);
        }
      }

      .update-input {
        flex: 1;
      }
      .update-adress {
        position: relative;
        line-height: 40rpx;
        padding-bottom: 18rpx;
      }
    }
    // 详细地址
    .detail {
      width: 100%;
      height: 50rpx;
      margin: 0;
      /deep/ .uni-place {
        font-family: PingFangSC, PingFangSC-Regular;
        font-weight: 400;
        color: #999999;
        text-align: left;
        line-height: 50rpx;
      }
    }
    .detail-ios {
      padding: 20rpx 14rpx;
    }
  }
  .add_address {
    margin: 0 auto;
    button {
      margin-top: 40rpx;
      margin: 40rpx 18rpx 0 18rpx;
      height: 86rpx;
      line-height: 86rpx;
      border-radius: 8rpx;
      opacity: 1;
      font-size: 30rpx;
      font-family: PingFangSC, PingFangSC-Medium;
      font-weight: 600;
      text-align: center;
      color: #333333;
      letter-spacing: 0px;
      border: 0 none;
    }
    .add_btn {
      background: #a7f3c9;

      .img_btn {
        width: 44rpx;
        height: 44rpx;
        vertical-align: middle;
        margin-bottom: 8rpx;
      }
    }

    .del_btn {
      background: #f6f6f6;
    }
  }
}

.customer-box {
  height: 100vh;
}
/deep/ .uni-icons {
  font-size: 24px !important;
}
/deep/ .content-clear-icon {
  display: inline-block;
  width: 36rpx;
  height: 36rpx;
  line-height: 36rpx;
  padding-top: 4rpx;
  padding-bottom: 4rpx;
}
</style>