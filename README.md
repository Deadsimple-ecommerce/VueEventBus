[![Latest Stable Version](https://poser.pugx.org/deadsimple/vueeventbus/v/stable)](https://packagist.org/packages/deadsimple/vueeventbus) [![Total Downloads](https://poser.pugx.org/deadsimple/vueeventbus/downloads)](https://packagist.org/packages/deadsimple/vueeventbus) [![License](https://poser.pugx.org/deadsimple/vueeventbus/license)](https://packagist.org/packages/deadsimple/vueeventbus)

# Deadsimple Vue Global Event Bus Magento2 Composer Library

**Please note:** an Event Bus is NOT the solution for all your cross component communication. But there are tons of scenarios where using Veux or other state management frameworks are complete overkill for what you try to accomplish. Which is communication between two separate Vue Components, loaded in two complete different sections of your page.

More about Vue Event Buses can be [found here](https://alligator.io/vuejs/global-event-bus/)

If you want to know more about how to use VueJS in Magento 2 have a look at [this repository](https://github.com/Deadsimple-ecommerce/VueLibrary)

## Installation

Use composer to install the module: `composer require deadsimple/vueeventbus`

## Usage / Example
Let's start with a use case; on your product detail page on your product header you want to add a button that says: "Add to Wishlist". This button needs to save the product to the wishlist. The wishlist is accessible from your navigation on top of your page and you would like to show a realtime counter that adds up when you click the button "Add to Wishlist". But how can you make these two complete separate components communicate between each other? This is where the Event Bus comes in place.

Let's create the following two files `wishlist-button.vue` &  `wishlist-header-link.vue`:

⚡️⚡️⚡️

`wishlist-button.vue`
```
<template>
    <div>
       <button @click="addToWishlist">Add to Wishlist</button>
    </div>
</template>
<script>
define([
    'Vue',
    'VueEventBus',
], function (Vue, EventBus) {

    Vue.component('wishlist-button', {
        template: template,
        methods: {
            addToWishlist() {
                // You probably want to save the product in a wishlist collection somewhere, but I'm leaving this out in this example
                
                EventBus.$emit('addedToWishlist'); // Transmit a signal 
            }
        }
    });

});
</script>
``` 

⚡️⚡️⚡️

`wishlist-header-link.vue`
```
<template>
    <a href="#">
        My whishlist ( {{ counter }} )
    </a>
</template>
<script>
define([
    'Vue',
    'VueEventBus',
], function (Vue, EventBus) {

    Vue.component('wishlist-header-link', {
        template: template,
        data() {
            return { 
                counter: 0
            }
        },
         mounted() {
            this.getCount();
            
            const self = this;
            
            // Listen to the Event Bus for the transmitted signal set in the other file and fire a 
            EventBus.$on('addedToWishlist', function() {
             this.counter = this.counter + 1;
            })
         }
    });

});
</script>
``` 

⚡️⚡️⚡️

In this example you can see we are including `VueEventBus` in both files, and it's this small tool that's the glue between both elements for event communication, where we emit the signal from one file to the other and act in the other file based on the event.
