<template>
  <div>
    <div class="container-fluid">
      <div class="page-header">
        <Breadcrumbs main="Doctors" title="Doctors List" />
      </div>
    </div>
    <div class="container-fluid">
      <div class="row">
        <div class="col-md-12">
          <div class="card">
            <div class="card-body">
              <div class="table-responsive datatable-vue user-list">
                <div>
                  <b-modal
                    id="modal-1"
                    title="Confirmation"
                    @ok="deleteBatchRow"
                  >
                    <p class="my-4">Are you sure!</p>
                  </b-modal>
                </div>
                <div></div>
                <!--<commonTable
                  :selectMode="selectMode"
                  :items="items"
                  :fields="tablefields"
                  :filter="filter"
                  :perPage="perPage"
                  :totalRows="totalRows"
                  :currentPage="currentPage"
                  :selectable="false"
                >
                </commonTable>-->
                <b-table
                  class="text-center"
                  :select-mode="selectMode"
                  show-empty
                  striped
                  hover
                  head-variant="light"
                  bordered
                  stacked="md"
                  :items="items"
                  :fields="tablefields"
                  :filter="filter"
                  :current-page="currentPage"
                  :per-page="perPage"
                  @filtered="onFiltered"
                  @row-selected="rowSelected"
                >
                  <template v-slot:head(delete)>
                    <b-button
                      variant="danger"
                      :disabled="selectedRows.length === 0"
                      @click="showMsgBoxTwo"
                      >Delete</b-button
                    >
                  </template>

                  <template v-slot:cell(action)="{ item, field: { key } }">
                    <!-- <b-checkbox
                      v-model="item[key]"
                      @change="deleteSelected(item)"
                    ></b-checkbox> -->
                    <a href="#" class="">editer</a>
                    <a href="#"> suspendre</a>
                  </template>
                </b-table>
              </div>
              <b-col md="12" class="my-1 p-0 pagination-justify">
                <b-pagination
                  v-model="currentPage"
                  :total-rows="totalRows"
                  :per-page="perPage"
                  aria-controls="my-table"
                  class="mt-4"
                ></b-pagination>
              </b-col>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { mapGetters } from "vuex";

export default {
  data() {
    return {
      modes: ["multi", "single", "range"],
      tablefields: [
        { key: "name", label: "Nom", sortable: true },
        { key: "email", label: "Email", sortable: false },
        { key: "lastLogin", label: "Derniere connection", sortable: false },
        { key: "action" },
      ],
      filter: null,
      totalRows: 1,
      currentPage: 1,
      perPage: 10,
      pageOptions: [5, 10, 15],
      selectMode: "multi",
      selected: [],
    };
  },
  created() {
    this.$store.dispatch("user/getUsers");
  },
  mounted() {
    // Set the initial number of items
    this.totalRows = this.items.length;
  },
  computed: {
    ...mapGetters({
      items: "user/getUsers",
    }),
    sortOptions() {
      // Create an options list from our fields
      return this.tablefields
        .filter((f) => f.sortable)
        .map((f) => {
          return { text: f.label, value: f.key };
        });
    },
    selectedRows() {
      return this.items.filter((item) => item.delete);
    },
  },
  methods: {
    deleteSelected(item) {
      // let objIndex = this.selected.findIndex((obj) => obj.id == item.id);
      // if (objIndex > -1) {
      //   this.selected.splice(objIndex, 1);
      // } else {
      //   this.selected.push(item);
      // }
    },
    getImgUrl(path) {
      return require("@/assets/images/users/" + path);
    },
    rowSelected(item) {
      this.selected = item;
      if (item[0]) {
        this.edit = this.edit !== item[0].id ? item[0].id : null;
      }
    },
    onFiltered(filteredItems) {
      // Trigger pagination to update the number of buttons/pages due to filtering
      this.totalRows = filteredItems.length;
      this.currentPage = 1;
    },
    deleteRow() {
      for (var i = this.items.length - 1; i >= 0; i--) {
        for (var j = 0; j < this.selected.length; j++) {
          if (this.items[i] && this.items[i].id === this.selected[j].id) {
            this.items.splice(i, 1);
          }
        }
      }
    },
    deleteBatchRow() {
      for (var i = 0; i < this.selected.length; i++) {
        if (this.items[i].id == this.selected[i].id) {
          this.items.splice(this.items[i], 1);
        }
      }
    },
    showMsgBoxTwo() {
      this.$bvModal
        .msgBoxConfirm("Are you sure!", {
          title: "Confirmation",
          size: "md",
          buttonSize: "sm",
          okVariant: "primary",
          okTitle: "YES",
          cancelTitle: "CANCLE",
          footerClass: "p-2",
          hideHeaderClose: false,
          // centered: true
        })
        .then((value) => {
          this.deleteRow();
          this.selected = [];
        })
        .catch((err) => {
          // An error occurred
        });
    },
  },
};
</script>
