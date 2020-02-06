---
layout: post
title:  "Document Scanner(📃🔍) - OCR & Information Extraction"
date:   2020-02-06 23:22:43 +0530
categories: jekyll update
author: "Avishek Sarkar"
permalink: /Document-Scanner-OCR-&-Information-Extraction/
description: Smart Information Extraction using OCR technology is transforming businesses worldwide. See how we at Box8 have found an innovative approach to take it to the next level.
imageUrl: https://nanonets.com/blog/content/images/2019/08/OCR_illustration-2.gif
---


<center><img src="https://cdn.dribbble.com/users/391380/screenshots/5431062/icon.gif" ></center>

### **<span style="text-decoration:underline;">CONTENT</span>**



1.  <a href='#intro'>Introduction</a>
2.  <a href="#landscape">The OCR Landscape</a>
3.  <a href="#failures">Limitations of current OCR APIs </a>
4.  <a href="#ocr_need"> Should I even consider using OCR then?</a>
5.  <a href="#good_ocr"> What defines a good OCR product?</a>
6.  <a href="#docparser"> OCR & Document Scanner</a>
7. <a href="#pipeline"> Document Scanner Information Extraction Pipeline</a>
8. <a href="#use_cases">How we use Document Scanner at Box8 ?</a>
9. <a href="#future">Where to next? </a>

### 1. <a name="intro" style="text-decoration:underline;"> Introduction</a>

Simply defined, OCR is a set of computer vision tasks that convert scanned documents and images into machine readable text. It takes images of documents, invoices and receipts, finds text in it and converts it into a format that machines can better process. You want to read information off of ID cards or read numbers on a bank cheque, OCR is what will drive your software.

 You might need to read the different characters from a cheque, extract the account number, amount, currency, date etc. <b>But how do you know which character corresponds to which field?</b>


   <center><img src = "https://nanonets.com/blog/content/images/2019/08/clearcheck21_micr_reader-568141.jpg " style="width:70%"></center>
   

### 2. **<a name="landscape" style="text-decoration:underline;"> The OCR landscape</a>**

The APIs provided by many are limited to solving a very limited set of use cases and are averse to customizations. 


![alt_text](https://nanonets.com/blog/content/images/2019/08/OCR_illustration-02--3-.jpg "image_tooltip")

More often than not, a business planning to use OCR technology needs an in-house team to build on the OCR API available to them to actually apply it to their use case. The OCR technology available in the market today is mostly a partial solution to the problem.



### 3. **<a name="failures" style="text-decoration:underline;">Limitations of current OCR APIs </a>**
1. Require a considerable amount of post-processing
2. Work well only in specific constraints 
3. Tilted text in images
4. Handwritten text, cursive fonts, font sizes
5. Noisy/blurry images



### 4. **<a name="ocr_need" style="text-decoration:underline;">Should I even consider using OCR then?</a>**
Short answer is <b>Yes</b>.

Anywhere there is a lot of paperwork or manual effort involved, OCR technology can enable image and text based process automation. Being able to digitize information in an accurate way can help business processes become smoother, easier and a lot more reliable along with reducing the manpower required to execute these processes. For big organizations who have to deal with a lot of forms, invoices, receipts, etc, being able to digitize all the information, storing and structuring the data, making it searchable and editable is a step closer to a paper-free world.

Think about the following use cases - 
1. <span style="text-decoration:underline;">Legal documents</span> -
    Dealing with different forms of documents - affidavits, judgments, filings, etc. digitizing, databasing and making them searchable.

 2. <span style="text-decoration:underline;">Table extraction</span> - 
    Automatically detect tables in a document, get text in each cell, column headings for research, data entry, data collection, etc.

  3.  <span style="text-decoration:underline;">Banking</span> - analyzing cheques, reading and updating passbooks, ensuring KYC compliance, analyzing applications for loans, accounts and other services.

  4. <span style="text-decoration:underline;">Healthcare</span> - have patients medical records, history of illnesses, diagnoses, medication, etc digitized and made searchable for the convenience of doctors.

5. <span style="text-decoration:underline;">Invoices</span> - automating reading bills, invoices and receipts, extracting products, prices, date-time data, company/service name for retail and logistics industry.

### 5. <a name="good_ocr" style="text-decoration:underline;">What defines a good OCR product?</a>

 1. How it deals with the images coming in 
 2. How it performs in real-world problems
 3. How it uses the machine-readable text


### 6. <a name="docparser" style="text-decoration:underline;">Document Scanner & OCR</a>
Dcoument Scanner is a data capture solution built to retrieve data from image documents. It takes an image and extracts the data required in near real-time.

Document Scanner was built to solve the above mentioned problems. We have been able to productize a pipeline for OCR by working with it as not just for character recognition but getting structured usable information.

<center><img src="https://ph-files.imgix.net/a8b55670-2332-43fa-9a36-7e6619308363?auto=format&auto=compress&codec=mozjpeg&cs=strip&w=627.3015873015872&h=380&fit=max&dpr=2"></center>
 


### 7.<a name="pipeline" style="text-decoration:underline;"> Document Scanner Pipline</a>

  To extract information from an image Document Scanner needs a set of rules that tell it what data points to look for and where to look for in the image document. These rules generally contain information as to how a particular data point looks like and where one can locate it in the document.

 <center><img src="http://drive.google.com/uc?export=view&id=1_rkrGFUKEGx7dG0Ys6kIOWrd_xaPo-q4" width="80%" height="80%"></center>

### <a style="text-decoration:underline;" >Benefits of Document Sanner </a>

1. Document Scanner saves precious time by providing structured usable information which can be used for autofilling forms etc.
2. Prevents error due to User entry.
3. Increases overall productivity.
4. Compatibility with local languages. 


### 8. <a name="use_cases" style="text-decoration:underline;">How we use Document Scanner at Box8 ?</a>


#### <a style= "text-decoration:underline"> Employee Onboarding Using Aadhar </a > 


Employee Creation requires filling of multiple fields and is quite time taking.
It is also expected that the user input is accurate as fields like Document 
No (Aadhar No.) need to be unique. Failure to do so means refilling of the form
which further increases the time taken to create an employee. Using 
Document Scanner with Aadhaar helps speed up the process and helps in eliminating error due to user input . 

<center><img src= "http://drive.google.com/uc?export=view&id=1AO1Dw44v4hMBtu1-N74srcS9ecWjRFHi" width="80%" height="80%" > </center>


#### <a style="text-decoration:underline">Digitzation of Vendor Invoices </a>


   We use Document Scanner to extract data from a vendor invoice , like GST info, invoice number ,information of items purchased , the total amount to be paid etc. Further it also helps us to identify if any of the above information is missing or incorrect than the data entered manually and automatically raise disputes.

<center><img src="https://nanonets.com/blog/content/images/2019/08/OCR_illustration-2.gif" height="50%" width="70%" ></center>

### 9. <a name="future" style="text-decoration:underline;">Where to next?</a>

#### <a name="future" style="text-decoration:underline;">Automated intelligent structured field extraction </a>

As we discussed above to get structured information from Document Scanner we use a set of rules that tell it which data points and where to look for them in the document. Now think of a system that can take it to the next level. What if instead of giving rules  we just tell it what data points we want and it looks for them automatically?


Sounds interesting right and it might seem simple at first, let me break it down for you . Not only does it need to understand what a data point is and differentiate data points among each other, it also needs to understand what the user actually wants. Its not so simple now is it .😛  
 
 If you have any ideas as to how we can approach this problem dont hesistate to contact us. <a href="mailto:avishek.sarkar@box8.in?subject= Document Scanner Blog "><b>📧</b></a>  

Thanks for reading. 👍