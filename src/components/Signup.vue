<template>
  <v-container grid-list-md>
    <v-row align="center" justify="center">
      <v-col class="align-center">
        <v-row class="align-center" justify="center">
          <v-col cols="12" sm="10" md="10" lg="6" class="align-center">
            <v-alert v-if="showMsg === 'error'"
                     dismissible
                     close-icon="mdi-close-circle"
                     :value="true"
                     type="error"
                     icon="mdi-alert"
                     dense
            >
              Please verify Sign Up information
            </v-alert>
            <v-alert v-if="showMsg === 'success'"
                     dismissible
                     :value="true"
                     type="success"
            >
              New User Signup Successful. Please
              <router-link class="white--text text-decoration-underline" to = "/auth">Login</router-link>
            </v-alert>
          </v-col>
        </v-row>
        <v-row align="center" justify="center">
          <v-col cols="12" sm="10" md="10" lg="6" class="align-center">
            <v-card class="login-card">
              <v-card-title>
                <span class="headline">{{ pageTitle }}</span>
              </v-card-title>
              <v-spacer/>

              <v-card-text>
                <v-form ref="form" v-model="valid" lazy-validation>
                  <v-container>
                    <v-text-field
                      v-model="user.first_name"
                      label="First Name"
                    ></v-text-field>

                    <v-text-field
                      v-model="user.last_name"
                      label="Last Name"
                    />
                    <v-text-field
                      v-model="user.username"
                      :counter="70"
                      label="Username"
                      :rules="rules.username"
                      maxlength="70"
                      required
                    />
                    <v-text-field
                      :type="showPassword ? 'text' : 'Password'"
                      v-model="user.password"
                      :counter="20"
                      :rules="rules.password"
                      label="Password"
                      maxlength="20"
                      :append-icon="showPassword ? 'mdi-eye' : 'mdi-eye-off'"
                      @click:append="showPassword = !showPassword"
                      required
                    />
                    <v-text-field
                      :type="showConfirmPassword ? 'text' : 'Password'"
                      v-model="user.confirm_password"
                      :counter="20"
                      :rules="rules.confirm_password"
                      label="Confirm Password"
                      maxlength="20"
                      :append-icon="showConfirmPassword ? 'mdi-eye' : 'mdi-eye-off'"
                      @click:append="showConfirmPassword = !showConfirmPassword"
                      required
                    />
                    <v-text-field
                      v-model="user.email"
                      label="Email Address"
                      :rules="rules.email"
                      required
                    />
                  </v-container>
                  <v-btn class="blue white--text" :disabled="!valid" @click="signUp">Sign Up</v-btn>
                  <v-btn class="blue white--text" @click="reset">Reset</v-btn>
                </v-form>
              </v-card-text>
            </v-card>
          </v-col>
        </v-row>
      </v-col>
    </v-row>
  </v-container>
</template>


<script>
import router from '../router';
import {APIService} from '../http/APIService';

const apiService = new APIService();

export default {
  name: 'SignUpUser',
  components: {},
  data() {
    return {
      customers: [],
      valid:true,
      showError: false,
      user: {},
      pageTitle: "Sign Up",
      showMsg: '',
      rules: {
        username: [
          v => !!v || "Username is required",
          v => (v && v.length > 3) || "A username must be more than 3 characters long",
          v => /^[a-z0-9_]+$/.test(v) || "A username can only contain letters and digits"
        ],
        password: [
          v => !!v || "Password is required",
          v => (v && v.length > 7) || "The password must be longer than 7 characters"
        ],
        confirm_password: [
          v => !!v || "Password is required",
          v => (v && v.length > 7) || "The password must be longer than 7 characters",
          v => (v == this.$data.user.password) || "Password and Confirm Password should match"
        ],
        email: [
          v => /^\w+([\.-]?\w+)*@\w+([\.-]?\w+)*(\.\w{2,3})+$/.test(v) || "Enter a valid email address"
        ],
      },
      showPassword: false,
      showConfirmPassword: false,
    };
  },
  computed: {
    list: {
      get() {
        return this.customers
      },
      set(newValue) {
        this.customers = newValue
      }
    }
  },
  methods: {
    signUp() {
      apiService.signUp(this.user).then(response => {
        if (response.status === 201) {
          this.stock = response.data;
          this.showMsg = "success";
        } else {
          this.showMsg = "error";
        }
      }).catch(error => {
        if (error.response.status === 401) {
          router.push("/auth");
        } else if (error.response.status === 400) {
          this.showMsg = "error";
        }
      });
    },
    reset() {
      this.$refs.form.reset()
    }
  }
}
</script>
<style scoped>
.aform {
  margin-left: auto;
  width: 60%;
}
</style>
