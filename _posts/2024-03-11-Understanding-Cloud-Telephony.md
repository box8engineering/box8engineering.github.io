---
layout: post
title:  "Cloud Telephony Transformation: Revolutionizing Communication in Eatclub"
date:   2024-03-11 17:00:00 +0530
categories: jekyll update
author: "Aayush Jain"
permalink: aayush-jain/Understanding-Cloud-Telephony/
description: Understanding how Telephony works in Eatclub 
imageUrl: assets/image/cloud_telephony_img.jpeg
---
<p align="center">
  <img src= "/assets/image/cloud_telephony_img.jpeg" alt="Image1"/>
</p>


In the ever-evolving landscape of communication technologies, Cloud Telephony stands as a transformative force, reshaping the way businesses and individuals connect.
Cloud Telephony is a service that provides resources to make outbound calls, redirect the incoming calls, and transfer calls to call center agents. 



### USE OF CLOUD TELEPHONY IN EATCLUB
In Eatclub, we facilitate engagement between customers and the Eatclub team, fostering effective communication in both directions. We have identified various entities that interact with each other to ensure the streamlined flow of operations. These entities are categorized as follows:


1. Own_Customer
2. Third_Party_Customer
3. DM (Delivery Masters)
4. Outlet_Manager
5. CC (Customer Care)
6. Chatbot (In App Support)
   
To ensure seamless communication between each of these entities, we leverage a third-party vendor. We integrate our APIs into their call flows, allowing for the identification of caller types.
This integration enables us to connect callers to their respective destinations based on the status of orders related to them.

### HOW APIs ARE INTEGRATED IN CALL FLOWS ?

Vendor offers customization options for call flows based on our specific requirements. To initiate a call between two entities, we configure a call flow and assign a bridge number allocated by vendors to that particular flow. Within this flow, we input the URL and header information of an endpoint hosted on our server. The call is then redirected to the appropriate destination based on the response received from this endpoint. 


### UNDERSTANDING CENTRAL CALLING API 
In Eatclub , we have designed a single API which can handle all calls which falls under our domain of defined entities. These calls can be from : 

- Customers to DM
- Customers to Outlets
- Customers to CC
- DM to Outlets
- DM to Customers
- DM to CC
- Outlets to Customer
- Outlets to CC
- CC to Customers
- CC to Outlets
- CC to DMs
- Chatbot to CC
- (Free to add new)

NOTE : We have further categorized customers into multiple categories.
- Own Customer
- Third_Party_1_ Customer
- Third_Party_2_ Customer
This classification is done because we get different types of data from each of them. For eg: Third Party order platforms provide a masked number of their customers while we have our customer’s  original number.

### HOW DOES THIS API WORKS ?
When a call lands on a call flow, a request is received at our endpoint. This request undergoes processing through various steps before sending a response. Let's understand each of them.

1. **CALLER IDENTIFICATION**

   In the first step, we identify the caller and assign an entity to them from our predefined set of entities. This identification is done on the basis of  parameters received along with the request.

2. **RETRIEVAL OF ORDER RELATED DETAILS**

   Once we identify the caller and determine the corresponding entity, we proceed to retrieve the order associated with this caller. This retrieval is based on the customer's phone number or the third-party Order ID provided by the customer.
   Various order-related details, including Order ID, Order Status, DM phone number (if assigned), Customer Phone Number, Outlet ID to which the order is assigned, last order status update time, etc., are obtained.
   Different logic is applied for each type of entity, and we employ predefined strategies to gather this information for every entity. Various DB tables are used to fetch this information.

3. **FINDING DESTINATION OF CALL**

   Here comes the most exciting part of this process. After obtaining all the required information, we process the accumulated data through our self-designed state machine.
   This state machine keeps a record of the type of caller, the entity to which they will connect, the relevant order state, and the permissible duration for this operation.

4. **RETURNING PHONE NUMBER IN RESPONSE**

   After determining the destination of the call, it's time to return the phone number of the entity to which the call will be directed. The response is returned in JSON format, containing the phone number of the destination along with the meta code.
   In the event that no destination is found, the request appears invalid, or it originates from an unrecognized entity, a meta response is sent describing the issue along with an error meta code.


### UNDERSTANDING THE NEED OF STATE MACHINE CONCEPT
To enhance customer experiences, operations need to be adaptable to changing requirements over time. It's common to adjust destinations for specific order states, permit callers to contact within a fixed time range, or redirect calls if someone doesn't answer. 
Deploying an entire server for these routine changes isn't an optimal practice. The process of understanding requirements, implementing, testing, deploying, and monitoring can be time-consuming for similar types of adjustments.
Recognizing this, we opted for a configurable design. This design empowers the operations team to modify call configurations in real-time without relying on the tech team. This approach eliminates the need for the traditional development lifecycle, allowing for swift and efficient adjustments to meet evolving operational needs.


### TECHNICAL PREVIEW OF CALLING API
Let us deep dive into the technical specifications of this API.
This is hosted on https://opsapi.box8.co.in/ server. Source code of this API is under the cloud_telephony namespace of the monolith codebase. Here is the direct link to Source Code.


1. **AUTHENTICATION**
   
To secure this endpoint , we have added JWT based authentication. Here the Service Account token of Number Masking is used to authenticate all the requests. This token has a validity of 1 year.


2. **PARAMETERS**
   
This endpoint expects multiple parameters , some are mandatory while the others are optional. 
Caller phone number as from , caller entity type as call_from , entity type of person whom caller wishes to call as call_to are mandatory parameters required .
In case caller’s input is expected , this parameter is taken as dtmf. For forwarding calls , in case first destination is unavailable to pick the call , transit should be given of the last entity you tried to call, Service provider as provider , Unique call Id as unique_call_id and bridge number of flow as bridge_number are all optional parameters.

3. **LOGGING**
   
Monitoring is considered one of the most important aspects of development , which paves the way for future enhancements and bug fixes.
We have implemented a systematic step by step logging which is pushed to elasticsearch. We can fetch these logs by searching keyword “sequential_call_logs” on Kibana. After the completion of the call , call details are also pushed to the Database for records. Call_logs table in Monolith DB is designated to store this data.

4. **MONITORING**
   
Performance metrics are monitored on newrelic/custom dashboards/
Also , we have made a dashboard to monitor call success rate via third_party_vendors .
To view this dashboard , [Click Here](https://redasheks.box8.co.in/dashboards/88-call-success-rate---kaleyra)

5. **POSTMAN COLLECTION**
   
Here is the postman collection of sample requests and their responses.
[Postman Collection](https://box8-accounts.postman.co/workspace/opsapi~0d59114f-9f11-43ae-8638-01b3ae33f00e/request/10136000-8e774271-1773-4660-a7f8-21abebaf2b0d?ctx=documentation)


      

