<template>
  <v-app id="inspire-login">
    <div
      class="grey pr-0 pl-0 mr-0 ml-0" style="height: 100vh"
    >
      <v-row class="fill-height dense no-gutters">
        <v-col
          cols="4"
          sm="0"
          md="4" class="fill-height">
          <v-img
            :src="require('~/assets/images/login.jpg')"
            gradient="to top right, rgba(100,115,201,.33), rgba(25,32,72,.7)"
            class="fill-height"
            cover
          ></v-img>
        </v-col>
        <v-col
          cols="8"
          sm="12"
          md="8" class="fill-height">
          <div class="login-space align-center d-flex justify-center fill-height" style="width: 100%">
            <v-card class="elevation-1 login-form" color="transparent">
              <form @submit.prevent="submit">
                <v-card-title v-if="form.errors.length">
                  <v-alert
                    color="#C51162"
                    dark
                    icon="mdi-material-design"
                    border="right"
                    v-for="(error,i) in form.errors"
                    :key="i"
                  >{{ error }}</v-alert>
                </v-card-title>
                <v-card-text>
                  <v-text-field
                    prepend-icon="mdi-account"
                    v-model="form.email"
                    name="login"
                    label="Login"
                    type="text"
                    solo
                  ></v-text-field>
                  <v-text-field
                    v-model="form.password"
                    id="password"
                    prepend-icon="mdi-lock"
                    name="password"
                    label="Password"
                    type="password"
                    solo
                  ></v-text-field>
                  <div class="d-inline-flex align-center" style="width: 100%">
                    <v-checkbox
                      v-model="form.remember"
                      label="Remember me"
                    ></v-checkbox>
                    <v-spacer></v-spacer>
                    <a to="/forgot-password">Forgot Password?</a>
                  </div>
                </v-card-text>
                <v-card-actions>
                  <v-btn
                    class="mx-2"
                    fab
                    dark
                    small
                    color="primary"
                    to="/"
                  >
                    <v-icon dark>
                      mdi-arrow-left
                    </v-icon>
                  </v-btn>
                  <v-spacer></v-spacer>
                  <v-btn color="primary" type="submit" :disabled="form.processing">Login</v-btn>
                </v-card-actions>
              </form>
            </v-card>
          </div>
        </v-col>
      </v-row>
    </div>
  </v-app>
</template>


<!--<template>
  <div>
    &lt;!&ndash; Validation Errors &ndash;&gt;
    <BreezeValidationErrors :errors="form.errors" class="mb-4" />

    <form @submit.prevent="submit">
      <div>
        <BreezeLabel for="email" value="Email" />
        <BreezeInput
          id="email"
          type="email"
          class="mt-1 block w-full"
          v-model="form.email"
          required
          autofocus
          autocomplete="username"
        />
      </div>

      <div class="mt-4">
        <BreezeLabel for="password" value="Password" />
        <BreezeInput
          id="password"
          type="password"
          class="mt-1 block w-full"
          v-model="form.password"
          required
          autocomplete="current-password"
        />
      </div>

      <div class="block mt-4">
        <label class="flex items-center">
          <BreezeCheckbox name="remember" :checked="form.remember" />
          <span class="ml-2 text-sm text-gray-600">Remember me</span>
        </label>
      </div>

      <div class="flex items-center justify-end mt-4">
        <NuxtLink
          to="/forgot-password"
          class="underline text-sm text-gray-600 hover:text-gray-900"
        >
          Forgot your password?
        </NuxtLink>

        <BreezeButton
          class="ml-4"
          :class="{ 'opacity-25': form.processing }"
          :disabled="form.processing"
        >
          Log in
        </BreezeButton>
      </div>
    </form>
  </div>
</template>-->

<script>
export default {
  head: {
    title: "Login",
  },

  layout: "guest",

  components: {
  },

  data() {
    return {
      form: {
        email: "",
        password: "",
        remember: false,
        processing: false,
        errors: [],
      },
    };
  },

  methods: {
    async submit() {
      this.form.processing = true;
      this.form.errors = [];

      try {
        let result = await this.$auth.loginWith("laravelSanctum", {data: this.form});
        this.$store.commit('access/user/setUser', this.$auth.user);
        this.form.processing = false;
        this.$router.push({ name: 'dashboard'})
      } catch (e) {
        this.form.processing = false;
        Object.keys(e.response.data.errors).forEach((key) => {
          Object.values(e.response.data.errors[key]).forEach((error) => {
            this.form.errors.push(error);
          });
        });
        setTimeout(() => {
          this.form.errors = [];
        }, 5000)
      }
    },
  },
};
</script>
