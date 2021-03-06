<template>
  <v-data-table
    :headers="headers"
    :items="userRoles"
    :loading="loading"
    class="elevation-1"
  >
    <template v-slot:top>
      <v-toolbar
        flat
      >
        <v-toolbar-title>User Roles</v-toolbar-title>
        <v-divider
          class="mx-4"
          inset
          vertical
        ></v-divider>
        <v-spacer></v-spacer>
        <v-dialog
          v-model="dialog"
          max-width="500px"
        >
          <template v-slot:activator="{ on, attrs }">
            <v-btn
              color="primary"
              dark
              class="mb-2"
              v-bind="attrs"
              v-on="on"
            >
              New Item
            </v-btn>
          </template>
          <v-card>
            <v-card-title>
              <span class="headline">{{ formTitle }}</span>
            </v-card-title>

            <v-card-text>
              <v-container>
                <v-row>
                  <v-col
                    cols="12"
                    sm="6"
                    md="6"
                  >
                    <v-autocomplete
                      v-model="editedItem.userId"
                      :items="users"
                      outlined
                      :loading="loadingUsers"
                      label="Select a user"
                      item-text="name"
                      item-value="id"
                      return-id
                    ></v-autocomplete>
                  </v-col>
                  <v-col
                    cols="12"
                    sm="6"
                    md="6"
                  >
                     <v-autocomplete
                      v-model="editedItem.roleId"
                      :items="roles"
                      :loading="loadingRoles"
                      outlined
                      label="Select a role"
                      item-text="title"
                      item-value="id"
                      return-id
                    ></v-autocomplete>
                  </v-col>
                </v-row>
              </v-container>
            </v-card-text>

            <v-card-actions>
              <v-spacer></v-spacer>
              <v-btn
                color="red darken-1"
                text
                @click="close"
                :loading="saving"
              >
                Cancel
              </v-btn>
              <v-btn
                color="blue darken-1"
                text
                @click="save"
                :loading="saving"
              >
                Save
              </v-btn>
            </v-card-actions>
          </v-card>
        </v-dialog>

      </v-toolbar>
    </template>
    <template v-slot:item.actions="{ item }">
      <v-icon
        small
        class="mr-2"
        @click="editItem(item)"
      >
        mdi-pencil
      </v-icon>

        <v-icon
          small
          class="mr-2"
          @click="deleteItem(item)"
        >
          mdi-delete
      </v-icon>
    </template>
    <v-dialog v-model="dialogDelete" max-width="500px">
      <v-card>
        <v-card-title class="headline">Are you sure you want to delete this item?</v-card-title>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="blue darken-1" text @click="closeDelete">Cancel</v-btn>
          <v-btn color="blue darken-1" text @click="deleteItemConfirm">OK</v-btn>
          <v-spacer></v-spacer>
        </v-card-actions>
      </v-card>
    </v-dialog>
  </v-data-table>
</template>

<script>
  export default {
    layout: 'basic',

    data: () => ({
      dialog: false,
      dialogDelete: false,
      loading: false,
      loadingUsers: false,
      loadingRoles: false,
      saving: false,
      headers: [
        {
          text: 'Name',
          align: 'start',
          sortable: false,
          value: 'user.name',
        },
        { text: 'User Id', value: 'userId' },
        {
          text: 'Role Title',
          align: 'start',
          sortable: false,
          value: 'role.title',
        },
        {
          text: 'Role Type',
          align: 'start',
          sortable: false,
          value: 'role.type',
        },
        { text: 'Role Id', value: 'roleId' },
        { text: 'Actions', value: 'actions', sortable: false },
      ],
      users: [],
      roles:[],
      userRoles: [],
      editedIndex: -1,

      editedItem: {
        userId: '',
        roleId: ''
      },

      defaultItem: {
        userId: '',
        roleId: ''
      },
    }),

    computed: {
      formTitle () {
        return this.editedIndex === -1 ? 'New Item' : 'Edit Item'
      },
    },

    watch: {
      dialog (val) {
        val || this.close()
      },

      dialogDelete (val) {
        val || this.closeDelete()
      },
    },

    created () {
      this.fetchUsers()
      this.fetchRoles()
      this.initialize()
    },

    methods: {
      initialize () {
        this.loading = true
        this.$axios.get('/user-role?include=userRole.user,userRole.role')
        .then(response => {
            this.userRoles = response.data.data
        })
        .finally(()=>{
          this.loading = false
        })
      },

      fetchUsers() {
        this.loadingUsers = true 
        this.$axios.get('/user')
        .then(response => {
            this.users = response.data.data
        })
        .finally(() => {
          this.loadingUsers = false
        })
      },

      fetchRoles() {
        this.loadingRoles = true 
        this.$axios.get('/role')
        .then(response => {
            this.roles = response.data.data
        })
        .finally(() => {
          this.loadingRoles = false
        })
      },

      editItem (item) {
        this.editedIndex = this.userRoles.indexOf(item)
        this.editedItem = Object.assign({}, item)
        this.dialog = true
      },

      deleteItem (item) {
        this.editedIndex = this.userRoles.indexOf(item)
        this.editedItem = Object.assign({}, item)
        this.dialogDelete = true
      },

      deleteItemConfirm () {
        this.$axios.delete('/user-role/'+this.editedItem.id)
        this.userRoles.splice(this.editedIndex, 1)
        this.closeDelete()
      },

      close () {
        this.dialog = false
        this.$nextTick(() => {
          this.editedItem = Object.assign({}, this.defaultItem)
          this.editedIndex = -1
        })
      },

      closeDelete () {
        this.dialogDelete = false
        this.$nextTick(() => {
          this.editedItem = Object.assign({}, this.defaultItem)
          this.editedIndex = -1
        })
      },

      save () {
        //TODO: will have to fixed update loading issues
        this.saving = true
        if (this.editedIndex > -1) {
          const updateData = {
            userId: this.editedItem.userId,
            roleId: this.editedItem.roleId
          }
          this.$axios.put('/user-role/'+ this.editedItem.id, updateData)
            .then(response => {
                this.initialize()
            })
            .finally(()=> {
              this.saving = false
            })
        } else {
            this.$axios.post('/user-role', this.editedItem)
            .then(response => {
                this.userRoles.push(this.editedItem)
            })
            .finally(()=> {
              this.saving = false
            })
        }
        this.close()
      },
    },
  }
</script>