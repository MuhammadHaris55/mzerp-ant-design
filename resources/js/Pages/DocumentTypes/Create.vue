<template>
  <app-layout>
    <template #header>
      <h2 class="font-semibold text-lg text-white p-4">Create Voucher</h2>
    </template>
    <div
      v-if="$page.props.flash.success"
      class="bg-yellow-600 text-white text-center"
    >
      {{ $page.props.flash.success }}
    </div>
    <div class="max-w-7xl mx-auto sm:px-6 lg:px-8 py-4">
      <div class="">
        <form @submit.prevent="form.post(route('documenttypes.store'))">
          <div class="p-2 mr-2 mb-2 mt-4 ml-6 flex flex-wrap">
            <label class="my-2 mr-8 text-right w-36 font-bold"
              >Voucher Name :</label
            ><input
              type="text"
              v-model="form.name"
              class="
                pr-2
                pb-2
                w-full
                lg:w-1/4
                rounded-md
                placeholder-indigo-300
              "
              label="name"
              placeholder="Enter Voucher name:"
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
              v-if="errors.name"
            >
              {{ errors.name }}
            </div>
          </div>

          <div class="px-4 py-2 flex justify-center items-center">
            <button
              class="
                border
                rounded-xl
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
              type="submit"
              :disabled="form.processing"
            >
              Create Voucher
            </button>
          </div>
        </form>
        <!-- <Form :v-model="form" @finish="onSubmit" @finishFailed="onError">
          <FormItem
            label="Username"
            name="username"
            :rules="[{ required: true, message: 'Please input voucher name!' }]"
          >
            <Input v-model:value="form.name" size="small" />
          </FormItem>
          <FormItem>
            <Button type="primary" html-type="submit">Create Voucher</Button>
          </FormItem>
        </Form> -->
      </div>
    </div>
  </app-layout>
</template>

<script>
import AppLayout from "@/Layouts/AppLayout";
import { useForm } from "@inertiajs/inertia-vue3";
import FlashMessage from "@/Layouts/FlashMessage";
import {
  Form,
  FormItem,
  Input,
  Button,
  Table,
  Select,
  InputSearch,
} from "ant-design-vue";
import "ant-design-vue/dist/antd.css";

export default {
  components: {
    AppLayout,
    FlashMessage,
    Form,
    FormItem,
    Input,
    Button,
    Table,
    Select,
    InputSearch,
  },

  props: {
    errors: Object,
  },

  setup() {
    const form = useForm({
      name: null,
    });
    return { form };
  },

  //   data() {
  //     return {
  //       form: this.$inertia.form({
  //         name: null,
  //       }),
  //     };
  //   },

  methods: {
    //         submit() {
    //             const response = await fetch("https://some.api/process-form", {
    //     method: "POST",
    //     body: _formData,
    //   },),},
    //     submit() {
    //       this.$inertia.post(route("documenttypes.store"), this.form);
    //     },
  },
};
</script>
