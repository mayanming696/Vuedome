<template>
  <div class="pos">
    <el-row>
      <el-col :span="7" class="pos-order" id="order-list">
        <el-tabs>
          <el-tab-pane label="点餐" class="eltaa">
            <el-table :data="tableData" border style="width:100%;" show-summary :summary-method="getSummaries">
              <el-table-column prop="goodsName" label="商品名称" style=" text-align:center;"></el-table-column>
              <el-table-column prop="count" label="数量" width="50"></el-table-column>
              <el-table-column prop="price" label="金额" width="70"></el-table-column>
              <el-table-column label="操作" width="160" fixed="right">
                <template slot-scope="scope">
                  <el-button type="primary" round size="mini" @click="addOrderLis(scope.row)">+</el-button>
                  <el-button type="primary" round size="mini" @click="addBack(scope.row)">-</el-button>
                  <el-button
                    type="danger"
                    icon="el-icon-delete"
                    circle
                    size="mini"
                    @click="delSingleGood(scope.row)"
                  ></el-button>
                </template>
              </el-table-column>
            </el-table>
            <div></div>
            <div class="footButtom">
              <el-button type="primary" plain @click="pendingOrder">挂单</el-button>
              <el-button type="danger" plain @click="deAllGoods">删除</el-button>
              <el-button type="success" plain @click="checkOut">结账</el-button>
            </div>
          </el-tab-pane>
          <el-tab-pane label="订单" class="eltaa">
            <span>订单记录</span>
            <el-table :data="allGoods" stripe style="width: 100%" border show-summary>
              <el-table-column prop="goodsName" label="商品" ></el-table-column>
              <el-table-column prop="count" label="数量"></el-table-column>
            </el-table>
          </el-tab-pane>
          <el-tab-pane label="外卖" class="eltaa">外卖</el-tab-pane>
        </el-tabs>
      </el-col>
      <el-col :span="17">
        <div class="oftenGoods">
          <div class="title">常用商品</div>
          <div class="oftenGoodslist">
            <ul>
              <li v-for="goods in oftenGoods" v-bind:key="goods" @click="addOrderLis(goods)">
                <span>{{goods.goodsName}}</span>
                <span class="oPrice">￥{{goods.price}}元</span>
              </li>
            </ul>
          </div>
        </div>
        <div class="goodType">
          <el-tabs>
            <el-tab-pane label="汉堡">
              <div>
                <ul class="cookList">
                  <li v-for="goods in type0Goods" v-bind:key="goods" @click="addOrderLis(goods)">
                    <span class="foodImg">
                      <img :src="goods.goodsImg" width="100%" />
                    </span>
                    <span class="foodName">{{goods.goodsName}}</span>
                    <br />
                    <span class="foodPrice">￥{{goods.price}}元</span>
                  </li>
                </ul>
              </div>
            </el-tab-pane>
            <el-tab-pane label="小食">
              <div>
                <ul class="cookList">
                  <li v-for="goods in type1Goods" v-bind:key="goods" @click="addOrderLis(goods)">
                    <span class="foodImg">
                      <img :src="goods.goodsImg" width="100%" />
                    </span>
                    <span class="foodName">{{goods.goodsName}}</span>
                    <br />
                    <span class="foodPrice">￥{{goods.price}}元</span>
                  </li>
                </ul>
              </div>
            </el-tab-pane>
            <el-tab-pane label="饮料">
              <div>
                <ul class="cookList">
                  <li v-for="goods in type2Goods" v-bind:key="goods" @click="addOrderLis(goods)">
                    <span class="foodImg">
                      <img :src="goods.goodsImg" width="100%" />
                    </span>
                    <span class="foodName">{{goods.goodsName}}</span>
                    <br />
                    <span class="foodPrice">￥{{goods.price}}元</span>
                  </li>
                </ul>
              </div>
            </el-tab-pane>
            <el-tab-pane label="套餐">
              <div>
                <ul class="cookList">
                  <li v-for="goods in type3Goods" v-bind:key="goods" @click="addOrderLis(goods)">
                    <span class="foodImg">
                      <img :src="goods.goodsImg" width="100%" />
                    </span>
                    <span class="foodName">{{goods.goodsName}}</span>
                    <br />
                    <span class="foodPrice">￥{{goods.price}}元</span>
                  </li>
                </ul>
              </div>
            </el-tab-pane>
          </el-tabs>
        </div>
      </el-col>
    </el-row>
  </div>
</template>
<script>
import axios from 'axios'
export default {
  name: 'pos',
  data () {
    return {
      tableData: [],
      oftenGoods: [],
      type0Goods: [],
      type1Goods: [],
      type2Goods: [],
      allGoods: []
    }
  },
  created: function () {
    axios
      .get(
        'https://www.fastmock.site/mock/e15443d7cc3f1a2df0988483e6103013/mayanming696/oftenGoods'
      )
      .then(reponse => {
        this.oftenGoods = reponse.data.oftenGoods
      })
      .catch(error => {
        console.log('拉取API失败')
        console.log(error)
        alert('网络错误，无法访问')
      })
    axios
      .get(
        'https://www.fastmock.site/mock/e15443d7cc3f1a2df0988483e6103013/mayanming696/type0Goods'
      )
      .then(reponse => {
        this.type0Goods = reponse.data.type0Goods[0]
        this.type1Goods = reponse.data.type0Goods[1]
        this.type2Goods = reponse.data.type0Goods[2]
        this.type3Goods = reponse.data.type0Goods[3]
      })
      .catch(error => {
        console.log('拉取API失败')
        console.log(error)
        alert('网络错误，无法访问')
      })
  },
  mounted: function () {
    var orderHeight = document.body.clientHeight
    document.getElementById('order-list').style.height = orderHeight + 'px'
  },
  methods: {
    addOrderLis (goods) {
      // 判断商品是否存在订单列表中
      let isHave = false
      for (let i = 0; i < this.tableData.length; i++) {
        if (this.tableData[i].goodsId === goods.goodsId) {
          isHave = true
        }
      }
      // 根据判断来编写
      if (isHave) {
        // 存在了就增加商品数量
        let arr = this.tableData.filter(a => a.goodsId === goods.goodsId)
        arr[0].count++
      } else {
        let newGoods = {
          goodsId: goods.goodsId,
          goodsName: goods.goodsName,
          price: goods.price,
          count: 1
        }
        this.tableData.push(newGoods)
      }
    },
    // 数量减少，如果减少到0就删除
    addBack (goods) {
      let arr = this.tableData.filter(a => a.goodsId === goods.goodsId)
      arr[0].count--
      if (arr[0].count === 0) {
        this.tableData = this.tableData.filter(
          o => o.goodsId !== goods.goodsId
        )
      }
    },
    // 删除商品某列
    delSingleGood (goods) {
      this.tableData = this.tableData.filter(o => o.goodsId !== goods.goodsId)
    },
    // 删除全部商品列表
    deAllGoods () {
      this.tableData = []
    },
    // 结算
    checkOut () {
      if (this.tableData.length !== 0) {
        this.tableData = []
        this.$message({
          message: '结账成功',
          type: 'success'
        })
      } else {
        this.$message.error('无法结账，请稍后重试')
      }
    },
    // 挂单功能
    pendingOrder () {
      // 将数组转换为字符串evl
      let str = JSON.stringify(this.tableData)
      localStorage.setItem('orderLists', str)
      let temp = localStorage.getItem('orderLists')
      // this.allGoods.push(JSON.parse(temp))// 数组里面还是数组
      this.allGoods = this.allGoods.concat(JSON.parse(temp))
      // this.allGoods=JSON.parse(temp)
      // localStorage.removeItem('key')// 删除名称为“key”的信息。
      // localStorage.clear()// 清空localStorage中所有信息
    },
    // 饿了吗自带的表格计算
    getSummaries (param) {
      const { columns, data } = param
      let sums = []
      var all = 0
      columns.forEach((column, index) => {
        if (index === 0) {
          sums[index] = '总价'
          return
        }
        if (index === 2) {
          for (var i = 0; i < this.tableData.length; i++) {
            all += this.tableData[i].price * this.tableData[i].count
          }
          sums[2] = all + '元'
          return
        }
        const values = data.map(item => Number(item[column.property]))
        if (!values.every(value => isNaN(value))) {
          sums[index] = values.reduce((prev, curr) => {
            const value = Number(curr)
            if (!isNaN(value)) {
              return prev + curr
            } else {
              return prev
            }
          }, 0)
        }
      })
      return sums
    }
  }
}
</script>
<style scoped>
.pos-order {
  background-color: #f9fafc;
  border-right: 1px solid #c0ccda;
}
.has-gutter {
  text-align: center;
}
.footButtom {
  margin-top: 20px;
}
.title {
  height: 20px;
  border-bottom: 1px solid #d3dce6;
  background-color: #f9fafc;
  padding: 10px;
  width: 100%;
  text-align: left;
}
.oftenGoodslist ul li {
  list-style: none;
  float: left;
  border: 1px solid #e5e9f2;
  padding: 10px;
  margin: 10px;
  background-color: #fff;
  cursor: pointer;
}
.oPrice {
  color: #58b7ff;
}
.goodType {
  clear: both;
}
.cookList li {
  list-style: none;
  width: 23%;
  border: 1px solid #e5e9f2;
  height: auot;
  overflow: hidden;
  background-color: #fff;
  padding: 2px;
  float: left;
  margin: 2px;
  cursor: pointer;
}
.cookList li span {
  display: block;
  float: left;
}
.cookList li {
  list-style: none;
  width: 23%;
  border: 1px solid #e5e9f2;
  height: auot;
  overflow: hidden;
  background-color: #fff;
  padding: 2px;
  float: left;
  margin: 2px;
}
.cookList li span {
  display: block;
  float: left;
}
.foodImg {
  font-size: 0;
  width: 120px;
  height: 120px;
  line-height: 120px;
}
.foodImg img {
  max-height: 100%;
  max-width: 100%;
  vertical-align: middle;
}
.foodName {
  margin-top: 20px;
  width: 50%;
  font-size: 18px;
  padding-left: 10px;
  color: brown;
}
.foodPrice {
  width: 50%;
  font-size: 16px;
  padding-left: 10px;
  padding-top: 10px;
  text-align: center;
  color: #58b7ff;
}
</style>
