<template>
  <div class="hotspots" :class="'hotspots_' + id">
    <div class="img__container" >
      <img class="img-fluid hotspots__image" style=""
        :src="image"
        alt="" @click="addHotSpots"
        :class="{editing: editMode}"
      >
      <div class="hotspot__container text-center" v-for="(dot, i) in dots"
        :class="'dot-' + i"
        :key="dot.i"
        :style="'top:' + dot.posY +'%;left:' + dot.posX + '%;'"
        @click="showHint(dot, i)"
        @mouseenter="showHintOnEnter(dot, i)"
        @mouseleave="showHintOnLeave(dot)"> 
        <div class="text-center hotspot hotspot_dot" 
            v-if="dot.type === 'dot'" :style="'background:' + dotsColor + ';'"
            @click="clickDot(dot, i)"
        ></div>
        <!-- <div v-if="dot.type === 'square'"
          class="text-center hotspot hotspot_square"
          :style="'background:' + dotsColor + ';'"
          @click="clickDot(dot, i)"
        ></div>  -->
        <div v-if="editMode" class="hotspot__settings">
          <span class="settings__close" @click="deleteHotSpot(i)">X</span> 
          <span v-if="defaultInput" class="settings__text" @click="dot.show_input = !dot.show_input">T</span>
          <div v-if="defaultInput && dot.show_input" class="hotspot__text">
            <input class="hotspot__input"
              v-model="dot.text"
              @change="changeDot(dot, i)"
              :style="inputStylesObj"
            >
          </div>
        </div>
        <div v-show="!editMode && dot.show_hint && dot.text" 
          class="hotspot__hint"
          :class="'hint-' + i"
          :style="hintStyleObj"> 
          <template v-if="!customHint"> {{ dot.text }} </template>     
          <template v-else>
            <div v-html="customHint">

            </div>
          </template>     
        </div>
      </div>
    </div>
  </div>
</template>
<script>
export default {
  name: 'HotSpot',
  props: {
    edit: {
      type: Boolean,
      default: false
    },
    showOnClick: {
      type: Boolean,
      default: false
    },
    onHover: {
      type: Boolean,
      default: true
    },
    onImageClick: {
      type: Boolean,
      default: false
    },
    defaultInput: {
      type: Boolean,
      default: true
    },
    image: String,
    data: Array,
    dotsColor: String,
    hintBg: String,
    hintTextColor: String,
    hintStyle: Object,
    inputBg: String,
    inputTextColor: String,
    inputStyle: Object,
    customHint: String
  },
  data() {
    return {
      imageSrc: this.image,
      dots: [],
      hintStyleObj: {
        background: this.hintBg,
        color: this.hintTextColor
      },
      inputStylesObj:{
        background: this.inputBg,
        color: this.inputTextColor
      },
      id: null,
      container: null
    }
  },
  computed: {
    editMode: {
      get() {
        return this.edit
      },
      set(value) {
        this.editMode = value
      }
    },
  },
  watch: {
    data: {
      handler: function() {
        if (this.data) {
          this.dots = JSON.parse(JSON.stringify(this.data))
        }
      },
      deep: true
    },
    hintBg: function() {
      if (this.hintBg) {
        this.hintStyleObj.background = this.hintBg
      }
    },
    hintTextColor: function() {
      if (this.hintTextColor) {
        this.hintStyleObj.color = this.hintTextColor
      }
    },
    hintStyle: function() {
      if (this.hintStyle && Object.keys(this.hintStyle).length) {
        this.hintStyleObj = JSON.parse(JSON.stringify(this.hintStyle))
      }
    },
    inputStyle: function() {
      if (this.inputStyle && Object.keys(this.inputStyle).length) {
        this.inputStylesObj = JSON.parse(JSON.stringify(this.inputStyle))
      }
    },
    inputBg: function() {
      if (this.inputBg) {
        this.inputStylesObj.background = this.inputBg
      }
    },
    inputTextColor: function() {
      if (this.inputTextColor) {
        this.inputStylesObj.color = this.inputTextColor
      }
    },
  },
  created() {
    this.id = Math.floor(Math.random() * 100) + 1;
    if (this.data) {
      this.dots = JSON.parse(JSON.stringify(this.data))
    }
    if (this.hintStyle && Object.keys(this.hintStyle).length) {
        this.hintStyleObj = JSON.parse(JSON.stringify(this.hintStyle))
      }
    if (this.inputStyle && Object.keys(this.inputStyle).length) {
      this.inputStylesObj = JSON.parse(JSON.stringify(this.inputStyle))
    }  
  },
  updated() {
    if (this.editMode) {
      if (this.dots.length) {
        this.dots.forEach(el => {
          el.show_hint = false
        })
      }
    }
  },
  mounted() {
    this.container = document.querySelector('.hotspots_' + this.id)
  },
  methods: {
    checkHintPosition(dot, i) {
      if (!dot.show_hint) return
      const image = this.container.querySelector('.hotspots__image')

      if (!image) return
      let imgPos = image.getBoundingClientRect()

      let dotObj = this.container.querySelector(`.dot-${i}`)

      if (!dotObj) return 
      let dotPos = dotObj.getBoundingClientRect()

      let side = dot.posX <= 50 ? 'left' : 'right'
      let hint = this.container.querySelector(`.hint-${i}`)

      if (!hint) return
      setTimeout(() => {
        let move = null
        if (side === 'left') {
          let distance = dotPos[side] - imgPos[side]            
          if (distance < hint.offsetWidth / 2) {
            move = parseInt(hint.offsetWidth) / 2 - parseInt(distance) + 25
          }
        } else {
          let distance = imgPos[side] - dotPos[side]
          if (distance < hint.offsetWidth / 2) {
            move = -(parseInt(hint.offsetWidth) / 2 - parseInt(distance))
          }
        }
        hint.style.left = move + 'px'
        if (dot.posY >= 50) {
          let distanceY= imgPos.bottom - dotPos.bottom 
          if (distanceY < hint.offsetHeight + 26) {
            let moveY = hint.offsetHeight + 26
            hint.style.top = -moveY + 'px'
          }       
        }
      }, 1)
    },

    addHotSpots(e) {
      if (!this.editMode && !this.onImageClick) {
        return 
      }
      else if (!this.editMode && this.onImageClick && this.dots.length) {
        this.showHintOnImageClick()
      } else if (this.editMode){
        const image = this.container.querySelector('.hotspots__image')
        if (!image) return
        let dot = {
          text: '',
          show_input: false,
          show_hint: false,
          posX: '',
          posY: '',
          color: 'white',
          type: 'dot',
          product: ''
        }

        let top_px = Math.round((e.clientY - image.getBoundingClientRect().top - 12) );
        let left_px = Math.round((e.clientX - image.getBoundingClientRect().left - 12) );
        dot.posX = left_px / image.offsetWidth * 100;
        dot.posY = top_px / image.offsetHeight * 100;
        this.dots.push(dot)
        let obj = {
          index: this.dots.length - 1,
          obj:  Object.assign({}, dot)
        }
        this.$emit('changeArr', this.copyDotsArr())
        this.$emit('add', obj)
    } 
    },
    deleteHotSpot(index) {
      let obj = {
          index: index,
          obj: Object.assign({}, this.dots[index])
        }
      this.$emit('delete', obj)
      this.dots.splice(index, 1)
      this.$emit('changeArr', this.dots)
    },
    showHintOnImageClick() {
      if (this.dots.find(f => !f.show_hint)) {
        this.dots.forEach ((dot, index) => {
          dot.show_hint = true
          this.addTextToHint(dot, index)
          this.checkHintPosition(dot, index)
        })
      } else {
        this.dots.forEach ((dot) => {
          dot.show_hint = false
        })
      }
    },
    showHint(dot, i) {
      if (!this.editMode) {
        if (this.showOnClick) {
          dot.show_hint = !dot.show_hint
          this.addTextToHint(dot, i)
          this.checkHintPosition(dot, i)
        }
      }
    },
    showHintOnEnter(dot, i) {
      if (this.onHover) {
        dot.show_hint = true
        this.addTextToHint(dot, i)
        this.checkHintPosition(dot, i)
      }
      else return  
    },
    showHintOnLeave(dot) {
      if (this.onHover)
        dot.show_hint = false
      else return  
    },
    changeDot(dot, index) {
      let obj = {
          index: index,
          obj: Object.assign({}, dot)
        }
      this.$emit('changeDot', obj)
      this.$emit('changeArr', this.dots)

    },
    clickDot(dot, index) {
      if (!this.editMode) return
      let obj = {
          index: index,
          obj: Object.assign({}, dot)
        }
      this.$emit('clickDot', obj)
    },
    copyDotsArr() {
      let arr = []
      this.dots.forEach(e => {
        arr.push(Object.assign({}, e))
      })
      return arr
    },
    addTextToHint(dot, i) {
      if (this.customHint) {
        // let hint = this.$refs['hint'][i]
        let hint = this.container.querySelector(`.hint-${i}`)

        if (hint) {
          let textContainer = hint.querySelector('.hint__text-content')
          if (textContainer) {
            textContainer.textContent = dot.text
          }
        }
      }
    }
  }
}
</script>
<style lang="css" >
.hotspots {
  display: flex;
  justify-content: center;
}
.img__container {
  position: relative;
  width: 100%;
  height: 100%;
}
.img__container img {
  width: 100%;
  height: 100%;
  object-fit: cover;
  cursor: pointer;
}
.editing {
  filter: brightness(0.5);
}
.hotspot__container {
  width: 20px;
  height: 20px;
  position: absolute;
}
.hotspot {
  text-align: center;
  opacity: 0.9;
  background: #fff;
}
.hotspot__container::after {
  content: '';
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  transform: scale(2.3);
}  
.hotspot__settings{
  position: absolute;
  top: 50%;
  left: 50%;
  width: 100%;
  height: 100%;
  border: 1px solid #5395ff;
  padding: 15px;
  transform: translate(-50%, -50%);
  z-index: 8;
} 
.hotspot__settings span {
  cursor: pointer;
  background-color: #5395ff;
  color: #fff;
  font-size: 14px;
  position: absolute;
  padding: 2px 5px;
  border-radius: 50%;
  right: -8px;
}
.hotspot__settings .settings__close{
  top: -6px;
}
.hotspot__settings .settings__text {
  font-weight: bold;
  bottom:-6px;
}
.hotspot__text {
  position: absolute;
  bottom: -70%;
  left: 50%;
  width: max-content;
  transform: translateX(-50%);
} 
.hotspot__input {
  background: #cccccc7d;
  width: 200px;
  border: 0;
  outline: none;
  padding: 5px;
  border-radius: 5px;
  color: #fff;
}

.hotspot_dot {
  border-radius: 50%;
}
.hotspot {
  font-size: 16px;
  line-height: 30px;
  width: 100%;
  height: 100%;
  opacity: 0.85;
  background: #fff;
  position: relative;
  z-index: 10;
  
}
.hotspot::before {
  content: '';
    position: absolute;
    width: 100%;
    height: 100%;
    left: 0;
    right: 0;
    background: inherit;
    opacity: 0.5;
    transform: scale(1.5);
    border-radius: inherit;
    animation: animate 1.5s linear infinite alternate; 
}

.hotspot__hint {
  border-radius: 5px;
  color: #333;
  background: #cccccc7d;
  padding: 10px;
  position: absolute;
  top: 26px;
  left: 50%;
  transform: translateX(-50%);
  text-align: center;
}
@keyframes animate {
  0% {
    transform: scale(1.1);  
  }
  100% {
    transform: scale(1.4);
  }
}
    
.hotspot_dot {
  border-radius: 50%;
}
.open-btn {
  cursor: pointer;
  font-weight: 600;
}
</style>