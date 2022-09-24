<template>
  <div class="red-packet-rain">
    <div class="container">
      <div class="header">
        <span>剩余时间</span>
        <span class="time">{{ num }}s</span>
      </div>
      <svg id="svgboard" version="1.1" xmlns="http://www.w3.org/2000/svg">
        为了更好的体验，请更换谷歌浏览器使用本网站
      </svg>
    </div>

    <!-- 不能放在 svg 盒子内部，否则点击事件会被挡住，无效 -->
    <span v-if="!num" class="reset" @click="reset">重来</span>
  </div>
</template>

<script>
import redBagImg from '@/assets/red-bag.png'
import starImg from '@/assets/star.png'
import coinImg from '@/assets/coin.png'
import fireworkImg from '@/assets/firework.png'
import fireworkSmallImg from '@/assets/firework-small.png'
import ribbonRedImg from '@/assets/ribbon-red.png'
import ribbonBlueImg from '@/assets/ribbon-blue.png'
import ribbonGreenImg from '@/assets/ribbon-green.png'
import ribbonPurpleImg from '@/assets/ribbon-purple.png'

export default {
  name: 'RedPacketRain',
  props: {
    count: { // 倒计时时长
      type: Number,
      default: 10
    }
  },
  data() {
    return {
      timer: null,
      num: this.count
    }
  },

  mounted() {
    this.init()
  },

  methods: {
    init() {
      this.num = this.count
      this.insertImage()
      this.timer = setInterval(() => {
        if (this.num >= 1) {
          this.num--
        } else {
          this.close()
        }
      }, 1000)
    },

    createSVGElement(elementName) {
      let node = document.createElementNS(
        'http://www.w3.org/2000/svg',
        elementName
      )

      return node
    },

    setAnimation(board, lists) {
      let maxY = document.documentElement.clientHeight
      for (let item of lists) {
        let image = this.createSVGElement('image')
         if (item.imageLink === redBagImg) {
          // image.addEventListener('click', () => {
          //   this.$emit('success')
          //   this.close()
          // })
          image.onclick = () => {
            this.$emit('success')
            alert('点中了红包')
            this.close()
          }
        }

        image.setAttribute('href', item.imageLink)
        image.setAttribute('width', item.width)
        image.setAttribute('height', item.height)
        image.setAttribute(
          'style',
          'transform-box: fill-box; transform-origin: center; cursor: pointer;'
        )
        // image.setAttribute('transform', 'rotate(25)')
        let motion = this.createSVGElement('animateMotion')
        motion.setAttribute(
          'path',
          `M ${item.x} ${item.y} L ${item.x - 500} ${maxY + 500}`
        )
        motion.setAttribute('begin', '0s')
        motion.setAttribute('dur', `${item.dur}s`)
        motion.setAttribute('repeatCount', 'indefinite')

        image.appendChild(motion)

        board.appendChild(image)
      }
    },

    insertImage() {
      let board = document.querySelector('#svgboard')
      let redArguments = this.getX()

      let starArguments = this.getX([], {
        num: 6,
        minWidth: 28,
        maxWidth: 28,
        minHeight: 28,
        maxHeight: 28,
        imageLink: starImg,
        durBase: 6
      })
      let coinArguments = this.getX([], {
        num: 5,
        minWidth: 70,
        maxWidth: 70,
        minHeight: 70,
        maxHeight: 70,
        imageLink: coinImg,
        durBase: 4
      })
      let fireworkArguments = this.getX([], {
        num: 8,
        minWidth: 88,
        maxWidth: 88,
        minHeight: 152,
        maxHeight: 152,
        imageLink: fireworkImg,
        durBase: 2
      })
      let fireworkSmallArguments = this.getX([], {
        num: 8,
        minWidth: 86,
        maxWidth: 86,
        minHeight: 149,
        maxHeight: 149,
        imageLink: fireworkSmallImg,
        durBase: 1
      })
      let ribbonRedArguments = this.getX([], {
        num: 6,
        minWidth: 21,
        maxWidth: 21,
        minHeight: 22,
        maxHeight: 22,
        imageLink: ribbonRedImg,
        durBase: 6
      })
      let ribbonBlueArguments = this.getX([], {
        num: 4,
        minWidth: 19,
        maxWidth: 19,
        minHeight: 16,
        maxHeight: 16,
        imageLink: ribbonBlueImg,
        durBase: 8
      })
      let ribbonGreenArguments = this.getX([], {
        num: 6,
        minWidth: 15,
        maxWidth: 15,
        minHeight: 18,
        maxHeight: 18,
        imageLink: ribbonGreenImg,
        durBase: 6
      })
      let ribbonPurpleArguments = this.getX([], {
        num: 4,
        minWidth: 13,
        maxWidth: 13,
        minHeight: 16,
        maxHeight: 16,
        imageLink: ribbonPurpleImg,
        durBase: 4
      })

      this.setAnimation(board, redArguments)
      this.setAnimation(board, starArguments)
      this.setAnimation(board, coinArguments)
      this.setAnimation(board, fireworkArguments)
      this.setAnimation(board, fireworkSmallArguments)
      this.setAnimation(board, ribbonRedArguments)
      this.setAnimation(board, ribbonBlueArguments)
      this.setAnimation(board, ribbonGreenArguments)
      this.setAnimation(board, ribbonPurpleArguments)
    },

    getX(box = [], config = {}) {
      // box可传引用，也可以return。
      let maxX = document.documentElement.clientWidth

      config = {
        num: 24,
        minWidth: 131,
        maxWidth: 227,
        minHeight: 206,
        maxHeight: 301,
        durBase: 10,
        imageLink: redBagImg,
        ...config
      }
      let {
        num,
        minWidth,
        maxWidth,
        minHeight,
        maxHeight,
        durBase,
        imageLink
      } = config
      for (let i = 0; i < num; i++) {
        let height = Math.random() * (maxHeight - minHeight) + minHeight

        box.push({
          key: +new Date() + Math.random() + i,
          width: Math.random() * (maxWidth - minWidth) + minWidth,
          height: height,
          x: Math.random() * maxX + 1,
          y: `-${height}`,
          dur: Math.random() * durBase + 1,
          imageLink: imageLink
        })
      }

      return box
    },

    romoveNode() {
      // document.querySelector('#svgboard').innerHTML = ''
      let svg = document.querySelector('#svgboard')
      let list = svg.childNodes
      for (let i = list.length - 1; i >= 0; i--) { // 一定要倒序，正序是删不干净的，可自行尝试
        list[i].onclick = null
        svg.removeChild(list[i])
      }
    },

    reset() {
      console.log(444)
      this.timer && clearInterval(this.timer)
      this.timer = null
      this.num = 10

      this.$nextTick(() => {
        this.init()
      })
    },

    close() {
      this.romoveNode()
      this.$emit('input', false)
    }
  }
}
</script>

<style scoped>
  .red-packet-rain {
    height: 100vh;
    width: 100vw;
    background: url('../assets/bg.png') center/cover no-repeat;
  }

  .container {
    width: 1200px;
    margin: 0 auto;
  }

  .header {
    padding-top: 40px;
    color: #FFF;
    display: flex;
    align-items: center;
  }

  .header .time {
    display: flex;
    align-items: center;
    justify-content: center;
    width: 100px;
    height: 100px;
    margin-left: -10px;
    background: url('../assets/time-bg.png') center/100% 100% no-repeat;
    font-size: 24px;
    font-weight: bold;
  }

  .reset {
    position: fixed;
    right: 8px;
    bottom: 8px;
    display: inline-block;
    width: 48px;
    height: 48px;
    border-radius: 50%;
    background: #FFF;
    text-align: center;
    line-height: 48px;
    font-size: 12px;
    color: #666;
    font-weight: bold;
    cursor: pointer;
  }

  #svgboard {
    position: fixed;
    top: 0;
    left: 50%;
    margin-left: -600px;
    width: 1200px;
    height: 100%;
  }
</style>
