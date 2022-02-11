<template>
  <header>
    <button class="button--green" @click="$emit('showRegisterModal')">
      Nova Pessoa
    </button>
  </header>
</template>

<script>
export default {
  name: 'Header'
}
</script>

<style scoped>
header {
  align-items: center;
  display: flex;
  justify-content: flex-end;
  margin: 0 auto;
  padding: 20px 0;
  min-height: 10vh;
  max-width: 1200px;
}
</style>