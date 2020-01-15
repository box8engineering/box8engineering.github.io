---
layout: post
title:  "Microservices- Beyond Terminology"
date:   2020-01-13 23:22:43 +0530
categories: jekyll update
author: Ankit Tarway
---
<h1>Problem Statement </h1>

Assume you have to design a scalable online delivery based grocery application that facilitates the following feature. 

<h1> For vendors:- </h1>

1. Must able to register and list items they sell

2. Should able to accept the order from customers and maintain the stocks of different items

3. View records and stats about sales and remaining stocks.

4. Mention locations where they provide services

5. Discounts on the specific items


<h1>For Users:-</h1>

1. Must able to register and view items available

2. Able to search a specific item across different vendors

3. Make a cart of different items (all from the same vendor )

4. Pay online or cash on delivery 

5. Get help and support if any issue occurs 

6. Give feedback and rate vendor

7. List their location to get item delivered 

9. Track status of the order 

10. View previous Orders.

10. And many more one can come up with.

<h1>For Delivery person:- </h1>

1. Get the location of the store and the customer.
2. Mark order status 

<h1>How would you do it?</h1>

Answer to the above question requires an understanding of different phases of the application development. In this article, we will focus on the application architecture majorly on microservices architecture with a basic understanding of Monolithic architecture.

<h1>Monolithic Architecture </h1>

Monolithic as the name suggests denotes a single large application that packs everything within it. Each service (or feature) is written as a separate module or class that uses the same database. A Module usually(in practice) freely access and modify the different service that application provides as per the need.
For the above problem statement, a monolithic architecture could be like:-

![Monolithic-Architecture](/assets/image/tarwayblog.png){:class="img-responsive"}