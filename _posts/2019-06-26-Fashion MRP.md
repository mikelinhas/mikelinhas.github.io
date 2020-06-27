---
layout: post
title: Fashion MRP
description: MRP Webapp built with Vuejs, ExpressJS, NodeJS, and MongoDB
techstack: [JavaScript, VueJS, ExpressJS, NodeJS, MongoDB]
---

---

## Introduction

In 2018 I decided to help out a start-up which was starting to grow their fashion business. Their sales and production had really taken off and they needed to track their warehouses, production and supplier purchases. At this point in time, companies need to decide either to build or to buy an MRP. Thankfully for me, they decided to build.

**VueJS** was becoming extremely popular and after testing it with some small ideas, it seemed like a good choice. The MRP was going to include complex calls to the database, so I decided to use **MongoDB** and **NodeJS**. 

<p class="center" style="margin-top: 60px"> The basic outline of the project was decided. </p>

<div class="center-div" style="max-width: 500px; margin-top: 20px;">
	<img src="/assets/images/MEVNstack.png" alt="MEVN Stack">
</div>

  > The REST server API was built using **ExpressJS**, creating what is know as the **MEVN** stack (Mongodb, ExpressJS, VueJS, NodeJS).

 ---

## Project Outline

### VueJS Frontend

The user interface had to be as simple as possible. Any requirement from the company was considered and developed. 

The main functionality from the client's point of view was:

* Control the `finished products` stock
* Control the `production orders` made to the `producers`
* Control the `materials` stock
* Control the `purchase orders` made to the `suppliers`

The final approach that tied everything in was:

* Creating a `bill of materials` for each `finished product`

##### Finished products

With a simple glance, the user can see how many products are in stock and how many are ordered to be produced. The "ordered" column, shows a call to the database which adds up all the production orders which include that product.

<div class="center-div" style="margin-top: 40px; margin-bottom: 70px">
  <p class="image-subtitle"> Checking and modifying stock </p>
  <img src="/assets/examples/stock-example.gif" title="Stock example" alt="Stock example">
</div>


##### Bill of Materials

When making each finished product, it is very important to track how much material is used. Creating a `bill of materials` helps the user define and adjust the amount of material that will be removed from the database each time a product is manufactured.

<div class="center-div" style="margin-top: 40px;">
  <p class="image-subtitle"> Adding a bill of materials </p>
  <img src="/assets/examples/bom-example.gif" title="Bom example" alt="Bom example">

</div>


##### Materials

The `materials` are bought from the `suppliers` and stored at the `producers` facilities. Taking into account the amount of `materials`, the `production orders` and the `purchase order` it is easy to see if there is enough material stock for the near future.

From a producer's point of view, the user can see how much material they have, and how much they will need for production.

<div class="center-div" style="margin-top: 20px;">
	<img src="/assets/examples/material-producer-example.png" alt="Material producer example">
</div>

From a supplier's point of view, the user can see how much material they have at each producer facility, and how much they will need to order.

<div class="center-div" style="margin-top: 20px;">
	<img src="/assets/examples/material-supplier-example.png" alt="Material supplier example">
</div>

---

### NodeJS + ExpressJS: Sessions and API

Keeping track of user's sessions, privileges and roles was done using **express-session** and **MongoDB** and **passport**.

```javascript
const session = require('express-session');
const MongoStore = require('connect-mongo')(session);
const passport = require('passport');
const LocalStrategy = require('passport-local').Strategy;

// User's passwords are hashed when created and verified
const passwordHash = require('password-hash');
```

Each time a user calls an API endpoint, NodeJS checks to see if the user's cookie is valid, and wether the user has a specific role for the app. For example, deleting something from the database can only be done for a user with administrator priviledges.


---

### NodeJS + MongoDB: Database 

The database is split into 7 main collections:

* `materials`: Material info and stock
* `products`: Product info and stock
* `boms`: Bill of materials relating products and materials
* `suppliers`: Supplier information.
* `suppliers.orders`: Purchase orders to suppliers.
* `producers`: Producer information.
* `producers.orders`: Production orders to producers.

Every item in these collections has a unique `_id` which is used to relate information from one to collection to another. 

For example, controling the `material` stock in each `producer` facility is done:

```javascript
//material Collection
{"_id" : "MATERIAL-1-id",
 "name" : "Material 1",
 "stock" : [
   {"producer_id": "PRODUCER-1-id",
    "stock": 3},
   {"producer_id": "PRODUCER-2-id",
    "stock": 5}
 ],
 "supplier_id" : "SUPPLIER-1-id",
 ...
}

//suppliers Collection
{"_id": "SUPPLIER-1-id"
 ...
}

//producers Collection
{"_id": "PRODUCER-1-id"
 ...
}
```

An example of a Bill Of Material would be:

```javascript
//boms collection
{"_id" : "BOM-x-id",
 "product_id" : "PRODUCT-1-id",
 "materials" : [
   {"material_id": "MATERIAL-1-id"
    "supplier_id" : "SUPPLIER-1-id",
    "quantity" : 4},
   {"material_id": "MATERIAL-2-id",
    "supplier_id" : "SUPPLIER-1-id",
    "quantity" : 0.2}
  ],
  ...
}
```

##### MongoDB Aggregation Framework

Most of the **complex queries** to the database are done using the <a href="https://docs.mongodb.com/manual/core/aggregation-pipeline/" target="_blank">Aggregation Pipeline Framework</a>.

 ---

## Live Demo

Checkout the live demo at <a href="http://graphup.herokuapp.com/" target="_blank"><img class="inline-image" src="/assets/heroku.png" alt="Heroku"></a>
