<template>

  <v-card>
    <v-toolbar color="green">
      <v-app-bar-nav-icon></v-app-bar-nav-icon>

      <v-toolbar-title>Tablero principal</v-toolbar-title>

      <v-spacer></v-spacer>

      <v-btn icon>
        <v-icon>mdi-magnify</v-icon>
      </v-btn>

      <v-btn icon>
        <v-icon>mdi-dots-vertical</v-icon>
      </v-btn>

      <template v-slot:extension>
        <v-tabs v-model="tab" align-tabs="title">

          <v-tab v-for="item in itemsTab" :key="item" :value="item">
            {{ item }}

          </v-tab>
        </v-tabs>
      </template>
    </v-toolbar>

    <v-window v-model="tab">

      <v-window-item v-for="item in itemsTab" :key="item" :value="item">
        <v-card flat>

          <v-data-table :custom-filter="filterOnlyCapsText" :headers="headers" :items="items" :search="search"
            :item-value="name">

            <template v-slot:top>
              <v-text-field v-model="search" class="pa-2" label="Search">

              </v-text-field>

            </template>

            <template v-slot:[`item.actions`]="{ item }">
              <v-icon small class="mr-2" @click="viewItem(item)">
                mdi-eye
              </v-icon>
              <v-icon small @click="editItem(item)">
                mdi-lead-pencil
              </v-icon>

            </template>

          </v-data-table>

        </v-card>

        <template v-slot:activator="{ on, attrs }">
          <v-btn color="primary" dark class="mb-2" v-bind="attrs" v-on="on">
            New Item
          </v-btn>
        </template>
      </v-window-item>

    </v-window>

  </v-card>

  <v-dialog v-model="dialog" max-width="500px">
    <v-card>
      <v-card-title>
        <span class="text-h5">{{ formTitle }}</span>
      </v-card-title>

      <v-card-text>
        <v-container>
          <v-row>
            <v-col cols="12" sm="6" md="4">
              <v-text-field v-model="editedItem.name" label="name" required></v-text-field>
            </v-col>
            <v-col cols="12" sm="6" md="4">
              <v-text-field v-model="editedItem.cores" label="Cores" required @keypress="onlyNumber"></v-text-field>
            </v-col>
            <v-col cols="12" sm="6" md="4">
              <v-text-field v-model="editedItem.threads" label="Threads" required @keypress="onlyNumber"></v-text-field>
            </v-col>
            <v-col cols="12" sm="6" md="4">
              <v-text-field v-model="editedItem.baseClock" label="Base clock" required></v-text-field>
            </v-col>
            <v-col cols="12" sm="6" md="4">
              <v-text-field v-model="editedItem.boostClock" label="Boost clock" required></v-text-field>
            </v-col>
            <v-col cols="12" sm="6" md="4">
              <v-text-field v-model="editedItem.tdp" label="TDP (W)" required></v-text-field>
            </v-col>
          </v-row>
        </v-container>
      </v-card-text>

      <v-card-actions v-show="view === true">
        <v-spacer></v-spacer>
        <v-btn color="blue darken-1" text @click="close">
          Cancel
        </v-btn>
        <v-btn color="blue darken-1" text @click="save">
          Save
        </v-btn>
      </v-card-actions>
    </v-card>
  </v-dialog>
  <v-dialog v-model="dialogOk" max-width="500px">
    <v-card>
      <v-card-title class="text-h5">Elemento guardado de manera correcta</v-card-title>
      <v-card-actions>
        <v-spacer></v-spacer>
        <v-btn color="blue darken-1" text>OK</v-btn>
        <v-spacer></v-spacer>
      </v-card-actions>
    </v-card>
  </v-dialog>
</template>


<script>
export default {

  data() {

    return {
      tab: null,
      dialog: false,
      dialogOk: false,
      view: false,
      items: [],
      editedIndex: -1,
      editedItem: {
        name: '',
        cores: 0,
        threads: 0,
        baseClock: '',
        boostClock: '',
        tdp: ''
      }
      ,
      defaultItem: {
        name: '',
        cores: 0,
        threads: 0,
        baseClock: '',
        boostClock: '',
        tdp: ''
      },
      itemsTab: [
        'Consulta', 'Edición', 'Agregar'
      ],
      text: '',
      search: '',
      headers: [
        {
          title: 'CPU Model',
          align: 'start',
          key: 'name',
        },
        {
          title: 'Cores',
          align: 'end',
          key: 'cores',
        },
        {
          title: 'Threads',
          align: 'end',
          key: 'threads',
        },
        {
          title: 'Base Clock',
          align: 'end',
          key: 'baseClock',
        },
        {
          title: 'Boost Clock',
          align: 'end',
          key: 'boostClock',
        },
        {
          title: 'TDP (W)',
          align: 'end',
          key: 'tdp',
        },
        {
          title: 'Ver',
          align: 'end',
          key: 'ver',
        },
        { text: 'Actions', align: 'start', value: 'actions', sortable: false },

      ],

      items: [
        {
          name: 'Intel Core i9-11900K',
          cores: 8,
          threads: 16,
          baseClock: '3.5 GHz',
          boostClock: '5.3 GHz',
          tdp: '125W',
        },
        {
          name: 'AMD Ryzen 9 5950X',
          cores: 16,
          threads: 32,
          baseClock: '3.4 GHz',
          boostClock: '4.9 GHz',
          tdp: '105W',
        },
        {
          name: 'Intel Core i7-10700K',
          cores: 8,
          threads: 16,
          baseClock: '3.8 GHz',
          boostClock: '5.1 GHz',
          tdp: '125W',
        },
        {
          name: 'AMD Ryzen 5 5600X',
          cores: 6,
          threads: 12,
          baseClock: '3.7 GHz',
          boostClock: '4.6 GHz',
          tdp: '65W',
        },
        {
          name: 'Intel Core i5-10600K',
          cores: 6,
          threads: 12,
          baseClock: '4.1 GHz',
          boostClock: '4.8 GHz',
          tdp: '125W',
        },
        {
          name: 'AMD Ryzen 7 5800X',
          cores: 8,
          threads: 16,
          baseClock: '3.8 GHz',
          boostClock: '4.7 GHz',
          tdp: '105W',
        },
        {
          name: 'Intel Core i3-10100',
          cores: 4,
          threads: 8,
          baseClock: '3.6 GHz',
          boostClock: '4.3 GHz',
          tdp: '65W',
        },
        {
          name: 'AMD Ryzen 3 3300X',
          cores: 4,
          threads: 8,
          baseClock: '3.8 GHz',
          boostClock: '4.3 GHz',
          tdp: '65W',
        },
        {
          name: 'Intel Pentium Gold G6400',
          cores: 2,
          threads: 4,
          baseClock: '4.0 GHz',
          tdp: '58W',
        },
        {
          name: 'AMD Athlon 3000G',
          cores: 2,
          threads: 4,
          baseClock: '3.5 GHz',
          tdp: '35W',
        }
      ],


    }
  },
  methods: {


    viewItem(item) {
      this.editedIndex = this.items.indexOf(item)
      this.editedItem = Object.assign({}, item)

      this.dialog = true
      this.view = false
    },
    editItem(item) {
      this.editedIndex = this.items.indexOf(item)
      this.editedItem = Object.assign({}, item)
      this.view = true
      this.dialog = true
    },
    close() {
      this.dialog = false
      this.$nextTick(() => {
        this.editedItem = Object.assign({}, this.defaultItem)
        this.editedIndex = -1
      })
    },
    save() {
      if (this.editedIndex > -1) {
        Object.assign(this.items[this.editedIndex], this.editedItem)
      } else {
        this.items.push(this.editedItem)
      }
      this.close()
      this.dialogOk = true
    },
    onlyNumber($event) {
      let keyCode = ($event.keyCode ? $event.keyCode : $event.which);
      if ((keyCode < 48 || keyCode > 57)) {
        $event.preventDefault();
      }
    }
  },
  watch: {
    dialog(val) {
      val || this.close()
    },

  },
  computed: {
    formTitle() {
      return this.editedIndex === -1 ? 'New Item' : 'Edit Item'
    },
  },


}

</script>
