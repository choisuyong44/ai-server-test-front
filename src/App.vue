<template>
  <div>
    <h2>교육생들한테 한 마디 하기</h2>
    <input v-model="inputText" placeholder="음성 생성할 텍스트를 입력하세요" />
    <button @click="synthesize">생성</button>

    <!-- 즉시 재생용 오디오 컴포넌트 -->
    <div v-if="audioSrc">
      <audio hidden :src="audioSrc" controls autoplay></audio>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue'
import axios from 'axios'

const inputText = ref('')
const audioSrc = ref('') // 즉시 재생용 오디오 URL 저장

const synthesize = async () => {
  try {
    const response = await axios.post('http://localhost:8000/synthesize', null, {
      params: { text: inputText.value},
      responseType: 'blob',  // WAV를 blob으로 받음
      timeout: 60000,
    })

    // Blob → 브라우저 메모리 URL로 변환 후 audio에 연결
    const blob = new Blob([response.data], { type: 'audio/wav' })
    audioSrc.value = window.URL.createObjectURL(blob)

  } catch (error) {
    console.error('음성 생성 요청 실패:', error)
    alert('음성 생성 요청 중 오류가 발생했습니다.')
  }
}
</script>

<style scoped>
input {
  margin-right: 10px;
  padding: 5px;
  font-size: 16px;
}
button {
  padding: 5px 10px;
  font-size: 16px;
  cursor: pointer;
}
audio {
  margin-top: 20px;
}

</style>
