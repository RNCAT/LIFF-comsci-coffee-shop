<template>
  <div style="overflow: hidden">
    <v-app-bar dense flat>
      <v-toolbar-title class="pa-0 mb-3">Register</v-toolbar-title>
    </v-app-bar>
    <v-main>
      <v-container
        fluid
        ma-0
        pa-0
        fill-width
        style="background-color: black; border-radius: 0px 0px 0px 120px"
      >
        <v-row>
          <v-col cols="12" class="pt-5 pb-0 ma-0">
            <div class="text-center pt-0 pb-0 ma-0">
              <img
                src="~/assets/profile.png"
                style="width: auto; max-height: 150px"
              />
              <div class="ma-0 pt-0 text-title text-center">
                ComSci Coffee Shop
              </div>
            </div>
          </v-col>
        </v-row>
      </v-container>
      <v-container class="pt-10 pb-0 pr-0 pl-0">
        <v-row>
          <v-col cols="12" class="pl-10 pr-10">
            <v-form>
              <div class="text-subtitle text-center pb-5">Customer Info</div>
              <v-text-field
                v-model="form.lineName"
                label="Line User"
                disabled
                dense
                required
              >
              </v-text-field>
              <v-text-field
                v-model="form.firstName"
                label="First Name"
                :rules="[rules.required, rules.counter]"
                dense
                required
              >
              </v-text-field>
              <v-text-field
                v-model="form.lastName"
                label="Last Name"
                :rules="[rules.required, rules.counter]"
                dense
                required
              >
              </v-text-field>

              <v-text-field
                v-model="form.phone"
                label="Phone"
                :rules="[rules.required, rules.number]"
                dense
                required
              >
              </v-text-field>

              <div class="mt-5 text-subtitle text-center">Address</div>
              <v-textarea
                clearable
                v-model="form.address"
                :value="form.address"
                label="Address"
                :rules="[rules.required]"
                dense
                required
              >
              </v-textarea>

              <v-container class="pt-5 pb-5 pr-0 pl-0 text-center">
                <v-btn
                  rounded
                  v-bind:disabled="btnstatus"
                  class="w-100 mt-2 main-btn"
                  @click="Register"
                  >Submit</v-btn
                >
              </v-container>
            </v-form>
          </v-col>
        </v-row>
      </v-container>
    </v-main>
  </div>
</template>

<script>
export default {
  data() {
    return {
      liffId: process.env.LIFF_ID_REGISTER,
      webhook: process.env.WEBHOOK_API_URI,
      form: {
        lineId: "",
        lineName: "",
        firstName: "",
        lastName: "",
        phone: "",
        address: "",
      },

      menu: false,
      btnstatus: true,
      rules: {
        required: (value) => !!value || "Required.",
        counter: (value) => value.length <= 20 || "Max 20 characters",
        number: (value) =>
          Number.isInteger(Number(value)) ||
          "The value must be an integer number",
      },
    };
  },
  async mounted() {
    await liff.init({ liffId: this.liffId });

    await this.getUserProfile();
  },

  methods: {
    async getUserProfile() {
      if (liff.isLoggedIn()) {
        const profile = await liff.getProfile();

        this.form.lineId = profile.userId;
        this.form.lineName = profile.displayName;
        this.btnstatus = false;
      } else {
        await liff.login();
      }
    },

    async Register() {
      const user = this.form;

      await this.$axios.post(`${this.webhook}/user/${this.form.lineId}`, user);

      await liff.sendMessages([
        {
          type: "text",
          text: "register",
        },
      ]);

      if (
        liff.getContext().type !== "none" &&
        liff.getContext().type !== "external"
      ) {
        liff.closeWindow();
      }
    },
  },
};
</script>

<style>
</style>

