<template>
	<aby-page ref="page">
		<aby-header title="订单详情" slot="header" :pageNum="pageNum">
		</aby-header>
		<div class="mui-content aby-detail" slot="content" v-if="info!=''">
			<div class="aby-detail-header mui-text-center">
				<h4 id="orderStateDesc">{{info.orderStateDesc}}</h4>
				<div v-if="identityType == 'seller'">
					<p v-if="info.orderState==0" id="countDown">买家还有<span id="countDownTimestamp" style="display: inline-block;color: #FF9800;"></span>完成支付，超时将自动关闭</p>
					<p v-if="info.orderState==1">买家已付款，等待发团日期发团</p>
					<p v-if="info.orderState==2" id="waitFinishTime">等待买家确认完成，还有<span id="waitConfirmDownTimestamp" style="display: inline-block;color: #FF9800;"></span>自动确认</p>
					<p v-if="info.orderState==3">完成时间：{{info.endTime | filterConvertDate}}</p>
					<p v-if="info.orderState==4">订单已被取消，取消时间：{{info.closeTime | filterConvertDate}}</p>
					<p v-if="info.orderState==5">订单超时，已自动关闭</p>
					<p v-if="info.orderState==6">如有疑问可拨打采购商电话<br />如果存在争议可直接联系呱啦啦客服介入调解。</p>
					<p v-if="info.orderState==7">您已同意退款，退款金额:￥{{info.refundAmount}}（订单金额-{{info.serviceFee}}退款手续费），将于2-7个工作日退回到买家付款账户。<br />退款时间：{{info.refundDealTime|filterConvertDate}}</p>
					<p v-if="info.orderState==8">您已拒绝采购商的退款申请，拒绝理由：{{info.refuseReason}}<br />拒绝时间：{{info.refundDealTime|filterConvertDate}}</p>
					<p v-if="info.orderState==9">采购商已经下单，请尽快确认订单</p>
				</div>
				<div v-if="identityType == 'buyer'">
					<p v-if="info.orderState==0" id="countDown">您有<span id="countDownTimestamp" style="display: inline-block;"></span><br />完成支付，如果超时将自动关闭</p>
					<p v-if="info.orderState==1">订单已支付，等待发团日期发团</p>
					<p v-if="info.orderState==2" id="waitFinishTime">旅游团已发出，行程结束后记得确认付款喔～还有<span><span id="waitConfirmDownTimestamp" style="display: inline-block;color: #FF9800;"></span></span>自动确认</p>
					<p v-if="info.orderState==3">完成时间：{{info.endTime | filterConvertDate}}</p>
					<p v-if="info.orderState==4">供应商已取消了您的订单</p>
					<p v-if="info.orderState==5">订单超时，已自动关闭</p>
					<p v-if="info.orderState==6">已提交退款申请给供应商，若供应商1个工作日内未处理将转交呱啦啦客服介入处理</p>
					<p v-if="info.orderState==7">供应商已确认无误，系统已将￥{{info.refundAmount}}元退款给您，将于2-7个工作日退回到您的付款账户，请注意查收。<br />退款时间：{{info.refundDealTime|filterConvertDate}}</p>
					<p v-if="info.orderState==8">您的退款申请已被供应商拒绝，拒绝理由：{{info.refuseReason}}<br />拒绝时间：{{info.refundDealTime|filterConvertDate}}</p>
					<p v-if="info.orderState==9">等待供应商确认订单</p>
				</div>
				<aby-button v-for="(v,i) in initBtn(info.orderState)" @click.native="onBtn(info,v)" :key="i" :title="v.title" :size="v.size" :class="v.bclass"></aby-button>
			</div>
			<div class="aby-detail-line"></div>
			<!-- 显示对方信息 -->
			<div v-if="$store.state.userId == info.buyerId">
				<div class="aby-detail-publisher">
					<ul class="mui-table-view">
						<li class="mui-table-view-cell mui-media" @click="toHomepage(info.buyerId)">
							<img class="mui-media-object mui-pull-left" :src="info.buyerFace">
							<div class="mui-media-body mui-ellipsis">
								{{info.buyerCpname}}
								<!--<p>
									<img class="aby-gold" src="../../static/images/ico/ico_bond_3x.png" />
								</p>-->
							</div>
						</li>
					</ul>
				</div>
				<div class="aby-detail-line"></div>
				<div class="aby-detail-operation">
					<aby-button type="default" title="在线联系" class="aby-button-contact" @click.native="toChat()">
						<aby-icon-color type="chat" class="icochat" slot="imgs"></aby-icon-color>
					</aby-button>
					<aby-button type="default" title="电话联系" class="aby-button-contact" @click.native="$tool.dialTelToApp(info.buyerPhone)">
						<aby-icon-color type="call" class="icocall" slot="imgs"></aby-icon-color>
					</aby-button>
					<span class="aby-line-vertical"></span>
				</div>
			</div>
			<div v-else>
				<div class="aby-detail-publisher">
					<ul class="mui-table-view">
						<li class="mui-table-view-cell mui-media" @click="toHomepage(info.sellerId)">
							<img class="mui-media-object mui-pull-left" :src="info.sellerFace">
							<div class="mui-media-body mui-ellipsis">
								{{info.sellerCpname}}
								<p></p>
							</div>
						</li>
					</ul>
				</div>
				<div class="aby-detail-line"></div>
				<div class="aby-detail-operation">
					<aby-button type="default" title="在线联系" class="aby-button-contact" @click.native="toChat()">
						<aby-icon-color type="chat" class="icochat" slot="imgs"></aby-icon-color>
					</aby-button>
					<aby-button type="default" title="电话联系" class="aby-button-contact" @click.native="$tool.dialTelToApp(info.sellerPhone)">
						<aby-icon-color type="call" class="icocall" slot="imgs"></aby-icon-color>
					</aby-button>
					<span class="aby-line-vertical"></span>
				</div>
			</div>
			<div class="space"></div>

			<div class="aby-detail-content" v-if="info.orderInfo.proType == 1">
				<ul class="mui-table-view">
					<li class="mui-table-view-cell mui-media">
						<div class="mui-media-object mui-pull-left">产品标题</div>
						<div class="mui-media-body">{{info.orderInfo.proTitle}}</div>
					</li>
					<!--基于产品订单详情Start-->
					<li class="mui-table-view-cell mui-media">
						<div class="mui-media-object mui-pull-left">产品编号</div>
						<div class="mui-media-body">{{info.orderInfo.proSummary.lineId}}</div>
					</li>
					<!--基于产品订单详情End-->
					<li class="mui-table-view-cell mui-media">
						<div class="mui-media-object mui-pull-left">出行时间</div>
						<div class="mui-media-body">{{info.goData}}</div>
					</li>
					<!--基于产品订单详情Start-->
					<li class="mui-table-view-cell mui-media">
						<div class="mui-media-object mui-pull-left">出发地</div>
						<div class="mui-media-body">{{info.orderSummary.goCity}}</div>
					</li>
					<!--基于产品订单详情End-->
					<li class="mui-table-view-cell mui-media">
						<div class="mui-media-object mui-pull-left">人数</div>
						<div class="mui-media-body">{{info.orderSummary.peopleCnt}}成人 <span v-if="info.orderSummary.childCnt !=0">{{info.orderSummary.childCnt}}儿童</span></div>
					</li>
					<!--基于产品订单详情Start-->
					<li class="mui-table-view-cell mui-media">
						<div class="mui-media-object mui-pull-left">房间数</div>
						<div class="mui-media-body">{{info.orderSummary.roomCnt}}间</div>
					</li>
					<!--基于产品订单详情End-->
				</ul>
				<div class="aby-detail-linegray100"></div>
				<div class="aby-detail-total mui-text-right">
					合计<span class="aby-detail-price">￥{{info.strPayment}}</span>
					<p class="aby-price-service">手续费：￥{{serviceFee}}</p>
					<p class="aby-price-pro">成交后将扣除手续费(订单总额*6‰，最低20元)</p>
				</div>
			</div>
			<div v-else>
				<div class="aby-detail-content">
					<ul class="mui-table-view">
						<li class="mui-table-view-cell mui-media">
							<div class="mui-media-object mui-pull-left">产品标题</div>
							<div class="mui-media-body">{{info.orderInfo.proTitle}}</div>
						</li>
						<li class="mui-table-view-cell mui-media">
							<div class="mui-media-object mui-pull-left">出行时间</div>
							<div class="mui-media-body">{{info.goData}}</div>
						</li>
						<li class="mui-table-view-cell mui-media">
							<div class="mui-media-object mui-pull-left">人数</div>
							<div class="mui-media-body">{{info.orderSummary.adultsNum}}成人 <span v-if="info.orderSummary.childNum !=0">{{info.orderSummary.childNum}}儿童</span></div>
						</li>
					</ul>
				</div>
				<div class="aby-detail-line"></div>
				<div class="aby-detail-total aby-detail-total-arg mui-text-right">
					合计<span class="aby-detail-price">￥{{info.strPayment}}</span>
					<p class="aby-price-service" v-if="identityType == 'seller'">手续费：￥{{serviceFee}}</p>
					<p class="aby-price-pro" v-if="identityType == 'seller'">成交后将扣除手续费(订单总额*6‰，最低20元)</p>
				</div>
				<div class="aby-detail-content space">
					<ul class="mui-table-view">
						<li class="mui-table-view-cell mui-media" @click="toAgrDetail">
							<div class="mui-media-object mui-pull-left">订单协议</div>
							<div class="mui-media-body">
								<span class="aby-font-blue mui-navigate-right">查看</span>
							</div>
						</li>
					</ul>
				</div>
			</div>

			<div class="space"></div>
			<div class="aby-detail-content" v-if="$store.state.userId == info.sellerId">
				<ul class="mui-table-view">
					<li class="mui-table-view-cell mui-media">
						<div class="mui-media-object mui-pull-left">联系人</div>
						<div class="mui-media-body">{{info.buyerNick}}</div>
					</li>
					<li class="mui-table-view-cell mui-media">
						<div class="mui-media-object mui-pull-left">联系电话</div>
						<div class="mui-media-body">{{info.buyerPhone}}</div>
					</li>
				</ul>
			</div>
			<div class="aby-detail-content" v-else>
				<ul class="mui-table-view">
					<li class="mui-table-view-cell mui-media">
						<div class="mui-media-object mui-pull-left">联系人</div>
						<div class="mui-media-body">{{info.sellerNick}}</div>
					</li>
					<li class="mui-table-view-cell mui-media">
						<div class="mui-media-object mui-pull-left">联系电话</div>
						<div class="mui-media-body">{{info.sellerPhone}}</div>
					</li>
				</ul>
			</div>
			<div class="space"></div>
			<div class="aby-detail-content" v-if="info.sellerNote">
				<ul class="mui-table-view">
					<li class="mui-table-view-cell mui-media">
						<div class="mui-media-object mui-pull-left">订单备注</div>
						<div class="mui-media-body">{{info.sellerNote}}</div>
					</li>
				</ul>
			</div>

			<div class="space"></div>
			<div class="aby-detail-content" v-if="info.orderSummary.needInvoice == 1">
				<ul class="mui-table-view">
					<li class="mui-table-view-cell mui-media">
						<div class="mui-media-object mui-pull-left">发票</div>
						<div class="mui-media-body">{{info.orderSummary.invoiceName}}</div>
					</li>
					<li class="mui-table-view-cell mui-media">
						<div class="mui-media-object mui-pull-left">税务编号</div>
						<div class="mui-media-body">{{info.orderSummary.invoiceNumer}}</div>
					</li>
					<li class="mui-table-view-cell mui-media">
						<div class="mui-media-object mui-pull-left">发票类型</div>
						<div class="mui-media-body">{{info.orderSummary.invoiceContent}}</div>
					</li>
				</ul>
			</div>

			<div class="space"></div>
			<div class="aby-detail-content">
				<ul class="mui-table-view">
					<li class="mui-table-view-cell mui-media">
						<div class="mui-media-object mui-pull-left">订单编号</div>
						<div class="mui-media-body">{{info.orderCode}}
							<!--<aby-button type="default" title="复制" class="aby-button-cope mui-pull-right"></aby-button>-->
						</div>

					</li>
					<li class="mui-table-view-cell mui-media">
						<div class="mui-media-object mui-pull-left">下单时间</div>
						<div class="mui-media-body">{{info.createTime | filterConvertDate}}</div>
					</li>
				</ul>
			</div>

		</div>
	</aby-page>
</template>

<script>
	export default {
		components: {},
		data() {
			return {
				orderId: this.$route.params.orderId,
				cpId: '',
				identityType: this.$route.params.identityType,
				page: this.$route.params.page,
				agreementId: '',
				pageNum: 1,
				serviceFee: '',
				btnList: [{
						id: 1,
						title: '确认订单',
						size: 'small',
						bclass: 'aby-button-line-blue'
					},
					{
						id: 2,
						title: '取消订单',
						size: 'small',
						bclass: 'aby-button-line-default'
					},
					{
						id: 3,
						title: '订单备注',
						size: 'small',
						bclass: 'aby-button-line-default'
					},
					{
						id: 4,
						title: '修改价格',
						size: 'small',
						bclass: 'aby-button-line-blue'
					},
					{
						id: 5,
						title: '付款',
						size: 'small',
						bclass: 'aby-button-line-blue'
					},
					{
						id: 6,
						title: '申请退款',
						size: 'small',
						bclass: 'aby-button-line-default'
					},
					{
						id: 7,
						title: '退款处理',
						size: 'small',
						bclass: 'aby-button-line-blue'
					},
					{
						id: 8,
						title: '退款详情',
						size: 'small',
						bclass: 'aby-button-line-default'
					},
					{
						id: 9,
						title: '退款处理',
						size: 'small',
						bclass: 'aby-button-line-blue'
					},
					{
						id: 10,
						title: '确认完成',
						size: 'small',
						bclass: 'aby-button-line-blue'
					},
					{
						id: 11,
						title: '删除',
						size: 'small',
						bclass: 'aby-button-line-default'
					},
				],
				info: '',
				timer: '',
				isCear: false,
			}
		},
		methods: {
			init() {
				this.orderId = this.$route.params.orderId;
				this.identityType = this.$route.params.identityType;
				this.agreementId = this.$route.params.agreementId || '';
				this.info = '';
				this.isCear = false;
				this.getDetail();
			},
			// 列表按钮显示
			initBtn(orderState) {
				// 处理按钮
				let bList = [];
				let newBtnList = [];
				if(this.identityType == 'buyer') {
					// 买家
					switch(orderState) {
						case 0:
							bList = [2, 5];
							break; // 待付款
						case 1:
							bList = [6];
							break; // 待出行
						case 2:
							bList = [6, 10];
							break; // 待完成
						case 3:
							bList = [11];
							break; // 已完成
						case 4:
							bList = [11];
							break; // 已取消
						case 5:
							bList = [11];
							break; // 超时关闭
						case 6:
							bList = [9];
							break; // 退款中
						case 7:
							bList = [11];
							break; // 已退款
						case 8:
							bList = [7];
							break; // 拒绝退款
						case 9:
							bList = [2];
							break; // 待确认
					}
				} else if(this.identityType == 'seller') {
					// 卖家
					switch(orderState) {
						case 0:
							bList = [3, 4];
							break; // 待付款
						case 1:
							bList = [3];
							break; // 待出行
						case 2:
							bList = [3];
							break; // 待完成
						case 3:
							bList = [11];
							break; // 已完成
						case 4:
							bList = [11];
							break; // 已取消
						case 5:
							bList = [11];
							break; // 超时关闭
						case 6:
							bList = [7];
							break; // 退款中
						case 7:
							bList = [11];
							break; // 已退款
						case 8:
							bList = [8];
							break; // 拒绝退款
						case 9:
							bList = [1, 2];
							break; // 待确认
					}
				}
				this.btnList.forEach((v, i) => {
					bList.forEach((x) => {
						if(x == v.id) newBtnList.push(v);
					})
				});

				return newBtnList;
			},
			// 按钮事件
			onBtn(liObj, btnObj) {
				if(btnObj.id == 3) {
					// 订单备注
					let sellerNote = liObj.sellerNote ? liObj.sellerNote : '';
					this.$tool.prompt({
						inputValue: sellerNote,
						inputPlaceholder: '请输入备注内容'
					}, '备注', (e) => {
						let reqInfo = {};
						reqInfo.orderId = liObj.orderInfo.orderId;
						reqInfo.sellerNote = e.value;
						this.$abyApi.Order.addIntro(reqInfo, (res) => {
							this.getDetail();
						});
					});
				} else if(btnObj.id == 4) {
					// 修改价格
					let payment = liObj.payment;
					this.$tool.prompt({
						inputValue: payment,
						inputPlaceholder: '请输入新价格'
					}, '输入价格', (e) => {
						let reqInfo = {};
						reqInfo.orderId = liObj.orderInfo.orderId;
						reqInfo.payment = e.value;
						if(!this.$tool.isRealNum(reqInfo.payment)) {
							this.$tool.toast('请输入数字！');
							return;
						};
						if(reqInfo.payment < 100) {
							this.$tool.toast('金额不能低于¥100！');
							return;
						};

						if(parseFloat(reqInfo.payment).toFixed(2).length > 10) {
							this.$tool.toast('结算价格过大，暂不支持！');
							return;
						};
						this.$abyApi.Order.editPrice(reqInfo, (res) => {
							this.getDetail();
						});
					})
				} else if(btnObj.id == 5) {
					// 去支付
					this.$router.push({
						name: "payWay",
						params: {
							orderId: liObj.id,
						}
					})
				} else if(btnObj.id == 6) {
					// 申请退款
					this.$router.push({
						name: 'refundApply',
						params: {
							orderId: liObj.id,
							identityType: this.identityType
						}
					});
				} else if(btnObj.id == 7 || btnObj.id == 8 || btnObj.id == 9) {
					// 退款处理 退款详情
					this.$router.push({
						name: 'refundDetail',
						params: {
							orderId: liObj.id,
							identityType: this.identityType
						}
					});
				} else {
					this.$tool.confirm('您确定要' + btnObj.title + '吗？', (res) => {
						let reqInfo = {};
						reqInfo.orderId = liObj.id;
						if(btnObj.id == 1) {
							// 确认订单
							this.$abyApi.Order.confirmOrder(reqInfo, (res) => {
								this.getDetail();
							});
						} else if(btnObj.id == 2) {
							// 取消订单
							this.$abyApi.Order.cancelOrder(reqInfo, (res) => {
								this.getDetail();
							});
						} else if(btnObj.id == 10) {
							// 确认订单
							this.$abyApi.Order.transactionCompletion(reqInfo, (res) => {
								this.getDetail();
							});
						} else if(btnObj.id == 11) {
							// 删除订单
							this.$abyApi.Order.deleteOrder(reqInfo, (res) => {
								this.getDetail();
							});
						}
					});
				}
			},
			// 获得详情
			getDetail() {
				this.$refs.page.showLoading();
				let reqInfo = {};
				reqInfo.orderId = this.orderId;
				this.$abyApi.Order.getOrderDetail(reqInfo, (res) => {
					this.$refs.page.closeLoading();
					res.data.orderInfo.proSummary = JSON.parse(res.data.orderInfo.proSummary);
					res.data.orderSummary = JSON.parse(res.data.orderSummary);
					this.info = res.data;
					// 获取手续费
					let reqfee = {};
					reqfee.payment = res.data.payment;
					reqfee.identityType = this.identityType;
					this.$abyApi.Order.getServiceFee(reqfee, (rtn) => {
						this.serviceFee = rtn.data;
					});
					//倒计时
					if(this.info.orderState == 0) {
						let remainTime = this.info.countDownTimestamp;
						let timer;
						timer = setInterval(() => {
							if(remainTime > 0) {
								this.info.countDownTimestamp = remainTime - 1;
								remainTime = remainTime - 1;
								if(!this.isCear) {
									document.getElementById('countDownTimestamp').innerHTML = this.$tool.getRTime(remainTime + 1);
								}
							} else {
								clearInterval(timer);
//								document.getElementById('orderStateDesc').innerHTML = '已超时';
//								document.getElementById('countDown').innerHTML = '订单超时，已自动关闭'
							}

						}, 1000);
					} else if(this.info.orderState == 2) {
						//待确认状态的订单
						let remainTime = this.info.waitConfirmDownTimestamp;
						let timer;
						timer = setInterval(() => {
							if(remainTime > 0) {
								this.info.waitConfirmDownTimestamp = remainTime - 1;
								remainTime = remainTime - 1;
								if(!this.isCear) {
									document.getElementById('waitConfirmDownTimestamp').innerHTML = this.$tool.getRTime(remainTime + 1);
								}
							} else {
								clearInterval(this.timer);
//								document.getElementById('orderStateDesc').innerHTML = '已完成';
//								document.getElementById('waitFinishTime').innerHTML = '完成时间：' + this.$tool.abyDateFun.getNowFormatDate();
							}
						}, 1000);
					}
				})
			},
			//聊天
			toChat() {
				this.$router.push({
					name: 'chat',
					params: {
						userId: this.identityType == 'seller' ? this.info.buyerId : this.info.sellerId
					}
				});
			},
			//查看协议详情
			toAgrDetail() {
				if(this.agreementId != '') {
					this.$router.back();
				} else {
					this.$router.push({
						name: 'agrDetail',
						params: {
							agreementId: this.info.orderInfo.proid,
							identityType: this.identityType
						}
					});
				}
			},
			toHomepage(userId) {
				let reqInfo = {
					userId: userId
				};
				this.$abyApi.User.getBasciInfo(reqInfo, (res) => {
					this.$refs.page.closeLoading();
					this.cpId = res.cpUserInfo.cpId;
					this.$router.push({
						name: "homePage",
						params: {
							cpId: this.cpId
						}
					})
				});
			}

		},
		mounted() {
			this.getDetail();

			this.pageNum = this.page == 'agrDetail' ? 2 : 1;
		},
		watch: {
			list(val) {
				this.list = val;
			}
		},
		beforeRouteEnter(to, from, next) {
			if(to.params.agreementId || from.name == 'orderList') {
				next(vm => {
					vm.init()
				})
			} else {
				next(vm => {
					vm.isCear = false
				})
			}
		},
		beforeRouteLeave(to, from, next) {
			if(to.name == 'orderList' || to.name == 'refundApply' || to.name == 'payWay' || to.name == 'agrDetail' || to.name == 'chat') {
				this.isCear = true;
			}
			next();
		}
	}
</script>

<style>

</style>