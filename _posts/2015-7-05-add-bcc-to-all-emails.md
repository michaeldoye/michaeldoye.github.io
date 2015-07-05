---
layout: post
title: "WooCommerce: Add BCC to all emails"
published: true
tags: 
  - php
  - woocommerce
---






Sometimes it is quite handy to have access to all emails that get sent out from WooCommerce. 

The snippet below will allow you to do this by adding a bcc header to all outgoing WooCommerce emails.


```javascript


add_filter( 'woocommerce_email_headers', 'add_bcc_all_emails', 10, 2);
 
function add_bcc_all_emails($headers, $object) {
 
    $headers = array();
    $headers[] = 'Bcc: Name <me@email.com>';
    $headers[] = 'Content-Type: text/html';
 
    return $headers;
}
```
