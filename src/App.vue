<script setup lang="ts">
import { computed, ref } from 'vue';
import poney from "@/assets/poney.png";

const values = ref(
  {
    rent: 921,
    insurance: 16.44,
    electricity: 168,
    internet: 38,
    marion: 0,
    raphael: 0,
    totalTricount: 0,
    paidByRaphael: 0,
    paidByMarion: 0,
  }
);

/**
 * Total rent and charges, rounded to two decimal places.
 */
const totalRentAndCharges = computed((): number => {
  const calculation = values.value.rent + values.value.electricity + values.value.internet + values.value.insurance;
  return +calculation.toFixed(2);
});

/**
 * Raphael part of the total, proportional to his salary.
 */
const raphaelPart = computed((): number => {
  const calculation = values.value.raphael / (values.value.raphael + values.value.marion) * totalRentAndCharges.value;
  return +calculation.toFixed(2);
});

/**
 * Marion part of the total, proportional to her salary.
 */
const marionPart = computed((): number => {
  const calculation = values.value.marion / (values.value.raphael + values.value.marion) * totalRentAndCharges.value;
  return +calculation.toFixed(2);
});

const tricount = computed((): number => {
  const shouldHavePaid = values.value.raphael / (values.value.raphael + values.value.marion) * values.value.totalTricount;
  if (shouldHavePaid < values.value.paidByRaphael) return values.value.paidByRaphael - shouldHavePaid;
  else if (shouldHavePaid > values.value.paidByRaphael) return -(shouldHavePaid - values.value.paidByRaphael);
  else return 0;
});

// Marion pays the internet bill directly, so it's deducted from what she owes.
const totalMarionPart = computed((): number => +(marionPart.value + tricount.value - values.value.internet).toFixed(2));

const selectAll = (event: Event) => {
  const { target } = event;
  (target as HTMLInputElement).select();
};

</script>

<template>
  <header>
    <h1 class="header">
      <img class="image image-left" :src="poney" alt="" />
      <div>Loyer du cœur des PONEYS</div>
      <img class="image image" :src="poney" alt="" />
    </h1>
  </header>

  <main>
    <div class="part-wrapper">
      <div class="section">
        <div class="label">
          <label for="rent">Loyer</label>
        </div>

        <div class="input">
          <input @click="selectAll" v-model="values.rent" id="rent" type="number" />€
        </div>
      </div>

      <div class="section">
        <div class="label">
          <label for="insurance">Assurance</label>
        </div>

        <div class="input">
          <input @click="selectAll" v-model="values.insurance" id="insurance" type="number" />€
        </div>
      </div>

      <div class="section">
        <div class="label">
          <label for="electricity">Électricité</label>
        </div>

        <div class="input">
          <input @click="selectAll" v-model="values.electricity" id="electricity" type="number" />€
        </div>
      </div>

      <div class="section">
        <div class="label">
          <label for="internet">Internet</label>
        </div>

        <div class="input">
          <input @click="selectAll" v-model="values.internet" id="internet" type="number" />€
        </div>
      </div>

      <div class="section">
        <div class="label">Total</div>

        <div class="input total">{{ totalRentAndCharges }}€</div>
      </div>
    </div>

    <div class="part-wrapper">
      <div class="section">
        <div class="label">
          <label for="marion">Salaire Marion</label>
        </div>

        <div class="input">
          <input @click="selectAll" v-model="values.marion" id="marion" type="number" />€
        </div>
      </div>

      <div class="section">
        <div class="label">
          <label for="marion">Salaire Raphaël</label>
        </div>

        <div class="input">
          <input @click="selectAll" v-model="values.raphael" id="raphael" type="number" />€
        </div>
      </div>

      <div class="section">
        <div class="label">
          Part de Marion
        </div>

        <div class="input" v-if="!isNaN(marionPart)">
          {{ marionPart }}€
        </div>
      </div>

      <div class="section">
        <div class="label">
          Part de Raphaël
        </div>

        <div class="input" v-if="!isNaN(raphaelPart)">
          {{ raphaelPart }}€
        </div>
      </div>
    </div>

    <div class="part-wrapper">
      <div class="section">
        <div class="label">
          <label for="tricount-total">Total Tricount</label>
        </div>

        <div class="input">
          <input @click="selectAll" v-model="values.totalTricount" id="tricount-total" type="number" />€
        </div>
      </div>

      <div class="section">
        <div class="label">
          <label for="paid-by-marion">Payé par Marion</label>
        </div>

        <div class="input">
          <input @click="selectAll" v-model="values.paidByMarion" id="paid-by-marion" type="number" />€
        </div>
      </div>

      <div class="section">
        <div class="label">
          <label for="paid-by-raphael">Payé par Raphaël</label>
        </div>

        <div class="input">
          <input @click="selectAll" v-model="values.paidByRaphael" id="paid-by-raphael" type="number" />€
        </div>
      </div>
    </div>

    <div class="total-total"
      v-if="!isNaN(totalMarionPart) && values.rent && values.insurance && values.electricity && values.internet && values.marion && values.raphael && values.totalTricount && values.paidByMarion && values.paidByRaphael">
      Marion doit {{ totalMarionPart }}€
    </div>
  </main>
</template>

<style lang="scss">
.header {
  display: flex;
  align-items: center;
  margin-bottom: 2rem;
  text-align: center;
}

.image {
  display: block;
  width: 150px;

  &-left {
    transform: scale(-1, 1);
  }
}

.section {
  display: flex;
  flex-wrap: nowrap;
  justify-content: space-between;

  &>* {
    width: 50%;
  }
}

.input {
  text-align: right;

  input {
    cursor: pointer;
  }
}

.label,
.label label {
  font-weight: 900;
}

.part-wrapper {
  border-bottom: 4px solid black;
  padding-bottom: 1rem;
  margin-bottom: 1rem;

  &>* {
    width: 100%;
  }
}

.total {
  font-weight: 900;
  font-size: 32px;
}

.total-total {
  font-weight: 700;
  text-align: center;
  font-size: 50px;
}
</style>
