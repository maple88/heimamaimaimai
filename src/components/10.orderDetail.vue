<template>
    <div class="right-auto">
        <div class="bg-wrap" style="min-height: 765px;">
            <div class="sub-tit">
                <a href="#" @click='$router.go(-1)' class="add">
                   <i class="iconfont icon-reply"></i>返回</a>
                <ul>
                    <li class="selected">
                        <a href="javascript:;">查看订单</a>
                    </li>
                </ul>
            </div>
            <div class="order-progress">
                <ul>
                    <li class="first active">
                        <div class="progress">下单</div>
                        <div class="info">{{orderInfo.add_time | shortTimePlus}}</div>
                    </li>
                    <li class="">
                        <div class="progress">已付款</div>
                        <div class="info">{{orderInfo.complete_time | shortTimePlus}}</div>
                    </li>
                    <li class="">
                        <div class="progress">已经发货</div>
                        <div class="info">{{orderInfo.confirm_time | shortTimePlus}}</div>
                    </li>
                    <li class="last">
                        <div class="progress">已完成</div>
                        <div class="info">{{orderInfo.express_time | shortTimePlus}}</div>
                    </li>
                </ul>
            </div>
            <div class="form-box accept-box form-box1">
                <dl class="head form-group">
                    <dd>
                        订单号：{{orderInfo.order_no}}
                        <!-- <a href="#/site/goods/payment/12" class="btn-pay"> -->
                        <router-link v-show="orderInfo.status == 1" class="btn-pay" :to="'/payMoney/'+orderInfo.id">
                            去付款
                        </router-link>
                        <a href="javascript:void(0)" @click='qianshou' v-show="orderInfo.status == 2" class="btn-pay">确认收货</a>
                        <!---->
                    </dd>
                </dl>
                <dl class="form-group">
                    <dt>订单状态：</dt>
                    <dd>
                        {{orderInfo.statusName}}
                    </dd>
                </dl>
                <dl class="form-group">
                    <dt>快递单号：</dt>
                    <dd>
                        48264892648964982364
                    </dd>
                </dl>
                <dl class="form-group">
                    <dt>支付方式：</dt>
                    <dd>在线支付</dd>
                </dl>
            </div>
            <div class="table-wrap">
                <table width="100%" border="0" cellspacing="0" cellpadding="5" class="ftable">
                    <tbody>
                        <tr>
                            <th align="left">商品信息</th>
                            <th width="60%">名称</th>
                            <th width="10%">单价
                            </th>
                            <th width="10%">数量</th>
                            <th width="10%">金额</th>
                        </tr>
                        <tr v-for="(item,index) in goodsList" :key="item.id">
                            <td width="60">
                                <img :src="item.imgurl" class="img">
                                                        </td>
                            <td align="left">
                                <!-- <a target="_blank" href="/goods/show-92.html"> -->
                                	<router-link :to="'/detail/'+item.goods_id">
                                	{{item.goods_title}}
                                </router-link>
                            </td>
                            <td align="center">
                                <s>￥{{item.real_price}}</s>
                                <p>￥{{item.real_price }}</p>
                            </td>
                            <td align="center">{{item.quantity}}</td>
                            <td align="center">￥{{item.quantity*item.real_price}}</td>
                        </tr>
                        <tr>
                            <td colspan="7" align="right">
                                <p>商品金额：
                                    <b class="red">￥{{allprice}}</b>&nbsp;&nbsp;+&nbsp;&nbsp;运费：
                                    <b class="red">￥20</b>
                                </p>
                                <p style="font-size: 22px;">应付总金额：
                                    <b class="red">￥{{allprice + 20}}</b>
                                </p>
                            </td>
                        </tr>
                    </tbody>
                </table>
            </div>
            <div class="form-box accept-box">
                <dl class="head form-group">
                    <dd>收货信息</dd>
                </dl>
                <dl class="form-group">
                    <dt>顾客姓名：</dt>
                    <dd>{{orderInfo.accept_name}}</dd>
                </dl>
                <dl class="form-group">
                    <dt>送货地址：</dt>
                    <dd>{{orderInfo.area}} </dd>
                </dl>
                <dl class="form-group">
                    <dt>联系电话：</dt>
                    <dd>{{orderInfo.mobile}}</dd>
                </dl>
                <dl class="form-group">
                    <dt>电子邮箱：</dt>
                    <dd> {{ orderInfo.email }}</dd>
                </dl>
                <dl class="form-group">
                    <dt>备注留言：</dt>
                    <dd>{{ orderInfo.message }}</dd>
                </dl>
            </div>
        </div>
    </div>
</template>
<script>
export default {
    name: "orderDetail",
    data: function() {
        return {
            orderId: 0,
            goodsList: [],
            orderInfo: {}
        }
    },
    created() {
        this.orderId = this.$route.params.orderId
        this.getOrderInfo()
    },
    computed: {
        allprice() {
            let price = 0;
            this.goodsList.forEach(v => {
                price += (v.real_price * v.quantity)
            })
            return price
        }
    },
    methods: {
        // 获取商品信息的方法
        getOrderInfo() {
            this.$axios
                .get(`site/validate/order/getorderdetial/${this.orderId}`)
                .then(res => {
                    this.goodsList = res.data.message.goodslist
                    this.orderInfo = res.data.message.orderinfo
                    console.log(res)
                })
        },
        qianshou() {
            this.$confirm('一旦确认钱就到商家手里了呦', '提示', {
                confirmButtonText: '确了个定',
                cancelButtonText: '先欠着',
                type: 'warning'
            }).then(() => {
            	this.orderId = this.$route.params.orderId
                this.$axios
                    .get(`site/validate/order/complate/${this.orderId}`)
                    .then(result => {
                        // //console.log(result);
                        if (result.data.status === 0) {
                            this.$message.success(result.data.message);
                            // 重新获取订单信息
                            this.getOrderInfo();
                        }
                    });
                this.$message({
                    type: 'success',
                    message: '收货成功!'
                });
            }).catch(() => {
                this.$message({
                    type: 'info',
                    message: '算你狠'
                });
            });
        }
    }
}
</script>
<style>
</style>