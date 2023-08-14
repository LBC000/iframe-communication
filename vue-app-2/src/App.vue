<template>
	<div>app-2</div>

	<div>
		接收父窗口的信息：{{ msg }}
	</div>

	<input v-model="inputValue" />
	<button @click="sendFn()">给父窗口发信息</button>

	<HelloWorld msg="Vite + Vue" />
</template>

<script setup>
import HelloWorld from './components/HelloWorld.vue';
import { ref, onMounted, onBeforeUnmount } from 'vue';

// 给父窗口发信息 开始
const inputValue = ref('');

function sendFn() {
	if (inputValue.value) {
		window.top.postMessage({ msg: inputValue.value, eventName: 'alert' }, 'http://127.0.0.1:9001');

		console.log(inputValue.value, '发信息1');
	} else {
		alert('请输入内容');
	}
}
// 给父窗口发信息 结束

// 接收父窗口消息 开始
const msg = ref('');
onMounted(async () => {
	// 在外部vue的window上添加postMessage的监听，并且绑定处理函数handleMessage
	window.addEventListener('message', handleMessage);
});

const handleMessage = (event) => {
	// 根据上面制定的结构来解析iframe内部发回来的数据
	if (event.origin === 'http://127.0.0.1:9001') {
		const data = event.data;

		msg.value = data.msg;
		console.log(event, '页面2收到数据了');
	}
	
	// console.log(event, '页面2收到数据了-2');
};

onBeforeUnmount(() => {
	window.removeEventListener('message', handleMessage);
});
// 接收父窗口消息 结束
</script>

<style scoped>
.logo {
	height: 6em;
	padding: 1.5em;
	will-change: filter;
	transition: filter 300ms;
}
.logo:hover {
	filter: drop-shadow(0 0 2em #646cffaa);
}
.logo.vue:hover {
	filter: drop-shadow(0 0 2em #42b883aa);
}
</style>
