<template>
  <BreezeAuthenticatedLayout>
    <v-data-table
      :headers="columns"
      :items="posts"
      :options.sync="options"
      sort-by="calories"
      class="elevation-1"
      :server-items-length="length"
    >
      <template v-slot:top>
        <v-toolbar
          flat
        >
          <v-toolbar-title>Post Dashboard</v-toolbar-title>
          <v-divider
            class="mx-4"
            inset
            vertical
          ></v-divider>
          <v-spacer></v-spacer>
          <v-dialog v-model="dialogDelete" max-width="500px">
            <v-card>
              <v-card-title class="text-h5">Are you sure you want to delete this item?</v-card-title>
              <v-card-actions>
                <v-spacer></v-spacer>
                <v-btn color="blue darken-1" text @click="closeDelete">Cancel</v-btn>
                <v-btn color="blue darken-1" text @click="deleteItemConfirm">OK</v-btn>
                <v-spacer></v-spacer>
              </v-card-actions>
            </v-card>
          </v-dialog>
          <v-dialog
            v-model="approveDialog"
            persistent
            max-width="290"
          >
            <template v-slot:activator="{ on, attrs }">
              <v-btn
                color="primary"
                dark
                v-bind="attrs"
                v-on="on"
              >
                Open Dialog
              </v-btn>
            </template>
            <v-card>
              <v-card-title class="text-h5">
                Approve Post?
              </v-card-title>
              <v-card-text>Do you want to approve this post?</v-card-text>
              <v-card-actions>
                <v-spacer></v-spacer>
                <v-btn
                  color="green darken-1"
                  text
                  @click="approveDialog = false"
                >
                  Cancel
                </v-btn>
                <v-btn
                  color="green darken-1"
                  text
                  @click="approvePost"
                >
                  Approve
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
          @click="approveConfirm(item)"
        >
          mdi-check
        </v-icon>
        <v-icon
          small
          @click="deleteItem(item)"
        >
          mdi-delete
        </v-icon>
      </template>
      <template v-slot:no-data>
        <v-btn
          color="primary"
          @click="initialize"
        >
          Reset
        </v-btn>
      </template>
    </v-data-table>
    <v-snackbar
      v-model="snackbar"
    >
      {{ message }}

      <template v-slot:action="{ attrs }">
        <v-btn
          color="pink"
          text
          v-bind="attrs"
          @click="snackbar = false"
        >
          Close
        </v-btn>
      </template>
    </v-snackbar>

  </BreezeAuthenticatedLayout>
</template>

<script>
import BreezeAuthenticatedLayout from "@/layouts/authenticated.vue";

export default {
  name: "post-dashboard",

  components: {
    BreezeAuthenticatedLayout,
  },

  data: () => ({
    dialog: false,
    dialogDelete: false,
    approveDialog: false,
    posts: [],
    options: {},
    columns: [
      {
        text: 'Title',
        align: 'start',
        sortable: false,
        value: 'title',
      },
      {
        text: 'Category',
        align: 'start',
        sortable: false,
        value: 'category',
      },
      {
        text: 'Posted at',
        align: 'start',
        sortable: false,
        value: 'created_at',
      },
      { text: 'Actions', value: 'actions', sortable: false },
    ],
    snackbar: false,
    message: '',
    post: {}
  }),

  methods: {
    async fetchPosts() {
      let response = await this.$axios.$get(`/api/v1/posts/all?page=${this.options.page}&per_page=6`);
      this.length = response.DATA.last_page;
      this.posts = response.DATA.data;
    },
    approveConfirm(item) {
      this.approveDialog = true;
      this.post = item;
    },
    async approvePost() {
      console.log(this.post);
      if (this.post.id) {
        let response = await this.$axios.$post('/api/v1/posts/approve', { id: this.post.id });
        this.message = response.MESSAGE;
        this.snackbar = true;
        this.approveDialog = false;
      }
    },
    deleteItem() {

    },
    initialize() {

    },
    deleteItemConfirm() {
      this.closeDelete()
    },
    closeDelete() {
      this.dialogDelete = false
      this.$nextTick(() => {
        this.editedItem = Object.assign({}, this.defaultItem)
        this.editedIndex = -1
      })
    },
  },

  mounted() {
    this.fetchPosts();
  },

  watch: {
    options: {
      handler () {
        this.fetchPosts();
      },
      deep: true
    },
  },
}
</script>

<style scoped>

</style>
