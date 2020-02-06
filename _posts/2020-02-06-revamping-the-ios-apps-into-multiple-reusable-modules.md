---
layout: post
title:  "Revamping the iOS Apps into multiple reusable modules"
date:   2020-02-06 23:22:43 +0530
categories: jekyll update
author: "Rajat Sharma"
permalink: /revamping-the-ios-apps-into-multiple-reusable-modules/
description: Assume you have to design a scalable online delivery based grocery application that facilitates the following feature.
imageUrl: /assets/rajat_blog_images/A5.png
---
From the past couple of years, we were following the standard approach to write our iOS applications. When we had a single application(Box8), things were organised and our codebase was pretty clean.
As we kept on expanding our business, On introducing two more applications into the market, The code was starting to get convoluted.
Our business model is more or less the same across all the applications except for critical UI components, UI Design, Network endpoints. One small change in business requirements leads to a change in all the applications which was taxing, very time-consuming and anti-scalable.
<p align="center">
  <img src="/assets/rajat_blog_images/flow1.png" alt="Image1"/>
</p>
​
## Initial Approach
The idea behind making the applications more scalable and robust is to construct the applications which are backed up by a **Modular** architecture.

Def: It Refers to the design of any system composed of separate components that can be connected together. The beauty of modular architecture is that you can replace or add any one component without affecting the rest of the system. The opposite of a modular architecture is an integrated architecture, in which no clear divisions exist between components.

Of course, **Modular** architecture is not a new concept and many other organizations have adopted it. There were downsides of adopting this architecture like Single point of failures, but the upsides were much more.
We knew that making an application from the ground up using this approach was going to be a very serious business but our iOS team was all charged up. :)

Initially, we started identifying the core components which were unique across all existing applications and could be separated out. The next step was to divide the identified components into Dynamic framework or static library based on their feature and functionality. Then we needed to embed the constructed frameworks/modules into their respective targets and Ta-da!.
### But
There were a few challenges with the above approach.
*   During the development phase, we found out that embedding one framework into another leads to a very complex dependency graph which increases the overall app size significantly.
*   There can be a possibility of Cyclic Dependency ( Framework A being  used by Framework B and Framework B being  used by Framework A).
*   There can be few dependencies that have private headers and accessing the underlying functions could change the module map of the dependency.
​

### Solution?
**Cocoapods** to the rescue.
​
Using Cocoapods as a dependency manager simplifies the overall dependency graph of the system and very swiftly manages the underlying pods which ultimately avoids problems that we faced earlier.
So we defined pods of each component and assigned the dependencies used by the respective component in its Podspec file.
We wanted to design  a component in such a way that it should represent a single standalone entity.
<p align="center">
  <img src="/assets/rajat_blog_images/flow2.png" alt="Image2"/>
</p>
The above Diagram represents Pod to Pod and App to Pod interaction.
The Core subcomponent represents the code base of a pod.
Flow Delegate is a protocol that helps to manage the navigation of the underlying view controllers.
The interface defines the public properties and functions and acts as an intermediate layer b/w modules.
Below is the overall design of the modular architecture
## Architecture
<p align="center">
  <img src="/assets/rajat_blog_images/A5.png" alt="Image2"/>
</p>
​
### Benefits
*   Faster build times
*   Decoupling Code base and separation of concerns.
*   Every isolated module feature can have its own dependency injection container and module dependencies
*   Easier for new developers/ team members to onboard that will work in isolated working domain.


## Conclusion
The modular architecture is equally applicable regardless of dependency management tools or platforms(e.g. Android ,web or cross-platform like _ReactNative or Flutter_).
It is pretty arguable that only large scale projects can benefit from this type of architecture.
The impact of this kind of modularity exceeds mere practicality, altering the way we design and code internally.