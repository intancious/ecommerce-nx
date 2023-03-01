<template>
  <div class="container-fluid mt-custom">
    <div class="fade-in">
      <div class="row">
        <div class="col-md-3">
          <!-- sidebar -->
          <Sidebar />
          <!-- end sidebar -->
        </div>
        <div class="col-md-9">
          <div class="card border-0 rounded shadow-sm border-top-orange">
            <form @submit.prevent="updateUser">
              <div class="card-body">
                <h5><i class="fa fa-user"></i> MY PROFILE</h5>
                <hr />
                <div class="mb-3 row">
                  <label for="name" class="col-sm-2 col-form-label">Nama</label>
                  <div class="col-sm-10">
                    <div class="form-group">
                      <input
                        type="text"
                        v-model="user.name"
                        placeholder="Masukkan Nama User"
                        class="form-control"
                      />
                      <div v-if="validation.name" class="mt-2">
                        <b-alert show variant="danger">{{
                          validation.name[0]
                        }}</b-alert>
                      </div>
                    </div>
                  </div>
                </div>
                <div class="mb-3 row">
                  <label for="email" class="col-sm-2 col-form-label"
                    >Email</label
                  >
                  <div class="col-sm-10">
                    <div class="form-group">
                      <input
                        type="email"
                        v-model="user.email"
                        placeholder="Masukkan Email Address"
                        class="form-control"
                        readonly
                      />
                      <div v-if="validation.email" class="mt-2">
                        <b-alert show variant="danger">{{
                          validation.email[0]
                        }}</b-alert>
                      </div>
                    </div>
                  </div>
                </div>
                <div class="mb-3 row">
                  <label for="password" class="col-sm-2 col-form-label"
                    >Password</label
                  >
                  <div class="col-sm-10">
                    <div class="form-group">
                      <input
                        type="password"
                        v-model="user.password"
                        placeholder="Masukkan Password"
                        class="form-control"
                      />
                      <div v-if="validation.password" class="mt-2">
                        <b-alert show variant="danger">{{
                          validation.password[0]
                        }}</b-alert>
                      </div>
                    </div>
                  </div>
                </div>
                <div class="mb-3 row">
                  <label
                    for="password confirmation"
                    class="col-sm-2 col-form-label"
                    >Password Confirmation</label
                  >
                  <div class="col-sm-10">
                    <div class="form-group">
                      <input
                        type="password"
                        v-model="user.password_confirmation"
                        placeholder="Masukkan Konfirmasi Password"
                        class="form-control"
                      />
                    </div>
                  </div>
                </div>
                <div>
                  <button class="btn btn-info mr-1 btn-submit" type="submit">
                    <i class="fa fa-paper-plane"></i> UPDATE
                  </button>
                  <button class="btn btn-warning btn-reset" type="reset">
                    <i class="fa fa-redo"></i> RESET
                  </button>
                </div>
              </div>
            </form>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
//import sidebar
import Sidebar from "@/components/web/sidebar.vue";

export default {
  //middleware
  middleware: "isCustomer",

  //layout
  layout: "default",

  //register components
  components: {
    Sidebar
  },

  //meta
  head() {
    return {
      title: "Profile - Customer"
    };
  },

  //data function
  data() {
    return {
      //table header
      fields: [
        {
          label: "No. Invoice",
          key: "invoice"
        },
        {
          label: "Grand Total",
          key: "grand_total"
        },
        {
          label: "Status Payment",
          key: "status",
          tdClass: "text-center"
        },
        {
          label: "Actions",
          key: "actions",
          tdClass: "text-center"
        }
      ],

      //state search
      search: "",
      user: {
        name: "",
        email: "",
        password: "",
        password_confirmation: ""
      },
      validation: []
    };
  },

  mounted() {
    this.user.name = this.$auth.user.name;
    this.user.email = this.$auth.user.email;
  },

  //hook "asyncData"
  async asyncData({ store }) {
    await store.dispatch("customer/invoice/getInvoicesData");
  },

  //computed
  computed: {
    //invoices
    invoices() {
      return this.$store.state.customer.invoice.invoices;
    }
  },

  //method
  methods: {
    //method "searchData"
    searchData() {
      //commit to mutation "SET_PAGE"
      this.$store.commit("customer/invoice/SET_PAGE", 1);

      //dispatch on action "getInvoicesData"
      this.$store.dispatch("customer/invoice/getInvoicesData", this.search);
    },

    //method "changePage"
    changePage(page) {
      //commit to mutation "SET_PAGE"
      this.$store.commit("customer/invoice/SET_PAGE", page);

      //dispatch on action "getInvoicesData"
      this.$store.dispatch("customer/invoice/getInvoicesData", this.search);
    },

    async updateUser() {
      let token = this.$auth.token;
      //define formData
      let formData = new FormData();

      formData.append("name", this.user.name);
      formData.append("email", this.user.email);
      formData.append("password", this.user.password);
      formData.append("password_confirmation", this.user.password_confirmation);
      formData.append("_method", "PATCH");

      //sending data to action "updateUser" vuex
      await this.$axios
        .post("api/customer/profile/${token}", formData)

        //success
        .then(() => {
          //sweet alert
          this.$swal.fire({
            title: "BERHASIL!",
            text: "Data Berhasil Diupdate!",
            icon: "success",
            showConfirmButton: false,
            timer: 2000
          });

          window.location.reload();
        })

        //error
        .catch(error => {
          //assign error to state "validation"
          this.validation = error.response.data;
        });
    }
  }
};
</script>

<style></style>
