---
layout: post
title: Financial Statement Portfolio Webapp
description: Saving financials in an organized manner to track stock price, income statements, etc.
techstack: [Python, Django, Javascript, ReactJS, PostgreSQL]
---

<hr />

## Introduction



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

<hr />

### NodeJS + ExpressJS: Sessions and API

<hr />
### NodeJS + MongoDB: Database 