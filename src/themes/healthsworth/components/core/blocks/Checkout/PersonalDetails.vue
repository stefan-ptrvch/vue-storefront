<template>
  <div class="personal-details">
    <div class="row pl20">
      <div class="col-xs-1 col-sm-2 col-md-1">
        <div
          class="number-circle lh35 cl-white brdr-circle align-center weight-700"
          :class="{ 'bg-cl-th-accent' : isActive || isFilled, 'bg-cl-tertiary' : !isFilled && !isActive }"
        >
          1
        </div>
      </div>
      <div class="col-xs-11 col-sm-9 col-md-11">
        <div class="row mb15">
          <div class="col-xs-12 col-md-7" :class="{ 'cl-bg-tertiary' : !isFilled && !isActive }">
            <h3 class="m0 mb5">
              {{ $t('Personal Details') }}
            </h3>
          </div>
          <div class="col-xs-12 col-md-5 pr30">
            <div class="lh30 flex end-lg" v-if="isFilled && !isActive">
              <a href="#" class="cl-tertiary flex" @click.prevent="edit">
                <span class="pr5">
                  {{ $t('Edit personal details') }}
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
          <base-input
            class="col-xs-12 col-md-6 mb25"
            type="text"
            :autofocus="true"
            name="first-name"
            :placeholder="$t('First name *')"
            v-model.trim="personalDetails.firstName"
            @blur="$v.personalDetails.firstName.$touch()"
            @keyup="emitValidation()"
            autocomplete="given-name"
            :validations="[
              {
                condition: $v.personalDetails.firstName.$error && !$v.personalDetails.firstName.required,
                text: $t('Field is required')
              },
              {
                condition: !$v.personalDetails.firstName.minLength,
                text: $t('Name must have at least 3 letters.')
              }
            ]"
          />

          <base-input
            class="col-xs-12 col-md-6 mb25"
            type="text"
            name="last-name"
            :placeholder="$t('Last name *')"
            v-model.trim="personalDetails.lastName"
            @blur="$v.personalDetails.lastName.$touch()"
            @keyup="emitValidation()"
            autocomplete="family-name"
            :validation="{
              condition: $v.personalDetails.lastName.$error && !$v.personalDetails.lastName.required,
              text: $t('Field is required')
            }"
          />
        </div>
      </div>
    </div>
    <div class="row pl20" v-if="!isActive && isFilled">
      <div class="hidden-xs col-sm-2 col-md-1"/>
      <div class="col-xs-12 col-sm-9 col-md-11">
        <div class="row fs16 mb35">
          <div class="col-xs-12 h4">
            <p>
              {{ personalDetails.firstName }} {{ personalDetails.lastName }}
            </p>
            <template v-if="createAccount && !currentUser">
              <base-checkbox
                class="mt25"
                id="createAccountCheckboxInfo"
                v-model="createAccount"
                disabled
              >
                {{ $t('Create a new account') }}
              </base-checkbox>
              <p class="h5 cl-tertiary">
                {{ $t('The new account will be created with the purchase. You will receive details on e-mail.') }}
              </p>
            </template>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { required, minLength, email, sameAs } from 'vuelidate/lib/validators'
import PersonalDetails from '@vue-storefront/core/components/blocks/Checkout/PersonalDetails'

import BaseCheckbox from 'theme/components/core/blocks/Form/BaseCheckbox'
import BaseInput from 'theme/components/core/blocks/Form/BaseInput'
import ButtonFull from 'theme/components/theme/ButtonFull'
import Modal from 'theme/components/core/Modal'
import Tooltip from 'theme/components/core/Tooltip'

export default {
  components: {
    ButtonFull,
    Tooltip,
    Modal,
    BaseCheckbox,
    BaseInput
  },
  mixins: [PersonalDetails],
  methods: {
    emitValidation () {
      this.$emit('valid', !(this.$v.personalDetails.$invalid))
    }
  },
  mounted () {
    this.personalDetails.emailAddress = 'asd@asd.com'
    this.emitValidation()
  },
  validations: {
    personalDetails: {
      firstName: {
        required,
        minLength: minLength(3)
      },
      lastName: {
        required
      },
      emailAddress: {
        required,
        email
      }
    },
    password: {
      required
    },
    rPassword: {
      required,
      sameAsPassword: sameAs('password')
    },
    acceptConditions: {
      required
    }
  }
}
</script>

<style lang="scss" scoped>
.link {
  text-decoration: underline;
}

.login-prompt {
  @media (min-width: 1200px) {
    margin-top: 30px;
  }
}
</style>
