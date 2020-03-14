<template>
  <div class="col-md-12">
    <div class="panel">
      <div class="panel-heading">
        <router-link :to="{ name: 'couriers.add' }" class="btn btn-primary btn-sm btn-flat">
          <i class="fa fa-plus"></i> Tambah
        </router-link>
        <div class="pull-right">
          <input type="text" class="form-control" placeholder="Cari..." v-model="search" />
        </div>
      </div>
      <div class="table table-responsive">
        <div class="panel-body">
          <!-- BAGIAN INI AKAN MENAMPILKAN DATA LIST KURIR DALAM BENTUK TABLE -->
          <b-table striped hover bordered :items="couriers.data" :fields="fields" show-empty>
            <!-- KITA MENGGUNAKAN CUSTOM TEMPLATE KARENA AKAN MENAMPILKAN GAMBAR -->
            <template v-slot:cell(photo)="row">
              <img
                :src="'/storage/couriers/' + row.item.photo"
                :width="80"
                :height="50"
                :alt="row.item.name"
              />
            </template>
            <template v-slot:cell(outlet_id)="row">{{ row.item.outlet.name }}</template>
            <template v-slot:cell(actions)="row">
              <router-link
                :to="{ name: 'couriers.edit', params: {id: row.item.id} }"
                class="btn btn-warning btn-sm"
              >
                <i class="fa fa-pencil"></i>
              </router-link>
              <button class="btn btn-danger btn-sm" @click="deleteCourier(row.item.id)">
                <i class="fa fa-trash"></i>
              </button>
            </template>
          </b-table>

          <!-- BERISI INFORMASI JUMLAH DATA DAN PAGINATION -->
          <div class="row">
            <div class="col-md-6">
              <p v-if="couriers.data">
                <i class="fa fa-bars"></i>
                {{ couriers.data.length }} item dari {{ couriers.meta.total }} total data
              </p>
            </div>
            <div class="col-md-6">
              <div class="pull-right">
                <b-pagination-nav
                  v-model="page"
                  :total-rows="couriers.meta.total"
                  :per-page="couriers.meta.per_page"
                  aria-controls="couriers"
                  v-if="couriers.data && couriers.data.length > 0"
                ></b-pagination-nav>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { mapActions, mapState } from "vuex";

export default {
  name: "DataCourier",
  created() {
    this.getCouriers(); //KETIKA PAGE DI-LOAD, FUNGSI UNTUK MENGAMBIL DATA DIJALANKAN
  },
  data() {
    return {
      //FIELD TABLE YANG AKAN DITAMPILKAND DATANYA
      fields: [
        { key: "photo", label: "#" },
        { key: "name", label: "Nama Lengkap" },
        { key: "email", label: "Email" },
        { key: "outlet_id", label: "Outlet" },
        { key: "actions", label: "Aksi" }
      ],
      search: ""
    };
  },
  computed: {
    ...mapState("courier", {
      couriers: state => state.couriers //STATE YANG MENYIMPAN DATA KURIR
    }),
    //STATE PAGE UNTUK MENGAMBIL DAN MENGUBAH DATA STATE
    page: {
      get() {
        return this.$store.state.courier.page;
      },
      set(val) {
        this.$store.commit("courier/SET_PAGE", val);
      }
    }
  },
  watch: {
    //KETIKA TERJADI PERUBAHAN VALUE DARI PAGE
    page() {
      this.getCouriers(); //MAKA FUNGSI INI DIJALANKAN
    },
    //KETIKA TERJADI PERUBAHAN VALUE DARI SEARCH BOX
    search() {
      this.getCouriers(this.search); //FUNGSI INI DIJALANKAN
    }
  },
  methods: {
    ...mapActions("courier", ["getCouriers", "removeCourier"]), //MEMANGGIL FUNGSI YANG ADA DI STORE COURIER

    //FUNGSI DELETE YANG AKAN DIBAHAS NANTINYA
    deleteCourier(id) {
      this.$swal({
        title: "Kamu Yakin?",
        text: "Tindakan ini akan menghapus secara permanent!",
        type: "warning",
        showCancelButton: true,
        confirmButtonColor: "#3085d6",
        cancelButtonColor: "#d33",
        confirmButtonText: "Iya, Lanjutkan!"
      }).then(result => {
        if (result.value) {
          this.removeCourier(id);
        }
      });
    }
  }
};
</script>