<template>
  <div class="hello">
    <div>
      <button class="button" @click="editMode=!editMode">
        <template v-if="!editMode">
          Edit mode
        </template>
        <template v-if="editMode">
          View mode
        </template>
      </button>
      <button class="button button2" @click="() => {styles = newStyle}">Change style</button>
    </div>
    <div class="settings">
      <h5>Hint</h5>
      <div class="settings__text-color">
        <label>Text color</label>
        <input type="color" v-model="text_color" @change="changeColor">
      </div>
      <div class="settings__bg-color">
        <label>BG color</label>
        <input type="color" v-model="bg_color">
      </div>
      <div class="settings__bg-color">
        <label>Dots color</label>
        <input type="color" v-model="dots_bg">
      </div>
      <h5>Input</h5>
      <div class="settings__bg-color">
        <label>Input bg color</label>
        <input type="color" v-model="inputBg">
      </div>
      <div class="settings__bg-color">
        <label>Input text color</label>
        <input type="color" v-model="inputTextColor">
      </div>
      <div>
        <label>{{ this.currentIndex }}</label>
        <textarea v-model="hint_text"></textarea>
        <button @click="changeText">Ok</button>
      </div>
    </div>
    <div class="container">
      <HotSpots :image="img" 
                :edit="editMode"
                :showOnClick="false"
                :onHover="true"
                :onImageClick="true"
                :defaultInput="true"
                :customHint="customHint"
                @change="changingDots"
                @add="addedDot"
                @delete="deleteDot"
                @changeDot="changeDot"
                @clickDot="clickDot"
                :dotsColor="dots_bg"
                :hintBg="bg_color"
                :hintTextColor="text_color"
                :hintStyle="hintStyle"
                :inputStyle="inputStyles"
                :inputBg="inputBg"
                :inputTextColor="inputTextColor"
                :data="dotsArr"

      />
      <!-- :hintStyle="hintStyle" -->
      <!-- :inputStyle="inputStyles" -->
      <!-- :customHint="customHint" -->

    </div>
  </div>
</template>

<script>
import HotSpots from './HotSpots.vue'
export default {
  name: 'HelloWorld',
  components: {HotSpots},
  data() {
    return {
      image: 'https://cutewallpaper.org/21/nature-mountains-wallpaper/Wallpaper-Nature-Mountains-Lake-Switzerland-Forest-Alps-.jpg',
      img: 'https://n1s1.hsmedia.ru/44/fc/48/44fc4800170256d5e4b8b0a2c7033457/728x485_1_ba47ab3690e4d738ba4818741f9c9a25@2560x1707_0xac120003_3943829481668767955.jpeg',
      editMode: false,
      hintStyle: {
        color: '#fff',
        background: '#333',
        padding: '20px',
        border: '1px solid greenyellow',
      },
      newStyle: {
        padding: '20px',
        color: '#000',
        textShadow: '0 0 10px #fff',
        background: '#f42d82',
        border: '2px solid #000' 
      },
      styles: '',
      text_color: '#fff',
      bg_color: '#333',
      dots_bg: '',
      input_bg: '',
      input_color: '',
      hint_text: '',
      inputTextColor:'',
      inputBg:'',
      inputStyles: {
        // background:'#333',
        // color:'#fff',
        // border:'1px solid greenyellow',
        // padding: '10px 15px'
      },
      activeDot: {},
      currentIndex: '',
      dotsArr: [],
      dotsArrBuf: [
        {text: 'Jisso', show_input: true, show_hint: false, posX: 15.5, posY: 10, color: 'white', type: 'dot'},
        {text: 'Jennie', show_input: true, show_hint: false, posX: 30.5, posY: 39.2, color: 'white', type: 'dot'},
        {text: 'Rose', show_input: true, show_hint: false, posX: 57.4999, posY: 14.2, color: 'white', type: 'dot'},
        {text: 'Lisa', show_input: true, show_hint: false, posX: 74.125, posY: 27.8, color: 'white', type: 'dot'}
      ],
      customHint: ''
        // '<div class="custom-hint"><div class="hint__text-content"></div> <a class="custom-hint-link" href="#">Order</a></div>'
    }
  },
  methods: {
    /*eslint-disable*/
    changeText() {

      // this.activeDot.text = this.hint_text
      // console.log('active dot')
      // console.log(this.activeDot)
      // console.log('array')

      // console.log(this.dotsArrBuf)
      // this.dotsArrBuf[this.currentIndex].text = this.hint_text
      
      // this.hint_text= ''
      // this.activeDot = {}
      this.dotsArr = this.dotsArrBuf
    },
    changingDots(e) {
      // console.log('new dots array')
      // console.log(e)
      // this.dotsArrBuf = e
    },
    addedDot(e) {
      // console.log('added dot')
      // console.log(e)
      // this.currentIndex = e.index 
      // this.activeDot = e.obj
    },
    deleteDot(e) {
      // console.log('delete dot')
      // console.log(e)
    },
    changeDot(e) {
      // console.log('change dot')
      // console.log(e)
    },
    clickDot(e) {
      console.log('click dot')
      console.log(e)
      this.currentIndex = e.index 
      this.activeDot = e.obj
      this.hint_text = e.obj.text
    },
    changeColor() {
      // console.log(this.text_color)
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
.button {
  border: 0;
  border-radius: 5px;
  padding: 5px 10px;
  font-size: 20px;
  color: #fff;
  background: #05c57c; 
  cursor: pointer;
  &:hover {
    background: darken(#05c57c, 15);
  }
}
.container {
  width: 50%;
  margin: 0 auto;
}
.open-btn {
  cursor: pointer;
  font-weight: 600;
}
.settings {
  display: flex;
  justify-content: center;
  align-items: center;
  div {
    margin-inline: 20px;
  }
  label {
    margin-right: 10px;
  }
}
.button2 {
  background: #35374e;
  margin-left: 30px;
}
</style>
