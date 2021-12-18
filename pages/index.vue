<!-- Please remove this file from your project -->
<template>
  <div
    class="relative flex items-top justify-center min-h-screen bg-gray-100 sm:items-center sm:pt-0"
  >
    <div class="max-w-4xl mx-auto sm:px-6 lg:px-8">
      <div class="flex justify-center pt-5 sm:pt-0">
        <LogoNuxt />
        <LogoOmise />
      </div>  
      <div class="mt-8 bg-white overflow-hidden shadow sm:rounded-lg p-6">
        <h2 class="text-2xl leading-7 font-semibold">
          Nuxt.JS + Omise Payment gateway (Dev mode)
        </h2>
        <hr class="mt-3 mb-3" />
        <form @submit.prevent="Submit" v-if="!omise.transaction.show">
          <InputLable title="Name">
            <Input type="text" required v-model="omise.form.name" />
          </InputLable>
          <InputLable title="E-mail">
            <Input type="email" required v-model="omise.form.email" />
          </InputLable>
          <InputLable title="Phone">
            <Input
              type="tel"
              required
              maxlength="10"
              pattern="[0-9]{10}"
              v-model="omise.form.phone"
            />
          </InputLable>
          <InputLable title="Items">
            <Select
              v-if="omise.shop.select.data.length > 0"
              v-model="omise.shop.select.value"
              :items="omise.shop.select.data"
            />
          </InputLable>
          <hr class="mt-3 mb-3" />
          <Button class="w-full" type="submit" :disabled="omise.button.loading">
            <div class="flex justify-center items-center">
              <IconSpinner v-if="omise.button.loading" />
              Pay
            </div>
          </Button>
        </form>
        <div v-if="omise.transaction.show && omise.transaction.status">
          <h2 class="text-2xl leading-7 font-semibold mb-3">Thank you 🥳</h2>
          <p>
            Your order has been placed. We will contact you soon. <br />
            Order ID: {{ omise.transaction.id }}
          </p>
        </div>
        <div v-if="omise.transaction.show && !omise.transaction.status">
          <h2 class="text-2xl leading-7 font-semibold mb-3">Whoops! Something went worng 😓</h2>
          <p>
            Your order has been failed. Please try again. <br />
          </p>
        </div>
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import Vue from 'vue'

import DataItem from '@/interfaces/dataItem'
import SelectOption from '@/interfaces/selectOption'
import TransactionStatus from '@/interfaces/transactionStatus'

export default Vue.extend({
  name: 'IndexPage',
  data() {
    return {
      omise: {
        publicKey: this.$config.publicKey,
        button: {
          loading: false,
        },
        form: {
          name: '',
          email: '',
          phone: '',
          items: '',
        },
        transaction: {
          id: '',
          show: false,
          status: false,
        },
        shop: {
          name: this.$config.shopname,
          items: {
            data: [] as DataItem[],
          },
          select: {
            value: '',
            data: [] as SelectOption[],
          },
        },
      },
    }
  },
  mounted() {
    this.$axios.get('/items/list').then((resp) => {
      const data = resp.data as DataItem[]

      this.omise.shop.items.data = data
      this.omise.shop.select.data = this.formatSelect(data)
    }).catch((err) => {
      if(!err.response){
        alert("Failed to get items list")
        this.omise.button.loading = true;
      }
    })
    window.OmiseCard.configure({
      publicKey: this.omise.publicKey,
    })
  },
  methods: {
    Submit() {
      if (this.omise.shop.select.value === '') return

      // Get Item
      const item = this.omise.shop.items.data[
        this.findIndexArrayOfObject(this.omise.shop.select.value)
      ] as DataItem

      this.omise.button.loading = true

      window.OmiseCard.open({
        amount: item.price,
        frameLabel: this.omise.shop.name,
        frameDescription: item.name,
        otherPaymentMethods: ['installment', 'truemoney'],
        onCreateTokenSuccess: (token: string) => {
          this.$axios
            .post('/payment', {
              token: token,
              name: this.omise.form.name,
              email: this.omise.form.email,
              phone: this.omise.form.phone,
              amount: item.price,
            })
            .then((resp) => {
              const data = resp.data as TransactionStatus
              this.omise.transaction.id = data.id
              this.omise.transaction.status = true

              this.ShowStatus();
            })
            .catch((_) => {
              this.ShowStatus();
            })
        },
        onFormClosed: () => {
          this.omise.button.loading = false
        },
      })
    },
    formatSelect(data: Array<any>): SelectOption[] {
      let resp = [] as SelectOption[]

      data.map((item) => {
        resp.push({
          selected: false,
          value: item.id,
          text: item.name,
        })
      })

      return resp
    },
    ShowStatus(){
      this.omise.button.loading = false
      this.omise.transaction.show = true
    },
    findIndexArrayOfObject(itemid: string): number {
      return (
        this.omise.shop.select.data.map((i: SelectOption) =>
          i.value.toString()
        ) as string[]
      ).indexOf(itemid)
    },
  },
})
</script>
