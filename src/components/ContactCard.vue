<template>
  <div class="contact-card">
    <div class="card__info">
      <h3 class="card__name">
        {{ contact.fullName }}    
      </h3>
      <p class="card__document">
        {{ contact.document }}    
      </p>
    </div>

    <div class="card__actions">
      <div @click="$emit('editContact', contact.id)" class="button">
        <div class="button__icon">
          <img src="@/assets/edit.svg" alt="Editar Contato" />
        </div>
      </div> 

      <div @click="$emit('deleteContact', contact.id)" class="button">
        <div class="button__icon">
          <img src="@/assets/delete.svg" alt="Excluir Contato" />
        </div>
      </div>  
    </div>  
  </div>
</template>

<script>
export default {
  name: 'ContactCard',

  props: {
    contact: {
      default: () => ({}),
      required: true,
      type: Object
    }
  }
}
</script>

<style scoped>
.contact-card {
  background: var(--white);
  border-radius: 20px;

  align-items: center;
  display: grid;
  grid-template-columns: 3fr 1fr;
  gap: 10px;
  height: 100px;
  margin: 0 auto;
  padding: 20px 30px;
  width: 280px;
}

.card__name {
  color: var(--dark-green);
  font-size: 1.4rem;
}

.card__actions {
  align-items: center;
  display: flex;
  justify-content: space-between;
}
</style>