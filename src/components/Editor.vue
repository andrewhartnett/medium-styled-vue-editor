<template>
  <div>
    <nav>
      <div class="lastSaved">{{saved}}</div>
      <button class="saveBtn" @click.prevent="save">Save</button>
    </nav>
    <div id="medium-editor">
      <dynamic-input v-for="(block, i) in blocks" :key="i" :data="block" :index="i" @newLine="newLine" @deleteLine="deleteLine" @moveIndex="focusOnIndex"></dynamic-input>
    </div>
  </div>
</template>

<script>

import DynamicInput from './DynamicInput'

export default {
  components: {DynamicInput},
  data() {
    return {
      saved: "",
      blocks: [] // Each block is a line of content
    }
  },
  props: ['html', 'config'], // html will be the source of truth. config will handle onSave method
  methods: {
    newLine(v){
      const index = v + 1
      if(this.blocks[index] !== undefined){
        this.focusOnIndex(index)
        return
      }
      const blocks = [...this.blocks]
      blocks.splice(index, 0, {'type': 'p', 'content': ''})
      this.blocks = blocks
      this.focusOnIndex(index)
    },
    deleteLine(v){
      const blocks = [...this.blocks]
      blocks.splice(v, 1)
      this.blocks = blocks
      this.focusOnIndex(v - 1)
    },
    focusOnIndex(index){
      const element = document.getElementById('di-'+index)
      if(element){
        console.log('there is an element')
        element.focus()
      }else{
        setTimeout(()  => {
          this.focusOnIndex(index)
        }, 100)
        console.log('there is no element')
      }
    },
    save() {
      let html = "";

      this.blocks.forEach(b => {
        if(!b.content) return
        html += `<${b.type}>${b.content}</${b.type}>\n`
      })

      this.saved = "Saved " + (new Date()).toLocaleTimeString()

      try{
        this.config.save({html: html, blocks: this.blocks})
      }catch(e) {
        console.log('Save callback missing from config')
        console.log({html: html, blocks: this.blocks})
      }
      
    }
  },
  mounted() {
    if(this.html){
      // Loop through html and break into blocks
    }else{
      // Default state for the blocks if empty
      this.blocks = [
        {'type': 'h1', 'content': '', 'placeholder': 'Title Goes Here'},
        {'type': 'p', 'content': '', 'placeholder': 'Start Writing'}
      ]
    }
  }
}
</script>

<style>

#medium-editor {
  margin: 20px auto;
  padding: 0;
  width: 60%;
}

textarea {
  border: none;
  background: none;
  outline: none;
  resize: none;
  width: 100%;
  overflow: hidden;
  padding:0;
  font-family: sans-serif;
}

nav {
  display: flex;
  justify-content: flex-end;
  align-items: center;
}

nav button {
  background: #e0e0e0;
  border: none;
  padding: 10px 20px;
  cursor: pointer;
}

nav button:hover {
  background: #ccc;
}

::selection {
  background: #ffb7b7; /* WebKit/Blink Browsers */
}
::-moz-selection {
  background: #ffb7b7; /* Gecko Browsers */
}

nav div.lastSaved {
  color:#aaa;
  margin-right: 10px;
}


</style>