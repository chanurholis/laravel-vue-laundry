<template>
  <div class="col-md-12">
    <div class="panel">
      <div class="panel-heading">
        <router-link :to="{ name: 'expenses.create' }" class="btn btn-primary btn-sm btn-flat">
          <i class="fa fa-plus"></i> Tambah
        </router-link>
        <div class="pull-right">
          <input type="text" class="form-control" placeholder="Cari..." v-model="search" />
        </div>
      </div>
      <div class="panel-body">
        <b-table striped hover bordered :items="expenses.data" :fields="fields" show-empty>
          <template v-slot:cell(status)="row">
            <span class="label label-success" v-if="row.item.status == 1">Diterima</span>
            <span class="label label-warning" v-else-if="row.item.status == 0">Diproses</span>
            <span class="label label-default" v-else>Ditolak</span>
          </template>
          <template v-slot:cell(user)="row">{{ row.item.user.name }}</template>
          <template v-slot:cell(reason)="row">{{ row.item.reason == '' ? '-':row.item.reason }}</template>
          <template v-slot:cell(actions)="row">
            <router-link
              :to="{ name: 'expenses.edit', params: {id: row.item.id} }"
              class="btn btn-warning btn-sm"
              v-if="row.item.status == 0"
            >
              <i class="fa fa-pencil"></i>
            </router-link>
            <router-link
              :to="{ name: 'expenses.view', params: {id: row.item.id} }"
              class="btn btn-info btn-sm"
            >
              <i class="fa fa-eye"></i>
            </router-link>
            <button
              class="btn btn-danger btn-sm"
              @click="deleteExpenses(row.item.id)"
              v-if="row.item.status == 0"
            >
              <i class="fa fa-trash"></i>
            </button>
          </template>
        </b-table>

        <div class="row">
          <div class="col-md-6">
            <p v-if="expenses.data">
              <i class="fa fa-bars"></i>
              {{ expenses.data.length }} item dari {{ expenses.meta.total }} total data
            </p>
          </div>
          <div class="col-md-6">
            <div class="pull-right">
              <b-pagination-nav
                v-model="page"
                :total-rows="expenses.meta.total"
                :per-page="expenses.meta.per_page"
                aria-controls="expenses"
                v-if="expenses.data && expenses.data.length > 0"
              ></b-pagination-nav>
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
  name: "DataExpenses",
  created() {
    this.getExpenses();
  },
  data() {
    return {
      fields: [
        { key: "description", label: "Permintaan" },
        { key: "price", label: "Biaya" },
        { key: "note", label: "Catatan" },
        { key: "user", label: "Kurir" },
        { key: "status", label: "Status" },
        { key: "reason", label: "Alasan" },
        { key: "actions", label: "Aksi" }
      ],
      search: ""
    };
  },
  computed: {
    ...mapState("expenses", {
      expenses: state => state.expenses
    }),
    page: {
      get() {
        return this.$store.state.expenses.page;
      },
      set(val) {
        this.$store.commit("expenses/SET_PAGE", val);
      }
    }
  },
  watch: {
    page() {
      this.getExpenses();
    },
    search() {
      this.getExpenses(this.search);
    }
  },
  methods: {
    ...mapActions("expenses", ["getExpenses", "removeExpenses"]),
    deleteExpenses(id) {
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
          this.removeExpenses(id);
        }
      });
    }
  }
};
</script>
