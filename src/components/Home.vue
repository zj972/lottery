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
    <div class="mask" v-if="showWinner">
      <!--<svg id="dh-crown" viewBox="0 0 88 81" width="100%" height="100%" style="color: yellow;">
        <path fill="currentColor" fill-rule="nonzero" d="M83 23c-2.6 0-4.8 2.3-4.8 5 0 1.6.8 3 2 4L69.6 48.2l-4-27.8c2.5-.3 4.3-2.3 4.3-4.7 0-2.7-2.2-4.8-4.8-4.8-2.7 0-4.8 2-4.8 4.8 0 2 1.3 3.8 3 4.4l-8 22-9.8-32c2-.6 3.4-2.4 3.4-4.6C48.8 3 46.6.8 44 .8c-2.6 0-4.8 2.2-4.8 4.8 0 2.2 1.4 4 3.4 4.6l-9.8 32-8-22c1.8-.6 3-2.4 3-4.4 0-2.7-2-4.8-4.7-4.8-2.6 0-4.7 2-4.7 4.8 0 2.4 1.8 4.3 4 4.7l-3.8 27.8-11-16.5c1.4-.8 2.3-2.3 2.3-4C9.8 25.2 7.6 23 5 23 2.4 23 .2 25.2.2 27.8c0 2.7 2.2 4.8 4.8 4.8.4 0 .8 0 1.2-.2l9.7 41s18.6-1.6 28-1.6c9.4 0 28 1.6 28 1.6L82 32.6H83c2.6 0 4.8-2 4.8-4.7s-2.2-5-4.8-5zM26 62.7c-2 0-3.6-1.6-3.6-3.5 0-2 1.6-3.5 3.6-3.5s3.6 1.6 3.6 3.6-1.6 3.6-3.6 3.6zm18 2.2c-3.2 0-5.8-2.5-5.8-5.7 0-3 2.6-5.7 5.8-5.7 3.2 0 5.8 2.6 5.8 5.8 0 3.3-2.6 5.8-5.8 5.8zm18-2.2c-2 0-3.6-1.6-3.6-3.5 0-2 1.6-3.5 3.6-3.5s3.6 1.6 3.6 3.6-1.6 3.6-3.6 3.6z"></path>
      </svg>-->
    </div>
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
      remain: [],
      // 中奖结果
      showWinner: false
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
    },
    selectWinner () {
      // 计算奖品总数
      let sum = 0
      this.number.map(v => {
        sum += v
      })
      // 抽取中奖用户
      let user = []
      for (let i = 0; i < sum; i++) {
        let index = parseInt(Math.random() * this.user.length, 10)
        while (this.user[index].check === 'check') {
          index = parseInt(Math.random() * this.user.length, 10)
        }
        this.user[index].check = 'check'
        user = user.concat(this.user[index])
      }
      // 抽取奖品
      let check = []
      this.prize.map((value, index) => {
        console.log(this.number, value.type)
        let num = this.number[value.type] ? this.number[value.type] : 0
        for (let i = 0; i < num; i++) {
          let index = parseInt(Math.random() * value.data.length, 10)
          while (value.data[index].check === 'none') {
            index = parseInt(Math.random() * value.data.length, 10)
          }
          value.data[index].check = 'none'
          check = check.concat(value.data[index])
        }
        this.remain[value.type] -= num
      })
      // 合并用户和奖品
      user.map((value, index) => {
        value.prize = check[index]
        return value
      })
      this.winner = this.winner.concat(user)
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
    .mask {
      position: absolute;
      top: 0;
      bottom: 0;
      width: 100%;
      height: 100%;
      background: rgba(0, 0, 0, 0.7);
      z-index: 999;
    }
  }
</style>
