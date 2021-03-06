<template>
  <div class="payment pt20">
    <div class="row pl20" v-if="isActive">
      <div class="hidden-xs col-sm-2 col-md-1"/>
      <div class="col-xs-11 col-sm-9 col-md-10">
        <div class="row" v-if="isActive">

          <div class="col-xs-12">
            <h4>
              {{ $t('Payment method') }}
            </h4>
          </div>
          <div v-for="(method, index) in paymentMethods" :key="index" class="col-md-6">
            <label v-if="method.name !== 'Stripe'" class="radioStyled"> {{ $t(method.title ? method.title : method.name) }}
              <input
                v-if="method.name !== 'Stripe'"
                type="radio"
                :value="method.code"
                name="payment-method"
                v-model="payment.paymentMethod"
                @change="$v.payment.paymentMethod.$touch(); changePaymentMethod();"
              >
              <span class="checkmark"/>
            </label>
          </div>
          <span class="validation-error" v-if="!$v.payment.paymentMethod.required">{{ $t('Field is required') }}</span>
        </div>
      </div>
    </div>
    <div class="row pl20" v-if="!isActive && isFilled">
      <div class="hidden-xs col-sm-2 col-md-1"/>
      <div class="col-xs-12 col-sm-9 col-md-11">
        <div class="row fs16 mb35">
          <div class="col-xs-12 h4">
            <p>
              {{ payment.firstName }} {{ payment.lastName }}
            </p>
            <p>
              {{ payment.streetAddress }} {{ payment.apartmentNumber }}
            </p>
            <p>
              {{ payment.city }} {{ payment.zipCode }}
            </p>
            <p>
              <span v-if="payment.state">{{ payment.state }}, </span>
              <span>{{ getCountryName() }}</span>
            </p>
            <div v-if="payment.phoneNumber">
              <span class="pr15">{{ payment.phoneNumber }}</span>
              <tooltip>{{ $t('Phone number may be needed by carrier') }}</tooltip>
            </div>
            <p v-if="generateInvoice">
              {{ payment.company }} {{ payment.taxId }}
            </p>
            <div class="col-xs-12">
              <h4>{{ $t('Payment method') }}</h4>
            </div>
            <div class="col-md-6 mb15">
              <label class="radioStyled"> {{ getPaymentMethod().title }}
                <input type="radio" value="" checked disabled name="chosen-payment-method">
                <span class="checkmark"/>
              </label>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { required } from 'vuelidate/lib/validators'
import Payment from '@vue-storefront/core/components/blocks/Checkout/Payment'

import BaseCheckbox from 'theme/components/core/blocks/Form/BaseCheckbox'
import BaseInput from 'theme/components/core/blocks/Form/BaseInput'
import BaseSelect from 'theme/components/core/blocks/Form/BaseSelect'
import ButtonFull from 'theme/components/theme/ButtonFull'
import Tooltip from 'theme/components/core/Tooltip'

export default {
  components: {
    BaseCheckbox,
    BaseInput,
    BaseSelect,
    ButtonFull,
    Tooltip
  },
  mixins: [Payment],
  computed: {
    countryOptions () {
      return this.countries.map((item) => {
        return {
          value: item.code,
          label: item.name
        }
      })
    }
  },
  validations () {
    return {
      payment: {
        paymentMethod: {
          required
        }
      }
    }
  }
}
</script>
