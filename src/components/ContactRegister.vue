<template>
  <section class="overlay" @click.self="$emit('showRegisterModal', false)">
    {{ contact }}
    <form id="contact-register" @submit.prevent="saveContact">
      <div class="register__header">
        <h3>Cadastro de Contato</h3>

        <button type="submit" class="button transparent">
          <div class="button__icon">
            <img src="@/assets/save.svg" alt="Salvar Contato" />
          </div>
        </button>  
      </div>

      <div class="register__principal">
        <label for="fullName">Nome Completo</label>
        <input 
          v-model="fullName"
          type="text" 
          name="fullName" 
          placeholder="Fulano da Silva Souza" 
          required
        />

        <div class="principal__switches">
          <div class="switch__pf">
            <label class="switch__label" for="pf">Pessoa Física</label>
            <label class="switch">
              <input 
                v-model="pf" 
                type="checkbox" 
                name="pf" 
              />

              <span class="slider round"></span>
            </label>
          </div>

          <div class="switch__pj">
            <label class="switch__label" for="pj">Pessoa Jurídica</label>
            <label class="switch">
              <input 
                v-model="pj" 
                type="checkbox" 
                name="pj" 
              />

              <span class="slider round"></span>
            </label>
          </div>
        </div>
      </div>

      <div class="register__pf" v-show="pf">
        <label for="cpf">CPF</label>
        <input 
          v-model="cpf"
          type="tel" 
          name="cpf" 
          placeholder="000.000.000-00"
          :required="pf"
          v-mask="'###.###.###-##'"
        />

        <label for="rg">RG</label>
        <input 
          v-model="rg"
          type="tel" 
          name="rg" 
          placeholder="00.000.000-0"
          v-mask="'##.###.###-#'"
        />
      </div>
      
      <div class="register__pj" v-show="pj">
        <label for="cnpj">CNPJ</label>
        <input 
          v-model="cnpj"
          type="text" 
          name="cnpj" 
          placeholder="00.000.000/0000-00" 
          :required="pj"
          v-mask="'##.###.###/####-##'"
        />

        <label for="inscricaoEstadual">Inscrição Estadual</label>
        <input 
          v-model="inscricaoEstadual"
          type="tel" 
          name="inscricaoEstadual" 
          placeholder="00.000.0000-0"
          v-mask="'##.###.####-#'"
        />
      </div>

      <div class="register__group">
        <label for="group">Grupo</label>
        <select name="group" id="group" v-model="group">
          <option value="X">x</option>
          <option value="Y">y</option>
          <option value="Z">z</option>
        </select>
      </div>

      <div class="register__header">
        <h3>Telefones {{ phoneList.length > 0 ? `(${phoneList.length})` : '' }}</h3>
      </div>

      <div class="register__phone">
        <label for="phone"></label>
        <input 
          v-model="phone"
          type="tel"
          name="phone" 
          placeholder="(00) 00000-0000" 
          v-mask="['(##) ####-####', '(##) #####-####']"
        />

        <button type="button" class="button--green" @click.self="addPhone" :disabled="!this.phone">
          Adicionar
        </button>
      </div>

      <div class="register__phonelist">
        <div class="phone" v-for="(ph, index) in phoneList" :key="index">
          <input 
            :value="ph"
            type="text" 
            name="phone" 
            placeholder="(00) 00000-0000"
            disabled
          />

          <div @click="deletePhone(ph)" class="button button--red">
            <div class="button__icon">
              <img src="@/assets/delete.svg" alt="Excluir Telefone" />
            </div>
          </div>
        </div>
      </div>
    </form>
  </section>
</template>

<script>
export default {
  name: 'ContactRegister',

  props: {
    contact: {
      default: () => ({}),
      required: false,
      type: Object
    }
  },

  data () {
    return {
      id: '',
      fullName: '',
      pf: true,
      pj: false,
      cpf: '',
      rg: '',
      cnpj: '',
      inscricaoEstadual: '',
      group: 'X',
      phoneList: [],
      phone: '',
    }
  },

  watch: {
    pf () {
      return this.pj = !this.pf;
    },

    pj () {
      return this.pf = !this.pj;
    }
  },

  mounted () {
    if (this.contact) {
      const { id, fullName, cpf, rg, cnpj, inscricaoEstadual, group, personType, phoneList } = this.contact;
      this.id = id;
      this.fullName = fullName;
      this.group = group;
      this.phoneList = phoneList;

      if (personType === 'PF') {
        this.cpf = cpf;
        this.rg = rg;
        this.pf = true;
        return;
      }

      this.cnpj = cnpj;
      this.inscricaoEstadual = inscricaoEstadual;      
      this.pj = true;      
    }
  },

  unmounted () {
    this.resetFields();
  },

  methods: {
    resetFields () {
      this.id = '',
      this.fullName = '',
      this.pf = true,
      this.pj = false,
      this.cpf = '',
      this.rg = '',
      this.cnpj = '',
      this.inscricaoEstadual = '',
      this.group = '',
      this.phoneList = [],
      this.phone = ''
    },

    addPhone () {
      this.phoneList.push(this.phone);
      this.phone = '';
    },

    deletePhone (phone) {
      this.phoneList = this.phoneList.filter(ph => ph !== phone);
    },

    saveContact () {
      if (this.fullname < 3) {
        return alert('Informe o nome completo do contato.');
      }

      if (this.pf && this.cpf.length < 11) {
        return alert('CPF incorreto.');
      }

      if (this.pj && this.cnpj.length < 14) {
        return alert('CNPJ incorreto.');
      }

      const contact = {
        id: this.id || crypto.randomUUID(),
        fullName: this.fullName,
        document: this.pf ? this.cpf : this.cnpj,
        document2: this.pf ? this.rg : this.inscricaoEstadual,
        personType: this.pf ? 'PF' : 'PJ',
        group: this.group,
        phoneList: this.phoneList
      };

      this.$emit('addContact', contact);
      this.$emit('showRegisterModal', false);
    }
  },
}
</script>

<style scoped>
.overlay {
  background: #858383a2;
  min-height: 100vh;
  padding: 20px 0;
  position: absolute;
  top: 0;
  width: 100%;
}

#contact-register {
  background: var(--white);
  border-radius: 20px;
  margin: 0 auto;
  min-height: 400px;
  padding: 20px;
  width: 300px;
}

.register__header {
  border-bottom: 1px solid var(--gray);
  align-items: center;
  display: flex;
  justify-content: space-between;
  margin: 20px 0;
}

.register__header:first-of-type {
  margin-top: 0;
}

.register__header h3 {
  color: var(--dark-green);
}

.register__principal,
.register__pf,
.register__pj,
.register__phone,
.register__phonelist {
  align-items: center;
  display: flex;
  flex-direction: column;
  gap: 10px;
  justify-content: center;
  margin-bottom: 20px;
}

label:not(.switch__label, .switch) {
  width: 100%;
}

input,
select {
  background: var(--light-green);
  border: 1px solid var(--primary-text);
  border-radius: 4px;
  color: var(--dark-green);
  padding: 12px 4px;
  width: 100%;
}

input:focus,
select:focus {
  outline: none;
}

select {
  margin-top: 10px;
}

.principal__switches {
  align-items: center;
  display: flex;
  gap: 10px;
  justify-content: center;
  width: 100%;
}

.principal__switches label {
  font-size: 0.8rem;
  margin-right: 5px;
}

.switch__pf,
.switch__pj {
  align-items: center;
  display: flex;
}

/* START TOGGLE SWITCH */
.switch {
  display: inline-block;
  height: 20px;
  position: relative;
  width: 40px;
}

.switch input { 
  height: 0;
  opacity: 0;
  width: 0;
}

.slider {
  background-color: var(--light-red);
  cursor: pointer;
  bottom: 0;
  left: 0;
  position: absolute;
  right: 0;
  top: 0;

  transition: .4s;
  -webkit-transition: .4s;
}

.slider:before {
  background-color: var(--white);
  bottom: 4px;
  content: "";
  height: 12px;
  left: 4px;
  position: absolute;
  width: 16px;

  transition: .4s;
  -webkit-transition: .4s;
}

input:checked + .slider {
  background-color: var(--green);
}

input:focus + .slider {
  box-shadow: 0 0 1px var(--green);
}

input:checked + .slider:before {
  transform: translateX(16px);
  -ms-transform: translateX(16px);
  -webkit-transform: translateX(16px);
}

/* Rounded sliders */
.slider.round {
  border-radius: 34px;
}

.slider.round:before {
  border-radius: 10px;
}
/* END TOGGLE SWITCH */

.button--green {
  width: 100% !important;
}

.button--green:hover {
  transform: scale(1.01);
}

.button--red {
  border-radius: 10px;
  padding: 8px 20px;
  text-align: center;
  width: 100px;
}

.phone {
  align-items: center;
  display: flex;
  gap: 10px;
  justify-content: space-between;
  width: 100%;
}

@media (min-width: 768px) {
  #contact-register {
    width: 500px;
  }
}
</style>
