<template>
  <li @click="openPersonInfo" ref="personItem" class="person-item">
    <figure class="picture-wrapper" v-if="isVisible">
      <img
        alt="Photo"
        class="picture"
        :src="`https://api.smotrim.ru/api/v1/pictures/${props.pictureId}/bq/redirect`"
        loading="lazy"
      />
    </figure>
    <div class="person-info">
      <b>{{ props.name }}</b> <b>{{ props.surname }}</b>
    </div>
    <teleport to="body">
      <div v-if="modalOpen" class="modal-wrapper">
        <div class="modal">
          <span class="close" @click="modalOpen = false"><close-icon /></span>
          <header class="header-modal">
            <figure>
              <img
                alt="Photo"
                class="picture-modal"
                :src="`https://api.smotrim.ru/api/v1/pictures/${props.pictureId}/bq/redirect`"
                loading="lazy"
              />
            </figure>
            <section class="header-person">
              <h1>{{ props.name }}</h1>
              <h1>{{ props.surname }}</h1>
            </section>
          </header>
          <section v-if="personInfo" v-html="personInfo.body"></section>
        </div>
      </div>
    </teleport>
  </li>
</template>

<script setup lang="ts">
import { onMounted, ref, defineProps } from "vue";
import CloseIcon from "@/assets/icon/close-icon.vue";
import fetchAbort from "@/fetchAbort";

var props = defineProps<{
  pictureId: number;
  name: string;
  surname: string;
  personId: number;
}>();

var modalOpen = ref(false);
var personItem = ref();
var isVisible = ref<boolean>(false);
var personInfoLoaded = ref<boolean>(false);
var personInfo = ref<unknown>(null);

onMounted(() => {
  const observer = new IntersectionObserver(handleIntersectionObserver, {
    threshold: 0.95,
  });
  observer.observe(personItem.value);
});

async function fetchPersonInfo(personId: string, signal: AbortSignal) {
  try {
    var url = ` https://cdnapi.smotrim.ru/api/v1/persons/${personId}`;
    var request = await fetch(url, { method: "GET", signal });
    var data = await request.json();
    data = data.data;

    return data;
  } catch (e) {
    console.error(`Error ${e}`);
  } finally {
    personInfoLoaded.value = true;
  }
}

async function openPersonInfo() {
  var customFetch = fetchAbort(fetchPersonInfo);
  personInfo.value = await customFetch(props.personId.toString());
  modalOpen.value = true;
}

function handleIntersectionObserver(entries: IntersectionObserverEntry[]) {
  entries.forEach((entry) => {
    if (entry.isIntersecting) isVisible.value = true;
  });
}
</script>

<style scoped>
.modal-wrapper {
  position: absolute;
  top: 0;
  left: 0;
  width: 100vw;
  height: 100vh;
  z-index: 90;
  backdrop-filter: blur(10px);
  background: #f3f9fa90;
  overflow: hidden;
}
.modal {
  position: absolute;
  display: flex;
  flex-direction: column;
  width: 605px;
  height: 624px;
  overflow-y: auto;
  background: white;
  z-index: 99;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  padding: 25px;
  border-radius: 15px;
  -webkit-box-shadow: 0 2px 41px -13px rgba(143, 141, 143, 1);
  -moz-box-shadow: 0 2px 41px -13px rgba(143, 141, 143, 1);
  box-shadow: 0 2px 41px -13px rgba(143, 141, 143, 1);
}
.header-modal {
  display: flex;
  width: 100%;
  height: 160px;
}
.header-person {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: flex-start;
}
.person-item {
  display: flex;
  width: 160px;
  height: 208px;
  background: white;
  flex-direction: column;
  justify-content: space-between;
  align-items: center;
  cursor: pointer;
}
.picture-wrapper {
  display: flex;
  width: 150px;
  height: 150px;
  margin: 0;
}
.person-info {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
}
.picture {
  width: 150px;
  height: 150px;
  border-radius: 50%;
}
.picture-modal {
  width: 150px;
  height: 150px;
  border-radius: 10px;
}
.close {
  position: absolute;
  top: 25px;
  right: 25px;
  cursor: pointer;
}
</style>
