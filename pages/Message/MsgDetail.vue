<template>
	<aby-page>
		<aby-header title="详情" slot="header"></aby-header>
		<div class="mui-content" slot="content" v-if="info">
			<div class="divtitle">
				{{info.msgTitle}}
			</div>
			<div class="divcontent">
				<p><label>消息类型</label>{{info.msgTypeName}}</p>
				<p><label>时间</label>{{info.publishTime|filterConvertDate}}</p>
				<p><label>详情</label>{{info.msgContent}}</p>
			</div>
		</div>
	
	</aby-page>
	
</template>

<script>
	export default {
		components: {},
		props: [],
		data() {
			return {
				info:this.$route.params.info||'',
			}
		},
		methods: {
		},
		mounted() {
		},
		watch: {
		}
	}
</script>

<style scoped>
	.mui-content{
		height: 100%;
		background-color: #F0F0F0;
	}
	.divtitle{
		background-color: #FFFFFF;
		padding: 20px 15px 15px 15px;
		text-align: center;
		margin-top: 10px;
		font-size: 16px;
		border-bottom: 1px solid #F0F0F0;
	}
	.divcontent{
		background-color: #FFFFFF;
		padding: 15px;
	}
	.divcontent p{
		padding-left: 80px;
		color: #232323;
	}
	.divcontent label{
		position: absolute;
		left: 15px;
		color: #AAAAAA;
	}
</style>