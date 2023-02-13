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
            <div class="card-body">
              <h5><i class="fa fa-user"></i> MY PROFILE</h5>
              <hr />
              <div class="mb-3 row">
                <label for="name" class="col-sm-2 col-form-label">Nama</label>
                <div class="col-sm-10">
                  <input
                    type="text"
                    readonly
                    class="form-control-plaintext"
                    id="name"
                    v-model="$auth.user.name"
                  />
                </div>
              </div>
              <div class="mb-3 row">
                <label for="email" class="col-sm-2 col-form-label">Email</label>
                <div class="col-sm-10">
                  <input
                    type="text"
                    readonly
                    class="form-control-plaintext"
                    id="email"
                    v-model="$auth.user.email"
                  />
                </div>
              </div>
              <div>
                <b-button
                  :to="{
                    // name: 'customer-profile-edit-id',
                    // params: { id: item.id }
                  }"
                  variant="info"
                  size="sm"
                >
                  EDIT PROFILE
                </b-button>
              </div>
            </div>
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
      search: ""
    };
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
    }
  }
};
</script>

<style></style>
