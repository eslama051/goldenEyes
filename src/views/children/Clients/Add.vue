<template>
  <div class="create_wrapper">
    <!-- Start Breadcrumb -->
    <Breadcrumb :items="items" />
    <!-- End Breadcrumb -->

    <div class="custom_card">
      <div class="card-header">
        <h4 class="card-title">{{ $t("addNew") }}</h4>
      </div>

      <!-- ==== Start Form ==== -->
      <form @submit.prevent="validateCreateForm">
        <div class="container">
          <div class="row justify-content-between">
            <!-- Name ar-->
            <div class="col-md-6 py-0">
              <div class="input_wrapper top_label">
                <input
                  type="text"
                  class="form-control"
                  @input="helper_checkIfInputIsEmpty"
                  v-model.trim="data.name"
                />
                <label for="name_input" class="form-label">{{
                  $t("forms.labels.name")
                }}</label>
              </div>
            </div>
            <!-- Name en-->
            <!-- <div class="col-md-6 py-0">
              <div class="input_wrapper top_label">
                <input
                  type="text"
                  class="form-control"
                  @input="helper_checkIfInputIsEmpty"
                  v-model.trim="data.name"
                />
                <label for="name_input" class="form-label">{{
                  $t("forms.labels.name_en")
                }}</label>
              </div>
            </div> -->
            <!-- adress -->
            <div class="col-md-6 py-0">
              <div class="input_wrapper top_label">
                <input
                  type="text"
                  class="form-control"
                  @input="helper_checkIfInputIsEmpty"
                  v-model.trim="data.address"
                />
                <label for="name_input" class="form-label">{{
                  $t("forms.labels.address")
                }}</label>
              </div>
            </div>

            <!-- tax number -->
            <div class="col-lg-12 py-0">
              <div class="input_wrapper top_label">
                <input
                  type="text"
                  class="form-control"
                  @input="helper_checkIfInputIsEmpty"
                  v-model.trim="data.taxNumber"
                />
                <label for="name_input" class="form-label">{{
                  $t("forms.labels.tax_number")
                }}</label>
              </div>
            </div>
          </div>
        </div>

        <div class="buttons_wrapper">
          <button
            class="button_style_1"
            :class="btnIsLoading ? 'disabled' : ''"
          >
            {{ $t("forms.submit") }}
            <span class="btn_loader" v-if="btnIsLoading"></span>
          </button>
        </div>
      </form>
      <!-- ==== End Form ==== -->
    </div>
  </div>
</template>

<script>
export default {
  name: "Create",

  data() {
    return {
      // ========== Breadcrumbs
      items: [
        {
          text: this.$t("breadcrumb.mainPage"),
          disabled: false,
          href: "/",
        },
        {
          text: this.$t("breadcrumb.clients.title"),
          disabled: false,
          href: "/clients",
        },
        {
          text: this.$t("breadcrumb.clients.add"),
          disabled: true,
          href: "",
        },
      ],

      // ========== Loading
      btnIsLoading: false,

      // ========== create_data
      data: {
        name: null,
        address: null,
        taxNumber: null,
      },

      // ========== Fixed Data
      countries: [],
      cities: [],
      permissions: [{ id: 1, name: "pemissionTest" }],
    };
  },

  methods: {
    // Uplode Image
    uplodeImg_1(obj) {
      this.data.image = obj;
    },

    // Validate Data
    validateCreateForm() {
      this.btnIsLoading = true;

      if (!this.data.name) {
        this.$iziToast.error({
          timeout: 2000,
          message: this.$t("forms.validation.name"),
          position: "bottomRight",
        });
        this.btnIsLoading = false;
        return;
      }
      if (!this.data.address) {
        this.$iziToast.error({
          timeout: 2000,
          message: this.$t("forms.validation.address"),
          position: "bottomRight",
        });
        this.btnIsLoading = false;
        return;
      } else {
        this.submitData();
        return;
      }
    },

    // Submit Data
    submitData() {
      const submit_data = new FormData();
      // submit_data.append("image", this.data.image.img_file);
      submit_data.append("name", this.data.name);
      submit_data.append("address", this.data.address);
      submit_data.append("tax_number", this.data.taxNumber);

      this.$axios({
        method: "POST",
        url: "client",
        data: submit_data,
      })
        .then(() => {
          this.$iziToast.success({
            timeout: 2000,
            message: this.$t("addSuccess"),
            position: "bottomRight",
          });
          this.$router.push({ path: "/clients" });
          this.btnIsLoading = false;
        })
        .catch((err) => {
          this.$iziToast.error({
            timeout: 2000,
            message: err.response.data.message,
            position: "bottomRight",
          });
          this.btnIsLoading = false;
        });
    },

    // ============ Get Main Data
    // getMainData() {
    // Permission
    // this.$axios({
    //   method: "GET",
    //   url: `permission`,
    // }).then((res) => {
    //   this.permissions = res.data.data.map((item) => {
    //     return {
    //       id: item.id,
    //       name: `${item.ar.name} - ${item.en.name}`,
    //     };
    //   });
    // });

    // Country
    //   this.$axios({
    //     method: "GET",
    //     url: `country`,
    //   }).then((res) => {
    //     this.countries = res.data.data.map((item) => {
    //       return {
    //         id: item.id,
    //         name: `${item.ar.name} - ${item.en.name}`,
    //       };
    //     });
    //   });
    // },

    // ============ Get Run Time Data
    // getCities() {
    //   this.$axios({
    //     method: "GET",
    //     url: `city`,
    //   }).then((res) => {
    //     this.cities = res.data.data.map((item) => {
    //       return {
    //         id: item.id,
    //         name: `${item.ar.name} - ${item.en.name}`,
    //       };
    //     });
    //   });
    // },
  },

  created() {
    // this.getMainData();
  },
};
</script>
