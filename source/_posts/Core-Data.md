---
title: Core Data
date: 2016-09-12 23:06:37
tags:
---

## Core Data

### Core Data Stack
*Managed Object Model* - It describs the schema that you use in the app. In Xcode, the Managed Object Model is defined in a file with the extension __.xcdatamodeld__. You can use the visual editor to define the entities and their attributes, as well as, relationships.

 
*Persistant Store Coordinator* - SQLite is the default persistant store in iOS. However Core Data allows developers to setup  multiple stores containing different entities. The Persistant Store Coordinator is the party responsible to manage different persistant object stores and save th objects to the stores. Forget about it you don't understand what it is. You'll not interact with Persistant Store Coordinator directly when using Core Data.

*Managed Object Context* - Think of it as a "scratch pad" containing objects that interacts with data in persistent store. Its job is to manage objects created and returned using Core Data. Among the components in the Core Data Stack, the Managed Object Context is the one you'll work with for most of the time. In general, whenever you need to fetch and save objects in persistent store, the context is the first component you'll talk to.

The below illustration can probably give you a better idea about the Core Data Stack

![](http://www.appcoda.com/wp-content/uploads/2012/12/Core-Data-Stack.jpg)
