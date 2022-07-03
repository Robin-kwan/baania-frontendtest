<template>
  <v-app>
    <div class="px-2">
      <div class="header">
        <v-row no-gutters justify="center">
          <div class="box">
            <v-row no-gutters justify="center" align="end" class="pt-16 pb-6">
              <div class="input-box">
                <h4>URL</h4>
                <div style="width: 100%">
                  <v-text-field
                    outlined
                    hide-details
                    dense
                    height="53px"
                    background-color="white"
                    placeholder="http://localhost"
                    full-width
                  ></v-text-field>
                </div>
              </div>
              <div class="input-box">
                <h4>PORT</h4>
                <div style="width: 100%">
                  <v-text-field
                    outlined
                    hide-details
                    dense
                    height="53px"
                    background-color="white"
                    full-width
                    placeholder="8000"
                  ></v-text-field>
                </div>
              </div>
              <v-btn
                dark
                color="#3c64b1"
                class="large-btn elevation-0"
                width="200px"
                height="53px"
              >
                CONNECT
              </v-btn>
            </v-row>
          </div>
        </v-row>
      </div>
      <v-row no-gutters justify="center">
        <div class="box py-6">
          <v-row no-gutters justify="space-between" align="end">
            <h2>HOUSE LIST</h2>
            <v-btn
              dark
              color="#22BB66"
              class="large-btn elevation-0"
              width="200px"
              height="53px"
              @click="openAddDialog()"
            >
              CREATE
            </v-btn>
          </v-row>
          <v-data-table
            :headers="headers"
            :items="items"
            :items-per-page="5"
            mobile-breakpoint="0"
            class="mt-11"
          >
            <template #[`item.actions`]="{ item }">
              <v-btn
                rounded
                color="#FFF7E6"
                style="color: #ff9900"
                class="elevation-0 mr-1 px-5"
                @click="openUpdateDialog(item)"
              >
                VIEW DETAIL
              </v-btn>
              <v-btn
                rounded
                color="#FDF4F7"
                style="color: #b93e5c"
                class="elevation-0 ml-1 px-5"
                @click="deleteItem(item.id)"
              >
                DELETE
              </v-btn>
            </template>
          </v-data-table>
        </div>
      </v-row>
      <v-row no-gutters justify="center">
        <div class="bottombox">
          <v-row no-gutters justify="center" align="center" class="pt-16 pb-11">
            <div style="width: 405px">
              <v-select
                v-model="postcode"
                :items="postcodes"
                outlined
                hide-details
                height="53px"
                background-color="white"
                label="select post code"
              />
              <div v-if="average && median" class="result mt-3">
                <span> Average : {{ average }} </span>
                <br />
                <span> Median : {{ median }} </span>
              </div>
            </div>
          </v-row>
        </div>
      </v-row>
    </div>
    <AddDialog ref="addDialog" @reload="getData()" />
    <UpdateDialog ref="updateDialog" @reload="getData()" />
    <AlertDialog ref="alert" />
  </v-app>
</template>

<script>
import axios from "axios";
import AddDialog from "./components/AddDialog.vue";
import UpdateDialog from "./components/UpdateDialog.vue";
import AlertDialog from "./components/AlertDialog.vue";

export default {
  name: "App",
  components: {
    AddDialog,
    UpdateDialog,
    AlertDialog,
  },
  data: () => ({
    items: [],
    postcode: "",
    median: "",
    average: "",
    pricelist: [],
    headers: [
      {
        text: "ID",
        align: "center",
        value: "id",
        width: "6%",
      },
      { text: "Name", align: "center", value: "name", width: "23%" },
      { text: "Post Code", align: "center", value: "post_code", width: "23%" },
      { text: "Price", align: "center", value: "price", width: "23%" },
      {
        text: "Action",
        align: "center",
        value: "actions",
        width: "25%",
        sort: false,
      },
    ],
  }),
  methods: {
    openAddDialog() {
      this.$refs.addDialog.open();
    },
    openUpdateDialog(item) {
      this.$refs.updateDialog.open(item);
    },
    alert(val) {
      let params = {};
      if (val == 0) {
        params = {
          success: false,
          message: "Failed to delete",
        };
      }
      if (val == 1) {
        params = {
          success: true,
          message: "Delete successfully",
        };
      }
      this.$refs.alert.open(params);
    },
    getData() {
      axios.get("https://test-backend.baania.dev/home").then((response) => {
        this.items = response.data.payload;
        // update average & median
        if (this.postcode) {
          this.getPricelist();
          this.getMedian();
          this.getAverage();
        }
      });
    },
    deleteItem(id) {
      axios
        .delete("https://test-backend.baania.dev/home/" + id)
        .then(() => {
          this.getData();
          this.alert(1);
        })
        .catch(() => {
          this.alert(0);
        });
    },
    getPricelist() {
      let array = this.items.filter((item) => item.post_code == this.postcode);
      this.pricelist = array.map((item) => {
        return item.price;
      });
    },
    getMedian() {
      this.pricelist.sort(function (a, b) {
        return a - b;
      });
      const mid = this.pricelist.length / 2;
      this.median =
        mid % 1
          ? this.pricelist[mid - 0.5]
          : (
              (parseFloat(this.pricelist[mid - 1]) +
                parseFloat(this.pricelist[mid])) /
              2
            ).toFixed(1);
    },
    getAverage() {
      this.average = (
        this.pricelist.reduce((a, b) => a + parseFloat(b), 0) /
        this.pricelist.length
      ).toFixed(1);
    },
  },
  computed: {
    postcodes() {
      return this.items.map((item) => {
        return item.post_code;
      });
    },
  },
  created() {
    this.getData();
  },
  watch: {
    async postcode() {
      await this.getPricelist();
      await this.getMedian();
      await this.getAverage();
    },
  },
};
</script>

<style>
@import url("https://fonts.googleapis.com/css2?family=Prompt:wght@400;500;600;700&display=swap");
.v-application {
  font-family: "Prompt", sans-serif !important;
  min-width: 900px;
}
* {
  box-sizing: border-box;
}
h2,
h3,
h4 {
  font-weight: 500;
}
h3 {
  font-size: 1.125rem;
}
th,
td,
tr {
  border: 1px solid #e0e0e0;
}
tr {
  height: 75px !important;
}
tr:hover {
  background-color: #f4f7fc !important;
}
th {
  font-size: 1rem !important;
  color: #000 !important;
}
.input-box {
  width: 100%;
  max-width: 405px;
  flex: 1;
  margin-right: 64px;
}
.header {
  width: 100%;
  display: flex;
  justify-content: center;
  /* Stripes */
  background: #f4f7fc;
}
.box {
  width: 100%;
  max-width: 1140px;
}
.bottombox {
  width: 100%;
  max-width: 1140px;
  background-color: #f4f7fc;
  height: 235px;
  margin-bottom: 90px;
}
.v-btn {
  font-size: 1rem !important;
  font-weight: 400 !important;
}
.v-btn--rounded {
  font-size: 0.875rem !important;
  font-weight: 400 !important;
}
.v-text-field .v-input__control .v-input__slot {
  min-height: auto !important;
}
.v-text-field--outlined fieldset {
  color: #e0e0e0 !important;
}
.v-card {
  border-radius: 10px !important;
}
.result {
  color: #3c64b1;
  font-weight: 600;
  font-size: 1.125rem;
}
</style>
