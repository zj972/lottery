<template>
  <el-row class="home">
    <!-- 剩余人员 -->
    <el-col :span="9" class="people-box">
      <el-row>
        <el-col :span="6" v-for="(item, index) of user" :key="index" class="people">
          <app-icon :name="item.name" :check="item.check" :icon="'/static/icon/' + item.name + '.jpg'"></app-icon>
        </el-col>
      </el-row>
    </el-col>
    <!-- 中间（选项及结果） -->
    <el-col :span="6" class="center-box">
      <!-- 选项 -->
      <el-card class="card-box">
        <div class="input-box" v-if="item.data.length !== 0" v-for="(item, index) of prize" :key="index">
          {{item.name}}
          <el-input-number v-model="number[item.type]" @change="handleChangeNumber" :min="0" :max="remain[item.type]" size="mini"></el-input-number>
        </div>
        <el-button type="success" size="small" @click="selectWinner">抽取</el-button>
      </el-card>
      <!-- 结果 -->
      <div class="result-box">
        <el-row class="result" v-for="(item, index) of winner" :key="index">
          <el-col :span="12">
            <app-icon :name="item.name" :icon="'/static/icon/' + item.name + '.jpg'"></app-icon>
          </el-col>
          <el-col :span="12">
            <app-icon :name="item.prize.name" :icon="'/static/prize/' + item.prize.name + '.jpg'"></app-icon>
          </el-col>
        </el-row>
      </div>
    </el-col>
    <!-- 剩余奖品 -->
    <el-col :span="9" class="prize-box">
        <el-row v-for="(item, index) of prize" :key="index">
          <el-col :span="4" v-for="(val, index) of item.data" :key="index">
            <app-icon :name="val.name" :check="val.check" :icon="'/static/prize/' + val.name + '.jpg'"></app-icon>
          </el-col>
        </el-row>
    </el-col>
  </el-row>
</template>

<script>
import Icon from './Icon'
import User from '../assets/json/user.json'
import Prize from '../assets/json/prize.json'
export default {
  name: 'Home',
  components: {
    'app-icon': Icon
  },
  data () {
    return {
      user: [],
      prize: [],
      // 中奖列表
      winner: [],
      // 存储要抽奖品的数量
      number: [],
      // 存储抽取奖品的数量上限
      remain: []
    }
  },
  mounted () {
    this.user = User
    this.prize = Prize
    this.prize.map(v => {
      this.remain[v.type] = v.data.length
    })
  },
  methods: {
    handleChangeNumber (val) {
      console.log(val)
    },
    selectWinner () {
      // 计算奖品总数
      let sum = 0
      this.number.map(v => {
        sum += v
      })
      console.log('用户数量：' + this.user.length)
      console.log('奖品数量：' + sum)
      // 抽取中奖用户
      let user = []
      for (let i = 0; i < sum; i++) {
        let index = parseInt(Math.random() * this.user.length, 10)
        // console.log('抽中下标：' + index)
        // console.log('获奖用户：' + this.user[index].name)
        while (this.user[index].check === 'check') {
          index = parseInt(Math.random() * this.user.length, 10)
        }
        this.user[index].check = 'check'
        user = user.concat(this.user[index])
      }
      // 抽取奖品
      let check = []
      this.prize.map((value, index) => {
        let num = this.number[value.type]
        for (let i = 0; i < num; i++) {
          let index = parseInt(Math.random() * value.data.length, 10)
          // console.log('抽中下标：' + index)
          // console.log('抽中的奖品：' + data[index].name)
          while (value.data[index].check === 'none') {
            index = parseInt(Math.random() * value.data.length, 10)
          }
          value.data[index].check = 'none'
          check = check.concat(value.data[index])
        }
        console.log(this.remain, value.type, this.remain[value.type], num)
        this.remain[value.type] -= num
      })
      console.log('抽选的奖品：', check)
      // 合并用户和奖品
      user.map((value, index) => {
        value.prize = check[index]
        return value
      })
      this.winner = this.winner.concat(user)
      console.log('结果：', this.winner)
      this.number = []
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style lang="scss" scoped>
  .home {
    width: 100%;
    height: 100%;
    background: url(/static/bg.jpg) fixed center center no-repeat;
    background-size: cover;
    .people-box {
      height: 100%;
      &>.el-row {
         height: 100%;
      }
      .people {
        display: flex;
        height: 14%;
        justify-content: center;
        align-items: center;
      }
    }
    .prize-box {
      height: 100%;
    }
    .center-box {
      position: relative;
      height: 100%;
      text-align: center;
      .card-box {
        display: inline-block;
        margin-top: 20px;
        .input-box {
          margin-bottom: 10px;
        }
      }
      .result-box {
        width: 100%;
        position: absolute;
        bottom: 0;
        .result {
          width: 100%;
        }
      }
    }
  }
</style>
