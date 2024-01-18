<script setup>
import { onBeforeMount, ref } from 'vue';

const APP_ID = 51835505;
const VERSION = "5.131";
const wall = ref([]);

const loadFromLocalStorage = () => {
  const savedData = localStorage.getItem('wallData');
  if (savedData) {
    wall.value = JSON.parse(savedData);
  }
};

const saveToLocalStorage = (data) => {
  localStorage.setItem('wallData', JSON.stringify(data));
};

onBeforeMount(async () => {
  loadFromLocalStorage(); 

  await VK.init({
    apiId: APP_ID
  });

  await VK.Auth.login()

  VK.Api.call("wall.get", {
    domain: "wpfrussia",
    v: VERSION,
    count: 100,
    language: "ru"
  }, r => {
    wall.value = r.response.items;
    saveToLocalStorage(wall.value); 
  })
})
</script>

<template>
  <div class="vk-posts-widget" style="height: 400px; width: 600px; overflow-y: auto;" @scroll="checkScroll">
    <div v-for="post in wall">
        <div v-if="post.attachments && post.attachments.length > 0 && post.attachments[0].photo && post.attachments[0].photo.sizes" v-for="r in post.attachments[0].photo.sizes">
          <div v-if="r.type == 'p'">
            <img :width="r.width" :height="r.height" :src="r.url">
          </div>
        </div> 
        <div>{{ post.text }}</div>      
    </div>
  </div>
</template>

