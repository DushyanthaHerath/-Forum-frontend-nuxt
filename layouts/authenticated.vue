<template>
  <v-app id="inspire">
    <v-app-bar
      app
      color="blue accent-4"
      flat
    >
      <v-container class="py-0 fill-height">
        <v-menu
          v-model="showMenu"
          absolute
          offset-y
          style="max-width: 600px"
        >
          <template v-slot:activator="{ on, attrs }">
            <v-avatar
              class="mr-10"
              color="red"
              size="36"
              v-bind="attrs"
              v-on="on"
            ><v-img v-if="avatar.length > 2" :src="avatar"></v-img><span v-if="avatar.length < 3" class="white--text text-h6">{{ avatar }}</span></v-avatar>
          </template>
          <v-list>
            <v-list-item>
              <v-list-item-title @click="logout">Log Out</v-list-item-title>
            </v-list-item>
          </v-list>
        </v-menu>

        <v-btn
          v-for="link in links"
          :key="link.title"
          text
          color="white"
          :to="link.to"
        >
          {{ link.title }}
        </v-btn>

        <v-spacer></v-spacer>

        <v-responsive max-width="260">
          <v-text-field
            dense
            flat
            hide-details
            rounded
            solo-inverted
            placeholder="Search"
          ></v-text-field>
        </v-responsive>
      </v-container>
    </v-app-bar>

    <v-main class="grey lighten-3">
      <v-container>
        <v-row>
          <v-col cols="2">
            <v-sheet rounded="lg">
              <v-list>
                <v-list-item>
                  <v-list-item-avatar>
                    <v-img :src="avatar"></v-img>
                  </v-list-item-avatar>
                </v-list-item>

                <v-list-item link>
                  <v-list-item-content>
                    <v-list-item-title class="text-h6">
                      {{ name }}
                    </v-list-item-title>
                    <v-list-item-subtitle></v-list-item-subtitle>
                  </v-list-item-content>

                  <v-list-item-action>
                    <v-icon>mdi-menu-down</v-icon>
                  </v-list-item-action>
                </v-list-item>
              </v-list>
              <v-divider></v-divider>
              <v-list shaped>
                <v-subheader>Categories</v-subheader>
                <v-list-item-group
                  v-model="selectedItem"
                  color="primary"
                >
                  <v-list-item
                    v-for="(item, i) in items"
                    :key="i"
                  >
                    <v-list-item-icon>
                      <v-icon v-text="item.action"></v-icon>
                    </v-list-item-icon>
                    <v-list-item-content>
                      <v-list-item-title v-text="item.title"></v-list-item-title>
                    </v-list-item-content>
                  </v-list-item>
                </v-list-item-group>
              </v-list>
            </v-sheet>
          </v-col>

          <v-col>
            <v-sheet
              min-height="70vh"
              rounded="lg"
            >
              <slot />
            </v-sheet>
          </v-col>
        </v-row>
      </v-container>
    </v-main>
  </v-app>
</template>

<script>
export default {
  data: () => ({
    menu: [
      { title: 'Home', to: '/', restricted: false },
      { title: 'Dashboard', to: '/', restricted: false },
      { title: 'Posts', to: '/post-dashboard', restricted: true},
      { title: 'Profile', to: '/', restricted: false },
      { title: 'Updates', to: '/', restricted: false },
    ],
    showMenu: false,
    selectedItem: 0,
    items: [
      {
        action: 'mdi-ticket',
        title: 'Attractions',
      },
      {
        action: 'mdi-silverware-fork-knife',
        active: true,
        title: 'Dining',
      },
      {
        action: 'mdi-school',
        title: 'Education',
      },
      {
        action: 'mdi-human-male-female-child',
        title: 'Family',
      },
      {
        action: 'mdi-bottle-tonic-plus',
        title: 'Health',
      },
      {
        action: 'mdi-briefcase',
        title: 'Office',
      },
      {
        action: 'mdi-tag',
        title: 'Promotions',
      },
    ],
  }),
  methods: {
    logout() {
      this.$auth.logout()
    }
  },
  computed: {
    avatar() {
      return this.$store.getters['access/user/getUser'].avatar || this.$store.getters['access/user/getUser'].name.substring(0,2).toUpperCase();
    },
    name() {
      return this.$store.getters['access/user/getUser'].name;
    },
    email() {
      return this.$store.getters['access/user/getUser'].email;
    },
    links() {
      if(this.$store.getters['access/user/getUser'].role_id == '2') {
        return this.menu.filter(function (item) {
          return !item.restricted;
        });
      }
      return this.menu;
    }
  },
  created() {
    this.$store.commit('access/user/setUser', this.$auth.user);
  }
}
</script>
