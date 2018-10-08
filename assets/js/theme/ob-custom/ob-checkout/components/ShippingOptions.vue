<template>
<div>
  <div id="loader" v-if="loading"><div><img src="/content/img/cartLoading.svg"></div></div>
  <div class="checkout--payment-options">
    <div class="checkout--radio-options" v-if="showShippingOptions">
      <div v-for="(option, index) in shippingOptions" :key="index" class="form-field">
        <input ref="radioBtn" class="form-radio" type="radio" :id="option.id" :value="option.id" @click="toggleInput">
        <label class="form-label" :for="option.id">{{option.description}} <span class="checkout-price">${{option.cost.toFixed(2)}}</span></label>
      </div>
      <div class="continue-btn">
        <div class="button button--CTA" @click="addShipping">Select Shipping</div>
        <p class="checkout--previous-step" @click="goBack">Back to Shipping</p>
      </div>
    </div>
    <PaymentOptions :paymentOptions="paymentOptions" v-if="showPayment" @click="clickShowParent"></PaymentOptions>
  </div>
  </div>
</template>

<script>
import PaymentOptions from './PaymentOptions';

export default {
  props: ['shippingOptions','service'],
  data() {
    return {
      loading:false,
      chosenShipping:'',
      paymentOptions:[],
      showShippingOptions:true,
      showPayment:false
    }
  },
  methods: {
    goBack() {
      this.$emit('click')
    },
    clickShowParent() {
      this.showShippingOptions = true;
      this.showPayment = false;
      document.getElementById('CheckoutDeliv').classList.add('active');
      document.getElementById('CheckoutPay').classList.remove('active');
    },
    toggleInput(e) {
      this.$refs.radioBtn.forEach(el => {
        el.checked = false;
      });
      e.currentTarget.checked = true;
      this.chosenShipping = e.currentTarget.id;
    },
    addShipping() {
      this.loading = true;
      async function updateShipping(service,shipping) {
        const newState = await service.selectShippingOption(shipping);
      }
      updateShipping(this.service,this.chosenShipping).then(response => {
        this.loadPaymentMethods();
      });
    },
    loadPaymentMethods() {
      async function fetchPaymentMethods(service) {
        const state = await service.loadPaymentMethods();
        return state.data.getPaymentMethods();
      }
      fetchPaymentMethods(this.service).then(response => {
        this.paymentOptions = response;
        this.showShippingOptions = false;
        this.showPayment = true;
        this.loading = false;
        document.getElementById('CheckoutDeliv').classList.remove('active');
        document.getElementById('CheckoutPay').classList.add('active');
      });
    }
  },
  components: {
    PaymentOptions
  }
}
</script>
