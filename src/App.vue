<template>
  <Header @showRegisterModal="showRegisterModal = true" />
  <ContactListing 
    v-if="!showLoading"
    :contacts="contacts"
    @editContact="editContact($event)"
    @deleteContact="deleteContact($event)"
  />

  <transition name="register-modal">
    <ContactRegister 
      v-if="showRegisterModal"
      :contact="contactToEdit"
      @addContact="addOrUpdateContact($event)"
      @showRegisterModal="showRegisterModal = $event"
    />
  </transition>

  <Loading v-if="showLoading" />
</template>

<script>
import { collection, doc, getDocs, addDoc, updateDoc, deleteDoc } from "firebase/firestore";
import { db } from "@/firebase/firebase";

import Header from "@/components/Header";
import ContactListing from "@/components/ContactListing";
import ContactRegister from "@/components/ContactRegister";
import Loading from '@/components/Loading.vue';

export default {
  name: 'App',

  components: {
    Header,
    ContactListing,
    ContactRegister,
    Loading
  },

  data () {
    return {
      showRegisterModal: false,
      contacts: [],
      contactToEdit: null,
      showLoading: false
    }
  },

  watch: {
    showRegisterModal () {
      if (!this.showRegisterModal) {
        this.contactToEdit = null;
      }
    }
  },

  created () {
    this.getContacts();
  },

  methods: {
    async getContacts () {
      this.showLoading = true;
      this.contacts = [];
      const dataCollection = collection(db, 'contacts');
      const results = await getDocs(dataCollection);

      results.docs.forEach((doc) => {
        if (!this.contacts.some((contact) => contact.id === doc.id)) {
          const data = {
            id: doc.id,
            fullName: doc.data().fullName,
            personType: doc.data().personType,
            group: doc.data().group,
            phoneList: doc.data().phoneList
          };

          if (data.personType === "PF") {
            data.cpf = doc.data().document
            data.rg = doc.data().document2
          }

          if (data.personType === "PJ") {
            data.cnpj = doc.data().document
            data.inscricaoEstadual = doc.data().document2
          }          

          this.contacts.push(data);
        }
      });
      
      this.showLoading = false;
    },

    async addOrUpdateContact (contact) {
      this.showLoading = true;
      const contactExists = this.contacts.find(contactdb => contactdb.id === contact.id);

      if (contactExists) {
        await updateDoc(doc(db, 'contacts', contact.id), contact);
        this.getContacts();
        return;
      }
      
      await addDoc(collection(db, "contacts"), contact);
      this.getContacts();
    },

    editContact ({ id }) {
      this.contactToEdit = this.contacts.find(contact => contact.id === id);
      if (this.contactToEdit) {
        this.showRegisterModal = true;
      }
    },

    async deleteContact (contactId) {
      this.showLoading = true;
      await deleteDoc(doc(db, 'contacts', contactId));
      this.getContacts();
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
