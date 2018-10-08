<template>
<div><div id="loader" v-if="loading"><div>Loading...</div></div>
  <div class="checkout--payment-wrapper">
    <div class="checkout--radio-options">
      <div v-for="(option, index) in paymentOptions" :key="index" class="form-field">
        <input ref="radioPayBtn" class="form-radio" type="radio" :id="option.id" :value="option.id" @click="togglePayInput">
        <label class="form-label" :for="option.id">{{option.config.displayName}} <img :src="option.logoUrl"></label>
        <div class="cc-wrapper" v-if="option.config.displayName === 'Credit Card'">
          <input type="number" v-model="creditCard.ccNumber">
          <input type="number" v-model="creditCard.ccNumber">
          <input type="number" v-model="creditCard.ccCvv">
          <input type="text" v-model="creditCard.ccType">
          <input type="text" v-model="creditCard.ccName">
        </div>
      </div>
      <div class="continue-btn">
        <div class="button button--CTA" @click="submitPayment">Pay For Order</div>
        <p class="checkout--previous-step" @click="goBack">Back to Delivery</p>
      </div>
    </div>
    </div>
    </div>
</template>

<script>
export default {
  props: ['paymentOptions','service'],
    data() {
    return {
      loading:false,
      paymentMethod:'',
      showShipping:true,
      showPayment:false,
      creditCard: {
        methodId: '',
        paymentData: {
          ccExpiry: { month: '', year: '' },
          ccName: '',
          ccNumber: '',
          ccType: '',
          ccCvv: '',
       },
      },
      orderData:[]
    }
  },
  methods: {
    goBack() {
      this.$emit('click')
    },
    togglePayInput(e) {
      this.$refs.radioPayBtn.forEach(el => {
        el.checked = false;
      });
      e.currentTarget.checked = true;
      this.paymentMethod = e.currentTarget.id;
    },
    submitPayment(){
      async function submitPayment(service,paymentMethod) {
        await service.initializePayment({ methodId: paymentMethod });
      }
      submitPayment(this.service, this.paymentMethod).then(response => {
        this.submitOrder(paymentMethod);
      });
    },
    submitOrder(paymentMethod) {
      async function submitOrder(service,payment) {
        const state = await service.submitOrder({ payment });
        return state.data.getOrder();
      }
      submitOrder(service,this.creditCard).then(response => {
         this.finalData = response
      });
    },
  }
}
</script>
