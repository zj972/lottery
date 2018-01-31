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
      <div class="card-box">
        <div class="input-box" v-if="item.data.length !== 0" v-for="(item, index) of prize" :key="index">
          <el-tag type="success">{{item.name}}</el-tag>
          <el-slider v-model="number[item.type]"
                     :min="0"
                     :max="remain[item.type]"
                     @change="numberChange"
                     :show-stops="true" style="width: 70%;">
          </el-slider>
        </div>
        <div class="button-box">
          <el-button type="success" size="small" @click="start">开始</el-button>
          <el-button type="success" size="small" @click="selectWinner">停止</el-button>
        </div>
      </div>
    </el-col>
    <!-- 剩余奖品 -->
    <el-col :span="9" class="prize-box">
      <el-row v-for="(item, index) of prize" :key="index">
        <el-col :span="4" v-for="(val, index) of item.data" :key="index" class="prize">
          <app-icon :name="val.name" :check="val.check" :icon="'/static/prize/' + val.name + '.jpg'"></app-icon>
        </el-col>
      </el-row>
    </el-col>
    <app-winner :show="showWinner" :data="winner" @closeMask="closeMask"></app-winner>
  </el-row>
</template>

<script>
import Icon from './Icon'
import Winner from './Winner'
import User from '../assets/json/user.json'
import Prize from '../assets/json/prize.json'
import Confetti from '../assets/js/canvas'

export default {
  name: 'Home',
  components: {
    'app-icon': Icon,
    'app-winner': Winner
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
      showWinner: false,
      // 抽奖效果循环Key
      loopKey: false
    }
  },
  mounted () {
    if (window.localStorage.getItem('data')) {
      // 获取上次数据
      let data = JSON.parse(window.localStorage.getItem('data'))
      this.user = data.user
      this.prize = data.prize
      this.remain = data.remain
    } else {
      // 第一次打开
      this.user = User
      this.prize = Prize
      this.prize.map(v => {
        this.remain[v.type] = v.data.length
      })
      window.localStorage.setItem('data', JSON.stringify({
        time: new Date().toString(),
        user: this.user,
        prize: this.prize,
        remain: this.remain
      }))
    }
  },
  methods: {
    numberChange () {
      // 计算奖品总数
      let sum = 0
      this.number.map(v => {
        sum += v
      })
      if (sum > 8) {
        alert('最多只可以选8个人哦~')
      }
    },
    selectWinner () {
      // 停止动画
      if (!this.loopKey) {
        return
      }
      this.loopKey = false
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
      this.winner = user
      this.number = []
      // 渲染动画
      const DURATION = 8000
      const LENGTH = 200
      /* eslint-disable no-new */
      new Confetti({
        width: window.innerWidth,
        height: window.innerHeight,
        length: LENGTH,
        duration: DURATION,
        isLoop: false
      })
      // 渲染遮罩层
      this.showWinner = true
      // 清空focus状态
      this.user.map(v => {
        if (v.check === 'focus') {
          v.check = ''
        }
      })
      this.prize.map(v => {
        v.data.map(val => {
          if (val.check === 'focus') {
            val.check = ''
          }
        })
      })
      // 存储结果
      window.localStorage.setItem('data', JSON.stringify({
        time: new Date().toString(),
        user: this.user,
        prize: this.prize,
        remain: this.remain
      }))
    },
    start () {
      // 计算奖品总数
      let sum = 0
      this.number.map(v => {
        sum += v
      })
      let residue = 0
      this.user.map(v => {
        if (v.check !== 'check') {
          residue++
        }
      })
      if (sum > residue) {
        alert('人数不够啦~')
      }
      if (!sum) {
        alert('没有选奖品，这是要抽空气吗？')
      }
      if (!sum || sum > residue || this.loopKey === true) {
        return
      }
      this.loopKey = true
      for (let i = 0; i < sum; i++) {
        this.loopUser()
        this.loopPrize()
      }
    },
    loopUser () {
      let length = this.user.length
      let index = parseInt(Math.random() * length, 10)
      let obj = this
      if (this.user[index].check === '') {
        this.user[index].check = 'focus'
        setTimeout(() => {
          obj.user[index].check = obj.user[index].check === 'focus' ? '' : obj.user[index].check
          if (this.loopKey) {
            obj.loopUser()
          }
        }, 50)
      } else {
        this.loopUser()
      }
    },
    loopPrize () {
      let length = this.prize.length
      let index = parseInt(Math.random() * length, 10)
      let i = parseInt(Math.random() * this.prize[index].data.length, 10)
      let obj = this
      if (this.prize[index].data[i].check === '') {
        this.prize[index].data[i].check = 'focus'
        setTimeout(() => {
          obj.prize[index].data[i].check = obj.prize[index].data[i].check === 'focus' ? '' : obj.prize[index].data[i].check
          if (this.loopKey) {
            obj.loopPrize()
          }
        }, 50)
      } else {
        this.loopPrize()
      }
    },
    closeMask () {
      this.showWinner = false
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
        height: 12%;
        justify-content: center;
        align-items: center;
      }
    }
    .prize-box {
      height: 100%;
      .prize {
        display: flex;
        height: 120px;
        justify-content: center;
        align-items: center;
      }
    }
    .center-box {
      position: relative;
      height: 100%;
      text-align: center;
      .card-box {
        margin: 20px 30px 0;
        padding: 10px 5px;
        box-shadow: 2px 4px 10px 4px rgba(0,0,0,0.3);
        border-radius: 8px;
        .input-box,
        .button-box {
          display: flex;
          justify-content: space-around;
          align-items: center;
        }
        .input-box {
          margin: 5px 0;
        }
        .button-box {
          margin: 20px 0;
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
