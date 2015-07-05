---
layout: post
tags: [github, github-pages, jekyll]
title: "WooCommerce / Javascript: Get product variation description"
published: true
---





When using product variations in WooCommerce, you have the option of adding a description for a single variation. However some themes do not show this by default.

![My Unicorn](http://i.imgur.com/4RDnPM1.png)

One way to have it show up in your theme is to fetch it using AJAX as the user changes his variation selection on the product page.

The following code should go into your functions.php - this is going to fetch the term description from the database based on it's slug. We will be sending the slug and name to this function using AJAX.

```php


/*
 * get_variation_desc
 *
 * Get the variation description via ajax
 */
function get_variation_desc(){
  $variation_term = get_term_by('slug', $_POST['variation_slug'], $_POST['variation_term_name'] );
  $variation_desc = $variation_term->description;
  die($variation_desc);
}
add_action( 'wp_ajax_nopriv_get_variation_desc', 'get_variation_desc' );
add_action( 'wp_ajax_get_variation_desc', 'get_variation_desc' );
```

Next, add a script file to your theme / plugin directory and [enqueue it](https://codex.wordpress.org/Function_Reference/wp_enqueue_script). Note the `is_product()` condition - we only want to load this script on product pages.

```php
add_action('wp_enqueue_scripts', 'get_var_desc' );

function get_var_desc() {
  if ( is_product() ) {
    $plugin_dir = '/wp-content/plugins/my-cool-plugin';
    wp_register_script( 'custom-script', $plugin_dir . '/js/variation-description.js', array('jquery'), '0.0.1', true );
    wp_enqueue_script('custom-script');
  }
}
```
Your jQuery should look something like this - you may need to make adjustments depending on how you have customized your product page (if at all)

```javascript

jQuery(document).ready(function () {
var $ = jQuery;
  // This is where the desctription will appear (change it what you need)
  $('td.label:first').find('label').after('<span class="varDesc" style="display:none;"></span>');

 //  On change of the select (or swatches) find the slug and name
  $('form.variations_form').on( 'change', 'div.select', function() {
    variation_slug = $(this).find('.selected').attr('data-name');
    variation_term_name = $(this).attr('id');
    if ( variation_slug ) {
      getVarDesc();
    };
  });

  // Function which will send a post request to our 
  // functions.php file (via admin-ajax.php)
  function getVarDesc() {
    var ajaxurl = '/wp-admin/admin-ajax.php';
    var data = {
      "action"       : 'get_variation_desc',
      variation_slug : variation_slug,
      variation_term_name : variation_term_name,
    };
    $.post( ajaxurl, data, getVarDescCallback );
  }

  // $.post callback
  function getVarDescCallback( data ) {
    $('.varDesc').fadeIn( 500 ).html( data );
  }

  // Clear the description when the reset link is clicked
  $('#variations_clear').on('click', function() {
    $('.varDesc').empty();
  });

});
```
