---
layout: post
title: MEVN Stack MRP
---

<hr />

## Introduction

In 2018 I decided to help out some friends who were starting to grow their fashion business. Their sales and production had really taken off and they needed to track their warehouses, production and supplier purchases. At this point in time, companies nedd to decide either to build or to buy an MRP. Thankfully for me, they decided to build.

**VueJS** was becoming extremely popular and after testing it with some small ideas, it seemed like a good choice. The MRP was going to include complex calls to the database, so I decided to use **MongoDB** and **NodeJS**. 

<p class="center" style="margin-top: 60px"> The basic outline of the project was decided. </p>

<div class="center-div" style="max-width: 500px; margin-top: 20px;">
	<img src="/assets/images/MEVNstack.png" alt="MEVN Stack">
</div>

  > The REST server API was built using **ExpressJS**, creating what is know as the **MEVN** stack (Mongodb, ExpressJS, VueJS, NodeJS).

 <hr />

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

<div class="center-div" style="margin-top: 20px;">
	<img src="/assets/examples/stock-example.png" alt="Stock example">
</div>


##### Bill of Materials

With a simple glance, the user can see how many products are in stock and how many are ordered to be produced. The "ordered" column, shows a call to the database which adds up all the production orders which include that product.

<div class="center-div" style="margin-top: 20px;">
	<img src="/assets/examples/stock-example.png" alt="Stock example">
</div>


### NodeJS + ExpressJS: Sessions and API

### NodeJS + MongoDB: Database 