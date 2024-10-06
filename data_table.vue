<template>
  <v-container fluid>
    <v-data-table
      :headers="computedHeaders"
      :items="filteredDesserts"
      :sort-by="[{ key: 'calories', order: 'asc' }]"

      class="elevation-2 data-table"
    >
      <template v-slot:top>
        <v-toolbar flat>
          <v-toolbar-title class="title">รายชื่อสมาชิก</v-toolbar-title>

          <v-divider class="mx-4" inset vertical></v-divider>
          <v-spacer></v-spacer>

          <div class="search">
            <v-text-field
              v-model="search"
              append-icon="mdi-magnify"
              label="ค้นหา"
              class="search-field"
              outlined
            ></v-text-field>
          </div>

          <v-dialog v-model="dialog" max-width="500px">
            <template v-slot:activator="{ props }">
              <v-btn class="mb-2 primary" dark v-bind="props">
                เพิ่มข้อมูล
              </v-btn>
            </template>
            <v-card>
              <v-card-title>
                <span class="text-h5">{{ formTitle }}</span>
              </v-card-title>

              <v-card-text>
                <v-container>
                  <v-row>
                    <v-col cols="12" md="4" sm="6">
                      <v-text-field v-model="editedItem.id" label="ID" outlined></v-text-field>
                    </v-col>
                    <v-col cols="12" md="4" sm="6">
                      <v-text-field v-model="editedItem.username" label="Username" outlined></v-text-field>
                    </v-col>
                    <v-col cols="12" md="4" sm="6">
                      <v-text-field v-model="editedItem.password" label="Password" type="password" outlined></v-text-field>
                    </v-col>
                    <v-col cols="12" md="4" sm="6">
                      <v-text-field v-model="editedItem.email" label="Email" outlined></v-text-field>
                    </v-col>
                    <v-col cols="12" md="4" sm="6">
                      <v-text-field v-model="editedItem.status" label="Status" outlined></v-text-field>
                    </v-col>
                    <v-col cols="12" md="4" sm="6">
                      <v-file-input
                        label="Upload Picture"
                        accept="image/*"
                        prepend-icon="mdi-camera"
                        @change="handleFileUpload"
                      ></v-file-input>
                    </v-col>
                  </v-row>
                </v-container>
              </v-card-text>
              <v-card-actions>
                <v-spacer></v-spacer>
                <v-btn class="cancel-btn" variant="text" @click="close">Cancel</v-btn>
                <v-btn class="save-btn" variant="text" @click="save">Save</v-btn>
              </v-card-actions>
            </v-card>
          </v-dialog>

          <v-dialog v-model="dialogDelete" max-width="500px">
            <v-card>
              <v-card-title class="text-h5">
                คุณแน่ใจหรือไม่ที่จะลบข้อมูลนี้?
              </v-card-title>
              <v-card-actions>
                <v-spacer></v-spacer>
                <v-btn class="cancel-btn" variant="text" @click="closeDelete">Cancel</v-btn>
                <v-btn class="save-btn" variant="text" @click="deleteItemConfirm">OK</v-btn>
                <v-spacer></v-spacer>
              </v-card-actions>
            </v-card>
          </v-dialog>
        </v-toolbar>
      </template>

      <template v-slot:item.actions="{ item }">
        <v-icon class="me-2 my-custom-edit-icon" size="small" @click="editItem(item)">
          mdi-pencil
        </v-icon>
        <v-icon class="my-custom-delete-icon" size="small" @click="deleteItem(item)"> mdi-delete </v-icon>
      </template>
      <template v-slot:no-data>
        <v-btn class="primary" @click="initialize"> Reset </v-btn>
      </template>
    </v-data-table>
  </v-container>
</template>

<script>
import axios from "axios";

export default {
  data: () => ({
    dialog: false,
    dialogDelete: false,
    search: '',
    headers: [
      { title: 'ID', align: 'start', sortable: false, key: 'id' },
      { title: 'Username', key: 'username' },
      { title: 'Password', key: 'password' },
      { title: 'Email', key: 'email' },
      { title: 'Status', key: 'status' },
      { title: 'Picture', key: 'picture' },
      { title: 'Edit/Del', key: 'actions', sortable: false },
    ],
    desserts: [],
    editedIndex: -1,
    editedItem: {
      id: '',
      username: '',
      password: '',
      email: '',
      status: '',
      picture: '',
    },
    defaultItem: {
      id: '',
      username: '',
      password: '',
      email: '',
      status: '',
      picture: '',
    },
  }),

  computed: {
    formTitle() {
      return this.editedIndex === -1 ? 'เพิ่มข้อมูล' : 'แก้ไขข้อมูล';
    },
    computedHeaders() {
      return this.headers;
    },
    filteredDesserts() {
      if (this.search) {
        return this.desserts.filter(dessert => {
          return Object.values(dessert).some(value =>
            String(value).toLowerCase().includes(this.search.toLowerCase())
          );
        });
      }
      return this.desserts;
    },
  },

  watch: {
    dialog(val) {
      val || this.close();
    },
    dialogDelete(val) {
      val || this.closeDelete();
    },
  },

  async created() {
    this.fetchData();
  },

  methods: {
    initialize() {
      this.desserts = [];
    },

    editItem(item) {
      this.editedIndex = this.desserts.indexOf(item);
      this.editedItem = Object.assign({}, item);
      this.dialog = true;
    },

    close() {
      this.dialog = false;
      this.$nextTick(() => {
        this.editedItem = Object.assign({}, this.defaultItem);
        this.editedIndex = -1;
      });
    },

    closeDelete() {
      this.dialogDelete = false;
      this.$nextTick(() => {
        this.editedItem = Object.assign({}, this.defaultItem);
        this.editedIndex = -1;
      });
    },

    async save() {
      if (this.editedIndex > -1) {
        try {
          const response = await axios.post("http://localhost:7000/update", this.editedItem);
          const data = response.data;
          if (data.ok) {
            Object.assign(this.desserts[this.editedIndex], this.editedItem);
            alert('Item updated successfully');
          } else {
            alert(data.error);
          }
        } catch (error) {
          alert(error.message || 'An error occurred while updating the item');
        }
      } else {
        try {
          const response = await axios.post("http://localhost:7000/insert", this.editedItem);
          const data = response.data;
          if (data.ok) {
            this.desserts.push(this.editedItem);
            alert('Item added successfully');
          } else {
            alert(data.error);
          }
        } catch (error) {
          alert(error.message || 'An error occurred while adding the item');
        }
      }
      this.close();
    },

    async deleteItem(item) {
      if (confirm('Are you sure you want to delete this item?')) {
        try {
          const response = await axios.post("http://localhost:7000/delete", item);
          const data = response.data;
          if (data.ok) {
            this.desserts = this.desserts.filter(dessert => dessert.id !== item.id);
            alert('Item deleted successfully');
          } else {
            alert(data.error || 'Failed to delete item');
          }
        } catch (error) {
          alert(error.message || 'An error occurred while deleting the item');
        }
      }
    },

    async fetchData() {
      try {
        const response = await axios.get("http://localhost:7000/list");
        this.desserts = response.data.row;
      } catch (error) {
        alert('Failed to refresh data');
      }
    },

    handleFileUpload(event) {
      const file = event.target.files[0];
      if (file) {
        const reader = new FileReader();
        reader.onload = (e) => {
          this.editedItem.picture = e.target.result;
        };
        reader.readAsDataURL(file);
      }
    },
  },
};
</script>

<style scoped>
.data-table {
  background-color: #f9f9f9;
  border-radius: 12px;
  overflow: hidden;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
}

.title {
  font-size: 1.5em;
  font-weight: 600;
  color: #4a4a4a;
}

.search-field {
  margin-right: 20px;
}

.my-custom-edit-icon,
.my-custom-delete-icon {
  cursor: pointer;
  transition: transform 0.2s;
}

.my-custom-edit-icon:hover,
.my-custom-delete-icon:hover {
  transform: scale(1.2);
}

.save-btn {
  background-color: #4caf50;
  color: white;
  border-radius: 5px;
}

.cancel-btn {
  color: #f44336;
}

.v-toolbar {
  background-color: #e3f2fd;
}

.v-data-table-header {
  background-color: #2196f3;
  color: white;
}

.v-data-table {
  border-radius: 12px;
}
</style>
