<template>
  <div id="checkout">
    <div class="container">
      <div class="row" v-show="!orderPlaced">
        <div class="col-sm-7 col-xs-12 pb70">
          <div class="checkout-title py5 px20">
            <h1>
              {{ $t('Checkout') }}
            </h1>
          </div>
          <personal-details
            ref="persDetails"
            class="line relative"
            :is-active="activeSection.personalDetails"
            :focused-field="focusedField"
            @valid="persDetailsReady=$event" />
          <shipping
            ref="shipping"
            class="line relative"
            :is-active="activeSection.shipping"
            @valid="shippingReady=$event"/>
          <payment
            ref="payment"
            class="line relative"
            :is-active="activeSection.payment"/>
          <order-review
            ref="orderOverview"
            class="line relative"
            :is-active="activeSection.orderReview"
            @valid="overviewReady=$event"/>
          <div id="custom-steps"/>

          <div class="row" v-if="true">
            <div class="hidden-xs col-sm-2 col-md-1"/>
            <div class="col-xs-12 col-sm-9 col-md-11">
              <div class="row">
                <div class="col-xs-12 col-md-8 my30 px20">
                  <button-full
                    @click.native="submitOrder"
                    :disabled="!allDataReady"
                  >
                    {{ $t('Place the order') }}
                  </button-full>
                </div>
              </div>
            </div>
          </div>

        </div>
        <div class="hidden-xs col-sm-5 bg-cl-secondary">
          <cart-summary />
        </div>
      </div>
    </div>
    <thank-you-page v-show="orderPlaced" />
  </div>
</template>

<script>
import Checkout from '@vue-storefront/core/pages/Checkout'

import PersonalDetails from 'theme/components/core/blocks/Checkout/PersonalDetails'
import Shipping from 'theme/components/core/blocks/Checkout/Shipping'
import Payment from 'theme/components/core/blocks/Checkout/Payment'
import OrderReview from 'theme/components/core/blocks/Checkout/OrderReview'
import CartSummary from 'theme/components/core/blocks/Checkout/CartSummary'
import ThankYouPage from 'theme/components/core/blocks/Checkout/ThankYouPage'
import ButtonFull from 'theme/components/theme/ButtonFull'

export default {
  components: {
    PersonalDetails,
    Shipping,
    Payment,
    OrderReview,
    CartSummary,
    ThankYouPage,
    ButtonFull
  },
  mixins: [Checkout],
  mounted () {
    // Make all checkout components visible
    this.activeSection.personalDetails = true
    this.activeSection.shipping = true
    this.activeSection.payment = true
    this.activeSection.orderReview = true
  },
  data () {
    return {
      persDetailsReady: false,
      shippingReady: false,
      paymentReady: true,
      overviewReady: false
    }
  },
  methods: {

    submitOrder () {
      // Call the submission methods of the components
      this.$refs.persDetails.sendDataToCheckout()
      this.$refs.shipping.sendDataToCheckout()
      this.$refs.payment.sendDataToCheckout()
      this.$refs.orderOverview.placeOrder()
    }
  },

  computed: {
    allDataReady () {
      return this.persDetailsReady &&
        this.shippingReady &&
        this.paymentReady &&
        this.overviewReady
    }
  }
}
</script>

<style lang="scss">
  @import '~theme/css/base/text';
  @import '~theme/css/variables/colors';
  @import '~theme/css/helpers/functions/color';
  $bg-secondary: color(secondary, $colors-background);
  $color-tertiary: color(tertiary);
  $color-secondary: color(secondary);
  $color-error: color(error);
  $color-white: color(white);
  $color-black: color(black);

  #checkout {
    .number-circle {
      width: 35px;
      height: 35px;

      @media (max-width: 768px) {
        width: 25px;
        height: 25px;
        line-height: 25px;
      }
    }
    .radioStyled {
      display: block;
      position: relative;
      padding-left: 35px;
      margin-bottom: 12px;
      cursor: pointer;
      font-size: 16px;
      line-height: 30px;
      -webkit-user-select: none;
      -moz-user-select: none;
      -ms-user-select: none;
      user-select: none;

      input {
        position: absolute;
        opacity: 0;
        cursor: pointer;
      }

      .checkmark {
        position: absolute;
        top: 0;
        left: 0;
        height: 25px;
        width: 25px;
        border-radius: 50%;
        border: 1px solid $bg-secondary;

        &:after {
          content: "";
          position: absolute;
          display: none;
          top: 3px;
          left: 3px;
          width: 19px;
          height: 19px;
          border-radius: 50%;
          background: $color-secondary;
        }
      }

      input:checked ~ .checkmark:after {
        display: block;
      }
    }
  }

  .line {
    &:after {
      content: '';
      display: block;
      position: absolute;
      top: 0;
      left: 37px;
      z-index: -1;
      width: 1px;
      height: 100%;
      background-color: $bg-secondary;

      @media (max-width: 768px) {
        display: none;
      }
    }
  }

  .checkout-title {
    @media (max-width: 767px) {
      background-color: $bg-secondary;
      margin-bottom: 25px;

      h1 {
        font-size: 36px;
      }
    }
  }
</style>
