<template>
  <main class="main">
    <div class="cards-wrapper">
      <section class="cards-container">
        <section
          @click="handleClickOnCard('front')"
          :class="{
            blur: !cardFrontActive,
          }"
          class="card-container first"
        >
          <header class="card-title">
            <h3>iBank</h3>
            <h4>SUPER CARD</h4>
          </header>
          <div class="inner-wrapper">
            <section class="card-chip">
              <span class="chip-icon"><card-chip /></span>
              <span class="wireless-icon"><card-signal /></span>
            </section>
            <section class="card-number">
              <input
                :value="maskCardNumber"
                @input="handleInputCardField('number', $event)"
                class="number-input"
                placeholder="0000  0000  0000  0000"
                minlength="22"
                maxlength="22"
                type="text"
              />
            </section>
          </div>
          <section class="card-info">
            <div class="card-customer">
              <input
                :value="cardData.firstName"
                @input="handleInputCardField('firstName', $event)"
                class="customer-input"
                placeholder="Имя"
                minlength="2"
                maxlength="10"
                type="text"
              />
              <input
                :value="cardData.lastName"
                @input="handleInputCardField('lastName', $event)"
                class="customer-input"
                placeholder="Фамилия"
                minlength="2"
                maxlength="10"
                type="text"
              />
            </div>
            <div class="card-date">
              <input
                :value="cardData.monthExpired"
                @input="handleInputCardField('month', $event)"
                class="date-input"
                placeholder="М"
                minlength="2"
                maxlength="2"
                type="text"
              />
              /
              <input
                :value="cardData.yearExpired"
                @input="handleInputCardField('year', $event)"
                class="date-input"
                placeholder="Г"
                minlength="2"
                maxlength="2"
                type="text"
              />
            </div>
          </section>
        </section>
        <section
          @click="handleClickOnCard('back')"
          :class="{
            blur: cardFrontActive,
          }"
          class="card-container second"
        >
          <section class="magnet-line"><span class="line"></span></section>
          <section class="csv-section">
            <input
              :value="cardData.cvc"
              @input="handleInputCardField('cvc', $event)"
              class="csv-input"
              placeholder="CVC"
              minlength="3"
              maxlength="3"
              type="password"
            />
          </section>
        </section>
      </section>
      <div class="wrap-button">
        <button class="button" @click="handleSendData">Отправить</button>
      </div>
    </div>
    <person-component />
  </main>
</template>

<script setup lang="ts">
import CardSignal from "@/assets/icon/card-signal.vue";
import CardChip from "@/assets/icon/card-chip.vue";
import { computed, reactive, ref, toRefs } from "vue";
import PersonComponent from "@/components/PersonComponent.vue";

var cardFrontActive = ref(true);
var cardData = reactive<{
  number: string;
  firstName: string;
  lastName: string;
  cvc: string;
  monthExpired: string;
  yearExpired: string;
}>({
  number: "",
  firstName: "",
  lastName: "",
  cvc: "",
  monthExpired: "",
  yearExpired: "",
});

var maskCardNumber = computed(() => {
  var { number } = toRefs(cardData);
  var numbersStr = number.value.replace(/\s+/g, "");
  var result = "";
  var temp = [...numbersStr].reduce(
    (nums: string[], num: string, i: number) => {
      result = result + num;
      if ((i + 1) % 4 === 0) {
        result = result + "  ";
      }
      return nums;
    },
    []
  );
  return result.trim();
});

function handleInputCardField(field: string, event: Event) {
  var { number, firstName, lastName, monthExpired, yearExpired, cvc } =
    toRefs(cardData);
  var target = event.target as HTMLInputElement;
  if (field === "number") {
    number.value = target.value;
    number.value = number.value.replace(/[^0-9,\s]/g, "").trim();
    cardData.number = number.value;
  } else if (field === "firstName") {
    firstName.value = target.value;
    firstName.value = firstName.value
      .toUpperCase()
      .replace(/[^A-Z,\s]/g, "")
      .trim();
    cardData.firstName = firstName.value;
  } else if (field === "lastName") {
    lastName.value = target.value;
    lastName.value = lastName.value
      .toUpperCase()
      .replace(/[^A-Z,\s]/g, "")
      .trim();
    cardData.lastName = lastName.value;
  } else if (field === "month") {
    monthExpired.value = target.value;
    monthExpired.value = monthExpired.value.replace(/[^0-9,\s]/g, "").trim();
    cardData.monthExpired = monthExpired.value;
  } else if (field === "year") {
    yearExpired.value = target.value;
    yearExpired.value = yearExpired.value.replace(/[^0-9,\s]/g, "").trim();
    cardData.yearExpired = yearExpired.value;
  } else {
    cvc.value = target.value;
    cvc.value = cvc.value.replace(/[^0-9,\s]/g, "").trim();
    cardData.cvc = cvc.value;
  }
}
var handleSendData = () => {
  alert(JSON.stringify(cardData, null, 2));
};
var handleClickOnCard = (val: string) => {
  cardFrontActive.value = val === "front";
};
</script>

<style lang="scss">
.main {
  display: block;
  width: 100vw;
  height: 100vh;
  overflow: hidden;
}
.cards-wrapper {
  display: flex;
  width: 100%;
  height: 440px;
  position: relative;
  flex-direction: column;
}
.cards-container {
  display: flex;
  position: absolute;
  left: 50%;
  top: 50%;
  transform: translate(-50%, -50%);
  width: 515px;
  height: 310px;
}
.card-container {
  display: flex;
  width: 425px;
  height: 270px;
  border: #2c3e50 solid 1px;
  border-radius: 9px;
  flex-direction: column;
  padding: 2px 30px 15px;
  gap: 15px;
}
.first {
  position: absolute;
  background-image: linear-gradient(
    to right top,
    #f9f8f7,
    #f0f0e0,
    #d2edd5,
    #a6eadf,
    #7ee2f9
  );
  z-index: 2;
}
.second {
  position: absolute;
  top: 40px;
  left: 90px;
  background-image: linear-gradient(
    to right top,
    #f9f8f7,
    #f8f8f6,
    #f6f9f6,
    #f4f9f8,
    #f3f9fa
  );
  z-index: 1;
}
.card-title {
  display: flex;
  width: 100%;
  height: 35%;
  justify-content: space-around;
  align-items: center;
}
.card-chip {
  display: flex;
  width: 100%;
  justify-content: space-between;
  align-items: center;
}
.card-number {
  font-family: "Arial Rounded MT Bold";
  font-size: 22px;
}
.inner-wrapper {
  display: flex;
  flex-direction: column;
  width: 100%;
  gap: 15px;
  padding: 0 10px;
}
.card-info {
  display: flex;
  width: 100%;
  justify-content: space-between;
  align-items: center;
  font-family: "Arial Rounded MT Bold";
  font-size: 18px;
}
.card-customer {
  display: flex;
  width: 60%;
  align-items: center;
  gap: 4px;
}
.card-date {
  display: flex;
  width: 20%;
  align-items: center;
  gap: 4px;
}
.date-input {
  width: 30px;
}
.customer-input {
  width: 130px;
}
.number-input {
  width: 100%;
  letter-spacing: 4.2px;
}
.csv-input {
  width: 50px;
}
.wireless-icon {
  transform: rotate(90deg);
}
.chip-icon {
  display: flex;
  align-items: center;
  justify-content: center;
  border-radius: 2px;
  background: #ffde75;
}
.csv-section {
  display: flex;
  justify-content: flex-end;
  align-items: center;
  gap: 5px;
}
.magnet-line {
  display: flex;
  height: 65%;
}
.line {
  display: flex;
  width: 100%;
  height: 70px;
  position: relative;
  left: 25px;
  top: 25px;
  background: #1d1d1b;
}
.blur {
  filter: blur(2px);
}
.wrap-button {
  display: flex;
  width: 100%;
  height: 50px;
  justify-content: center;
  align-items: center;
}
.button {
  width: 100px;
  height: 35px;
}
</style>
