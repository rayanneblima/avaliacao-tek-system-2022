<template>
  <section id="contact-listing">
    <div v-if="contacts.length">
      <template v-for="contact in contacts" :key="contact.id">
        <ContactCard 
          :contact="contact"
          @editContact="$emit('editContact', $event)"
          @deleteContact="$emit('deleteContact', $event)"
        />  
      </template>
    </div>

    <div class="no-content" v-else>
      <div class="no-content__icon">
        <img src="@/assets/no-content.svg" alt="Não existem contatos adicionados" />
      </div>
      <h3>Nenhum contato cadastrado.</h3>
    </div>
  </section>
</template>

<script>
import ContactCard from "@/components/ContactCard";

export default {
  name: 'ContactListing',

  components: {
    ContactCard
  },

  props: {
    contacts: {
      default: () => ([]),
      required: true,
      type: Array
    }
  }
}
</script>

<style scoped>
#contact-listing,
.no-content {
  align-items: center;
  display: flex;
  flex-direction: column;
  gap: 20px;
  justify-content: center;
  padding-bottom: 20px;
}

.no-content {
  height: 60vh;
  gap: 10px;
}

.no-content h3 {
  color: var(--dark-green);
  font-size: 2rem;
}
</style>