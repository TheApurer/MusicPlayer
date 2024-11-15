<template>
  <div class="music-player">
    <!-- 音乐封面 -->
    <div class="cover">
      <img :src="props.coverImage" alt="Music Cover" />
      <div class="song-title">《{{ songTitle }}》</div> <!-- 歌曲名 -->
    </div>

    <!-- 播放器控件 -->
    <div class="controls">
      <button @click="togglePlay" :class="isPlaying ? 'i-ri-pause-large-line' : 'i-ri-play-large-line'"></button>
      <input
        type="range"
        v-model="currentTime"
        :max="duration"
        step="1"
        @input="seek"
        class="progress-bar"
      />
      <span>{{ formatTime(currentTime) }} / {{ formatTime(duration) }}</span>
    </div>

    <!-- 音频 -->
    <audio ref="audio" :src="props.audioSrc" @timeupdate="updateProgress" @ended="onEnd"></audio>
  </div>
</template>

<script lang="ts" setup>
import { ref, onMounted } from 'vue'

const props = defineProps({
  audioSrc: {
    type: String,
    required: true,
  },
  coverImage: {
    type: String,
    required: true,
  },
})

const audio = ref<HTMLAudioElement | null>(null)
const isPlaying = ref(false)
const currentTime = ref(0)
const duration = ref(0)
const isLoaded = ref(false)

// 监听音频的元数据加载事件
onMounted(() => {
  if (audio.value) {
    audio.value.addEventListener('loadedmetadata', () => {
      duration.value = audio.value!.duration
      isLoaded.value = true
    })
  }
})

const togglePlay = () => {
  if (audio.value) {
    if (isPlaying.value) {
      audio.value.pause()
    } else {
      audio.value.play()
    }
    isPlaying.value = !isPlaying.value
  }
}

const updateProgress = () => {
  if (audio.value) {
    currentTime.value = audio.value.currentTime
  }
}

const seek = () => {
  if (audio.value) {
    audio.value.currentTime = currentTime.value
  }
}

const onEnd = () => {
  isPlaying.value = false
  currentTime.value = 0
}

// 格式化时间
const formatTime = (time: number) => {
  const minutes = Math.floor(time / 60)
  const seconds = Math.floor(time % 60)
  return `${minutes}:${seconds < 10 ? '0' : ''}${seconds}`
}

// 处理歌曲名，去除文件后缀
const songTitle = props.audioSrc.split('/').pop()?.replace('.mp3', '') || 'Unknown Song'
</script>

<style scoped>
.music-player {
  display: flex;
  width: 100%;
  height: 90px;
  background: linear-gradient(160deg, #7aabef,#eff8ff,#f2f7fa);
  padding: 10px;
  border-radius: 15px;
  box-shadow: 0 16px 10px rgba(0, 0, 0, 0.2);
  align-items: center;
}

.cover {
  width: 120px;
  height: 120px;
  margin-right: 20px;
  overflow: hidden;
  border-radius: 12px;
  position: relative; /* 使歌曲名能够绝对定位 */
}

.cover img {
  width: 110%;
  height: 110%;
  object-fit: cover;
  transition: transform 0.3s ease-in-out;
  border-radius: 12px;
  
}

.cover img:hover {
  transform: scale(1.2);
}

/* 歌曲名样式 */
.song-title {
  position: absolute;
  top: 85px;
  left: 15px;
  font-size: 22px;
  color: #ffffff;
  font-weight: bold;
  text-shadow: 2px 2px 5px rgba(0, 0, 0, 0.7);
  max-width: 1000px; /* 限制歌曲名宽度 */
  overflow: hidden;
  text-overflow: ellipsis; /* 超出部分省略 */
  white-space: nowrap; /* 防止歌曲名换行 */
}

.controls {
  display: flex;
  flex-direction: column;
  justify-content: space-between;
  height: 100%;
  align-items: center;
  flex-grow: 1;
}

button {
  padding: 12px 25px;
  background-color: #3946af;
  color: #ffffff;
  border: none;
  border-radius: 20px;
  cursor: pointer;
  font-size: 30px;
  margin-bottom: 10px;
  transition: background-color 0.3s ease;
}

button:hover {
  background-color: #ff7b7b;
}

.progress-bar {
  width: 100%;
  height: 10px;
  background: #e0e0e0; /* 修改为你想要的颜色 */
  border-radius: 5px;
  position: relative;
  margin-top: 10px;
  appearance: none; /* 禁用默认样式 */
}

/* 进度条的动态进度部分 */
input[type="range"] {

  width: 100%;
  height: 10px;
  background: transparent; /* 让背景透明 */
  outline: none;
}

input[type="range"]::-webkit-slider-runnable-track {
  width: 100%;
  height: 10px;
  background: #a8f2fc; /* 设置进度条背景 */
  border-radius: 5px;
  box-shadow: 0 0 5px rgba(0, 0, 0, 0.3);
}

input[type="range"]::-webkit-slider-thumb {
  -webkit-appearance: none;
  appearance: none;
  width: 15px;
  height: 15px;
  background: #61b2dd; /* 设置滑块的颜色 */
  border-radius: 50%;
  cursor: pointer;
  margin-top: -3px; /* 通过调整此值，使滑块垂直居中 */
  border: 2px solid #9bd0ed; /* 设置滑块的描边 */
  box-shadow: 0 0 10px rgba(0, 0, 0, 0.3); /* 设置滑块的阴影效果 */
}

input[type="range"]:focus {
  outline: none; /* 去除焦点时的边框 */
}

/* 控制进度条的动态填充 */
input[type="range"]::-webkit-slider-runnable-track {
  background: #e3eef0; /* 控制进度条的填充颜色 */
}

span {
  font-size: 16px;
  color: #666;
}
</style>
