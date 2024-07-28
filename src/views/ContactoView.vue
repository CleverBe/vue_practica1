<template>
  <div>
    <h1>{{ title }}</h1>
    <button @click="setIsCreating()">Añadir Contacto</button>
    <div>
      <p v-if="errors.length > 0" style="color: red">{{ errorMessage }}</p>
    </div>
    <div style="margin: 10px">
      <label for="search" style="margin-right: 10px">Filtrar</label>
      <input id="search" type="text" v-model="searchTerm" />
    </div>
    <table>
      <thead>
        <tr>
          <th>ID</th>
          <th>Nombre</th>
          <th>Email</th>
          <th>Address</th>
          <th>Phone</th>
          <th>Country</th>
          <th>City</th>
          <td></td>
        </tr>
        <tr v-if="isCreating">
          <th></th>
          <th><input type="text" v-model="newContact.name" autofocus /></th>
          <th><input type="text" v-model="newContact.email" /></th>
          <th><input type="text" v-model="newContact.address" /></th>
          <th><input type="text" v-model="newContact.phone" /></th>
          <th><input type="text" v-model="newContact.country" /></th>
          <th><input type="text" v-model="newContact.city" /></th>
          <th style="">
            <button @click="addContact">Guardar</button>
            <button @click="cancelCreating()">X</button>
          </th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="contact in filteredContacts" :key="contact.id">
          <td>{{ contact.id }}</td>
          <td v-if="isEditing !== contact.id">{{ contact.name }}</td>
          <td v-else><input type="text" v-model="newContact.name" /></td>
          <td v-if="isEditing !== contact.id">{{ contact.email }}</td>
          <td v-else><input type="text" v-model="newContact.email" /></td>
          <td v-if="isEditing !== contact.id">{{ contact.address }}</td>
          <td v-else><input type="text" v-model="newContact.address" /></td>
          <td v-if="isEditing !== contact.id">{{ contact.phone }}</td>
          <td v-else><input type="text" v-model="newContact.phone" /></td>
          <td v-if="isEditing !== contact.id">{{ contact.country }}</td>
          <td v-else><input type="text" v-model="newContact.country" /></td>
          <td v-if="isEditing !== contact.id">{{ contact.city }}</td>
          <td v-else><input type="text" v-model="newContact.city" /></td>
          <td>
            <button
              v-if="isEditing !== contact.id"
              @click="setIsEditing(contact)"
            >
              Edit
            </button>
            <button v-else @click="saveChanges(contact.id)">Save</button>
            <button
              v-if="isEditing !== contact.id"
              @click="deleteContact(contact.id)"
            >
              Delete
            </button>
          </td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script>
export default {
  name: "ContactoView",
  data() {
    return {
      title: "Lista de contactos",
      contacts: [
        {
          id: 1,
          name: "John",
          email: "a@a.com",
          address: "123 Main St",
          phone: "555-555-5555",
          country: "USA",
          city: "New York",
        },
        {
          id: 2,
          name: "Jane",
          email: "b@b.com",
          address: "456 Oak St",
          phone: "666-666-6666",
          country: "Canada",
          city: "Toronto",
        },
        {
          id: 3,
          name: "Bob",
          email: "c@c.com",
          address: "789 Elm St",
          phone: "777-777-7777",
          country: "Mexico",
          city: "Mexico City",
        },
        {
          id: 4,
          name: "Alice",
          email: "d@d.com",
          address: "101 Maple St",
          phone: "888-888-8888",
          country: "France",
          city: "Paris",
        },
        {
          id: 5,
          name: "Charlie",
          email: "e@e.com",
          address: "202 Oak St",
          phone: "999-999-9999",
          country: "Germany",
          city: "Berlin",
        },
        {
          id: 6,
          name: "David",
          email: "f@f.com",
          address: "303 Maple St",
          phone: "111-111-1111",
          country: "Spain",
          city: "Madrid",
        },
      ],
      newContact: {
        name: "",
        email: "",
        address: "",
        phone: "",
        country: "",
        city: "",
      },
      isCreating: false,
      isEditing: false,
      errors: [],
      errorMessage: "",
      searchTerm: "",
    };
  },
  methods: {
    deleteContact(id) {
      this.contacts = this.contacts.filter((contact) => contact.id !== id);
    },
    setIsCreating() {
      if (this.isEditing) return;
      this.isCreating = true;
    },
    cancelCreating() {
      this.isCreating = false;
      this.errors = [];
      this.errorMessage = "";
      this.newContact = {
        name: "",
        email: "",
        address: "",
        phone: "",
        country: "",
        city: "",
      };
    },
    validateBody() {
      this.errors = [];
      this.errorMessage = "";

      if (!this.newContact.name) {
        this.errors.push("Name is required");
      }
      if (!this.newContact.email) {
        this.errors.push("Email is required");
      }
      if (!this.newContact.address) {
        this.errors.push("Address is required");
      }
      if (!this.newContact.phone) {
        this.errors.push("Phone is required");
      }
      if (!this.newContact.country) {
        this.errors.push("Country is required");
      }
      if (!this.newContact.city) {
        this.errors.push("City is required");
      }
      this.errorMessage = this.errors.join(" - ");
    },
    addContact() {
      this.validateBody();
      if (this.errors.length > 0) return;

      this.contacts.push({ id: this.contacts.length + 1, ...this.newContact });
      this.newContact = {
        name: "",
        email: "",
        address: "",
        phone: "",
        country: "",
        city: "",
      };
      this.isCreating = false;
    },
    setIsEditing({ id, ...contact }) {
      this.isCreating = false;
      this.newContact = contact;
      this.isEditing = id;
    },
    saveChanges(id) {
      this.validateBody();
      if (this.errors.length > 0) return;

      this.isEditing = false;

      const index = this.contacts.findIndex((contact) => contact.id === id);
      this.contacts[index] = {
        ...this.contacts[index],
        ...this.newContact,
      };
      this.newContact = {
        name: "",
        email: "",
        address: "",
        phone: "",
        country: "",
        city: "",
      };
    },
  },
  computed: {
    filteredContacts() {
      return this.contacts.filter(
        (contact) =>
          contact.name.toLowerCase().includes(this.searchTerm.toLowerCase()) ||
          contact.email.toLowerCase().includes(this.searchTerm.toLowerCase())
      );
    },
  },
};
</script>

<style scope>
h1 {
  color: #42b983;
}
table {
  width: 100%;
  margin: 20px auto;
  max-width: 1500px;
  border-collapse: collapse;
}
th,
td {
  border: 1px solid #ddd;
  padding: 8px;
  width: 195px;
}
th:first-child,
td:first-child {
  width: 100px;
}
th:last-child,
td:last-child {
  width: 100px;
}

th {
  background-color: #f2f2f2;
}
</style>
