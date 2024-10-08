<template>
  <el-card shadow="never" style="border-radius: 1px" :body-style="{ padding: '5px' }">
    <el-row :gutter="10">
      <el-col :span="24" style="margin-top: 5px">
        <!-- xterm虚拟终端 -->
        <div id="xterm"></div>
      </el-col>
    </el-row>
  </el-card>
</template>

<script setup>
import { onMounted, onUnmounted, reactive, getCurrentInstance } from 'vue';
//引入xterm终端依赖
import { Terminal } from 'xterm';
import { FitAddon } from 'xterm-addon-fit';
import 'xterm/css/xterm.css';
import 'xterm/lib/xterm.js';

const { proxy } = getCurrentInstance();

//终端适应父元素大小
const fitAddon = new FitAddon();

const data = reactive({
  //terminal
  term: null,
  socket: null,
  rows: 20,
  cols: 80,
});

const props = defineProps({
  webshelloptions: {
    type: Object,
    default() {
      return {};
    },
  },
});

onMounted(() => {
  data.rows = parseInt(document.body.clientHeight / 18);
  data.cols = parseInt(document.body.clientWidth / 9);
  // 优化体验
  initTerm();
  initSocket();
});

onUnmounted(() => {
  closeSocket();
});

const initTerm = () => {
  //初始化xterm实例
  data.term = new Terminal({
    rendererType: 'canvas', //渲染类型
    rows: data.rows, //行数
    cols: data.cols,
    convertEol: false, //启用时，光标将设置为下一行的开头
    scrollback: 10, //终端中的回滚量
    disableStdin: false, //是否应禁用输入
    cursorStyle: 'underline', //光标样式
    cursorBlink: true, //光标闪烁
    theme: {
      foreground: 'green', //字体
      background: '#060101', //背景色
      cursor: 'help', //设置光标
    },
  });
  //绑定dom
  data.term.open(document.getElementById('xterm'));

  data.term.loadAddon(fitAddon);
  fitAddon.fit();
  //获取终端的焦点
  data.term.focus();
  let _data = data; //一定要重新定义一个this，不然this指向会出问题
  //onData方法用于定义输入的动作
  data.term.onData(function (key) {
    // 这里key值是输入的值，数据格式就是后端定义的 {"operation":"stdin","state":"ls"}
    let msgOrder = {
      operation: 'stdin',
      data: key,
    };
    //发送数据
    _data.socket.send(JSON.stringify(msgOrder));
  });

  window.onresize = debounce(() => {
    data.cols = parseInt(document.body.clientWidth / 9);
    data.rows = parseInt(document.body.clientHeight / 18);
    data.term.resize(data.cols, data.rows);
    fitAddon.fit();
    data.socket.send('{"operation":"resize", "cols":' + data.cols + ',"rows":' + data.rows + '}');
  }, 300);
};

// 添加防抖函数
const debounce = (func, wait) => {
  let timeout;
  return function () {
    if (timeout) clearTimeout(timeout);
    timeout = setTimeout(() => {
      func.apply(this, arguments);
    }, wait);
  };
};

const initSocket = () => {
  if (data.socket !== null) {
    return;
  }

  const baseAPI = proxy.$http({ method: 'config' });
  const websocketAddr = baseAPI.replace('http', 'ws');

  //定义websocket连接地址
  let terminalWsUrl =
    websocketAddr +
    `/pixiu/kubeproxy/ws?cluster=${props.webshelloptions.cluster}&namespace=${props.webshelloptions.namespace}&pod=${props.webshelloptions.pod}&container=${props.webshelloptions.container}&command=${props.webshelloptions.command}`;

  //实例化
  data.socket = new WebSocket(terminalWsUrl, [localStorage.getItem('token')]);

  //连接成功时的方法
  socketOnOpen();
  //关闭连接时的方法
  socketOnClose();
  //接收消息的方法
  socketOnMessage();
  //报错时的方法
  socketOnError();
};

const socketOnOpen = () => {
  data.socket.onopen = () => {
    data.socket.send('{"operation":"resize", "cols":' + data.cols + ',"rows":' + data.rows + '}');
  };
};

const socketOnMessage = () => {
  data.socket.onmessage = (msg) => {
    //接收到消息后将字符串转为对象，输出data内容
    let content = JSON.parse(msg.data);
    data.term.write(content.data);
  };
};
const socketOnClose = () => {
  data.socket.onclose = () => {
    //关闭连接后打印在终端里
    data.socket = null;
  };
};
const socketOnError = () => {
  data.socket.onerror = () => {
    proxy.$message.error('websocket 连接失败');
  };
};
const closeSocket = () => {
  //若没有实例化，则不需要关闭
  if (data.socket === null) {
    return;
  }

  data.term.dispose();
  data.socket.close();
  window.onresize = null;
};
</script>

<style scoped="scoped"></style>
