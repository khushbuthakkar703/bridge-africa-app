<template>
  <div>
    <form novalidate class="md-layout" @submit.prevent="validateUser">
      <md-card class="md-layout-item md-size-50 md-small-size-100 p-card">
        <md-card-header>
          <div class="md-title center f-22">
            {{ $t("Login_To_Bridge_Africa") }}
          </div>
        </md-card-header>

        <md-card-content>
          <!-- lol -->
          <FlashMessage />
          <div class="center">
            <b-row>
              <b-col cols="12" md="6" lg="12" xl="6">
                <md-button
                  @click.prevent="authProvider('facebook')"
                  class="md-raised md-primary b-w"
                >
                  <b-icon icon="facebook" aria-hidden="true"></b-icon>
                  {{ $t("Login_With_Facebook") }}
                </md-button>
              </b-col>

              <b-col cols="12" md="6" lg="12" xl="6">
                <md-button
                  @click.prevent="authProvider('google')"
                  class="b-color b-w"
                  style="color: white"
                >
                  <b-icon icon="google" aria-hidden="true"></b-icon>
                  {{ $t("login_with_google") }}
                </md-button>
              </b-col>
            </b-row>
          </div>

          <br />

          <p class="t-center">-{{ $t("or") }} -</p>

          <md-field :class="getValidationClass('email')">
            <label for="email"> {{ $t("email") }} </label>
            <md-input
              type="email"
              name="email"
              id="email"
              autocomplete="email"
              v-model="form.email"
              :disabled="sending"
            />
            <span class="md-error" v-if="!$v.form.email.required">
              {{ $t("the_email_is_required") }}
            </span>
            <span class="md-error" v-else-if="!$v.form.email.email">
              {{ $t("invalid_email") }}
            </span>
          </md-field>

          <md-field>
            <label for="password"> {{ $t("Password") }} </label>
            <md-input
              type="password"
              name="password"
              id="password"
              v-model="form.password"
              :disabled="sending"
            />
          </md-field>

          <div class="md-layout md-gutter">
            <div class="md-layout-item md-small-size-100 m-left">
              <b-form-checkbox
                id="checkbox-1"
                name="checkbox-1"
                value="accepted"
                unchecked-value="not_accepted"
              >
                {{ $t("remeber_me") }}
              </b-form-checkbox>
            </div>

            <div class="md-layout-item md-small-size-100">
              <br />
            </div>
          </div>
        </md-card-content>

        <md-progress-bar md-mode="indeterminate" v-if="sending" />

        <div>
          <b-row>
            <b-col cols="6">
              <md-button
                type="submit"
                :disabled="sending"
                class="b-color f-left"
                style="color: white"
              >
                {{ $t("login") }}
              </md-button>
            </b-col>
            <b-col cols="6">
              <router-link to="signup">
                <md-button class="md-raised f-right"
                  >{{ $t("signup") }}
                </md-button>
              </router-link>
            </b-col>
          </b-row>

          <router-link to="recoverPass1" class="nav-link text">
            {{ $t("forget_password") }}
          </router-link>
        </div>

        <div></div>

        <div>
          <br />
          <br />

          <label class="f-12">
            {{ $t("by_loging_in_you_agree_to_bridge_africa") }}
          </label>
          <br />

          <label class="f-12">
            <b-link href="#"> {{ $t("terms_and_conditions") }} </b-link> &
            <b-link href="#"> {{ $t("Privacy_policies") }} </b-link>
          </label>
        </div>
      </md-card>

      <div class="md-layout-item md-size-50 md-small-size-100 b-div"></div>

      <md-snackbar :md-active.sync="userSaved">
        {{ $t("the_user") }} {{ lastUser }} {{ $t("was_saved_with_success") }} !
      </md-snackbar>
    </form>

    <hr class="localfoter" />

    <p class="text-center">
      <span class="display-inline">
        <b-link @click="$i18n.locale = 'en'"> {{ $t("english") }} </b-link>
        <span class="vl"></span>
        <b-link class="ml-2" @click="$i18n.locale = 'fr'">
          {{ $t("french") }}
        </b-link>
      </span>
      Bridge Africa © 2021
    </p>
  </div>
</template>

<script>
import { validationMixin } from "vuelidate";
import "vue-material/dist/vue-material.min.css";

import "@/assets/default.css";
import { required, email } from "vuelidate/lib/validators";
export default {
  name: "FormValidation",
  boolean: true,
  mixins: [validationMixin],
  data: () => ({
    form: {
      password: null,
      email: null
    },

    langs: ["en", "fr"],

    userSaved: false,
    sending: false,
    lastUser: null
  }),
  validations: {
    form: {
      password: {
        required
      },
      email: {
        required,
        email
      }
    }
  },
  methods: {
    getValidationClass(fieldName) {
      const field = this.$v.form[fieldName];
      if (field) {
        return {
          "md-invalid": field.$invalid && field.$dirty
        };
      }
    },

    authProvider(provider) {
      let self = this;
      this.$auth
        .authenticate(provider)
        .then(response => {
          self.socialLogin(provider, response);
          console.log(response);
        })
        .catch(err => {
          console.log({ err: err });
        });
    },

    socialLogin(provider, response) {
      this.$http
        .post("user/social/" + provider, response)
        .then(({ data }) => {
          console.log(data.data);

          this.$store.commit("auth/setUserData", data.data);
          this.flashMessage.show({
            status: "success",

            message: "Successfully Register"
          });

          if (this.$store.state.auth.user.user.profile_complete == null) {
            this.$router.push({ name: "welcome" });
          } else {
            this.$router.push({ name: "dashboard" });
          }
        })
        .catch(err => {
          console.log({ err: err });
        });
    },

    clearForm() {
      this.$v.$reset();

      this.form.password = null;
      this.form.email = null;
    },
    saveUser() {
      this.sending = true;

      this.login();
    },

    login() {
      this.$store
        .dispatch("auth/login", {
          email: this.form.email,
          password: this.form.password
        })
        .then(() => {
          this.sending = false;

          if (this.$store.state.auth.user.user.profile_complete == null) {
            this.$router.push({ name: "welcome" });
          } else {
            this.$router.push({ name: "dashboard" });
          }
        })
        .catch(err => {
          console.log(err);
          this.sending = false;

          if (err.response.status == 422) {
            console.log({ err: err });

            this.flashMessage.show({
              status: "error",

              message: err.response.data.message
            });
          } else {
            this.flashMessage.show({
              status: "error",

              message: "An error has occure"
            });
          }
        });
    },

    validateUser() {
      this.$v.$touch();
      if (!this.$v.$invalid) {
        this.saveUser();
      }
    }
  }
};
</script>

<style scoped>
.vl {
  border-left: 1px solid green;
  height: 50px;
}

.f-right {
  float: left;
}

.f-left {
  float: right;
}

.m-left {
  align-content: left;
  text-align: left;
}
.md-progress-bar {
  position: absolute;
  top: 0;
  right: 0;
  left: 0;
}
.md-checkbox {
  display: flex;
}
.b-color {
  background-color: orange;
  color: white;
}
.p-card {
  padding-left: 90px;
  padding-right: 90px;
  padding-top: 80px;
  padding-bottom: 80px;
}
.b-div {
  background-image: url("ash.jpg");
  background-position: center;
  background-size: cover;
}
.t-center {
  text-align: center;
}

.center {
  align-content: center;
  text-align: center;
}

.b-w {
  width: 230px;
}

.f-22 {
  font-size: 22px;
}
.f-12 {
  font-size: 12px;
}
@media only screen and (max-width: 768px) {
  .p-card {
    padding-left: 10px;
    padding-right: 10px;
    padding-top: 10px;
    padding-bottom: 100px;
  }
}

.localfoter {
  margin-top: -10px;
}
</style>
