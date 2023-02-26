<template>
  <div class="slava-ukraine-pop-up">
    <div class="slava-ukraine-popup__content">
      <h3 class="slava-ukraine-popup__title">Slava Ukraini!</h3>
      <p>Long live Ukraine!</p>
      <button class="slava-ukraine-popup__btn" @click="close">Heroyam slava!</button>
    </div>
  </div>
</template>

<script>
export default {
  name: "SlavaUkraine",

  methods: {
    close() {
        this.$emit('close');
      },
    }
}
</script>

