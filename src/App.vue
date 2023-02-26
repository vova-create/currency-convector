<template>
  <div>
    <CurrencyConvector/>
    <DisplayCurrentExchangeRatesBlock/>
    <SlavaUkraine v-if="slavaUkrainePopup" @close="closeSlavaUkrainePopUp"/>
  </div>
</template>
<script lang="ts">
import { defineComponent } from "vue";
import DisplayCurrentExchangeRatesBlock from "@/components/DisplayCurrentExchangeRatesBlock.vue";
import SlavaUkraine from "@/components/SlavaUkraine.vue";
import CurrencyConvector from "@/components/CurrencyConvector.vue";
import "@/assets/sass/index.sass";

export default defineComponent({
  name: "CurrencyConverter",
  components: {
    DisplayCurrentExchangeRatesBlock,
    SlavaUkraine,
    CurrencyConvector
  },
  data() {
    return {
      slavaUkrainePopup: true,
    };
  },
  methods: {
    closeSlavaUkrainePopUp() {
      this.slavaUkrainePopup = false
    },
  },
});
</script>
