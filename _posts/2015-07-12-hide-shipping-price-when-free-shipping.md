---
layout: post
title: Hide shipping price when free shipping is available
published: true
tags:
  - php
  - woocommerce
---

By default, WooCommerce shows the shipping price during checkout even when free
shipping is available.

The following snippet will unset flat_rate shipping if free shipping is set during
checkout.


```ruby
/**
 * woocommerce_package_rates is a 2.1+ hook
 * Hide shipping rates when free shipping is available
 */

add_filter( 'woocommerce_package_rates', 'hide_shipping_when_free_is_available', 10, 2 );

/**
 * @param array $rates Array of rates found for the package
 * @param array $package The package array/object being shipped
 * @return array of modified rates
 */
function hide_shipping_when_free_is_available( $rates, $package ) {

  // Only modify rates if free_shipping is present
    if ( isset( $rates['free_shipping'] ) ) {

      // This unsets the single flat_rate shipping
      unset( $rates['flat_rate'] );

      // This unsets all methods except for free_shipping
      $free_shipping          = $rates['free_shipping'];
      $rates                  = array();
      $rates['free_shipping'] = $free_shipping;
  }

  return $rates;
}
```
