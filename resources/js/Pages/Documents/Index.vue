<template>
  <app-layout>
    <template #header>
      <div class="grid grid-cols-2">
        <h2 class="font-semibold text-lg text-white p-4">Transactions</h2>
        <div class="justify-end">
          <!-- <select
            v-model="yr_id"
            class="
              pr-2
              ml-2
              pb-2
              text-gray-700
              w-full
              lg:w-5/12
              rounded-md
              float-right
            "
            label="year"
            @change="yrch"
          >
            <option v-for="type in years" :key="type.id" :value="type.id">
              {{ type.name }}
            </option>
          </select> -->
          <Select
            v-model:value="selected_year"
            :options="years"
            :field-names="{ label: 'end', value: 'id' }"
            filterOption="true"
            optionFilterProp="name"
            mode="single"
            placeholder="Please select"
            showArrow
            @change="yrch"
            class="w-1/2"
          />
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
            class="w-1/2 ml-2"
          />
        </div>
      </div>
    </template>

    <div class="max-w-7xl mx-auto sm:px-6 lg:px-8 py-2">
      <!-- <div class="p-2 mr-2 mb-2 ml-2 flex flex-wrap"> -->

      <Button v-if="yearclosed && can['create']" @click="create" class="ml-2"
        >Create</Button
      >

      <InputSearch
        class="ml-2"
        v-model:value="search"
        placeholder="input search text"
        style="width: 200px"
        @search="onSearch"
      />

      <div v-if="can['create']" class="mt-2">
        <form @submit.prevent="submit">
          <input
            class="ml-4 border-gray-800 w-1/4 ring-gray-800 ring-1 outline-none"
            type="file"
            placeholder="Upload Excel Sheet"
            title="Upload Excel Sheet"
            v-on:change="onFileChange"
            accept=".xlsx"
          />
          <div
            class="
              ml-2
              bg-red-100
              border border-red-400
              text-red-700
              px-4
              py-1
              rounded
              inline-block
            "
            role="alert"
            v-if="errors.file"
          >
            {{ errors.file }}
          </div>
          <button
            class="
              inline-flex
              items-center
              px-4
              py-2
              bg-gray-800
              border border-transparent
              rounded-md
              font-bold
              text-xs text-white
              uppercase
              tracking-widest
              hover:bg-gray-700
              active:bg-gray-900
              focus:outline-none focus:border-gray-900 focus:shadow-outline-gray
              transition
              ease-in-out
              duration-150
              ml-2
            "
            type="submit"
          >
            Upload Sales Sheet
          </button>
          <a
            class="
              inline-flex
              items-center
              px-4
              py-2
              bg-gray-800
              border border-transparent
              rounded-md
              font-bold
              text-xs text-white
              uppercase
              tracking-widest
              hover:bg-gray-700
              active:bg-gray-900
              focus:outline-none focus:border-gray-900 focus:shadow-outline-gray
              transition
              ease-in-out
              duration-150
              ml-2
            "
            type="button"
            :href="'documents/downloadFile'"
          >
            <!-- @click="downloadFormat" -->
            Download Sales Format
          </a>
          <a
            class="
              inline-flex
              items-center
              px-4
              py-2
              bg-gray-800
              border border-transparent
              rounded-md
              font-bold
              text-xs text-white
              uppercase
              tracking-widest
              hover:bg-gray-700
              active:bg-gray-900
              focus:outline-none focus:border-gray-900 focus:shadow-outline-gray
              transition
              ease-in-out
              duration-150
              ml-2
            "
            type="button"
            target="_blank"
            :href="'documents/Accountpdf'"
          >
            <!-- @click="downloadFormat" -->
            Download Accounts
          </a>
        </form>
      </div>
      <!-- class="pr-2 ml-2 pb-2 w-full lg:w-1/4 rounded-md float-right" -->

      <!-- class="pr-2 ml-2 pb-2 w-full lg:w-1/4 rounded-md" -->

      <!--   <div v-if="errors.type">{{ errors.type }}</div> -->

      <!-- <div v-if="errors.type">{{ errors.type }}</div> -->
      <!-- </div> -->
      <!-- <div class="w-full px-8"> -->
      <!-- ml-8 mr-8 -->
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
                v-if="yearclosed && can['edit']"
                type="primary"
                @click="edit(record.id)"
                class="mr-2"
                >Edit</Button
              >
              <Button
                size="small"
                v-else
                type="primary"
                @click="edit(record.id)"
                class="mr-2"
                >Show</Button
              >
              <Button
                size="small"
                v-if="record.delete && can['delete']"
                danger
                @click="destroy(record.id)"
                >Delete</Button
              >
              <div
                class="
                  border
                  bg-gray-300
                  text-md
                  rounded-full
                  shadow-md
                  px-4
                  inline-block
                  hover:bg-gray-700 hover:text-white
                "
              >
                <a :href="'pd/' + record.id" target="_blank">Voucher in PDF</a>
              </div>
            </template>
          </template>
        </Table>
      </div>
    </div>
  </app-layout>
</template>

<script>
import AppLayout from "@/Layouts/AppLayout";
import { Button, Table, Select, InputSearch } from "ant-design-vue";
import JetButton from "@/Jetstream/Button";
import Paginator from "@/Layouts/Paginator";
import moment from "moment";
import { pickBy } from "lodash";
import { throttle } from "lodash";
import Multiselect from "@suadelabs/vue3-multiselect";
import { useForm } from "@inertiajs/inertia-vue3";

export default {
  components: {
    AppLayout,
    Button,
    Table,
    Select,
    InputSearch,

    JetButton,
    Paginator,
    throttle,
    pickBy,
    moment,
    Multiselect,
    useForm,
  },

  props: {
    mapped_data: Object,
    errors: Object,
    data: Object,
    filters: Object,
    companies: Object,
    company: Object,
    years: Object,
    year: Object,
    yearclosed: Object,
    can: Object,
  },

  setup() {
    const form = useForm({
      avatar: null,
    });
    return { form };
  },
  data() {
    return {
      // co_id: this.$page.props.co_id,
      co_id: this.company,
      options: this.companies,
      search: "",
      selected: this.company.name,
      yr_id: this.$page.props.yr_id,
      years: this.years,
      selected_year: this.year.name,

      columns: [
        // {
        //   title: "ID",
        //   dataIndex: "id",
        //   // sorter: (a, b) => a.id - b.id,
        //   width: "10%",
        // },
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
          title: "Actions",
          dataIndex: "actions",
          key: "actions",
        },
      ],

      params: {
        search: this.filters.search,
        field: this.filters.field,
        direction: this.filters.direction,
      },
    };
  },

  methods: {
    onSearch() {
      this.$inertia.get(
        route("documents"),
        {
          // select: select.value,
          // search: search.value
          search: this.search,
        },
        { replace: true, preserveState: true }
      );
    },
    //   for file upload
    submit() {
      if (this.form.avatar) {
        this.form.post(route("sales.trial.read"));
      } else {
        alert("Please select file first");
      }
    },
    downloadFormat() {
      this.$inertia.get(route("documents.downloadFile"));
    },

    onFileChange(e) {
      var files = e.target.files || e.dataTransfer.files;
      if (!files.length) return;
      this.form.avatar = files[0];
    },
    // file upload end
    create() {
      this.$inertia.get(route("documents.create"));
    },

    edit(id) {
      this.$inertia.get(route("documents.edit", id));
    },

    destroy(id) {
      this.$inertia.delete(route("documents.destroy", id));
    },

    coch(value) {
      this.$inertia.get(route("companies.coch", value));
    },
    // yrch() {
    //   this.$inertia.get(route("companies.yrch", this.yr_id));
    // },
    yrch() {
      this.$inertia.get(route("years.yrch", this.yr_id));
    },

    sort(field) {
      this.params.field = field;
      this.params.direction = this.params.direction === "asc" ? "desc" : "asc";
    },
    search_data() {
      let params = pickBy(this.params);
      this.$inertia.get(this.route("documents"), params, {
        replace: true,
        preserveState: true,
      });
    },
  },
  watch: {
    params: {
      handler: throttle(function () {
        let params = pickBy(this.params);
        if (params.search == null) {
          this.$inertia.get(this.route("documents"), params, {
            replace: true,
            preserveState: true,
          });
        }
      }, 150),
      deep: true,
    },
  },
};
</script>
