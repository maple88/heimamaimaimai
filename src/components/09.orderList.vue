<template>
    <div class="right-auto">
        <div class="bg-wrap" style="min-height: 765px;">
            <div class="sub-tit">
                <ul>
                    <li class="selected">
                        <a href="/user/order-list.html">交易订单</a>
                    </li>
                </ul>
            </div>
            <div class="table-wrap">
                <table width="100%" border="0" cellspacing="0" cellpadding="0" class="ftable">
                    <tbody>
                        <tr>
                            <th width="16%" align="left">订单号</th>
                            <th width="10%">姓名</th>
                            <th width="12%">订单金额</th>
                            <th width="11%">下单时间</th>
                            <th width="10%">状态</th>
                            <th width="12%">操作</th>
                        </tr>
                        <tr v-for="(item,index) in orderList" :key="item.id">
                            <td>{{item.order_no}}</td>
                            <td align="left">{{item.accept_name}}</td>
                            <td align="left">
                                <strong style="color: red;">￥{{item.order_amount}}</strong>
                                <br> 在线支付
                            </td>
                            <td align="left">{{item.add_time | shortTimePlus}}</td>
                            <td align="left">
                                {{item.statusName}}
                            </td>
                            <td align="left">
                                <router-link  :to="'/vipCenter/orderDetail/'+item.id">
                                    查看订单
                                </router-link>
                                
                                <!-- <a href="#/site/goods/payment/12" class=""> -->
								<router-link v-show="item.status == 1" :to="'/payMoney/'+item.id">
                                	|去付款<!-- </a> -->
                                </router-link>
                                
                                <a href="javascript:void(0)">|取消</a>
                                <br>
                            </td>
                        </tr>
                    </tbody>
                </table>
                <div class="page-foot">
                    <!-- 放置分页插件 -->
                    <div class="block">
                        <span class="demonstration">调整每页显示条数</span>
                        <el-pagination 
                        @size-change="handleSizeChange" 
                        @current-change="handleCurrentChange" 
                        :current-page.sync="pageIndex" 
                        :page-sizes="[5, 10, 15, 20]" 
                        :page-size="100" 
                        layout="sizes, prev, pager, next" 
                        :total="totalcount"
                        background
                        >
                        </el-pagination>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>
<script>
export default {
    name: "orderList",
    data: function() {
        return {
            orderList: [],
            pageIndex: 1,
            pageSize: 5,
            totalcount: 0
        }
    },
    methods: {
        // 获取订单数据的方法
        getOrderList() {
            this.$axios
                .get(`site/validate/order/userorderlist?pageIndex=${
            this.pageIndex
          }&pageSize=${this.pageSize}`).then(res => {
                    console.log(res)
                    this.orderList = res.data.message
                    this.totalcount = res.data.totalcount

                })
        },
        // pageSize改变触发的事件
        handleSizeChange(pageSize){
        	console.log(pageSize)
        	this.pageSize = pageSize
        	this.getOrderList()
        },
        // 点击跳转当前页
        handleCurrentChange(pageIndex){
        	console.log(pageIndex)
        	this.pageIndex = pageIndex;
        	this.getOrderList()
        }
    },
    created() {
        this.getOrderList()
    }
}
</script>
<style>
td a{
	display: block;
}
</style>