<template>
  <div id="app">
    <div v-for="item in operationArrange">
      {{ item }}
      <span v-for="d in item">
        <i :class="'color color' + d"></i>
      </span>
    </div>

    <div></div>

  </div>
</template>

<script>
  export default {
      name: 'app',
      data () {
          return {
              msg: 'Welcome to Your Vue.js App',
              // 工作数
              jobNum: null,
              // 染色体代，每个染色体的长度为：jobNum * (operationNum - 1)
              chromosomes: [],
              // 操作的个数
              operationNum: null,
              // 工厂的个数
              factoryNum: null,
              // 当代染色的个数
              chromosomeNum: 20,
              // 当代最好的染色体
              bestChromosome: [],
              // 交叉概率
              crossRate: null,
              // 最大循环次数
              maxCycleNum: 20,
              // 变异概率
              variationRate: 0.3,
              // [1][2]  job 1 opera 2
              operationDetailOneShop: [
                  [
                      [{op: 3, time: 1}, {time:3, op: 1}, {time: 6, op: 2}, {time: 7, op: 4}, {time: 3, op: 6}, {time: 6, op: 5}],
                      [{time: 8, op: 2},{time: 5, op: 3},{time: 10, op: 5}, {time: 10, op: 6}, {time: 10, op: 1}, {time: 4, op: 4}],
                      [{time: 5,op: 3}, {time: 4, op: 4}, {time: 8, op: 6}, {time: 9, op: 1}, {time: 1, op: 2}, {time: 7, op: 5}],
                      [{time: 5,op: 2}, {time: 5, op: 1}, {time: 5, op: 3}, {time: 3, op: 4}, {time: 8, op: 5}, {time: 9, op: 6}],
                      [{time: 9,op: 3}, {time: 3, op: 2}, {time: 5, op: 5}, {time: 4, op: 6}, {time: 3, op: 1}, {time: 1, op: 4}],
                      [{time: 3,op: 2}, {time: 3, op: 4}, {time: 9, op: 6}, {time: 10, op: 1}, {time: 4, op: 5}, {time: 1, op: 3}],
                  ]
              ],
              // 多工厂调度计划
              operationDetail: [
                 [
                    [{op: 1, time: 2}, {op: 3, time: 1}, {op: 2, time: 3}, {op: 4, time: 3}],
                    [{op: 2, time: 3}, {op: 3, time: 2}, {op: 1, time: 3}, {op: 4, time: 4}],
                    [{op: 2, time: 10}, {op: 1, time: 10}, {op: 2, time: 10}, {op: 3, time: 6}],
                    [{op: 2, time: 6}, {op: 3, time: 8}, {op: 1, time: 9}, {op: 4, time: 10}],
                    [{op: 2, time: 3}, {op: 4, time: 2}, {op: 1, time: 1}, {op: 3, time: 4}],
                    [{op: 3, time: 4}, {op: 4, time: 3}, {op: 1, time: 2}, {op: 2, time: 1}]
                 ],[
                    [{op: 1, time: 1}, {op: 3, time: 3}, {op: 2, time: 4}, {op: 4, time: 4}],
                    [{op: 2, time: 2}, {op: 1, time: 2}, {op: 3, time: 3}, {op: 4, time: 4}],
                    [{op: 3, time: 1}, {op: 2, time: 2}, {op: 1, time: 4}, {op: 4, time: 3}],
                    [{op: 3, time: 7}, {op: 2, time: 7}, {op: 1, time: 8}, {op: 4, time: 7}],
                    [{op: 4, time: 3}, {op: 3, time: 2}, {op: 2, time: 2}, {op: 1, time: 3}],
                    [{op: 3, time: 5}, {op: 2, time: 3}, {op: 1, time: 1}, {op: 4, time: 2}]
                 ],[
                    [{op: 1, time: 2}, {op: 3, time: 4}, {op: 4, time: 2}, {op: 2, time: 3}],
                    [{op: 1, time: 10}, {op: 4, time: 10}, {op: 3, time: 15}, {op: 2, time: 8}],
                    [{op: 4, time: 3}, {op: 3, time: 1}, {op: 1, time: 4}, {op: 2, time: 2}],
                    [{op: 1, time: 2}, {op: 4, time: 3}, {op: 2, time: 2}, {op: 3, time: 3}],
                    [{op: 1, time: 5}, {op: 3, time: 1}, {op: 2, time: 2}, {op: 4, time: 2}],
                    [{op: 4, time: 2}, {op: 1, time: 3}, {op: 3, time: 2}, {op: 2, time: 3}]
                ]
              ],
              // 机器安排
              operationArrange: []
          }
      },
      methods: {
          // 设置初始值
          init() {
              let operationNum = 0
              for(let i in this.operationDetail[0][0]) {
                  operationNum ++
              }
              this.operationNum = operationNum

              this.factoryNum = this.operationDetail.length

              this.jobNum = this.operationDetail[0].length
          },
          // 初始化染色体  F-J,
          initChromosome() {

              this.chromosomes = []
              for(let k = 0; k < this.chromosomeNum; k++) {
                  let chromosome = []
                  let factoryChrome = []       // 排列：[1, 3, 2, 2, 3, 3]
                  for (let i = 0; i < this.operationNum; i++) {
                      for(let j = 0; j < this.jobNum; j++ ) {
                          chromosome[j + i * this.operationNum] = j + 1
                      }
                  }
                  for( let i = 0; i < this.jobNum; i++ ) {
                      // 1, 2, 3
                      factoryChrome.push(Math.ceil(this.factoryNum * Math.random()))
                  }
                  this.randomChromosome(chromosome)

                  this.chromosomes.push({
                    "jobChrome": chromosome,
                    "factoryChrome": factoryChrome
                  })
              }
          },
          // 产生下一代染色体
          ProduceNextGeneration() {
              let curChromosome = this.sortChromosome(this.chromosomes)
              let newChromosome = []
              for(let i = 0; i < curChromosome.length - 1; i ++) {
                  // 处理jobChrome，交叉
                  let {chromosome1, chromosome2} = this.pointCross(curChromosome[i].jobChrome, curChromosome[i + 1].jobChrome)
                  // 变异
                  if (Math.random() < this.variationRate) {
                      chromosome1 = (Math.random() > 0.5 ? this.ReverseOrderVariation(chromosome1) : this.exchangeVariation(chromosome1))
                      chromosome2 = (Math.random() > 0.5 ? this.ReverseOrderVariation(chromosome2) : this.exchangeVariation(chromosome2))
                  }
                  // 处理factoryChrome
                  let { factoryChromosome1, factoryChromosome2 } = this.pointCrossFactoryChrome(curChromosome[i].factoryChrome, curChromosome[i + 1].factoryChrome)

                  newChromosome.push({jobChrome: chromosome1, factoryChrome: factoryChromosome1})
                  newChromosome.push({jobChrome: chromosome2, factoryChrome: factoryChromosome2})
                  newChromosome.push(curChromosome[i])
                  newChromosome.push(curChromosome[i + 1])
              }
              // 选取父代和子代中最好的
              this.chromosomes = this.sortChromosome(newChromosome).slice(0, this.chromosomes.length)
              this.getBestChromosome()
          },
          // 按时间大小进行排序
          sortChromosome(chromosomes) {
              chromosomes.sort((a, b) => {
                  return this.computedTime(a) - this.computedTime(b)
              })
              return chromosomes
          },
          // 随机染色体
          randomChromosome(chromosome) {
              chromosome.sort(() => {
                  return 0.5 - Math.random()
              })
              return chromosome
          },
          // 取得当代最好的染色体
          getBestChromosome() {
              // 遍历当前染色体代
              let minTime, index
              this.chromosomes.forEach((d, i) => {
                  let time = this.computedTime(d)
                  if(!minTime || time < minTime) {
                      minTime = time
                      index = i
                  }
              })
              // 比较当前最好的 染色体，和这一代最好的染色体对比
              if (this.bestChromosome.length > 0) {
                  let time = this.computedTime(this.bestChromosome)
                  if (minTime < time) {
                      this.bestChromosome = this.chromosomes[index]
                  }
              } else {
                  this.bestChromosome = this.chromosomes[index]
              }
          },
          // 获得机器安排
          getDistribution(chromosome) {
              let operationArrange = []
              // 该Job的第几个操作
              let jobNum = new Array(this.jobNum).fill(-1)
              // 下一个job要开始的最早时间
              let jobStartTime = new Array(this.jobNum).fill(0)
              chromosome.jobChrome.forEach((i) => {   // i为工作编号
                  // 第几个操作
                  jobNum[i-1]++
                  // 第几个工厂
                  let factory = chromosome.factoryChrome[i - 1]
                  let op = this.operationDetail[factory - 1][i - 1][jobNum[i-1]].op - 1
                  let time = this.operationDetail[factory - 1][i - 1][jobNum[i-1]].time
                  if(operationArrange['fac' + factory + 'op' + op]) {
                      if(operationArrange['fac' + factory + 'op' + op].length < jobStartTime[i-1]) {
                          operationArrange['fac' + factory + 'op' + op] += '0'.repeat(jobStartTime[i-1] - operationArrange['fac' + factory + 'op' + op].length)
                      }
                  }
                  else {
                      if(0 < jobStartTime[i-1]) {
                          operationArrange['fac' + factory + 'op' + op] = '0'.repeat(jobStartTime[i-1])
                      }
                  }

                  operationArrange['fac' + factory + 'op' + op] ? operationArrange['fac' + factory + 'op' + op] += (i + '').repeat(time) : operationArrange['fac' + factory + 'op' + op] = (i + '').repeat(time)
                  jobStartTime[i-1] = operationArrange['fac' + factory + 'op' + op].length
              })
              return operationArrange
          },
          // 计算总时间
          computedTime(chromosome) {
              let operationArrange = this.getDistribution(chromosome)
              let Time = []
              for(let d in operationArrange) {
                  Time.push(operationArrange[d].length)
              }
              return Math.max.apply(null, Time)
          },
          // 画甘特图
          drawChart(chromosome) {
              this.operationArrange = this.getDistribution(chromosome)
          },
          pointCrossFactoryChrome(chromosome1, chromosome2) {
              let { station2 } = this.randomTwoStation()
              let c1 = chromosome1.slice(0, station2),
                  c2 = chromosome1.slice(station2, chromosome1.length),
                  c3 = chromosome2.slice(0, station2),
                  c4 = chromosome2.slice(station2, chromosome2.length)
              return {
                  factoryChromosome1: c1.concat(c4),
                  factoryChromosome2: c3.concat(c2)
              }
          },
          // 单点交叉
          pointCross(chromosome1, chromosome2) {
              let { station2 } = this.randomTwoStation()
              let c1 = chromosome1.slice(0, station2),
                  c2 = chromosome1.slice(station2, chromosome1.length),
                  c3 = chromosome2.slice(0, station2),
                  c4 = chromosome2.slice(station2, chromosome2.length)
              // 处理使其合法
              let { legalChromosome1, legalChromosome2 } = this.makeLegal(c1.concat(c4), c3.concat(c2))
              return {
                  chromosome1: legalChromosome1,
                  chromosome2: legalChromosome2
              }
          },
          // 处理使其合法
          makeLegal(c1, c2) {
              let copyC1 = c1.concat(),
                  copyC2 = c2.concat();
              // 找到不重复的部分
              for(let i = 0; i < c1.length; i++) {
                  let d = c1[i]
                  let index = c2.findIndex((value, ind) => {
                      return value === d
                  }) + 1
                  if (index) {
                      c1.splice(i, 1)
                      c2.splice(index - 1, 1)
                      i--
                  }
              }

              // 处理不重复的部分
              for(let i = 0, len = c1.length; i < len; i+=2) {
                  let d1 = c1[0], d2 = c2[0]
                  let index = this.getIndex(copyC1, d1)
                  copyC1[index] = d2
                  index = this.getIndex(copyC2, d2)
                  copyC2[index] = d1

                  c1.splice(0, 1)
                  c2.splice(0, 1)
                  c1.splice(this.getIndex(c1, d1), 1)
                  c2.splice(this.getIndex(c2, d2), 1)
              }

              return {
                  legalChromosome1: copyC1,
                  legalChromosome2: copyC2
              }
          },
          // 获取数组中 d 的第一个位置
          getIndex(arr, d) {
              return arr.findIndex((value) => {
                  return d === value
              })
          },
          // 互换变异
          exchangeVariation(chromosome) {
              let {station1, station2} = this.randomTwoStation()
              // 交换两个位置的初始基因
              let gene = chromosome[station1]
              chromosome[station1] = chromosome[station2]
              chromosome[station2] = gene
              return chromosome
          },
          // 逆序变异
          ReverseOrderVariation(chromosome) {
              let {station1, station2} = this.randomTwoStation()
              // 逆序重新排列这两个位置的基因
              let chromosome1 = chromosome.slice(0, station1),
                  chromosome2 = chromosome.slice(station1, station2),
                  chromosome3 = chromosome.slice(station2, chromosome.length)
              chromosome2.reverse()
              return chromosome1.concat(chromosome2, chromosome3)
          },
          // 随机两个位置
          randomTwoStation() {
              // 随机确定两个位置
              let station1, station2, len = (this.operationNum - 1) * this.jobNum
              do {
                  station1 = parseInt(len * Math.random())
                  station2 = parseInt(len * Math.random())
              } while(station1 === station2)
              if (station1 > station2) {
                  let st = station1
                  station1 = station2
                  station2 = st
              }
              return { station1, station2 }
          }
      },
      created() {
          this.init()
          // 初始化第一代染色体
          this.initChromosome()
          // 获取最好的染色体
          this.getBestChromosome()

          // 产生下一代染色体
          let bestTime, bestTimeNum = 0
          for (let i = 0; i < this.maxCycleNum; i++) {
              this.ProduceNextGeneration()
              let time = this.computedTime(this.bestChromosome)
              if (bestTime === time) {
                  bestTimeNum++
              } else {
                  bestTime = time
                  bestTimeNum = 0
              }
              console.debug(time)
          }
          console.debug(bestTime)
          this.operationArrange = this.getDistribution(this.bestChromosome)
      },
      mounted() {
//        var bestChromosome = [2, 1, 3, 2, 1, 4, 1, 4, 5, 3, 4, 3, 2, 3, 1, 5, 2, 4, 6, 3, 6, 6, 2, 5, 6, 3, 1, 4, 2, 1, 5, 5, 4, 6, 5, 6]
//        this.drawChart(bestChromosome)
      }
  }
</script>

<style lang="scss">
  #app {
    font-family: Consolas, Monaco, monospace;
  }
  .color {
    display: inline-block;
    width: 10px;
    height: 20px;
    background-color: #fff;
  }
  .color0 {
    background-color: #000;
  }
  .color1 {
    background-color: red;
  }
  .color2 {
    background-color: yellow;
  }
  .color3 {
    background-color: darkgray;
  }
  .color4 {
    background-color: coral;
  }
  .color5 {
    background-color: palevioletred;
  }
  .color5 {
    background-color: palegreen;
  }
  .color6 {
    background-color: orangered;
  }

</style>
