<template>
  <div id="product" itemscope itemtype="http://schema.org/Product">
    <section class="bg-cl-secondary px20 product-top-section">
      <div class="container">
        <section class="row m0 between-xs">
          <div class="col-xs-12 col-md-6 center-xs middle-xs image">
            <product-gallery
              :gallery="gallery"
              :offline="offlineImage"
              :configuration="configuration"
              :product="product"
            />
          </div>
          <div class="col-xs-12 col-md-5 data">
            <breadcrumbs
              class="pt40 pb20 hidden-xs"
              :routes="breadcrumbs.routes"
              :active-route="breadcrumbs.name"
            />
            <h1 class="mb20 mt0 cl-mine-shaft product-name" data-testid="productName" itemprop="name">
              {{ product.name | htmlDecode }}
            </h1>
            <div class="mb20 uppercase cl-secondary">
              sku: {{ product.sku }}
            </div>
            <div itemprop="offers" itemscope itemtype="http://schema.org/Offer">
              <meta itemprop="priceCurrency" :content="currentStore.i18n.currencyCode">
              <meta itemprop="price" :content="parseFloat(product.priceInclTax).toFixed(2)">
              <div
                class="mb40 price serif"
                v-if="product.type_id !== 'grouped'"
              >
                <div
                  class="h3 cl-secondary"
                  v-if="product.special_price && product.priceInclTax && product.originalPriceInclTax"
                >
                  <span class="h2 cl-mine-shaft weight-700">
                    {{ product.priceInclTax * product.qty | price }}
                  </span>&nbsp;
                  <span class="price-original h3">
                    {{ product.originalPriceInclTax * product.qty | price }}
                  </span>
                </div>
                <div
                  class="h2 cl-mine-shaft weight-700"
                  v-if="!product.special_price && product.priceInclTax"
                >
                  {{ product.priceInclTax * product.qty | price }}
                </div>
              </div>
            </div>
            <product-links
              v-if="product.type_id =='grouped' && !loading"
              :products="product.product_links"
            />
            <product-bundle-options
              v-if="product.bundle_options && product.bundle_options.length > 0 && !loading"
              :product="product"
            />
            <product-custom-options
              v-else-if="product.custom_options && product.custom_options.length > 0 && !loading"
              :product="product"
            />
            <div class="row m0">
              <add-to-cart
                :product="product"
                class="col-xs-12 col-sm-4 col-md-6"
              />
            </div>
            <div class="row py40 add-to-buttons">
              <div class="col-xs-6 col-sm-3 col-md-6">
                <button
                  @click="isOnWishlist ? removeFromWishlist(product) : addToWishlist(product)"
                  class="
                    p0 inline-flex middle-xs bg-cl-transparent brdr-none
                    action h5 pointer cl-secondary
                  "
                  type="button"
                  data-testid="addToWishlist"
                >
                  <i class="pr5 material-icons">{{ favoriteIcon }}</i>
                  <template v-if="!isOnWishlist">
                    {{ $t('Add to favorite') }}
                  </template>
                  <template v-else>
                    {{ $t('Remove') }}
                  </template>
                </button>
              </div>
              <div class="col-xs-6 col-sm-3 col-md-6">
                <button
                  @click="isOnCompare ? removeFromList('compare') : addToList('compare')"
                  class="
                    p0 inline-flex middle-xs bg-cl-transparent brdr-none
                    action h5 pointer cl-secondary
                  "
                  type="button"
                  data-testid="addToCompare"
                >
                  <i class="pr5 material-icons">compare</i>
                  <template v-if="!isOnCompare">
                    {{ $t('Add to compare') }}
                  </template>
                  <template v-else>
                    {{ $t('Remove from compare') }}
                  </template>
                </button>
              </div>
            </div>
          </div>
        </section>
      </div>
    </section>
    <tabs>
      <tab name="Details" :selected="true">
        <section class="container px15 pt50 pb35 cl-accent details">
          <div
            class="h4 details-wrapper"
            :class="{'details-wrapper--open': detailsOpen}"
          >
            <div class="row between-md m0">
              <div class="col-xs-12 col-sm-6">
                <div
                  class="lh30 h5"
                  itemprop="description"
                  v-html="product.description"
                />
              </div>
              <div class="col-xs-12 col-sm-6">
                <div
                  class="lh30 h5"
                  v-html="product.short_description"
                />
              </div>
            </div>
          </div>
        </section>
      </tab>
      <tab name="Reviews">
        <reviews v-show="OnlineOnly"/>
      </tab>
    </tabs>
    <related-products
      type="upsell"
      :heading="$t('We found other products you might like')"
    />
  </div>
</template>

<script>
import Product from '@vue-storefront/core/pages/Product'
import VueOfflineMixin from 'vue-offline/mixin'
import RelatedProducts from 'theme/components/core/blocks/Product/Related.vue'
import Reviews from 'theme/components/core/blocks/Reviews/Reviews.vue'
import AddToCart from 'theme/components/core/AddToCart.vue'
import GenericSelector from 'theme/components/core/GenericSelector'
import ColorSelector from 'theme/components/core/ColorSelector.vue'
import SizeSelector from 'theme/components/core/SizeSelector.vue'
import Breadcrumbs from 'theme/components/core/Breadcrumbs.vue'
import ProductAttribute from 'theme/components/core/ProductAttribute.vue'
import ProductTile from 'theme/components/core/ProductTile.vue'
import ProductLinks from 'theme/components/core/ProductLinks.vue'
import ProductCustomOptions from 'theme/components/core/ProductCustomOptions.vue'
import ProductBundleOptions from 'theme/components/core/ProductBundleOptions.vue'
import ProductGallery from 'theme/components/core/ProductGallery'
import PromotedOffers from 'theme/components/theme/blocks/PromotedOffers/PromotedOffers'
import focusClean from 'theme/components/theme/directives/focusClean'

// Tabs
import Tab from 'theme/components/core/Tab.vue'
import Tabs from 'theme/components/core/Tabs.vue'

export default {
  components: {
    AddToCart,
    Breadcrumbs,
    ColorSelector,
    GenericSelector,
    ProductAttribute,
    ProductBundleOptions,
    ProductCustomOptions,
    ProductGallery,
    ProductLinks,
    ProductTile,
    PromotedOffers,
    RelatedProducts,
    Reviews,
    SizeSelector,
    Tab,
    Tabs
  },
  mixins: [Product, VueOfflineMixin],
  data () {
    return {
      detailsOpen: false
    }
  },
  directives: { focusClean },
  computed: {
    favoriteIcon () {
      return this.isOnWishlist ? 'favorite' : 'favorite_border'
    }
  },
  methods: {
    showDetails (event) {
      this.detailsOpen = true
      event.target.classList.add('hidden')
    }
  }
}
</script>

<style lang="scss" scoped>
@import '~theme/css/variables/colors';
@import '~theme/css/helpers/functions/color';
$color-primary: color(primary);
$color-tertiary: color(tertiary);
$color-secondary: color(secondary);
$color-white: color(white);
$bg-secondary: color(secondary, $colors-background);

.breadcrumbs {
  @media (max-width: 767px) {
    margin: 15px 0;
    padding: 15px 0 0 15px;
  }
}

.error {
  color: red;
  font-weight: bold;
  padding-bottom: 15px;
}
.data {
  @media (max-width: 767px) {
    border-bottom: 1px solid $bg-secondary;
  }
}

.image {
  @media (max-width: 1023px) {
    margin-bottom: 20px;
  }
}

.product-name {
  @media (max-width: 767px) {
    font-size: 36px;
  }
}

.price {
  @media (max-width: 767px) {
    color: $color-primary;
  }
}

.variants-label {
  @media (max-width: 767px) {
    font-size: 14px;
  }
}

.variants-wrapper {
  @media (max-width: 767px) {
    padding-bottom: 30px;
  }

 .sizes {
    @media (max-width: 767px) {
      width: 100%;
    }
  }

  .size-guide {
    height: 40px;
    @media (max-width: 767px) {
      width: 100%;
      margin-left: 0;
    }
  }
}

.product-top-section {
  @media (max-width: 767px) {
    padding: 0;
    background-color: $color-white;
  }
}

.add-to-buttons {
  @media (max-width: 767px) {
    padding-top: 30px;
    margin-bottom: 40px;
  }
}

.details {
  @media (max-width: 767px) {
    padding: 50px 15px 15px;
  }
}

.details-title {
  padding: 0 8px;

  @media (max-width: 767px) {
    font-size: 18px;
    margin: 0;
  }
}

.details-wrapper {
  @media (max-width: 767px) {
    position: relative;
    max-height: none;
    overflow: hidden;
    transition: all 0.3s ease;
    font-size: 14px;
  }

  &--open {
    max-height: none;
  }
}

.details-overlay {
  @media (max-width: 767px) {
    position: absolute;
    height: 75%;
    bottom: 0;
    left: 0;
    width: 100%;
    margin: 0;
    cursor: pointer;
    background: linear-gradient(rgba($color-white, 0), rgba($color-white, 1));
    &.hidden {
      display: none;
    }
  }
}

.price-original {
  text-decoration: line-through;
}

.action {
  &:hover {
    color: $color-tertiary;
  }
}

.attributes {
  list-style-type: none;
}

.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.3s;
}

.fade-enter,
.fade-leave-to /* .fade-leave-active below version 2.1.8 */ {
  opacity: 0;
}

.product-image {
  mix-blend-mode: multiply;
  width: 460px;
}

.qty-input {
  border-style: solid;
  border-width: 0 0 1px 0;
  width: 90px;
}

.qty-label {
  margin-bottom: 12px;
  cursor: pointer;
  font-size: 14px;
}
</style>
