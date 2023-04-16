<template>
  <div id="app" class="container">
    <br>
    
    <b-nav tabs align ='center'>
      <b-nav-item  @click="contact = null; searchMode=false">Все услуги</b-nav-item>
      <b-nav-item @click="addContact">Найти услугу</b-nav-item>
    </b-nav>
    <img  src="https://pitbox.ru/wp-content/uploads/2015/08/pokraska-avto.jpg" alt="Альтернативный текст" width="1250" height="250">

    <hr>
    <div v-if="!searchMode">
    <h3 align ='center' v-if="contacts.length > 0">Все услуги</h3>
    <b-table striped hover :items="contacts" @row-clicked="contactClick"></b-table>
  </div>

  <b-form @submit.prevent="postContact" v-if="contact && searchMode">
      <b-form-input type="text" v-model="search" placeholder="Поиск"></b-form-input>
    <b-list v-if="search.length > 0">
      <b-list-item v-for="contact in filteredContacts" :key = "contact.id">{{ contact.name }} - {{ contact.price }}<br></b-list-item>
    </b-list>
  </b-form>
  </div>
</template>
<script>
export default {
  name: 'App',
  data() {
    return {
      searchMode: false, 
      contacts: [],
      contact: null, 
      search: ''
    }
  },
  computed: {
    filteredContacts: function() {
      var self = this;
      return this.contacts.filter(function(contact) {
        return contact.name.toLowerCase().indexOf(self.search.toLowerCase()) !== -1 || contact.number.toLowerCase().indexOf(self.search.toLowerCase()) !== -1;
      });
    }
  },

  methods: {
    getContacts() {
      this.axios.get("api/contacts").then((response) => {
          this.contacts = response.data
      })
    },
    postContact() {
      if(this.contact.id > 0)
        this.axios.post("api/contact", this.contact).then(() => {
            this.searchMode = false
        }) 
      else 
        this.axios.put("api/contacts", this.contact).then((response) => {
              this.searchMode = false
              this.contact.id = response.data.insertId
              this.contacts.push(this.contact)
          })    
    },
    contactClick(contact) {
      this.contact = contact
      this.searchMode = false
    },
    addContact() {
      this.contact = {
        id: 0,
        name: '',
        number: ''
      }
      this.searchMode = true
    }
  },
  created() {
    this.getContacts()
  }
}
</script>