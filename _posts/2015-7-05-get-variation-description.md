---
layout: post
title: "WooCommerce / Javascript: Get product varitation description"
published: true
---





When using product variations in WooCommerce, you have the option of adding a description for a single variation. However some themes do not show this by default.

![My Unicorn](http://i.imgur.com/4RDnPM1.png)


```php


add_action( 'woocommerce_email_before_order_table', 'add_order_email_instructions', 10, 2 );
 
function add_order_email_instructions( $order, $sent_to_admin ) {
  
  if ( ! $sent_to_admin ) {
 
    if ( 'cod' == $order->payment_method ) {
      // cash on delivery method
      echo '<p><strong>Instructions:</strong> Full payment is due immediately upon delivery: <em>cash only, no exceptions</em>.</p>';
    } else {
      // other methods (ie credit card)
      echo '<p><strong>Instructions:</strong> Please look for "Madrigal Electromotive GmbH" on your next credit card statement.</p>';
    }
  }
}
```
