<template>
  <div>
    <b-table
      sticky-header="580px"
      head-variant="dark"
      striped
      hover
      :items="items"
      :fields="fields"
      :filter="filter"
      selectable
      @row-selected="onRowSelected"
      responsive="sm"
      :select-mode="selectMode"
      primary-key="a_id"
      :tbody-transition-props="transProps"
      class="flip-list-move"
    >
      <template #cell(selected)="{ rowSelected }">
        <template v-if="rowSelected">
          <span aria-hidden="true">&check;</span>
          <span class="sr-only">Selected</span>
        </template>
        <template v-else>
          <span aria-hidden="true">&nbsp;</span>
          <span class="sr-only">Not selected</span>
        </template>
      </template>
    </b-table>
    <div class="filter">
      <b-input-group size="sm">
        <b-form-input
          v-model="filter"
          type="search"
          id="filterInput"
          placeholder="Type to Search"
        ></b-form-input>
        <b-input-group-append>
          <b-button :disabled="!filter" @click="filter = ''">Clear</b-button>
        </b-input-group-append>
      </b-input-group>
    </div>
    <div class="button">
      <b-button-toolbar>
        <b-button-group>
          <b-button variant="warning" href="#/add_airline">
            <b-icon icon="plus-circle" aria-hidden="true"></b-icon> Add
          </b-button>
          <b-button
            variant="info"
            href="#/airline_information"
            @click="chooseairline"
          >
            <b-icon icon="file-earmark" aria-hidden="true"></b-icon> Information
          </b-button>
          <b-button variant="primary" href="#/admin_management">
            <b-icon icon="back" aria-hidden="true"></b-icon> Back
          </b-button>
        </b-button-group>
      </b-button-toolbar>
    </div>
  </div>
</template>

<script>
export default {
  name: "ShowAirline",
  props: ["alerter"],
  data: function () {
    return {
      transProps: {
        name: "flip-list",
      },
      fields: [
        "selected",
        { key: "flight_num" },
        "start",
        "destination",
        { key: "start_time", sortable: true },
        { key: "standard_price", sortable: true },
        { key: "mileage", sortable: true },
        { key: "quota", sortable: true },
      ],
      filter: null,
      items: [],
      selectMode: "range",
      selected: [],
      a_id: "",
    };
  },
  methods: {
    // 展示所有符合条件的航线
    get_airline: function () {
      this.$axios({
        url: this.serverURL + "admin/get_airline",
        method: "post",
        data: {
          id: this.$cookies.get("id"),
        },
      }).then((response) => {
        let data = response.data;
        if (data.success) {
          console.log(data.airline_info);
          this.items = data.airline_info;
        } else {
          this.alerter("错误", data);
        }
      });
    },
    onRowSelected: function (items) {
      this.selected = items;
      this.$cookies.set("a_id", this.selected[0].a_id);
    },
    chooseairline: function () {
      this.$cookies.set("a_id", this.selected[0].a_id);
    },
  },
  created: function () {
    this.get_airline();
  },
};
</script>

<style scoped>
.flip-list-move {
  transition: transform 1s;
}
.filter {
  width: 50%;
  top: 234px;
  margin: 0px auto;
  position: relative;
}
.button {
  width: 30%;
  left: 100px;
  top: 40px;
  margin: 0px auto;
  position: relative;
}
</style>