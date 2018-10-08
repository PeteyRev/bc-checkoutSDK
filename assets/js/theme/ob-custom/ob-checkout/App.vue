<template>
<div class="checkout--wrapper">
    <Header :service="service"></Header>
    <div class="checkout--body">
      <ShippingAddress :service="service"></ShippingAddress>
    </div>
</div>
</template>

<script>
import Header from './components/Header';
import ShippingAddress from './components/ShippingAddress';
import { createCheckoutService } from "@bigcommerce/checkout-sdk";

export default {
  data() {
    return {
      service:''
    }
  },
  components: {
    Header,
    ShippingAddress
  },
  methods: {
    initCheckout() {
      const service = createCheckoutService();
      this.service = service;
    }
  },
  created() {
    this.initCheckout();
  }
}
</script>
