<template>
<div>
    <div id="loader" v-if="loading"><div><img src="/content/img/cartLoading.svg"></div></div>
    <form @submit.prevent="submitShipping" v-if="showShipping">
      <div class="form-row form-row--half">
        <div class="form-field">
          <label class="form-label" for="email">Email</label>
          <input class="form-input" type="email" v-model="shippingAddress.email" name="email" required>
        </div>
        <div v-for="(field, index) in shippingFields" :key="index">
          <div class="form-field" v-if="field.name === 'phone'">
            <label class="form-label" :for="field.name"> {{ field.label }}</label>
            <input class="form-input" type="tel" v-model="shippingAddress.phone" :id="field.id" :name="field.name" :required="field.required ? true : false">
          </div>
          <div class="form-field" v-if="field.name === 'firstName'">
            <label class="form-label" :for="field.name"> {{ field.label }}</label>
            <input class="form-input" type="text" v-model="shippingAddress.firstName" :id="field.id" :name="field.name" :required="field.required ? true : false">
          </div>
          <div class="form-field" v-if="field.name === 'lastName'">
            <label class="form-label" :for="field.name"> {{ field.label }}</label>
            <input class="form-input" type="text" v-model="shippingAddress.lastName" :id="field.id" :name="field.name" :required="field.required ? true : false">
          </div>
          <div class="form-field" v-if="field.name === 'address1'">
            <label class="form-label" :for="field.name"> {{ field.label }}</label>
            <input class="form-input" type="text" v-model="shippingAddress.address1" :id="field.id" :name="field.name" :required="field.required ? true : false">
          </div>
          <div class="form-field" v-if="field.name === 'address2'">
            <label class="form-label" :for="field.name"> {{ field.label }}</label>
            <input class="form-input" type="text" v-model="shippingAddress.address2" :id="field.id" :name="field.name" :required="field.required ? true : false">
          </div>
          <div class="form-field" v-if="field.name === 'countryCode'">
            <label class="form-label" :for="field.name"> {{ field.label }}</label>
            <select class="form-select" v-model="shippingAddress.countryCode" :id="field.id" :name="field.name" :required="field.required ? true : false">
              <option value="">Choose a Country</option>
              <option selected value="US">United States</option>
            </select>
          </div>
          <div class="form-field" v-if="field.name === 'city'">
            <label class="form-label" :for="field.name"> {{ field.label }}</label>
            <input class="form-input" type="text" v-model="shippingAddress.city" :id="field.id" :name="field.name" :required="field.required ? true : false">
          </div>
          <div class="form-field" v-if="field.name === 'stateOrProvince'">
            <label class="form-label" :for="field.name"> {{ field.label }}</label>
            <input class="form-input" type="text" v-model="shippingAddress.stateOrProvince" :id="field.id" :name="field.name" :required="field.required ? true : false">
          </div>
          <div class="form-field" v-if="field.name === 'postalCode'">
            <label class="form-label" :for="field.name"> {{ field.label }}</label>
            <input class="form-input" type="text" v-model="shippingAddress.postalCode" :id="field.id" :name="field.name" :required="field.required ? true : false">
          </div>
        </div>
      </div>
      <div class="form-field continue-btn">
        <input type="submit" value="Continue" class="button button--CTA">
      </div>
    </form>
    <ShippingOptions :shippingOptions="shippingOptions,service" v-if="showShippingOptions" @click="clickShowParent"></ShippingOptions>
  </div>
</template>

<script>
import ShippingOptions from './ShippingOptions';
import Countries from './Countries'

export default {
  props: ['service'],
  data() {
    return {
      loading:false,
      shippingFields: [],
      showShipping:true,
      showShippingOptions:false,
      shippingAddress: {
        email:'',
        firstName:'',
        lastName:'',
        phone:'',
        countryCode:'',
        address1:'',
        address2:'',
        city:'',
        stateOrProvince:'',
        postalCode:''
      },
      shippingOptions:[]
    }
  },
  methods: {
    getShipFields() {
      const cartUrl = "/api/storefront/carts?include=";
      async function getShipFields(service) {
        const response = await fetch(cartUrl);
        const cartData = await response.json();
        const checkoutId = cartData[0].id;
        const state = await service.loadCheckout(checkoutId);
        return state;
      }
      getShipFields(this.service).then(state => {
        this.shippingFields = state.data.getShippingAddressFields();
      });
    },
    submitShipping() {
      let address = this.shippingAddress;
      this.loading = true;
      async function submitShipping(service) {
        const addship = await service.updateShippingAddress(address);
        const state = await service.updateBillingAddress(address);
        return state.data.getShippingOptions();
      }
      submitShipping(this.service).then(response => {
        this.shippingOptions = response;
        this.showShipping = false;
        this.showShippingOptions = true;
        this.loading = false;
        document.getElementById('CheckoutDeliv').classList.add('active');
        document.getElementById('CheckoutShip').classList.remove('active');
      });
    },
    clickShowParent() {
      this.showShipping = true;
      this.showShippingOptions = false;
      document.getElementById('CheckoutDeliv').classList.remove('active');
      document.getElementById('CheckoutShip').classList.add('active');
    }

  },
  created() {
    this.getShipFields();
  },
  components: {
    ShippingOptions,
    Countries
  }
}
</script>
