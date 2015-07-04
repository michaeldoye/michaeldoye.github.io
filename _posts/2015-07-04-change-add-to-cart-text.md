---
layout: post
title: "WooCommerce: Change “add to cart” button text"
published: true
---




The below snippet will allow you to change the add to cart button text on single product pages for WooCommerce.


```php

add_filter( 'woocommerce_product_single_add_to_cart_text', 'woo_custom_cart_button_text' );

function woo_custom_cart_button_text() {

	global $woocommerce;
	
	foreach($woocommerce->cart->get_cart() as $cart_item_key => $values ) {
		$_product = $values['data'];
	
		if( get_the_ID() == $_product->id ) {
			return __('Already in cart - Add Again?', 'woocommerce');
		}
	}
	
	return __('Add to cart', 'woocommerce');
}

/**
 * Change the add to cart text on product archives
 */
add_filter( 'add_to_cart_text', 'woo_archive_custom_cart_button_text' );

function woo_archive_custom_cart_button_text() {

	global $woocommerce;
	
	foreach($woocommerce->cart->get_cart() as $cart_item_key => $values ) {
		$_product = $values['data'];
	
		if( get_the_ID() == $_product->id ) {
			return __('Already in cart', 'woocommerce');
		}
	}
	
	return __('Add to cart', 'woocommerce');
}
```
