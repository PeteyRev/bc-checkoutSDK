<template>
<div>
<div class="checkout--header">
  <div class="left">
    <img src="https://cdn8.bigcommerce.com/s-r4tr0/images/stencil/250x100/logo_new_1487761119__90276.png">
  </div>
  <div class="right">
    <div class="checkout--totals">
      <span>Total:</span> ${{totals.subtotal}}
      <p>Shipping: ${{totals.shippingCostTotal}}</p>
    </div>
  </div>

</div>
  <div class="checkout--navigation">
    <div id="CheckoutShip" class="cn--shipping active">1. Shipping</div>
    <div id="CheckoutDeliv" class="cn--delivery">2. Delivery</div>
    <div id="CheckoutPay" class="cn--payment">3. Payment</div>
  </div>
</div>
</template>

<script>
export default {
  props: ['service'],
  data() {
    return{
      totals:[]
    }
  },
  methods: {
    cartTotals() {
      const cartUrl = "/api/storefront/carts?include=";
      async function cartTotals(service) {
        const response = await fetch(cartUrl);
        const cartData = await response.json();
        const checkoutId = cartData[0].id;
        const state = await service.loadCheckout(checkoutId);
        return state;
      }
      cartTotals(this.service).then(state => {
        const response = state.data.getCheckout();
        response.subtotal = response.subtotal.toFixed(2);
        response.shippingCostTotal = response.shippingCostTotal.toFixed(2);
        this.totals = response;
      });
    }
  },
  created() {
    this.cartTotals();
  },
};
</script>
