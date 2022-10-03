<template>
  <div class="create_wrapper">
    <!-- Start Breadcrumb -->
    <Breadcrumb :items="items" />
    <!-- End Breadcrumb -->

    <div class="custom_card">
      <div class="card-header">
        <h4 class="card-title">{{ $t("edit") }}</h4>
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
            <!-- Name en-->
            <div class="col-md-6 py-0">
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
            </div>

            <!-- category -->
            <div class="col-md-6">
              <div class="input_wrapper top_label">
                <label class="form-label">{{
                  $t("forms.labels.category")
                }}</label>
                <multiselect
                  v-model="data.status"
                  :options="status"
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
                  :options="status"
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
            <div class="col-12">
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
  name: "Update",

  props: ["id"],

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
          text: this.$t("breadcrumb.ades.edit"),
          disabled: true,
          href: "",
        },
      ],

      // ========== Loading
      btnIsLoading: false,

      // data_src
      data_srcs: [],
      data_src: null,

      // ========== create_data
      data: {
        image: null,
        images: null,
        name: null,
        desc: null,
        status: null,
        amountLeft: null,
        user: null,
        category: null,
        subCategorie: null,
        country: null,
        city: null,
        shippingWay: null,
        start_date: null,
        end_date: null,
        start_price: null,
        bit_value: null,
        sellPrice: null,
        productRadio: "",
      },

      // Menues
      dateMenu_start: false,
      dateMenu_end: false,

      // ========== Fixed Data
      status: [],
      categories: [],
      countries: [],
      subCategories: [],
      cities: [],
      shippingWaies: [],
      users: [],
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
    getDynamicbidValue() {
      const submit_data = new FormData();
      submit_data.append("bid_value", this.data.start_price);

      this.$axios({
        method: "POST",
        url: "calculateBidValue",
        data: submit_data,
      }).then((res) => {
        this.btnIsLoading = false;
        this.data.bit_value = res.data.data.bid_value;
      });
    },

    spliceImage(id) {
      let deletedImage = this.data_srcs.find((img) => {
        return img.id == id;
      });

      this.$axios
        .delete(`delete_product_image/${id}`)
        .then(() => {
          this.data_srcs = this.data_srcs.filter((img) => {
            return img.id != deletedImage.id;
          });
        })
        .catch(() => {});
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
      } else if (!this.data.desc) {
        this.$iziToast.error({
          timeout: 2000,
          message: this.$t("forms.validation.desc"),
          position: "bottomRight",
        });
        this.btnIsLoading = false;
        return;
      } else if (!this.data.status) {
        this.$iziToast.error({
          timeout: 2000,
          message: this.$t("forms.validation.status"),
          position: "bottomRight",
        });
        this.btnIsLoading = false;
        return;
      } else if (!this.data.user) {
        this.$iziToast.error({
          timeout: 2000,
          message: this.$t("forms.validation.user"),
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
      } else if (!this.data.country) {
        this.$iziToast.error({
          timeout: 2000,
          message: this.$t("forms.validation.country"),
          position: "bottomRight",
        });
        this.btnIsLoading = false;
        return;
      } else if (!this.data.city) {
        this.$iziToast.error({
          timeout: 2000,
          message: this.$t("forms.validation.city"),
          position: "bottomRight",
        });
        this.btnIsLoading = false;
        return;
      } else if (!this.data.shippingWay) {
        this.$iziToast.error({
          timeout: 2000,
          message: this.$t("forms.validation.shippingWay"),
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
      if (this.data.image) {
        submit_data.append("main_image", this.data.image.img_file);
      }
      if (this.data.images) {
        this.data.images.forEach((img, index) => {
          submit_data.append(`images[${index}]`, img);
        });
      }

      submit_data.append("name", this.data.name);
      submit_data.append("desc", this.data.desc);
      submit_data.append("qty", this.data.amountLeft);

      submit_data.append("user_id", this.data.user.id);
      submit_data.append("category_id", this.data.category.id);
      submit_data.append("sub_category_id", this.data.subCategorie.id);
      submit_data.append("country_id", this.data.country.id);
      submit_data.append("city_id", this.data.city.id);
      submit_data.append("shipping_way_id", this.data.shippingWay.id);
      submit_data.append("product_status_id", this.data.status.id);

      if (this.data.productRadio == "isDirectSell") {
        submit_data.append("is_direct_sale", 1);
        submit_data.append("product_price", this.data.sellPrice);
      } else {
        submit_data.append("is_bid", 1);
        submit_data.append("start_bid_date", this.data.start_date);
        submit_data.append("end_bid_date", this.data.end_date);
        submit_data.append("bid_price", this.data.start_price);
        submit_data.append("bid_value", this.data.bit_value);
      }
      submit_data.append("_method", "PATCH");

      this.$axios({
        method: "POST",
        url: `product/${this.id}`,
        data: submit_data,
      })
        .then(() => {
          this.$iziToast.success({
            timeout: 2000,
            message: this.$t("editSuccess"),
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

    // ============ Get Main Data
    getMainData() {
      // Status
      this.$axios({
        method: "GET",
        url: `productStatus`,
      }).then((res) => {
        this.status = res.data.data.map((item) => {
          return {
            id: item.id,
            name: `${item.ar.name} - ${item.en.name}`,
          };
        });
      });

      // Main_Category
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

      // Country
      this.$axios({
        method: "GET",
        url: `country`,
      }).then((res) => {
        this.countries = res.data.data.map((item) => {
          return {
            id: item.id,
            name: `${item.ar.name} - ${item.en.name}`,
          };
        });
      });

      // ShppingWay
      this.$axios({
        method: "GET",
        url: `shippingWay`,
      }).then((res) => {
        this.shippingWaies = res.data.data.map((item) => {
          return {
            id: item.id,
            name: `${item.ar.name} - ${item.en.name}`,
          };
        });
      });

      // Users
      this.$axios({
        method: "GET",
        url: `help-services?type=client`,
      }).then((res) => {
        this.users = res.data.data.map((item) => {
          return {
            id: item.id,
            name: `${item.username}`,
          };
        });
      });

      // Get Product
      this.getProduct();
    },

    getProduct() {
      this.$axios({
        method: "GET",
        url: `product/${this.id}`,
      }).then((res) => {
        this.data_src = res.data.data.main_image;
        this.data_srcs = res.data.data.images.map((img) => {
          return { id: img.id, link: img.image_url };
        });

        this.data.name = res.data.data.name;
        this.data.desc = res.data.data.desc;

        this.data.status = {
          id: res.data.data.productStatus.id,
          name: `${res.data.data.productStatus.ar.name} - ${res.data.data.productStatus.en.name}`,
        };

        this.data.amountLeft = res.data.data.qty;
        this.data.user = {
          id: res.data.data.user.id,
          name: res.data.data.user.username,
        };
        this.data.category = {
          id: res.data.data.category.id,
          name: `${res.data.data.category.ar.name} - ${res.data.data.category.en.name}`,
        };
        this.data.subCategorie = {
          id: res.data.data.subCategory.id,
          name: `${res.data.data.subCategory.ar.name} - ${res.data.data.subCategory.en.name}`,
        };
        this.data.country = {
          id: res.data.data.country.id,
          name: `${res.data.data.country.ar.name} - ${res.data.data.country.en.name}`,
        };
        this.data.city = {
          id: res.data.data.city.id,
          name: `${res.data.data.city.ar.name} - ${res.data.data.city.en.name}`,
        };
        this.data.shippingWay = {
          id: res.data.data.shippingWay.id,
          name: `${res.data.data.shippingWay.ar.name} - ${res.data.data.shippingWay.en.name}`,
        };

        this.data.start_date = res.data.data.start_bid_date;
        this.data.end_date = res.data.data.end_bid_date;
        this.data.start_price = res.data.data.product_price;
        this.data.bit_value = res.data.data.bid_value;

        this.data.sellPrice = res.data.data.product_price;

        // Radio
        this.data.productRadio = res.data.data.is_bid
          ? "isAcuation"
          : "isDirectSell";
      });
    },

    // ============ Get Run Time Data
    getSubCategories() {
      this.$axios({
        method: "GET",
        url: `subCategories/${this.data.category.id}`,
      }).then((res) => {
        this.subCategories = res.data.data.map((item) => {
          return {
            id: item.id,
            name: `${item.ar.name} - ${item.en.name}`,
          };
        });
      });
    },
    getCities() {
      this.$axios({
        method: "GET",
        url: `cities/${this.data.country.id}`,
      }).then((res) => {
        this.cities = res.data.data.map((item) => {
          return {
            id: item.id,
            name: `${item.ar.name} - ${item.en.name}`,
          };
        });
      });
    },
  },

  created() {
    this.getMainData();
  },
};
</script>
