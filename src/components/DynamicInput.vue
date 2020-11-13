<template>
  <div class="di-container">
    <div v-show="selected" class="options">
      <button @click.prevent="changeType('h1')">h1</button>
      <button @click.prevent="changeType('h2')">h2</button>
      <button @click.prevent="changeType('p')">p</button>
    </div>
    <textarea 
      :id="`di-${index}`"
      :class="`${data.type}`"
      autocomplete="off"
      type="text" 
      v-model="data.content" 
      :placeholder="data.placeholder" 
      @keydown="keyevent"
      @mouseup="checkSelected"
    ></textarea>
  </div>
</template>

<script>

export default {
  data() {
    return {
      selected: false
    }
  },
  props: ['data', 'index'],
  methods: {
    changeType(type) {
      this.data.type = type;
      document.getElementById('di-'+this.index).style.height = '40px';
      this.selected = false
    },
    newLine() {
      this.$emit('newLine', this.index)
    },
    deleteLine() {
      if(this.data.content.length == 0){
        this.$emit('deleteLine', this.index)
      }
    },
    moveIndex(dir) {
      if(dir == 'up'){
        this.$emit('moveIndex', this.index - 1)
      }
      if(dir == 'down'){
        this.$emit('moveIndex', this.index + 1)
      }
    },
    checkSelected() {
      this.selected = false
      if(this.data.content.length == 0) return;
      const selection = window.getSelection().toString().trim()
      if(selection == this.data.content.trim()){
        this.selected = true
      }
    },
    keyevent(e) {
      if(e.code == 'Enter'){
        e.preventDefault();
        return this.newLine()
      }
      if(e.code == 'Backspace') {
        return this.deleteLine()
      }
      e.target.style.height = '1px';
      e.target.style.height = (e.target.scrollHeight)+"px";
      
    }
  }
}
</script>

<style>

textarea.h1 {
  font-size: 32px;
  height: 34px;
}

textarea.h2 {
  font-size: 24px;
  height: 26px;
}

textarea.p {
  font-size: 18px;
  height: 20px;
}

div.di-container {
  position: relative;
}

div.options {
  display: flex;
  position: absolute;
  top: -18px;
  background: #565656;
}

div.options > button {
  background: none;
  border: 1px solid #444;
  color: #fff;
  cursor: pointer;
}

div.options > button:hover {
  background: #898989;
}
</style>