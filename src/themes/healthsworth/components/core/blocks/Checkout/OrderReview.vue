<template>
  <div class="order-review pt20">
    <div class="row pl20 pr20" v-show="isActive">
      <div class="hidden-xs col-sm-2 col-md-1"/>
      <div class="col-xs-12 col-sm-9 col-md-11">
        <div class="row mb15 mt20">
          <div class="col-xs-12">
            <div class="row">
              <div class="cartsummary-wrapper">
                <cart-summary />
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
    <modal name="modal-terms" static-data="terms">
      <p slot="header">
        {{ $t('Terms and conditions') }}
      </p>
    </modal>
  </div>
</template>

<script>
import { required } from 'vuelidate/lib/validators'
import Composite from '@vue-storefront/core/mixins/composite'

import BaseCheckbox from 'theme/components/core/blocks/Form/BaseCheckbox'
import ButtonFull from 'theme/components/theme/ButtonFull'
import CartSummary from 'theme/components/core/blocks/Checkout/CartSummary'
import Modal from 'theme/components/core/Modal'
import OrderReview from '@vue-storefront/core/components/blocks/Checkout/OrderReview'
import ValidationError from 'theme/components/core/ValidationError'

export default {
  components: {
    BaseCheckbox,
    ButtonFull,
    CartSummary,
    Modal,
    ValidationError
  },
  mixins: [OrderReview, Composite],
  mounted () {
    this.orderReview.terms = true
    this.$emit('valid', !(this.$v.orderReview.$invalid))
  },
  validations: {
    orderReview: {
      terms: {
        required
      }
    }
  }
}
</script>

<style lang="scss" scoped>
  .link {
    text-decoration: underline;
  }

  .cartsummary-wrapper {
    @media (min-width: 767px) {
      display: none;
    }
  }
</style>
