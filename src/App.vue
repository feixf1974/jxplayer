<template>
  <div class="bg-gray-100 min-h-screen flex flex-col items-center justify-center">
    <h1 class="text-2xl font-bold text-center mb-4">贤米音乐</h1>
    <div v-if="loading" class="text-gray-500">加载歌曲列表...</div>
    <div v-else>
      <ul class="space-y-2">
        <li v-for="song in songs" :key="song.id" class="bg-white p-4 rounded-lg shadow-md cursor-pointer" @click="playSong(song)">
          {{ song.title }} - {{ song.artist }}
        </li>
      </ul>
      <audio ref="audioRef" controls style="width: 100%; margin-top: 20px;"></audio>
    </div>
    <button v-if="!hasUserInteracted" @click="handleUserInteraction" class="mt-4 bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded">
      点击开始播放
    </button>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue';

// 请替换为你的 Navidrome 服务器地址和 API 密钥
const NAVIDROME_BASE_URL = 'http://mc.xueli8.com';
const NAVIDROME_API_KEY = 'your-api-key';

const loading = ref(true);
const songs = ref([]);
const audioRef = ref(null);
const hasUserInteracted = ref(false);

const fetchSongs = async () => {
  try {
    const response = await fetch(`${NAVIDROME_BASE_URL}/rest/getRandomSongs.view?u=zjx&p=Zjx123&f=json&v=1.16.1&c=your-client-name&size=10`, {
      headers: {
        'Authorization': `Bearer ${NAVIDROME_API_KEY}`
      }
    });
    const data = await response.json();
    songs.value = data['subsonic-response'].randomSongs.song;
    loading.value = false;
   

  } catch (error) {
    console.error('获取歌曲列表时出错:', error);
    loading.value = false;
  }
};

const playSong = (song) => {
  const songUrl = `${NAVIDROME_BASE_URL}/rest/stream.view?id=${song.id}&u=zjx&p=Zjx123&f=json&v=1.16.1&c=your-client-name`;
  audioRef.value.src = songUrl;
  audioRef.value.play();
};

const handleUserInteraction = () => {
  hasUserInteracted.value = true;
  if (songs.value.length > 0) {
    playSong(songs.value[0]);
  }
};

onMounted(() => {
  fetchSongs();
});
</script>

<style scoped>
/* 可以添加自定义样式 */
</style>    
