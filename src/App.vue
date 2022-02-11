<template>
  <Header @showRegisterModal="showRegisterModal = true" />
  <ContactListing 
    :contacts="contacts"
    @editContact="editContact($event)"
    @deleteContact="deleteContact($event)"
  />

  <transition name="register-modal">
    <ContactRegister 
      v-if="showRegisterModal"
      :contact="contactToEdit"
      @addContact="contacts.push($event)"
      @showRegisterModal="showRegisterModal = $event"
    />
  </transition>
</template>

<script>
import Header from "@/components/Header";
import ContactListing from "@/components/ContactListing";
import ContactRegister from "@/components/ContactRegister";

export default {
  name: 'App',

  components: {
    Header,
    ContactListing,
    ContactRegister
  },

  data () {
    return {
      showRegisterModal: false,
      contacts: [],
      contactToEdit: null
    }
  },

  methods: {
    editContact (contactId) {
      this.contactToEdit = this.contacts.find(contact => contact.id !== contactId);
      if (this.contactToEdit) {
        this.showRegisterModal = true;
      }
    },

    deleteContact (contactId) {
      this.contacts = this.contacts.filter(contact => contact.id !== contactId);
    }
  },
}
</script>

<style>
/* Animated Register Modal */
.register-modal-enter-active,
.register-modal-leave-active {
  transition: 0.4s ease-in-out all;
}
.register-modal-enter-from,
.register-modal-leave-to {
  opacity: 0.1;
}
</style>
