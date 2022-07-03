<template>
  <v-dialog v-model="dialog" width="331px">
    <v-card class="pa-12">
      <v-row no-gutters justify="center">
        <v-col align="center">
          <img
            v-if="success"
            src="../assets/success.svg"
            style="width: 80px; height: 80px"
          />
          <img
            v-if="!success"
            src="../assets/fail.svg"
            style="width: 80px; height: 80px"
          />
          <h2 class="header-msg">{{ success ? "Success" : "Fail" }}</h2>
          <span class="body-msg"> {{ message }}</span>
          <v-btn
            outlined
            color="#C0C5CE"
            width="164px"
            height="45px"
            class="mx-1 elevation-0 mt-5"
            @click="dialog = false"
          >
            <span style="color: #7c7e82">
              {{ success ? "CONTINUE" : "TRY AGAIN" }}
            </span>
          </v-btn>
        </v-col>
      </v-row>
    </v-card>
  </v-dialog>
</template>

<script>
export default {
  name: "AlertDialog",

  data: () => ({
    dialog: false,
    success: true,
    message: "",
  }),
  methods: {
    open(item) {
      this.dialog = true;
      this.success = item.success;
      this.message = item.message;
    },
  },
};
</script>

<style>
@import url("https://fonts.googleapis.com/css2?family=Poppins:wght@400;500;600;700&display=swap");
.header-msg {
  color: #8d9196;
  font-weight: 700;
}
.body-msg {
  color: #8d9196;
  font-weight: 600;
  font-size: 0.875rem;
  font-family: "Poppins", sans-serif !important;
}
</style>
