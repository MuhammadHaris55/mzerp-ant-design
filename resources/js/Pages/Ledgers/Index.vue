<template>
  <app-layout>
    <template #header>
      <div class="grid grid-cols-2">
        <h2 class="font-semibold text-lg text-white p-4">Ledgers</h2>
        <div class="flex justify-end items-center">
          <Select
            v-model:value="selected"
            :options="options"
            :field-names="{ label: 'name', value: 'id' }"
            filterOption="true"
            optionFilterProp="name"
            mode="single"
            placeholder="Please select"
            showArrow
            @change="coch"
            class="w-1/2"
          />
        </div>
      </div>
    </template>

    <div class="max-w-7xl mx-auto sm:px-6 lg:px-8 py-2">
      <FlashMessage />

      <form
        target="_blank"
        @submit.prevent="submit_range"
        v-bind:action="'range/' + form.account_id['id']"
        ref="form_range"
      >
        <!-- <form @submit.prevent="submit"> -->
        <multiselect
          class="
            ml-2
            w-full
            inline-block
            lg:w-1/4
            border border-black
            rounded-md
            float-left
          "
          v-model="form.account_id"
          :options="option"
          placeholder="Select account group"
          label="name"
          track-by="id"
          @update:model-value="getledger"
        ></multiselect>

        <input
          v-model="form.date_start"
          :min="form.start"
          :max="form.end"
          type="date"
          label="date"
          placeholder="Enter Begin date:"
          class="pr-2 ml-2 pb-2 rounded-md"
          @change="getledger"
          name="date_start"
        />
        <div v-if="errors.date_start">{{ errors.date_start }}</div>

        <input
          v-model="form.date_end"
          :min="form.start"
          :max="form.end"
          type="date"
          class="pr-2 ml-2 pb-2 rounded-md"
          label="date"
          placeholder="Enter End date:"
          @change="getledger"
          name="date_end"
        />
        <div v-if="errors.date_end">{{ errors.date_end }}</div>
        <Button html-type="submit" class="ml-2">Ledger Report</Button>

        <!-- <div
          class="
            border
            rounded-lg
            shadow-md
            p-1
            px-4
            mt-1
            bg-gray-800
            text-white
            ml-2
            inline-block
            hover:bg-gray-700 hover:text-white
          "
        >
          <button type="submit">Ledger Report</button>
        </div> -->
        <div class="relative overflow-x-auto mt-2 ml-2 sm:rounded-2xl">
          <Table
            :columns="columns"
            :data-source="mapped_data"
            :loading="loading"
            class="mt-2"
            size="small"
          >
            <template #bodyCell="{ column, record }">
              <template v-if="column.key === 'actions'">
                <!-- v-if="can['edit'] || can['delete']" -->
                <Button
                  size="small"
                  v-if="can['edit']"
                  type="primary"
                  @click="edit(record.id)"
                  class="mr-2"
                  >Edit</Button
                >
                <Button
                  size="small"
                  v-if="record.delete && can['delete']"
                  danger
                  @click="destroy(record.id)"
                  >Delete</Button
                >
              </template>
            </template>
          </Table>
        </div>
      </form>
    </div>
  </app-layout>
</template>

<script>
import AppLayout from "@/Layouts/AppLayout";
import FlashMessage from "@/Layouts/FlashMessage";
import { Button, Table, Select, InputSearch } from "ant-design-vue";
import "ant-design-vue/dist/antd.css";

import JetButton from "@/Jetstream/Button";
import { useForm } from "@inertiajs/inertia-vue3";
import Multiselect from "@suadelabs/vue3-multiselect";

export default {
  components: {
    AppLayout,
    FlashMessage,
    Button,
    Table,
    Select,
    JetButton,
    Multiselect,
  },

  props: {
    errors: Object,
    data: Object,
    companies: Object,
    company: Object,
    accounts: Array,
    account_first: Object,
    entries: Object,
    debits: Object,
    credits: Object,
    balance: Object,
    prebal: Object,
    date_start: Object,
    date_end: Object,
    min_start: Object,
    max_end: Object,
    mapped_data: Object,
  },

  data() {
    return {
      // co_id: this.$page.props.co_id,
      co_id: this.company,
      options: this.companies,
      search: "",
      selected: this.company.name,
      option: this.accounts,

      columns: [
        {
          title: "Reference",
          dataIndex: "ref",
          // sorter: (a, b) => {
          //     const nameA = a.name.toUpperCase();
          //     const nameB = b.name.toUpperCase();
          //     if (nameA < nameB) {
          //         return -1;
          //     }
          //     if (nameA > nameB) {
          //         return 1;
          //     }
          //     return 0;
          //     },
          width: "20%",
        },
        {
          title: "Date",
          dataIndex: "date",
        },
        {
          title: "Description",
          dataIndex: "description",
        },
        {
          title: "Debit",
          dataIndex: "debit",
        },
        {
          title: "Credit",
          dataIndex: "credit",
        },
        {
          title: "Balance",
          dataIndex: "balance",
        },
      ],

      //   form: this.$inertia.form({
      //     account_id: this.account_first.id,
      //     date_start: null,
      //     date_end: null,
      //   }),

      //   form: this.$refs.form({
      //     account_id: this.account_first.id,
      //     date_start: "null",
      //     date_end: null,
      //   }),

      form: {
        account_id: this.account_first ? this.account_first : this.accounts[0],
        date_start: this.date_start ? this.date_start : this.min_start,
        date_end: this.date_end ? this.date_end : this.min_start,
        start: this.min_start
          ? this.min_start
          : new Date().toISOString().substr(0, 10),
        end: this.max_end
          ? this.max_end
          : new Date().toISOString().substr(0, 10),
        // end: new Date().toISOString().substr(0, 10),
      },
    };
  },
  //   setup(props) {
  //     const form = useForm({
  //       account_id: props.account_first.id,
  //       date_start: null,
  //       date_end: "",
  //     });
  //     return { form };
  //   },

  methods: {
    // ------------------------- To generate PDF ------------------------
    submit_range: function () {
      this.$refs.form_range.submit();
    },
    // ----------------------- To generate on screen ledger -----------------------
    getledger() {
      //   this.$inertia.get(route("getledger", this.form.account_id));
      this.$inertia.get(route("ledgers", this.form));
    },
    meth() {
      this.form.get(route("range"));
    },
    //TO GENERATE AN PDF WITH BUTTON
    submit: function () {
      this.$refs.form.submit();
    },
    // submit() {
    //   //   entries = this.entries;
    //   //   if (this.difference === 0) {
    //   this.form.post(route("range"));
    //   //   this.$inertia.get(route("range"), this.form);
    //   //   } else {
    //   //     alert("Entry is not equal");
    //   //   }
    // },

    create() {
      this.$inertia.get(route("years.create"));
    },

    // route() {
    //   this.$inertia.post(route("range"), this.form);
    //   //   this.$inertia.get(route("range"));
    // },

    route() {
      // this.$inertia.post(route("companies.store"), this.form);
      this.$inertia.get(route("pd"));
    },

    route() {
      // this.$inertia.post(route("companies.store"), this.form);
      this.$inertia.get(route("trialbalance"));
    },

    edit(id) {
      this.$inertia.get(route("years.edit", id));
    },

    destroy(id) {
      this.$inertia.delete(route("years.destroy", id));
    },
    coch(value) {
      this.$inertia.get(route("companies.coch", value));
    },
  },
};
</script>
