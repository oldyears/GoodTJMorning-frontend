<template>
	<view class="container">
		<image class='QRcode' src="../../static/QRcode.jpg" mode="aspectFit"></image>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				
			}
		},
		methods: {
			
		}
	}
</script>

<style>
.QRcode {
	width: 100%;
	height: 100%;
	border-radius: 20%;
}	
	
.container {
  display: flex;
  justify-content: center;
  align-items: center;
  width: 100%;
  height: 100%;
}
</style>
