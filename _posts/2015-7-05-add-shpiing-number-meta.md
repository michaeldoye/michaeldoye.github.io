---
layout: post
title: "WooCommerce: add a shpping link or number to your orders"
published: true
---





Sending a customer their shipping or way-bill number can certainly come in quite handy, here's how you can add a meta box to the admin order area that allows you to save a shipping number or link.

First we display the meta box in the order admin area:

```javascript


/**
 * Display Metabox Shipment Tracking on order admin page
 **/

add_action( 'add_meta_boxes', 'MD_add_meta_boxes' );

function MD_add_meta_boxes(){

  add_meta_box(
    'woocommerce-order-my-custom',
    __( 'Shipping Number' ),
    'order_my_custom',
    'shop_order',
    'side',
    'default'
  );

}

/**
 * Add fields to the metabox
 **/

function order_my_custom( $post ){
  wp_nonce_field( 'save_MD_shipping', 'MD_shipping_nonce' );

  woocommerce_wp_text_input(
    array(
      'id' => '_MD_tracking',
      'class' => '',
      'label' => __('Shipping no: ', 'woocommerce')
    )
  );

}
```

Next, we want to be able to save the meta info we add when clicking the save-order button

```javascript

/**
 * Save Shipping Tracking information
 **/

add_action( 'save_post', 'save_MD_shipping' );

function save_MD_shipping( $post_id ) {

  // Check if nonce is set
  if ( ! isset( $_POST['MD_shipping_nonce'] ) ) {
    return $post_id;
  }

  if ( ! wp_verify_nonce( $_POST['MD_shipping_nonce'], 'save_MD_shipping' ) ) {
    return $post_id;
  }

  // Check that the logged in user has permission to edit this post
  if ( ! current_user_can( 'edit_post' ) ) {
    return $post_id;
  }

  //$shipping_service = sanitize_text_field( $_POST['_MD_shipping_service'] );
  $tracking_link = sanitize_text_field( $_POST['_MD_tracking'] );
 // update_post_meta( $post_id, '_MD_shipping_service', $shipping_service );
  update_post_meta( $post_id, '_MD_tracking', $tracking_link );
}
```
And lastly, you can optionally create a shortcode to be used in order emails or anywhere else on your site

```javascript

/**
 *  Add shortcode
 **/
function shipping_shortcode( $atts ) {
  extract( shortcode_atts(
    array(
      'order' => '',
    ), $atts )
  );
  return '<p><b>Shipping No:</b> '.get_post_meta( $order,  '_MD_tracking', true ) .'</p>';
}
add_shortcode( 'shipping', 'shipping_shortcode' );
```

The shortcode can be used as such: `[sipping order=00000]` - simply feed it the order number.
