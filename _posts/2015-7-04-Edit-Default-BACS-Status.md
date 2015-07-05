---
layout: post
title: "WooCommerce: How to edit the default payment status for BACS orders"
published: true
tags: 
  - php
---






By default, WooCommerce sets new orders placed via BACS (EFT) to the "on-hold" order status. This is sometimes a pain in the neck if your order flow needs to be customized.

The snippet below will change the default status for BACS orders.  Simply add it to your theme functions.php file.

```javascript

// Remove the gateways filter so we can add our own
remove_filter( 'woocommerce_payment_gateways', 'core_gateways' );
// Add our own one back
add_filter( 'woocommerce_payment_gateways', 'my_core_gateways' );

/**
 * Our custom core_gateways function.
 *
 * @access public
 * @param mixed $methods
 * @return void
 */
function my_core_gateways( $methods ) {
    $methods[] = 'WC_Gateway_BACS_custom';
    return $methods;
}

class WC_Gateway_BACS_custom extends WC_Gateway_BACS {

  /**
   * Process the payment and return the result
   *
   * @access public
   * @param int $order_id
   * @return array
   */
   
  function process_payment( $order_id ) {
    global $woocommerce;
    $order = new WC_Order( $order_id );

    // Mark as processing (or anything you want)
    $order->update_status('processing', __( 'Awaiting BACS payment', 'woocommerce' ));

    // Reduce stock levels
    $order->reduce_order_stock();

    // Remove cart
    $woocommerce->cart->empty_cart();     

    // Return thankyou redirect
    return array(
        'result'  => 'success',
        'redirect'  => $this->get_return_url( $order )
    );
  } 
}
```
