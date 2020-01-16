---
layout: post
title:  "Know Your Customer Behaviour - Snowplow Analytics"
date:   2020-01-16 23:22:43 +0530
categories: jekyll update
author: Ratnesh Keshari
permalink: ratnesh-keshari/know-your-customer-snowplow-analytics/
---


### Know your customer, How they behave on your platform

Every e-commerce organisation requires powerful analytics tool that can record
customer’s interaction with their Website,  App or Server and visualise the
behaviour at Customer level.

### What is Snowplow Analytics ?

Snowplow Analytics is an open source tool with rich  community support. It has
tremendous power to track and record events on Cloud(AWS & GCP)
infrastructure.<br> This tool requires a pipeline setup on Cloud. Once you are
done with setup you can put trackers in your website,  app or server. Trackers
will send data to data collector and this data will go through lot of
transformations. At the final stage you will have well structured data in your
choice of data warehouse.

![](https://cdn-images-1.medium.com/max/800/1*gXOO_mNFBxS7t-FoSO2OXA.png)

### Why Snowplow Analytics ?

1- Own your data and infrastructure.<br> 2- Snowplow atomic events have around
130 properties which is designed extensively to cover maximum of business use
cases.You can find these properties
[here](https://github.com/snowplow/snowplow/wiki/canonical-event-model) in
detail.<br> 3- It does data enrichments which helps to obtain high quality
data.<br> 4- Due to validation steps of the pipeline(only data which passes
validation will be present in data warehouse) you will have exceptionally high
quality data.<br> 5- It provides flexibility to record custom structured events
where you can form custom schema fields as per your business use cases.<br>
6-Data from all platforms (web/mobile/server side/ad impressions) is structured
in the same way and stored in the same place.<br> 7- It is cheaper than
GA360.Yon need to pay only cloud infrastructure cost.

### How do i do snowplow pipeline setup?

I will explain this part in my next blog.

### What to do with data ?

User interaction data from e-commerce website can be used to draw following use
cases. I am writing only few , you can think of more use cases based on your
data.<br> 1-  Track Traffic<br> 2- Track Marketing source and referrer<br> 3-
Funnel Analysis<br> 4- Drop off and Conversion Rate<br> 5- Optimise User
Experience<br> 6- Personalisation of the product

### Event Level Data Modelling ?

It is one essential step of pipeline which basically aggregates sequence of
atomic events recorded in data warehouse in an abstract set of data model units
that describes our data model which eventually helps to visualise our modelled
data and decision making.<br> We write sql query for data aggregation which is
generally complex and written by data team considering the business use case.I
have written one for my organisation([Box8](https://box8.in/)) for use case
“Draw customer journey who have visited corporate page”.<br> If you look at our
[web](https://box8.in/corporate) platform , there is corporate page where
customer need to fill address and click on order now. Later on customer add
items to cart, does cart checkout and complete his payment. These steps are very
essential for above use case. First I need to figure out list of customers who
have visited corporate page. I can pull this information from atomic events
table where page url match with corporate page url. Now I need to know how many
of them have clicked on order now button. This can also be achieved from click
events table. This is how we can aggregate our atomic data to describe complete
customer’s journey.<br> For the above use cases i have model our event level
data with 7 abstract units which are listed below.<br>  1-
is_visited_corporate_page<br>  2- is_clicked_on_order_now<br>  3-
is_clicked_on_checkout<br>  4- is_clicked_on_deliver_now<br>  5-
is_clicked_on_proceed_payment<br>  6-is_order_confirmed<br> 
7-is_order_cancelled<br> I am giving here the aggregate query that was written
for above use case and attached the visualisation image.<br> This is just for
reference purpose , it can’t fit your use case because it is based on custom
structure schema fields. Before proceeding to event-level data modelling you
need to be very clear with your use case and if custom structure schema fields
is required, it must be implemented. Custom structure schema fields are columns
of data warehouse table. For an example, I have created a purchase table which
record purchasing data when order confirmed. It has columns (total_payable,
tracking_id, coupon, order_type).<br> <br> 

    SELECT temp1.user_id,
          temp2.*
    FROM
      (SELECT domain_userid,
              domain_sessionid,
              user_id
      FROM atomic.events
      WHERE atomic.events.dvce_created_tstamp > (CURRENT_DATE - INTERVAL '3 DAY')
         AND user_id IS NOT NULL
      GROUP BY domain_userid,
                domain_sessionid,
                user_id) AS temp1
    INNER JOIN
      (SELECT en.domain_userid,
              en.domain_sessionid,
              en.app_id,
              CASE
                  WHEN ec.page_url IS NOT NULL THEN 1
                  ELSE 0
              END AS is_visited_corporate_page,
              CASE
                  WHEN eon.label IS NOT NULL THEN 2
                  ELSE 0
              END AS is_clicked_on_order_now,
              CASE
                  WHEN eoc.label IS NOT NULL THEN 3
                  ELSE 0
              END AS is_clicked_on_checkout,
              CASE
                  WHEN edn.label IS NOT NULL THEN 4
                  ELSE 0
              END AS is_clicked_on_deliver_now,
              edn.deliver_now_click_count,
              CASE
                  WHEN edl.label IS NOT NULL THEN 4
                  ELSE 0
              END AS is_clicked_on_deliver_later,
              CASE
                  WHEN epp.label IS NOT NULL THEN 5
                  ELSE 0
              END AS is_clicked_on_proceed_to_payment,
              epp.proceed_to_payment_click_count,
              CASE
                  WHEN epc.order_count IS NOT NULL THEN 6
                  ELSE 0
              END AS is_order_confirmed,
              epc.order_count,
              epc.total_payables,
              epc.tracking_ids,
              epc.order_types,
              CASE
                  WHEN ecp.label IS NOT NULL THEN 7
                  ELSE 0
              END AS is_order_cancelled
      FROM
         (SELECT domain_userid,
                 domain_sessionid,
                 app_id
          FROM atomic.events
          WHERE atomic.events.dvce_created_tstamp > (CURRENT_DATE - INTERVAL '3 DAY')
          GROUP BY domain_userid,
                  domain_sessionid,app_id) AS en
      LEFT JOIN
         (SELECT domain_userid,
                 domain_sessionid,
                 page_url
          FROM atomic.events
          WHERE atomic.events.dvce_created_tstamp > (CURRENT_DATE - INTERVAL '3 DAY')
            AND page_url = '
          GROUP BY domain_sessionid,
                  domain_userid,
                  page_url) AS ec ON ec.domain_userid = en.domain_userid
      AND ec.domain_sessionid = en.domain_sessionid
      LEFT JOIN
         (SELECT temp1.domain_userid,
                 temp1.domain_sessionid,
                 temp2.label
          FROM atomic.events AS temp1
          INNER JOIN atomic.in_box8_click_event_1 AS temp2 ON temp2.root_id = temp1.event_id
          WHERE temp1.dvce_created_tstamp > (CURRENT_DATE - INTERVAL '3 DAY')
            AND temp2.label = 'ORDER NOW'
            AND temp1.page_url = '
          GROUP BY temp1.domain_userid,
                  temp1.domain_sessionid,
                  temp2.label) AS eon ON eon.domain_userid = en.domain_userid
      AND eon.domain_sessionid = en.domain_sessionid
      LEFT JOIN
         (SELECT temp1.domain_userid,
                 temp1.domain_sessionid,
                 temp2.label
          FROM atomic.events AS temp1
          INNER JOIN atomic.in_box8_click_event_1 AS temp2 ON temp2.root_id = temp1.event_id
          WHERE temp1.dvce_created_tstamp > (CURRENT_DATE - INTERVAL '3 DAY')
            AND temp2.label = 'Checkout'
            AND temp2.parent_component='Cart Dropdown'
          GROUP BY temp1.domain_userid,
                  temp1.domain_sessionid,
                  temp2.label) AS eoc ON eoc.domain_userid = en.domain_userid
      AND eoc.domain_sessionid = en.domain_sessionid
      LEFT JOIN
         (SELECT temp1.domain_userid,
                 temp1.domain_sessionid,
                 temp2.label,
                 count(*) AS deliver_now_click_count
          FROM atomic.events AS temp1
          INNER JOIN atomic.in_box8_click_event_1 AS temp2 ON temp2.root_id = temp1.event_id
          WHERE temp1.dvce_created_tstamp > (CURRENT_DATE - INTERVAL '3 DAY')
            AND temp2.label = 'Deliver Now'
            AND temp2.parent_component = 'User Identity'
          GROUP BY temp1.domain_userid,
                  temp1.domain_sessionid,
                  temp2.label) AS edn ON edn.domain_userid = en.domain_userid
      AND edn.domain_sessionid = en.domain_sessionid
      LEFT JOIN
         (SELECT temp1.domain_userid,
                 temp1.domain_sessionid,
                 temp2.label
          FROM atomic.events AS temp1
          INNER JOIN atomic.in_box8_click_event_1 AS temp2 ON temp2.root_id = temp1.event_id
          WHERE temp1.dvce_created_tstamp > (CURRENT_DATE - INTERVAL '3 DAY')
            AND temp2.label = 'Deliver Later'
            AND temp2.parent_component = 'User Identity'
          GROUP BY temp1.domain_userid,
                  temp1.domain_sessionid,
                  temp2.label) AS edl ON edl.domain_userid = en.domain_userid
      AND edl.domain_sessionid = en.domain_sessionid
      LEFT JOIN
         (SELECT temp1.domain_userid,
                 temp1.domain_sessionid,
                 temp2.label,
                 count(*) AS proceed_to_payment_click_count,
                 listagg(json_extract_path_text(temp2.data,'order_type',TRUE),',') AS order_type,
                 listagg(json_extract_path_text(temp2.data,'orderType',TRUE),',') AS orderType
          FROM atomic.events AS temp1
          INNER JOIN atomic.in_box8_click_event_1 AS temp2 ON temp2.root_id = temp1.event_id
          WHERE temp1.dvce_created_tstamp > (CURRENT_DATE - INTERVAL '3 DAY')
            AND temp2.label = 'PROCEED_TO_PAYMENT'
            AND temp2.parent_component = 'Delivery Address'
          GROUP BY temp1.domain_userid,
                  temp1.domain_sessionid,
                  temp2.label) AS epp ON epp.domain_userid = en.domain_userid
      AND epp.domain_sessionid = en.domain_sessionid
      LEFT JOIN
         (SELECT temp1.domain_userid,
                 temp1.domain_sessionid,
                 count(*) AS order_count,
                 listagg(total_payable,',') as total_payables,
                 listagg(tracking_id,',') as tracking_ids,
                 listagg(order_type,',') as order_types,
                 listagg(coupon,',') as coupons
          FROM atomic.events AS temp1
          INNER JOIN atomic.in_box8_purchase_1 AS temp2 ON temp2.root_id = temp1.event_id
          WHERE temp1.dvce_created_tstamp > (CURRENT_DATE - INTERVAL '3 DAY')
          GROUP BY temp1.domain_userid,
                  temp1.domain_sessionid ) AS epc ON epc.domain_userid = en.domain_userid
      AND epc.domain_sessionid = en.domain_sessionid
      LEFT JOIN
         (SELECT temp1.domain_userid,
                 temp1.domain_sessionid,
                 temp2.label
          FROM atomic.events AS temp1
          INNER JOIN atomic.in_box8_click_event_1 AS temp2 ON temp2.root_id = temp1.event_id
          WHERE temp1.dvce_created_tstamp > (CURRENT_DATE - INTERVAL '3 DAY')
            AND temp2.label='Cancel Proceed'
          GROUP BY temp1.domain_userid,
                  temp1.domain_sessionid,
                  temp2.label ) AS ecp ON ecp.domain_userid = en.domain_userid
      AND ecp.domain_sessionid = en.domain_sessionid ) AS temp2 ON temp1.domain_userid = temp2.domain_userid
    AND temp1.domain_sessionid = temp2.domain_sessionid

<br> 

![](/assets/box8/image/snowplow_funnel_analysis.png)

*****

<br> 

<br> 

<br> 
