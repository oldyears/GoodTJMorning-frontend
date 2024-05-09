<template>
	<view class='employee-login'>
		<uni-nav-bar
		  @clickLeft="goBack"
		  left-icon="back"
		  leftIcon="arrowleft"
		  title="楼长登录"
		  statusBar="true"
		  fixed="true"
		  color="#000000"
		  backgroundColor="#a7f3c9"
		></uni-nav-bar>
		<view class="container">
			<h1>Employee Login</h1>
			<form >
				<view class="form-group">
					<lable for="username">UserName: </lable>
					<input type="text" id="username" v-model="username" required>
				</view>
				<view class="form-group">
					<label for="password">Password</label>
					<input type="password" id="password" v-model="password" required>
				</view>
				<button type="submit" @click="login()" class="btn btn-primary">Login</button>
			</form>
			<view v-if="error" class="error">{{ error }}</view>
			<h2>Employee QRcode</h2>
			<button @click="showImage">我要赚钱</button>
		</view>
		
	</view>
</template>

<script>
import { mapState } from "vuex";
import uniNavBar from "@/components/uni-nav-bar/uni-nav-bar.vue";
import { employeeLogin } from "../api/api";
	export default {
		components: {
		  uniNavBar,
		  // Empty,
		},
		name: 'EmployeeLogin',
		data() {
			return {
				username: '',
				password: '',
				error: ''
			}
		},
		computed: {
			...mapState(["addressBackUrl"]),
		},
		methods: {
			goBack() {
				uni.redirectTo({
					url: this.addressBackUrl,
				});
			},
			login() {
				const params = {
					username : this.username,
					password : this.password
				}
				employeeLogin(params).then((res) => {
					if (res.code === 1) {
						console.log("success");
					}
					else {
						this.error = '无效的用户名和密码'
					}
				})
			},
			showImage() {
			    uni.navigateTo({
			        url: '/pages/employee/employee_QRcode'
			    });
			}
		}
	}
</script>

<style lang='scss' scoped>
	.employee-login {
	  display: block;
	  justify-content: center;
	  align-items: center;
	  height: 100%;
	  width: 100%;
	  background-color: #f5f5f5;
	}
	
	.container {
	  height: 100%;
	  width: 80%;
	  padding: 40px;
	  background-color: #fff;
	  border-radius: 8px;
	  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
	}
	
	.form-group {
	  margin-bottom: 40px;
	}
	
	.error {
	  color: red;
	  margin-top: 10px;
	}
	
</style>