<template>
  <Codemirror
    v-if="data.autoRefresh"
    v-model:value="code"
    :options="data.cmOptions"
    placeholder=""
    :height="data.height"
    style="font-size: 15px; margin-top: 2px; font-family: Tahoma, Verdana, Arial, sans-serif"
    @change="change"
  />
</template>

<script setup lang="jsx">
import Codemirror from 'codemirror-editor-vue3';

// placeholder
import 'codemirror/addon/display/placeholder.js';
// language
import 'codemirror/mode/javascript/javascript.js';
import 'codemirror/mode/yaml/yaml.js';
import 'codemirror/mode/yaml-frontmatter/yaml-frontmatter.js';
// placeholder
import 'codemirror/addon/display/placeholder.js';
// theme
import 'codemirror/theme/dracula.css';

import 'codemirror/mode/yaml/yaml.js';
import 'codemirror/addon/fold/foldcode.js';
import 'codemirror/addon/fold/foldgutter.css';
import 'codemirror/addon/fold/indent-fold.js';
import 'codemirror/addon/fold/brace-fold.js';
import { ref, reactive, onMounted, watch } from 'vue';
const code = ref(null);
const data = reactive({
  cmOptions: {
    mode: 'yaml', // Language mode
    theme: 'dracula', // Theme
    readOnly: false,
    spellcheck: true,
    foldGutter: true,
    smartIndent: true, //自动根据上下文缩进
    gutters: ['CodeMirror-linenumbers', 'CodeMirror-foldgutter'], // 添加折叠图标
  },
  height: 600,
  autoRefresh: false,
  // textWidth: 80,
  textWidthStep: 2,
});
const props = defineProps({
  yaml: {
    type: String,
    default: '张三',
  },
  readOnly: {
    type: Boolean,
    default: false,
  },
  height: {
    type: Number,
    default: 600,
  },
});
onMounted(() => {
  code.value = props.yaml.valueOf();
  data.cmOptions.readOnly = props.readOnly.valueOf();
  data.height = props.height.valueOf();
  data.autoRefresh = true;
});

watch(() => {
  code.value = props.yaml.valueOf();
  data.cmOptions.readOnly = props.readOnly.valueOf();
  data.height = props.height.valueOf();
});

const change = () => {
  // this.props.yaml = code.value;
};
defineExpose({ code });
</script>
