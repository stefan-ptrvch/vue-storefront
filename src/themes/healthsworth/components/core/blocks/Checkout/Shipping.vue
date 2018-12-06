<template>
  <div class="pt20">
    <div class="row pl20">
      <div class="col-xs-1 col-sm-2 col-md-1">
        <div
          class="number-circle lh35 cl-white brdr-circle align-center weight-700"
          :class="{ 'bg-cl-th-accent' : isActive || isFilled, 'bg-cl-tertiary' : !isFilled && !isActive }"
        >
          2
        </div>
      </div>
      <div class="col-xs-11 col-sm-9 col-md-11">
        <div class="row mb15">
          <div class="col-xs-12 col-md-7" :class="{ 'cl-bg-tertiary' : !isFilled && !isActive }">
            <h3 class="m0 mb5">
              {{ $t('Shipping') }}
            </h3>
          </div>
          <div class="col-xs-12 col-md-5 pr30">
            <div class="lh30 flex end-lg" v-if="isFilled && !isActive">
              <a href="#" class="cl-tertiary flex" @click.prevent="edit">
                <span class="pr5">
                  {{ $t('Edit shipping') }}
                </span>
                <i class="material-icons cl-tertiary">edit</i>
              </a>
            </div>
          </div>
        </div>
      </div>
    </div>
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
            @change="$v.shipping.country.$touch(); changeCountry();"
          />

          <base-input
            class="col-xs-12 col-sm-6 mb25"
            type="text"
            name="phone-number"
            :placeholder="$t('Phone Number') + ' *'"
            v-model.trim="shipping.phoneNumber"
            autocomplete="tel"
            @blur="$v.shipping.phoneNumber.$touch()"
            :validations="[
              {
                condition: $v.shipping.phoneNumber.$error && !$v.shipping.phoneNumber.required,
                text: $t('Field is required')
              }
            ]"
          />

          <h4 class="col-xs-12">
            {{ $t('Shipping method') }}
          </h4>
          <div v-for="(method, index) in shippingMethods" :key="index" class="col-md-6">
            <label class="radioStyled"> {{ method.method_title }} | {{ method.amount | price }}
              <input
                type="radio"
                :value="method.method_code"
                name="shipping-method"
                v-model="shipping.shippingMethod"
                @change="$v.shipping.shippingMethod.$touch(); changeShippingMethod();"
              >
              <span class="checkmark"/>
            </label>
          </div>
          <span class="validation-error" v-if="$v.shipping.shippingMethod.$error && !$v.shipping.shippingMethod.required">
            {{ $t('Field is required') }}
          </span>
        </div>
      </div>
    </div>
    <div class="row" v-if="isActive">
      <div class="hidden-xs col-sm-2 col-md-1"/>
      <div class="col-xs-12 col-sm-9 col-md-11">
        <div class="row">
          <div class="col-xs-12 col-md-8 my30 px20">
            <button-full
              data-testid="shippingSubmit"
              @click.native="sendDataToCheckout"
              :disabled="$v.shipping.$invalid"
            >
              {{ $t('Continue to payment') }}
            </button-full>
          </div>
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
            <div class="col-xs-12">
              <h4>
                {{ $t('Shipping method') }}
              </h4>
            </div>
            <div class="col-md-6 mb15">
              <label class="radioStyled"> {{ getShippingMethod().method_title }} | {{ getShippingMethod().amount | price }}
                <input type="radio" value="" checked disabled name="chosen-shipping-method">
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
    if (currentStoreView().storeCode === 'nl') {
      this.shipping.country = 'NL'
      this.changeCountry()
    }
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
