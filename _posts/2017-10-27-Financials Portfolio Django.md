---
layout: post
title: Financial Portfolio Webapp
description: Monitoring financials in an organized manner to track stock price, income statements, etc.
techstack: [Python, Django, Javascript, ReactJS, PostgreSQL]
---

---

## Introduction

I developed the financials portfolio app in 2017, as a means for my colleagues and I to share knowledge and insights on investments. The idea was simple. Have an admin page where we can rate the companies and share info.

**Django** has an integrated database and admin page design which helps you create and manage the app easily, from a user's point of view. On the other hand, Django's template design is far slower than other modern frontend frameworks. Therefore, I decided to develop the front-end with **ReactJS**. 

<p class="center" style="margin-top: 60px"> The basic outline of the project. </p>

<div class="center-div" style="max-width: 500px; margin-top: 20px; margin-bottom: 40px;">
	<img src="/assets/images/DJRstack.png" alt="DJR Stack">
</div>

  > The Django Framework would include its own database in **PostgreSQL** and would also act as a REST server to access an **external API** for the frontend.


---


## Project Outline

### React Frontend

The frontend only needed to include company pages, where information from the external financial API would be shown. 

Therefore, it needed the following:

* A list of `companies` to select from (Django database)
* The `financial data` for each company page (External API through Django).

<div class="center-div" style="max-width: 500px; margin-top: 40px; margin-bottom: 40px">
  <p class="image-subtitle"> Selecting a company from the database</p>
  <img src="/assets/examples/company-list.gif" alt="companies example">
</div>

Each company page would then take you to the API data. Where `financial statements`, `balance sheets`, `stock prices`, `news`, and much more could be seen.


<div class="center-div" style="margin-top: 40px; margin-bottom: 40px">
  <p class="image-subtitle"> Viewing company data</p>
  <img src="/assets/examples/company-example.png" alt="company example">
</div>


---

### Django - PostgreSQL and Admin Page

Django's build includes an exremely useful way of creating models and migrating PostgreSQL database changes. These models are then the base of the admin page.

The database only needed to include a list of `companies` with their `api_symbol` and their user ratings. The api symbol was the way of communicating with the external API.

 <div class="center-div" style="max-width: 600px; margin-top: 40px; margin-bottom: 40px">
  <p class="image-subtitle"> Adding companies to the database</p>
  <img src="/assets/examples/admin-site-example.png" alt="admin site example">
</div>

---

### Django - External API

Once the front-end had the company information, it would then make an HTTP call to Django, asking for different financial information. Django would make the call to the external API, filter the necessary information from the call, and respond to the frontend.

<div class="center-div" style="max-width: 500px; margin-top: 40px; margin-bottom: 50px">
  <p class="image-subtitle"> API call example</p>
  <img src="/assets/images/DJRstack-rest.png" alt="rest example">
</div>
