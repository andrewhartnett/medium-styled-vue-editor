<template>
  <div id="app">
    <editor :config="config"></editor>
  </div>
</template>

<script>
import Editor from './components/Editor.vue'

export default {
  name: 'App',
  data() {
    return {
      config: {
        save: this.save
      }
    }
  },
  methods: {
    save(payload) {
      console.log(payload)
      this.copyToClipBoard(payload.html)
    },
    copyToClipBoard(html) {
      const el = document.createElement('textarea');
      el.style.visibility = 'hidden'
      el.value = html;
      document.body.appendChild(el);
      el.select();
      document.execCommand('copy');
      document.body.removeChild(el);

      alert('Html copied to clipboard')
    }
  },
  components: {
    Editor
  },
}
</script>
