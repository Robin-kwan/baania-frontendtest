<template>
  <div>
    <v-dialog v-model="dialog" width="700px">
      <v-card class="pa-8">
        <h3>Create</h3>
        <v-row dense class="pt-1">
          <v-col cols="6">
            <v-text-field
              v-model="name"
              outlined
              hide-details
              height="53px"
              background-color="white"
              color="#8D9196"
              label="Name"
              placeholder="Name"
            ></v-text-field>
          </v-col>
          <v-col cols="3">
            <v-text-field
              v-model="postcode"
              outlined
              hide-details
              height="53px"
              background-color="white"
              color="#8D9196"
              label="Post Code"
              placeholder="Post Code"
            ></v-text-field>
          </v-col>
          <v-col cols="3">
            <v-text-field
              v-model.number="price"
              outlined
              hide-details
              hide-spin-buttons
              type="number"
              height="53px"
              background-color="white"
              color="#8D9196"
              label="Price"
              placeholder="Price"
            ></v-text-field>
          </v-col>
          <v-col cols="12">
            <v-textarea
              v-model="description"
              outlined
              hide-details
              background-color="white"
              color="#8D9196"
              label="Description"
              placeholder="Description"
            ></v-textarea>
          </v-col>
        </v-row>
        <v-row no-gutters justify="center" class="mt-2">
          <v-btn
            outlined
            color="#C0C5CE"
            width="164px"
            height="45px"
            class="mx-1 elevation-0"
            @click="close()"
          >
            <span style="color: #7c7e82"> CANCEL </span>
          </v-btn>
          <v-btn
            dark
            color="#22BB66"
            width="164px"
            height="45px"
            class="mx-1 elevation-0"
            @click="create()"
          >
            CREATE
          </v-btn>
        </v-row>
      </v-card>
    </v-dialog>
    <AlertDialog ref="alert" />
  </div>
</template>

<script>
import axios from "axios";
import AlertDialog from "./AlertDialog.vue";

export default {
  name: "AddDialog",
  components: {
    AlertDialog,
  },
  data: () => ({
    dialog: false,
    name: "",
    postcode: "",
    price: "",
    description: "",
  }),
  methods: {
    open() {
      this.dialog = true;
    },
    close() {
      this.dialog = false;
    },
    alert(val) {
      this.close();
      let params = {};
      if (val == 0) {
        params = {
          success: false,
          message: "Let's try one more again",
        };
      }
      if (val == 1) {
        params = {
          success: true,
          message: "Create successfully",
        };
      }
      this.$refs.alert.open(params);
    },
    updateParent() {
      this.$emit("reload");
    },
    create() {
      const params = {
        name: this.name,
        post_code: this.postcode,
        price: this.price,
        desc: this.description,
      };
      axios
        .post("https://test-backend.baania.dev/home", params)
        .then(() => {
          this.name = "";
          this.postcode = "";
          this.price = "";
          this.description = "";
          this.alert(1);
          this.updateParent();
        })
        .catch(() => {
          this.alert(0);
        });
    },
  },
};
</script>
