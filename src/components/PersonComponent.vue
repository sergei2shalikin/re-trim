<template>
  <div class="list-wrapper">
    <div v-if="!personLoaded" class="spinner">Loading...</div>
    <div class="arrow-container">
      <arrow-right
        @click="handleArrowLeftClick"
        v-if="personMap && moveOn < 0"
        class="arrow-left"
      />
    </div>
    <person-list v-if="personMap">
      <person-list-item
        v-for="eachPerson of personMap.keys()"
        :key="eachPerson.id"
        :personId="eachPerson.id"
        :pictureId="eachPerson.picId"
        :name="eachPerson.name"
        :surname="eachPerson.surname"
        class="person-item"
        :style="{
          transform: `translateX(${moveOn}px) `,
        }"
      />
    </person-list>
    <div class="arrow-container">
      <arrow-right
        @click="handleArrowRightClick"
        v-if="personMap && lastPosition <= moveOn"
        class="arrow-right"
      />
    </div>
  </div>
</template>

<script setup lang="ts">
import PersonList from "@/components/PersonList.vue";
import PersonListItem from "@/components/PersonListItem.vue";
import fetchAbort from "@/fetchAbort";
import { onMounted, ref } from "vue";
import ArrowRight from "@/assets/icon/arrow-right.vue";

var personLoaded = ref(false);
var personMap = ref<Map<unknown, unknown>>(null);
var moveOn = ref<number>(0);
var lastPosition = ref<number>(0);

onMounted(async () => {
  var customFetch = fetchAbort(fetchPersons);
  personMap.value = await customFetch();
  lastPosition.value = -(
    personMap.value.size * 150 +
    (personMap.value.size * 40 - 40) -
    (150 * 5 + 4 * 4)
  );
});

function handleArrowRightClick() {
  if (lastPosition.value >= moveOn.value) return;
  moveOn.value -= 190;
}
function handleArrowLeftClick() {
  if (moveOn.value >= 0) return;
  moveOn.value = moveOn.value + 190;
}

async function fetchPersons(param: string, signal: AbortSignal) {
  try {
    var url = "https://cdnapi.smotrim.ru/api/v1/boxes/vesti2";
    var request = await fetch(url, { method: "GET", signal });
    var data = await request.json();
    var content = data.data.content;
    var persons = new Map();

    for (var eachItem of content) {
      if (eachItem.alias === "vesti3-persons") {
        for (var person of eachItem.content) {
          if (person.type === "person") {
            persons.set(person, {});
          }
        }
      }
    }
    return persons;
  } catch (e) {
    console.error(`Error ${e}`);
  } finally {
    personLoaded.value = true;
  }
}
</script>

<style scoped>
.list-wrapper {
  display: flex;
  justify-content: center;
  align-items: center;
}
.arrow-container {
  display: flex;
  width: 88px;
  height: 88px;
  z-index: 33;
}
.arrow-left {
  position: relative;
  top: -33px;
  left: 44px;
  transform: rotate(180deg);
}
.arrow-right {
  position: relative;
  top: -33px;
  left: -44px;
}
</style>
