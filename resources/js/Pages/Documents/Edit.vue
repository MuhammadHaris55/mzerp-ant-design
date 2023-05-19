<template>
  <app-layout>
    <template #header>
      <h2 class="font-semibold text-lg text-white p-4">Edit Transactions</h2>
    </template>
    <div class="max-w-7xl mx-auto sm:px-6 lg:px-8 py-4">
      <div class="">
        <form @submit.prevent="submit">
          <!--
        @submit.prevent="
          this.difference == 0 ? form.post(route('documents.store')) : ''
        "
        -->
          <!-- DOCUMENT TYPE ID -->
           <a-form-item label="Select Voucher :" :label-col="{ span: 4 }"
                            :wrapper-col="{ span: 14 }">
            <a-input
                  type="text"
                  v-model:value="document.type_name"
                  disabled
                  label="ref"
                  placeholder="Enter Voucher"
                />
             </a-form-item>

             <a-form-item label="Reference :" :label-col="{ span: 4 }"
                                :wrapper-col="{ span: 14 }">
                <a-input
                      type="text"
                      v-model:value="document.ref"
                      disabled
                      label="ref"
                      placeholder="Enter Voucher"
                    />
                 </a-form-item>

          <!-- <div class="p-2 mr-2 mb-2 ml-6 flex flex-wrap">
            <label class="my-2 mr-8 text-right w-36 font-bold"
              >Select Voucher :</label
            >
            <input
              type="text"
              v-model="document.type_name"
              disabled
              class="
                disabled:opacity-50
                pr-2
                pb-2
                w-full
                lg:w-1/4
                rounded-md
                placeholder-indigo-300
              "
              label="ref"
              placeholder="Enter Voucher"
            />
            <div v-if="errors.type">{{ errors.type }}</div>
          </div> -->
          <!-- DESCRIPTION -->
            <a-form-item label="Description :" :label-col="{ span: 4 }"
                        :wrapper-col="{ span: 14 }">
                <a-textarea :disabled="can['edit'] ? disabled : ''" v-model:value="form.description" placeholder="Enter your Description" />

                <div class="text-red-700 px-4 py-2" role="alert" v-if="errors.description">
                    {{ errors.description }}
                </div>
            </a-form-item>

          <!-- DESCRIPTION -->
          <!-- <div class="p-2 mr-2 mb-2 mt-4 ml-6 flex flex-wrap">
            <label class="my-2 mr-8 text-right w-36 font-bold"
              >Description :</label
            ><input
              type="text"
              v-model="form.description"
              class="
                disabled:opacity-50
                pr-2
                pb-2
                w-full
                lg:w-1/4
                rounded-md
                placeholder-indigo-300
              "
              label="description"
              placeholder="Enter Description"
              :disabled="can['edit'] ? disabled : ''"
            />
            <div
              class="
                ml-2
                bg-red-100
                border border-red-400
                text-red-700
                px-4
                py-2
                rounded
                relative
              "
              role="alert"
              v-if="errors.description"
            >
              {{ errors.description }}
            </div>
          </div> -->
          <!-- DATE -->

            <a-form-item label="Select Date :" :label-col="{ span: 4 }"
                        :wrapper-col="{ span: 14 }">
                <a-input
                v-model:value="form.date"
                type="date"
                :min="form.start"
                :max="form.end"
                required
                :disabled="can['edit'] ? disabled : ''"
                />
                <div class="text-red-700 px-4 py-2" role="alert" v-if="errors.date">
                    {{ errors.date }}
                </div>
            </a-form-item>
                    <a-form-item v-if="errors.entries" style="color: #c53a2e" label="Error :" :label-col="{ span: 4 }"
                        :wrapper-col="{ span: 14 }">
                <div v-if="errors.entries" class="bg-red-100 border px-4 border-red-400 text-red-700  rounded" role="alert">
                    {{ errors.entries }}
                </div>
            </a-form-item>


          <!-- <div class="p-2 mr-2 mb-2 mt-4 ml-6 flex flex-wrap">
            <label class="my-2 mr-8 text-right w-36 font-bold"
              >Select Date :</label
            ><input
              type="date"
              v-model="form.date"
              class="pr-2 pb-2 rounded-md placeholder-indigo-300"
              label="date"
              placeholder="Date:"
              name="date"
              :min="form.start"
              :max="form.end"
              :disabled="can['edit'] ? disabled : ''"
            />

            <div v-if="errors.date">{{ errors.date }}</div>
          </div> -->
          <!-- TABLE FOR ENTRIES ---- START ------------- -->
          <div class="panel-body flex justify-center items-start">
            <table class="table flex">
              <thead class="">
                <tr>
                  <th>Account:</th>
                  <th>Debit:</th>
                  <th>Credit:</th>
                </tr>
              </thead>
              <tbody>
                <tr v-for="(entry, index) in form.entries" :key="entry.id">
                  <!-- <tr v-for="(entry, index) in entries" :key="entry.id"> -->
                  <td>
                    <a-select
                    style="width:100%"
                    show-search
                    v-model:value="entry.account_id.id"
                    :options="option"
                    :field-names="{ label: 'name', value: 'id' }"
                    filterOption="true"
                    optionFilterProp="name"
                    mode="single"
                    placeholder="Select Account"
                    :disabled="can['edit'] ? disabled : ''"
                    showArrow
                    />
                    <!-- <multiselect
                      class="w-full rounded-md border border-black"
                      v-model="entry.account_id"
                      :options="option"
                      placeholder="Select account"
                      label="name"
                      track-by="id"
                      :disabled="can['edit'] ? disabled : ''"
                    ></multiselect> -->
                    <!-- <select v-model="entry.account_id" class="rounded-md w-36">
                      <option
                        v-for="account in accounts"
                        :key="account.id"
                        :value="account.id"
                      >
                        {{ account.name }}
                      </option>
                    </select> -->
                  </td>
                  <td>
                     <a-input
                          v-model:value="entry.debit"
                          type="number"
                          @change="debitchange(index)"
                          class="rounded-md w-36"
                         :disabled="can['edit'] ? disabled : ''"
                          />
                        <div class="text-red-700 px-4" role="alert" v-if="errors['entries.' + index + '.account_id']">
                            {{ ' - ' }}
                        </div>
                    <!-- <input
                      v-model="entry.debit"
                      type="number"
                      @change="debitchange(index)"
                      class="rounded-md w-36"
                      :disabled="can['edit'] ? disabled : ''"
                    /> -->
                  </td>
                  <td>
                       <a-input
                          v-model:value="entry.credit"
                          type="number"
                          @change="creditchange(index)"
                          class="rounded-md w-36"
                        :disabled="can['edit'] ? disabled : ''"
                        />
                        <div class="text-red-700 px-4" role="alert" v-if="errors['entries.' + index + '.account_id']">
                        {{ ' - ' }}
                        </div>
                    <!-- <input
                      v-model="entry.credit"
                      type="number"
                      @change="creditchange(index)"
                      class="rounded-md w-36"
                      :disabled="can['edit'] ? disabled : ''"
                    /> -->
                  </td>
                  <td>
                    <a-button
                      @click.prevent="deleteRow(index)"
                      v-if="index > 1 && can['edit']"
                      type="danger"

                    >
                      Delete
                    </a-button>
                    <a-button
                      v-else-if="index == 1 && can['edit']"

                      type="primary"
                      ghost
                      @click.prevent="addRow"
                    >
                      Add row
                    </a-button>
                    <!-- <div v-if="isError">{{ firstError }}</div> -->
                  </td>
                </tr>

                <tr v-if="can['edit']">
                  <th>Difference:</th>
                  <th>Debit:</th>
                  <th>Credit:</th>
                  <th></th>
                </tr>

                <tr v-if="can['edit']">
                  <td>
                    <a-input
                      type="number"
                      v-model:value="difference"
                      readonly
                      class="rounded-md w-full"
                    />
                  </td>
                  <td>
                    <a-input
                      type="number"
                      v-model:value="debit"
                      readonly
                      class="rounded-md w-36"
                    />
                  </td>
                  <td>
                    <a-input
                      type="number"
                      v-model:value="credit"
                      readonly
                      class="rounded-md w-36"
                    />
                  </td>
                  <td></td>
                </tr>
              </tbody>
            </table>
            <div v-if="isError">{{ firstError }}</div>
          </div>
          <!-- TABLE FOR ENTRIES ---- END ------------- -->

          <div
            v-if="can['edit']"
            class="px-4 py-2 flex justify-center items-center"
          >
            <!-- <a-button
              class="
                border
                rounded-xl
                shadow-md
                p-1
                px-4
                mt-4
                bg-gray-800
                text-white
                ml-2
                inline-block
                hover:bg-gray-700 hover:text-white
              "

              type="submit"
            >
              Update Transaction
            </a-button> -->
             <a-button type="primary" :disabled="form.processing" @click="submit()">  Update Transaction</a-button>

          </div>
        </form>
      </div>
    </div>
  </app-layout>
</template>

<script>
import AppLayout from "@/Layouts/AppLayout";
import { useForm } from "@inertiajs/inertia-vue3";
// import Label from "../../Jetstream/Label.vue";
import Datepicker from "vue3-datepicker";
import format from "date-fns/format";
import Multiselect from "@suadelabs/vue3-multiselect";
import {
    Form,
    Input,
    Button,
    Select,
} from "ant-design-vue";



export default {
  components: {
    AppLayout,
    Datepicker,
    format,
    Multiselect,
        "a-form": Form,
        "a-form-item": Form.Item,
        "a-input": Input,
        "a-textarea": Input.TextArea,
        "a-button": Button,
        "a-select": Select,
  },

  props: {
    errors: Object,

    doc_types: Object,
    doc_type_first: Object,

    document: Object,
    entriess: Object,

    accounts: Object,
    account_first: Object,

    min_start: Object,
    max_end: Object,

    can: Object,
  },

  data() {
    return {
      option: this.accounts,

      difference: 0,
      credit: 0,
      debit: 0,
      total: 0,
      //   count: entries.length,
      isError: null,
      form: this.$inertia.form({
        // entries: this.entries,
        start: this.min_start
          ? this.min_start
          : new Date().toISOString().substr(0, 10),
        end: this.max_end
          ? this.max_end
          : new Date().toISOString().substr(0, 10),
        type_id: this.document.type_id,
        date: this.document.date,
        description: this.document.description,

        entries: this.entriess,
      }),
    };
  },

  methods: {
    submit() {
      //   entries = this.entries;
      if (this.difference === 0) {
        this.$inertia.put(
          route("documents.update", this.document.id),
          this.form
        );
      } else {
        alert("Entry is not equal");
      }
    },
    //ON CHANGE FUNCTION ON DEBIT CREDIT TO NULL THE PARALLEL VALUES ---START ----
    debitchange(index) {
      let a = this.form.entries[index];
      a.credit = 0;

      this.tdebit();
      this.tcredit();
    },
    creditchange(index) {
      let b = this.form.entries[index];
      b.debit = 0;
      console.log(b.credit);

      this.tcredit();
      this.tdebit();
    },
    //ON CHANGE FUNCTION ON DEBIT CREDIT TO NULL THE PARALLEL VALUES --- END ----

    // CALCULATING TOTAL AMOUNT OF DEBIT AND CREDIT ----START ----------------
    tcredit() {
      let dtotal = 0;
      for (var i = 0; i < this.form.entries.length; i++) {
        dtotal = dtotal + parseInt(this.form.entries[i].credit);
      }
      this.credit = dtotal;
    },
    tdebit() {
      let dtotal = 0;
      for (var i = 0; i < this.form.entries.length; i++) {
        dtotal = dtotal + parseInt(this.form.entries[i].debit);
      }
      this.debit = dtotal;
    },
    // CALCULATING TOTAL AMOUNT OF DEBIT AND CREDIT ---- END ----------------

    //TO CHECK THAT THE DEBIT CREDIT ARE NOT ZERO --------------- STARTS ------------------
    checkingZero() {
      for (var i = 0; i < this.form.entries.length; i++) {
        if (
          this.form.entries[i].credit == 0 &&
          this.form.entries[i].debit == 0
        ) {
          this.difference = null;
          alert("Please fill debit OR credit field of a Transaction");
        }
      }
    },
    //TO CHECK THAT THE DEBIT CREDIT ARE NOT ZERO --------------- END ------------------

    addRow() {
      this.form.entries.push({
        // account_id: this.account_first.id,
        account_id: this.accounts[0],
        debit: 0,
        credit: 0,
      });
      this.difference = null;

      count += 1;
      console.log(count);
    },
    deleteRow(index) {
      this.form.entries.splice(index, 1);
      this.tcredit();
      this.tdebit();
      //   this.creditchange(index);
      //   this.debitchange(index);
    },
  },
  mount: {
    // tcredit() {
    //   let dtotal = 0;
    //   for (var i = 0; i < this.form.entries.length; i++) {
    //     dtotal = dtotal + parseInt(this.form.entries[i].credit);
    //   }
    //   this.credit = dtotal;
    // },
    // tdebit() {
    //   let dtotal = 0;
    //   for (var i = 0; i < this.form.entries.length; i++) {
    //     dtotal = dtotal + parseInt(this.form.entries[i].debit);
    //   }
    //   this.debit = dtotal;
    // },
  },
  watch: {
    //  FOR DIFFERENCE OF DEBIT CREDIT ---START -----
    debit: function () {
      this.checkingZero();
      let diff = 0;
      if (this.debit == 0 && this.credit == 0) {
        this.difference = null;
      } else {
        diff = parseInt(this.debit) - parseInt(this.credit);
        this.difference = diff;
      }
    },
    credit: function () {
      this.checkingZero();

      let diff = 0;
      console.log(this.credit);
      if (this.debit == 0 && this.credit == 0) {
        this.difference = null;
      } else {
        diff = parseInt(this.debit) - parseInt(this.credit);
        this.difference = diff;
      }
    },
    //  FOR DIFFERENCE OF DEBIT CREDIT --- END -----
  },
};
</script>
