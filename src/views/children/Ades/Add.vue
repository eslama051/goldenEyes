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
            <uplode-image-2 @inputChanged="image_uploded"></uplode-image-2>

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
                  $t("forms.labels.name_ar")
                }}</label>
              </div>
            </div>
            <!-- Name en
            <div class="col-md-6 py-0">
              <div class="input_wrapper top_label">
                <input
                  type="text"
                  class="form-control"
                  @input="helper_checkIfInputIsEmpty"
                  v-model.trim="data.name_en"
                />
                <label for="name_input" class="form-label">{{
                  $t("forms.labels.name_en")
                }}</label>
              </div>
            </div> -->

            <!-- category -->
            <div class="col-md-6">
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
                  :searchable="true"
                  :allow-empty="false"
                  :show-labels="false"
                >
                </multiselect>
              </div>
            </div>

            <!-- qunatity name -->
            <div class="col-md-6">
              <div class="input_wrapper top_label">
                <label class="form-label">{{
                  $t("forms.labels.qtyName")
                }}</label>
                <multiselect
                  v-model="data.qtyName"
                  :options="quantityNames"
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

            <!-- price -->
            <div class="col-md-6">
              <div class="input_wrapper top_label">
                <input
                  type="number"
                  class="form-control"
                  @input="helper_checkIfInputIsEmpty"
                  v-model.trim="data.price"
                />
                <label for="name_input" class="form-label">{{
                  $t("forms.labels.price")
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
        image: null,
        name: null,
        category: null,
        price: null,
        qtyName: null,
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
    image_uploded(payload) {
      this.data.image = payload;
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

      if (!this.data.image) {
        this.$iziToast.error({
          timeout: 2000,
          message: this.$t("forms.validation.image"),
          position: "bottomRight",
        });
        this.btnIsLoading = false;
        return;
      } else if (!this.data.name) {
        this.$iziToast.error({
          timeout: 2000,
          message: this.$t("forms.validation.name"),
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
      } else if (!this.data.qtyName) {
        this.$iziToast.error({
          timeout: 2000,
          message: this.$t("forms.validation.qtyName"),
          position: "bottomRight",
        });
        this.btnIsLoading = false;
        return;
      } else if (!this.data.price) {
        this.$iziToast.error({
          timeout: 2000,
          message: this.$t("forms.validation.price"),
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

      submit_data.append("name", this.data.name);
      submit_data.append("image", this.data.image.img_file);
      submit_data.append("price", this.data.price);
      submit_data.append("quantity_id", this.data.qtyName.id);
      submit_data.append("category_id", this.data.category.id);

      this.$axios({
        method: "POST",
        url: "product",
        data: submit_data,
      })
        .then(() => {
          this.$iziToast.success({
            timeout: 2000,
            message: this.$t("addSuccess"),
            position: "bottomRight",
          });
          this.$router.push({ path: "/ades" });
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
            name: `${item.ar.name} - ${item.en.name}`,
          };
        });
      });
      this.$axios({
        method: "GET",
        url: `quantity`,
      }).then((res) => {
        this.quantityNames = res.data.data.map((item) => {
          return {
            id: item.id,
            name: `${item.ar.name} - ${item.en.name}`,
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
