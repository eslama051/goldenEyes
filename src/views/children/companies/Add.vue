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
            <!-- Images -->
            <uplode-image-2
              @inputChanged="logo_uploded"
              label="LOGO"
            ></uplode-image-2>
            <uplode-image-2
              @inputChanged="header_uploded"
              label="HEADER"
            ></uplode-image-2>
            <uplode-image-2
              @inputChanged="footer_uploded"
              label="FOOTER"
            ></uplode-image-2>

            <!-- Name ar-->
            <div class="col-md-6 py-0">
              <div class="input_wrapper top_label">
                <input
                  type="text"
                  class="form-control"
                  @input="helper_checkIfInputIsEmpty"
                  v-model.trim="data.nameAr"
                />
                <label for="name_input" class="form-label">{{
                  $t("forms.labels.name_ar")
                }}</label>
              </div>
            </div>
            <!-- Name en -->
            <div class="col-md-6 py-0">
              <div class="input_wrapper top_label">
                <input
                  type="text"
                  class="form-control"
                  @input="helper_checkIfInputIsEmpty"
                  v-model.trim="data.nameEn"
                />
                <label for="name_input" class="form-label">{{
                  $t("forms.labels.name_en")
                }}</label>
              </div>
            </div>

            <!-- fake Name  -->
            <div class="col-md-6 py-0">
              <div class="input_wrapper top_label">
                <input
                  type="text"
                  class="form-control"
                  @input="helper_checkIfInputIsEmpty"
                  v-model.trim="data.fake_name"
                />
                <label for="name_input" class="form-label">{{
                  $t("forms.labels.fake_name")
                }}</label>
              </div>
            </div>
            <!-- sequence  -->
            <div class="col-md-6 py-0">
              <div class="input_wrapper top_label">
                <input
                  type="number"
                  class="form-control"
                  @input="helper_checkIfInputIsEmpty"
                  v-model.trim="data.sequence"
                />
                <label for="name_input" class="form-label">{{
                  $t("forms.labels.sequence")
                }}</label>
              </div>
            </div>

            <!-- address  -->
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

            <!-- tax_number  -->
            <div class="col-md-6 py-0">
              <div class="input_wrapper top_label">
                <input
                  type="text"
                  class="form-control"
                  @input="helper_checkIfInputIsEmpty"
                  v-model.trim="data.tax_number"
                />
                <label for="name_input" class="form-label">{{
                  $t("forms.labels.tax_number")
                }}</label>
              </div>
            </div>

            <!-- commercial_number  -->
            <div class="col-md-6 py-0">
              <div class="input_wrapper top_label">
                <input
                  type="text"
                  class="form-control"
                  @input="helper_checkIfInputIsEmpty"
                  v-model.trim="data.commercial_number"
                />
                <label for="name_input" class="form-label">{{
                  $t("forms.labels.commercial_number")
                }}</label>
              </div>
            </div>

            <!-- companies_accounts  -->
            <div class="col-md-6 py-0">
              <div class="input_wrapper top_label">
                <input
                  type="text"
                  class="form-control"
                  @input="helper_checkIfInputIsEmpty"
                  v-model.trim="data.companies_accounts"
                />
                <label for="name_input" class="form-label">{{
                  $t("forms.labels.companies_accounts")
                }}</label>
              </div>
            </div>

            <!--  category -->
            <div class="col-12">
              <div class="input_wrapper top_label">
                <label class="form-label">{{
                  $t("forms.labels.category")
                }}</label>
                <multiselect
                  v-model="data.category"
                  :options="categories"
                  label="name"
                  track-by="id"
                  placeholder=" "
                  :qtyle="true"
                  :allow-empty="false"
                  :show-labels="false"
                >
                </multiselect>
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
          text: this.$t("breadcrumb.ades.title"),
          disabled: false,
          href: "/ades",
        },
        {
          text: this.$t("breadcrumb.ades.add"),
          disabled: true,
          href: "",
        },
      ],

      // ========== Loading
      btnIsLoading: false,

      // ========== create_data
      data: {
        fake_name: null,
        logo: null,
        header: null,
        footer: null,
        sequence: null,
        address: null,
        tax_number: null,
        commercial_number: null,
        companies_accounts: null,
        nameAr: null,
        nameEn: null,
        category: null,
      },

      // Menues
      dateMenu_start: false,
      dateMenu_end: false,

      // ========== Fixed Data
      categories: [],
      quantityNames: [],
    };
  },

  watch: {
    [`data.productRadio`](newVal, oldVal) {
      if (newVal == "isDirectSell") {
        this.data.start_date = null;
        this.data.end_date = null;
        this.data.start_price = null;
        this.data.bit_value = null;
      } else {
        this.data.sellPrice = null;
      }
    },
  },

  methods: {
    // Uplode Images
    logo_uploded(payload) {
      this.data.logo = payload;
      console.log(payload);
    },
    header_uploded(payload) {
      this.data.header = payload;
    },
    footer_uploded(payload) {
      this.data.footer = payload;
    },

    images_uploded(imgs) {
      this.data.images = imgs
        .filter((img) => {
          return img.img_file;
        })
        .map((_img) => {
          return _img.img_file;
        });
    },

    spliceImage(id) {
      let deletedImage = this.data_src.find((img) => {
        return img.id == id;
      });

      this.$axios
        .post(`delete_product_image/${id}`)
        .then(() => {
          this.data_src = this.data_src.filter((img) => {
            return img.id != deletedImage.id;
          });
        })
        .catch(() => {});
    },

    // Validate Data
    validateCreateForm() {
      this.btnIsLoading = true;

      if (!this.data.logo) {
        this.$iziToast.error({
          timeout: 2000,
          message: this.$t("forms.validation.logo"),
          position: "bottomRight",
        });
        this.btnIsLoading = false;
        return;
      } else if (!this.data.header) {
        this.$iziToast.error({
          timeout: 2000,
          message: this.$t("forms.validation.header"),
          position: "bottomRight",
        });
        this.btnIsLoading = false;
        return;
      } else if (!this.data.footer) {
        this.$iziToast.error({
          timeout: 2000,
          message: this.$t("forms.validation.footer"),
          position: "bottomRight",
        });
        this.btnIsLoading = false;
        return;
      } else if (!this.data.nameAr) {
        this.$iziToast.error({
          timeout: 2000,
          message: this.$t("forms.validation.name_ar"),
          position: "bottomRight",
        });
        this.btnIsLoading = false;
        return;
      } else if (!this.data.nameEn) {
        this.$iziToast.error({
          timeout: 2000,
          message: this.$t("forms.validation.name_en"),
          position: "bottomRight",
        });
        this.btnIsLoading = false;
        return;
      } else if (!this.data.fake_name) {
        this.$iziToast.error({
          timeout: 2000,
          message: this.$t("forms.validation.fake_name"),
          position: "bottomRight",
        });
        this.btnIsLoading = false;
        return;
      } else if (!this.data.sequence) {
        this.$iziToast.error({
          timeout: 2000,
          message: this.$t("forms.validation.sequence"),
          position: "bottomRight",
        });
        this.btnIsLoading = false;
        return;
      } else if (!this.data.address) {
        this.$iziToast.error({
          timeout: 2000,
          message: this.$t("forms.validation.address"),
          position: "bottomRight",
        });
        this.btnIsLoading = false;
        return;
      } else if (!this.data.tax_number) {
        this.$iziToast.error({
          timeout: 2000,
          message: this.$t("forms.validation.tax_number"),
          position: "bottomRight",
        });
        this.btnIsLoading = false;
        return;
      } else if (!this.data.commercial_number) {
        this.$iziToast.error({
          timeout: 2000,
          message: this.$t("forms.validation.commercial_number"),
          position: "bottomRight",
        });
        this.btnIsLoading = false;
        return;
      } else if (!this.data.category) {
        this.$iziToast.error({
          timeout: 2000,
          message: this.$t("forms.validation.category"),
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
      console.log(this.data.category);
      submit_data.append("logo", this.data.logo.img_file);
      submit_data.append("header", this.data.header.img_file);
      submit_data.append("footer", this.data.footer.img_file);
      submit_data.append("ar[name]", this.data.nameAr);
      submit_data.append("en[name]", this.data.nameEn);
      submit_data.append("fake_name", this.data.fake_name);
      submit_data.append("sequence", this.data.sequence);
      submit_data.append("address", this.data.address);
      submit_data.append("tax_number", this.data.tax_number);
      submit_data.append("commercial_number", this.data.commercial_number);
      submit_data.append("companies_accounts", this.data.companies_accounts);
      submit_data.append("category_id", this.data.category.id);

      this.$axios({
        method: "POST",
        url: "company",
        data: submit_data,
      })
        .then(() => {
          this.$iziToast.success({
            timeout: 2000,
            message: this.$t("addSuccess"),
            position: "bottomRight",
          });
          this.$router.push({ path: "/companies" });
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

    // getDynamicbidValue() {
    //   const submit_data = new FormData();
    //   submit_data.append("bid_value", this.data.start_price);

    //   this.$axios({
    //     method: "POST",
    //     url: "calculateBidValue",
    //     data: submit_data,
    //   }).then((res) => {
    //     this.btnIsLoading = false;
    //     this.data.bit_value = res.data.data.bid_value;
    //   });
    // },

    // ============ Get Main Data
    getMainData() {
      // categories
      this.$axios({
        method: "GET",
        url: `category`,
      }).then((res) => {
        this.categories = res.data.data.map((item) => {
          return {
            id: item.id,
            name: `${item.ar.name} - ${item.en.name} `,
          };
        });
      });
    },

    // Country
    // this.$axios({
    //   method: "GET",
    //   url: `country`,
    // }).then((res) => {
    //   this.countries = res.data.data.map((item) => {
    //     return {
    //       id: item.id,
    //       name: `${item.ar.name} - ${item.en.name}`,
    //     };
    //   });
    // });

    // ShppingWay
    // this.$axios({
    //   method: "GET",
    //   url: `shippingWay`,
    // }).then((res) => {
    //   this.shippingWaies = res.data.data.map((item) => {
    //     return {
    //       id: item.id,
    //       name: `${item.ar.name} - ${item.en.name}`,
    //     };
    //   });
    // });

    // Users
    //   this.$axios({
    //     method: "GET",
    //     url: `help-services?type=client`,
    //   }).then((res) => {
    //     this.users = res.data.data.map((item) => {
    //       return {
    //         id: item.id,
    //         name: `${item.username}`,
    //       };
    //     });
    //   });
    // },

    // // ============ Get Run Time Data
    // getSubCategories() {
    //   this.$axios({
    //     method: "GET",
    //     url: `subCategories/${this.data.category.id}`,
    //   }).then((res) => {
    //     this.subCategories = res.data.data.map((item) => {
    //       return {
    //         id: item.id,
    //         name: `${item.ar.name} - ${item.en.name}`,
    //       };
    //     });
    //   });
    // },
    // getCities() {
    //   this.$axios({
    //     method: "GET",
    //     url: `cities/${this.data.country.id}`,
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
    this.getMainData();
  },
};
</script>
