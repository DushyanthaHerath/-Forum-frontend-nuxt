<template>
  <BreezeAuthenticatedLayout>
    <v-toolbar
      flat
    >
      <v-toolbar-title>
        <v-menu offset-y>
          <template v-slot:activator="{ on, attrs }">
            <v-btn
              color="primary"
              dark
              v-bind="attrs"
              v-on="on"
            >
              Sort
              <v-icon
                right
                dark
              >
                mdi-sort
              </v-icon>
            </v-btn>
          </template>
          <v-list>
            <v-list-item>
              <v-list-item-title>Date</v-list-item-title>
            </v-list-item>
            <v-list-item>
              <v-list-item-title>Most Viewed</v-list-item-title>
            </v-list-item>
          </v-list>
        </v-menu>
      </v-toolbar-title>
      <v-divider
        class="mx-4"
        inset
        vertical
      ></v-divider>
      <v-spacer></v-spacer>
      <v-btn
        color="primary"
        dark
        class="mb-2"
        v-bind="attrs"
        v-on="on"
        @click="dialog=true"
      >
        New Post
      </v-btn>
    </v-toolbar>
    <v-row>
      <v-col>
        <v-list three-line>
          <template v-for="(item, index) in items">
            <v-subheader
              v-if="item.header"
              :key="item.header"
              v-text="item.header"
            ></v-subheader>

            <v-divider
              v-else-if="item.divider"
              :key="index"
              :inset="item.inset"
            ></v-divider>

            <v-list-item
              v-else
              :key="item.title"
            >
              <v-list-item-avatar>
                <v-img :src="item.avatar"></v-img>
              </v-list-item-avatar>

              <v-list-item-content>
                <v-list-item-title v-html="item.title"></v-list-item-title>
                <v-list-item-subtitle v-html="item.subtitle"></v-list-item-subtitle>
              </v-list-item-content>
            </v-list-item>
          </template>
        </v-list>
      </v-col>
    </v-row>
    <v-row>
      <v-col>
        <div class="text-center">
          <v-pagination
            v-model="page"
            :length="length"
            circle
          ></v-pagination>
        </div>
      </v-col>
    </v-row>

    <v-dialog
      v-model="dialog"
      fullscreen
      hide-overlay
      transition="dialog-bottom-transition"
    >
      <v-card>
        <v-toolbar
          dark
          color="primary"
        >
          <v-btn
            icon
            dark
            @click="dialog = false"
          >
            <v-icon>mdi-close</v-icon>
          </v-btn>
          <v-toolbar-title>New Post</v-toolbar-title>
          <v-spacer></v-spacer>
          <v-toolbar-items>
            <v-btn
              dark
              text
              @click="save"
            >
              Save
            </v-btn>
          </v-toolbar-items>
        </v-toolbar>
        <v-form
          ref="form"
          v-model="valid"
          lazy-validation
        >
          <v-col cols="6">
            <v-text-field
              v-model="form.title"
              :rules="titleRules"
              label="Title"
              required
            ></v-text-field>
            <v-textarea
              v-model="form.content"
              :rules="contentRules"
              name="input-7-1"
              filled
              label="Content"
              auto-grow
              required
            ></v-textarea>
            <v-select
              :items="categories"
              item-text="name"
              item-value="id"
              v-model="form.category_id"
              label="Post Categories"
              solo
              required
            ></v-select>
          </v-col>
        </v-form>
        <v-divider></v-divider>
        <v-list
          three-line
          subheader
        >
          <v-subheader>General</v-subheader>
          <v-list-item>
            <v-list-item-action>
              <v-checkbox v-model="notifications"></v-checkbox>
            </v-list-item-action>
            <v-list-item-content>
              <v-list-item-title>Notifications</v-list-item-title>
              <v-list-item-subtitle>Notify me once post is approved</v-list-item-subtitle>
            </v-list-item-content>
          </v-list-item>
        </v-list>
      </v-card>
    </v-dialog>
  </BreezeAuthenticatedLayout>
</template>

<script>
import BreezeAuthenticatedLayout from "@/layouts/authenticated.vue";

export default {
  data: () => ({
    items: [],
    page: 1,
    length: 0,
    dialog: false,
    valid: true,
    form: {
      title: '',
      content: '',
      category_id: 1
    },
    titleRules: [
      v => !!v || 'Title is required'
    ],
    contentRules: [
      v => !!v || 'Content is required'
    ],
    notifications: '',
    categories: []
  }),

  head: {
    title: "Dashboard",
  },

  middleware: "authenticated",

  components: {
    BreezeAuthenticatedLayout,
  },

  mounted() {
    // Get create data
    Promise.all([this.fetchPosts(), this.getCategories()]);
  },

  methods: {
    async fetchPosts() {
      let response = await this.$axios.$get(`/api/v1/posts/all?page=${this.page}&per_page=6`);
      this.length = response.DATA.last_page;
      if (response.DATA.data.length) {
        this.items = [];
        this.items.push({header: 'Today'});
        response.DATA.data.forEach((item, i) => {
          this.items.push({
            avatar: item.avatar,
            title: item.title,
            subtitle: `<span class="text--primary">${item.posted_by}</span> &mdash; ${item.content}`,
          });
          this.items.push({divider: true, inset: true});
        });
      }
    },
    async save() {
      let valid = await this.$refs.form.validate();
      if (valid) {
        let response = await this.$axios.$post('/api/v1/posts/save', this.form);
      }
    },
    async getCategories() {
      let response = await this.$axios.$get(`/api/v1/posts/categories`);
      this.categories = response.DATA;
    }
  },

  watch: {
    page(value) {
      this.fetchPosts();
    }
  }
};
</script>
