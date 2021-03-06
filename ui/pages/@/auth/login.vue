<template>
  <v-layout fill-height>
    <v-row
      align="center"
      justify="center"
    >
      <v-col
        cols="12"
        sm="8"
        md="6"
        lg="4"
      >
        <v-card class="elevation-12">
          <v-card-text>
            <v-form ref="form" v-model="valid" lazy-validation>
              <v-text-field
                v-model="username"
                :rules="usernameRules"
                label="Username"
                name="username"
                autocomplete="on"
                required
              />
              <v-spacer />
              <v-text-field
                v-model="password"
                :rules="[...passwordRules]"
                :append-icon="showPassword ? 'visibility' : 'visibility_off'"
                :type="showPassword ? 'text' : 'password'"
                label="Password"
                name="password"
                autocomplete="on"
                hint="At least 8 characters"
                @click:append="showPassword = !showPassword"
              />
              <v-spacer />
              <h4>
                By logging in, you Agree with Roomler's
                <nuxt-link to="/tos/terms">
                  Terms of Service
                </nuxt-link>
              </h4>
            </v-form>
          </v-card-text>
          <v-card-actions>
            <v-container class="ma-0 pt-0 pb-0">
              <v-row>
                <v-col cols="12" sm="12">
                  <v-btn
                    x-small
                    class="mb-8"
                    to="/@/auth/forgot/password"
                  >
                    Forgot password
                  </v-btn>
                  <v-btn
                    x-small
                    class="mb-8"
                    to="/@/auth/forgot/username"
                  >
                    Forgot username
                  </v-btn>
                </v-col>
              </v-row>
              <v-row>
                <v-col cols="12" sm="12">
                  <v-btn
                    :disabled="!valid"
                    color="primary"
                    class="mb-8"
                    @click="login()"
                  >
                    Login
                  </v-btn>
                </v-col>
              </v-row>
            </v-container>

            <oauth-buttons />
          </v-card-actions>
        </v-card>
      </v-col>
    </v-row>
  </v-layout>
</template>

<style>
  .fab-container {
    position: absolute;
    bottom: 0;
    right: 0;
  }
</style>

<script>
import OauthButtons from '@/components/oauth-buttons'

export default {
  middleware: 'anonymous',
  components: {
    OauthButtons
  },
  data () {
    return {
      valid: true,

      username: null,
      usernameRules: [
        v => !!v || 'Username is required',
        v => (v && v.length >= 6) || 'Username must be at least 6 characters',
        v => /^[a-zA-Z0-9_-]+$/.test(v) || 'Username must be composed of only letters, numbers and - or _ character'
      ],

      password: null,
      passwordRules: [
        v => !!v || 'Password is required',
        v => (v && v.length >= 8) || 'Password must be at least 8 characters'
      ],
      showPassword: false
    }
  },
  methods: {
    async login () {
      const self = this
      if (this.$refs.form.validate()) {
        const response = await this.$store.dispatch('api/auth/login', {
          username: this.username,
          password: this.password
        })
        if (!response.hasError) {
          await this.$store.dispatch('api/invite/acceptPendingInvites')
          await this.$store.dispatch('connectWebSocket')
          self.$router.push({ path: '/' })
        }
      }
    }
  }
}
</script>
