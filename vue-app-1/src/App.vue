<template>
	<div>app-1</div>

	<iframe ref="iframeRef" class="iframe-box-1" id="iframe" src="http://127.0.0.1:9002/"></iframe>

	<input v-model="inputValue" />
	<button @click="sendFn()">给子iframe 发信息</button>

	<div>接收子页面的数据：{{ sonMsg }}</div>

	<HelloWorld msg="Vite + Vue" />
</template>

<script setup>
import { ref, onMounted, onBeforeUnmount } from 'vue';
import HelloWorld from './components/HelloWorld.vue';

const inputValue = ref('');
const iframeRef = ref(null);

function sendFn() {
	if (inputValue.value) {
		let iframe = document.querySelector('#iframe');
		// iframe.contentWindow.postMessage('hello world', '*');
		iframeRef.value.contentWindow.postMessage({ msg: inputValue.value }, '*');

		console.log(inputValue.value, '发信息1');
	} else {
		alert('请输入内容');
	}
}

// 接收子页面消息 开始
const sonMsg = ref('');
onMounted(async () => {
	// 在外部vue的window上添加postMessage的监听，并且绑定处理函数handleMessage
	window.addEventListener('message', handleMessage);
});

const handleMessage = (event) => {
	// 根据上面制定的结构来解析iframe内部发回来的数据
	if (event.origin === 'http://127.0.0.1:9002') {
		const data = event.data;

		sonMsg.value = data.msg;

		if (data.eventName == 'alert') {
			alert('触发了父窗口自定义事件-' + data.msg);
		}

		console.log(event, '页面1收到数据了');
	}

	// console.log(event, '页面1收到数据了-2');
};

onBeforeUnmount(() => {
	window.removeEventListener('message', handleMessage);
});
// 接收子页面消息 结束
</script>

<style scoped>
.iframe-box-1 {
	width: 100%;
	height: 300px;

	border: 0;
	border: 1px solid #ccc;
}
</style>
