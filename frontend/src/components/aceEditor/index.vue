<template>
  <div style="width: 100%;height: 150px"></div>
</template>

<script>
  import ace from 'brace';
  import "brace/mode/mysql";
  import "brace/ext/emmet";
  import "brace/ext/language_tools";
  export default {
    name: 'editor',
    props: {
      value: {
        type: String,
        required: true
      }
    },
    data () {
      return {
        editor: null,
        contentBackup: ''
      }
    },
    watch: {
      value (val) {
        if (this.contentBackup !== val) {
          this.editor.setValue(val, 1)
        }
      },
      theme: function (newTheme) {
        this.editor.setTheme('ace/theme/' + newTheme)
      },
      lang: function (newLang) {
        this.editor.getSession().setMode('ace/mode/' + newLang)
      }
    },
    mounted () {
      let vm = this
      let editor = vm.editor = ace.edit(this.$el)
      this.$emit('init', editor)
      let staticWordCompleter = {
        getCompletions: function (editor, session, pos, prefix, callback) {
          vm.$emit('setCompletions', editor, session, pos, prefix, callback)
        }
      }
      editor.completers = [staticWordCompleter]
      editor.setOptions({
        enableBasicAutocompletion: true,
        enableLiveAutocompletion: true
      })
      editor.$blockScrolling = Infinity
      editor.setFontSize(14)
      editor.setOption('enableEmmet', true)
      editor.getSession().setMode('ace/mode/mysql')
      editor.setTheme('ace/theme/xcode')
      editor.setValue(this.value, 1)
      editor.on('change', function () {
        let content = editor.getValue()
        vm.$emit('input', content)
        vm.contentBackup = content
      })
    }
  }
</script>

<style scoped>
</style>
