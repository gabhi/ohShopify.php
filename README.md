# shopify.php

Lightweight multi-paradigm PHP (JSON) client for the [Shopify API](http://api.shopify.com/).


## Requirements

* PHP 4 with [cURL support](http://php.net/manual/en/book.curl.php).
* Only compatible OAuth Shopify Apps.  For Legacy Authentication: [Use an old version of ohShopify.php](https://github.com/cmcdonaldca/ohShopify.php/blob/7ee7a344ca83518a0560ba585d4f8deab65bf5cd/shopify.php)


## Getting Started

Basic needs for authorization and redirecting

```php
<?php

    require 'shopify.php';

    /* Define your APP`s key and secret*/

    define('SHOPIFY_API_KEY','bee53c8118c830a404d4c6c103c31a04');
    define('SHOPIFY_SECRET','75fce0e0b8851b0e089ae5b20b513826');

    /* Define requested scope (access rights) - checkout https://docs.shopify.com/api/authentication/oauth#scopes   */
    define('SHOPIFY_SCOPE','read_products,read_orders,write_orders'); //eg: define('SHOPIFY_SCOPE','read_orders,write_orders');

//print_r($_SESSION);

    if (isset($_GET['code'])) { // if the code param has been sent to this page... we are in Step 2
        // Step 2: do a form POST to get the access token
        $shopifyClient = new ShopifyClient($_GET['shop'], "", SHOPIFY_API_KEY, SHOPIFY_SECRET);
        session_unset();

        // Now, request the token and store it in your session.
        $_SESSION['token'] = $shopifyClient->getAccessToken($_GET['code']);
        if ($_SESSION['token'] != '')
            $_SESSION['shop'] = $_GET['shop'];

        //header("Location: index.php");
        //exit;       
    }
    // if they posted the form with the shop name
   // else if (isset($_POST['shop'])) {
else{
        // Step 1: get the shopname from the user and redirect the user to the
        // shopify authorization page where they can choose to authorize this app
        $shop = isset($_POST['shop']) ? $_POST['shop'] : $_GET['shop'];
        $shopifyClient = new ShopifyClient($shop, "", SHOPIFY_API_KEY, SHOPIFY_SECRET);

        // get the URL to the current page
        $pageURL = 'http';
        if ($_SERVER["HTTPS"] == "on") { $pageURL .= "s"; }
        $pageURL .= "://";
        if ($_SERVER["SERVER_PORT"] != "80") {
            $pageURL .= $_SERVER["SERVER_NAME"].":".$_SERVER["SERVER_PORT"].$_SERVER["SCRIPT_NAME"];
        } else {
            $pageURL .= $_SERVER["SERVER_NAME"].$_SERVER["SCRIPT_NAME"];
        }

        // redirect to authorize url
        header("Location: " . $shopifyClient->getAuthorizeUrl(SHOPIFY_SCOPE, $pageURL));
        exit;
    }

    // first time to the page, show the form below
?>
    



<?php
    $sc = new ShopifyClient($_SESSION['shop'], $_SESSION['token'], SHOPIFY_API_KEY, SHOPIFY_SECRET);

        $products = $sc->call('GET', '/admin/products.json', array('published_status'=>'published'));

print_r($products);
?>


```
