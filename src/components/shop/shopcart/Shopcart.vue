<template>
    <div>
        <!-- 导航 -->
        <div class="section">
            <div class="location">
                <span>当前位置：</span>
                <router-link to="/">首页</router-link>&gt;
                <router-link to="">购物车</router-link>
            </div>
        </div>

        <div class="section">
            <div class="wrapper">
                <div class="bg-wrap">
                    <!-- 头部进度 -->
                    <div class="cart-head clearfix">
                        <h2><i class="iconfont icon-cart"></i>我的购物车</h2>
                        <div class="cart-setp">
                            <ul>
                                <li class="first active">
                                    <div class="progress">
                                        <span>1</span> 放进购物车
                                    </div>
                                </li>
                                <li>
                                    <div class="progress">
                                        <span>2</span> 填写订单信息
                                    </div>
                                </li>
                                <li class="last">
                                    <div class="progress">
                                        <span>3</span> 支付/确认订单
                                    </div>
                                </li>
                            </ul>
                        </div>
                    </div>

                    <!-- 商品列表 -->
                    <div class="cart-box">
                        <input id="jsondata" name="jsondata" type="hidden">
                        <table width="100%" align="center" class="cart-table" border="0" cellspacing="0" cellpadding="8">
                            <tbody>
                                <!-- 表头 -->
                                <tr>
                                    <th width="48" align="center">
                                        <el-switch active-color="#13ce66" :value="allSelected" @change="allChange"></el-switch>
                                    </th>
                                    <th align="left" colspan="2">商品信息</th>
                                    <th width="84" align="left">单价</th>
                                    <th width="104" align="center">数量</th>
                                    <th width="104" align="left">金额(元)</th>
                                    <th width="54" align="center">操作</th>
                                </tr>
                                <!-- 表体 -->
                                <tr v-for="item in goodsList" :key="item.id">
                                    <th width="48" align="center">
                                        <el-switch v-model="item.selected" active-color="#13ce66"></el-switch>
                                    </th>
                                    <th align="left" colspan="2">
                                        <img width="50" height="50" :src="item.img_url" alt="">
                                        <span>{{item.title}}</span>
                                    </th>
                                    <th width="84" align="left">
                                        ￥{{item.sell_price}}
                                    </th>
                                    <th width="104" align="center">
                                        <el-input-number size="mini" v-model="$store.state.car[item.id]" :min="1"></el-input-number>
                                    </th>
                                    <th width="104" align="left">
                                        <td>￥{{item.sell_price*$store.state.car[item.id]}}</td>
                                    </th>
                                    <th width="54" align="center">
                                        <el-button size="mini" @click="del(item.id)">删除</el-button>
                                    </th>
                                </tr>

                                <tr v-if="goodsList.length==0">
                                    <td colspan="10">
                                        <div class="msg-tips">
                                            <div class="icon warning"><i class="iconfont icon-tip"></i></div>
                                            <div class="info">
                                                <strong>购物车没有商品！</strong>
                                                <p>您的购物车为空，
                                                    <a href="/index.html">马上去购物</a>吧！</p>
                                            </div>
                                        </div>
                                    </td>
                                </tr>

                                <tr>
                                    <th align="right" colspan="8">
                                        已选择商品
                                        <b class="red" id="totalQuantity">5</b> 件 &nbsp;&nbsp;&nbsp;
                                        商品总金额（不含运费）：
                                        <span class="red">￥</span><b class="red" id="totalAmount">9999</b>元
                                    </th>
                                </tr>
                            </tbody>
                        </table>
                    </div>

                    <!--购物车底部-->
                    <div class="cart-foot clearfix">
                        <div class="right-box">
                            <button class="button">继续购物</button>
                            <button class="submit">立即结算</button>
                        </div>
                    </div>

                </div>
            </div>
        </div>
    </div>
</template>

<script>
    export default {
        data() {
            return {
                goodsList: []
            }
        },
        methods:{
            getshopcargoods(){
                /* 我们的id需要从全局vuex里面获取,通过this.$store.state.car */
               let ids = Object.keys(this.$store.state.car)
                console.log(ids);
                this.$http.get(this.$api.shopcartGoods+ids).then((res)=>{
                       if (res.data.status==0) {
                           
                            /* 给每个按钮添加一个控制开关属性，selected */
                            res.data.message.forEach(v=>v.selected = true);
                             this.goodsList = res.data.message
                       }
                })
            },
            /* 绑定点击事件，但点击的时候触发它 */
            allChange(newStatus){
                this.goodsList.forEach(v=>v.selected=newStatus)
            },
            /* 删除按钮 */
            del(id){
                /* 删除数组里面的id，过滤掉不要的，剩下要的，把要的赋值给数组 */
              this.goodsList = this.goodsList.filter(v=>v.id !=id);
              /* 调用删除方法 */
              this.$store.commit("del",id);
            }
        },
        computed:{
            /* 属性值 */
            allSelected(){
                /* 这个同data里面的值是相关的，如果监听到时true，那么这个计算属性也是true，如果有一个是false
                ，那么全是false */
                return this.goodsList.every(v=>v.selected);
            }
        },
        created(){
            this.getshopcargoods();
        }
    }
</script>

<style>
</style>