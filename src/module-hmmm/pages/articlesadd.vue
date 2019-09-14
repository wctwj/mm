<template>
  <div>
    <quill-editor class="quill"
      v-model="content"
      ref="myQuillEditor"
      :options="editorOption"
      @blur="onEditorBlur($event)"
      @focus="onEditorFocus($event)"
      @ready="onEditorReady($event)"
      @change="onEditorChange"
      style='height:400px;width:800px'
    ></quill-editor>
  </div>
</template>

<script>
import 'quill/dist/quill.core.css'

import 'quill/dist/quill.snow.css'

import 'quill/dist/quill.bubble.css'

import { quillEditor } from 'vue-quill-editor'

export default {
  components: {
    quillEditor
  },

  data() {
    return {
      content: '',

      editorOption: {}
    }
  },

  methods: {
    onEditorBlur(quill) {
      // console.log('editor blur!', quill)
    },

    onEditorFocus(quill) {
      // console.log('editor focus!', quill)
    },

    onEditorReady(quill) {
      // console.log('editor ready!', quill)
    },

    onEditorChange({ quill, html, text }) {
      // console.log('editor change!', quill, html, text)

      this.content = html
    }
  },

  computed: {
    editor() {
      return this.$refs.myQuillEditor.quill
    }
  }
}
</script>

<style scoped>
 .quill{
     background-color: #fff
 }
</style>
