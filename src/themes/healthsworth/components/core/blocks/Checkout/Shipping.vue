<template>
  <div class="pt20">
    <div class="row pl20" v-if="isActive">
      <div class="hidden-xs col-sm-2 col-md-1"/>
      <div class="col-xs-11 col-sm-9 col-md-10">
        <div class="row">
          <base-select
            class="col-xs-12 col-sm-6 mb25"
            name="countries"
            :options="countryOptions"
            :selected="shipping.country"
            :placeholder="$t('Country') + ' *'"
            :validations="[
              {
                condition: $v.shipping.country.$error && !$v.shipping.country.required,
                text: $t('Field is required')
              }
            ]"
            v-model="shipping.country"
            autocomplete="country-name"
            @blur="$v.shipping.country.$touch()"
            @change="$v.shipping.country.$touch(); changeCountry();
                     emitValidation()"
          />

          <base-input
            class="col-xs-12 col-sm-6 mb25"
            type="text"
            name="phone-number"
            :placeholder="$t('Phone Number') + ' *'"
            v-model.trim="shipping.phoneNumber"
            autocomplete="tel"
            @blur="$v.shipping.phoneNumber.$touch()"
            @keyup="emitValidation()"
            :validations="[
              {
                condition: $v.shipping.phoneNumber.$error && !$v.shipping.phoneNumber.required,
                text: $t('Field is required')
              }
            ]"
          />
        </div>
      </div>
    </div>
    <div class="row pl20" v-if="!isActive && isFilled">
      <div class="hidden-xs col-sm-2 col-md-1"/>
      <div class="col-xs-12 col-sm-9 col-md-11">
        <div class="row fs16 mb35">
          <div class="col-xs-12 h4" data-testid="shippingAddressSummary">
            <p>
              <span v-if="shipping.state">{{ shipping.state }}, </span>
              <span>{{ getCountryName() }}</span>
            </p>
            <div v-if="shipping.phoneNumber">
              <span class="pr15">{{ shipping.phoneNumber }}</span>
              <tooltip>{{ $t('Phone number may be needed by carrier') }}</tooltip>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { required, minLength } from 'vuelidate/lib/validators'
import shipping from '@vue-storefront/core/components/blocks/Checkout/Shipping'

import BaseCheckbox from 'theme/components/core/blocks/Form/BaseCheckbox'
import BaseInput from 'theme/components/core/blocks/Form/BaseInput'
import BaseSelect from 'theme/components/core/blocks/Form/BaseSelect'
import ButtonFull from 'theme/components/theme/ButtonFull'
import Tooltip from 'theme/components/core/Tooltip'

import { currentStoreView } from '@vue-storefront/store/lib/multistore'

export default {
  components: {
    ButtonFull,
    Tooltip,
    BaseCheckbox,
    BaseInput,
    BaseSelect
  },
  mixins: [shipping],
  methods: {
    emitValidation () {
      this.$emit('valid', !(this.$v.shipping.$invalid))
    }
  },
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
  mounted () {
    this.shipping.firstName = 'Asd'
    this.shipping.lastName = 'Asd'
    this.shipping.streetAddress = 'Asd'
    this.shipping.apartmentNumber = '123'
    this.shipping.zipCode = '123'
    this.shipping.city = 'Asd'
    this.shipping.shippingMethod = 'flatrate'
    if (currentStoreView().storeCode === 'nl') {
      this.shipping.country = 'NL'
      this.changeCountry()
    }
    this.emitValidation()
  },
  validations: {
    shipping: {
      firstName: {
        required,
        minLength: minLength(3)
      },
      lastName: {
        required
      },
      country: {
        required
      },
      streetAddress: {
        required
      },
      apartmentNumber: {
        required
      },
      shippingMethod: {
        required
      },
      zipCode: {
        required,
        minLength: minLength(3)
      },
      city: {
        required
      },
      phoneNumber: {
        required
      }
    }
  }
}
</script>
