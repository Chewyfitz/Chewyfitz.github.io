---
title: SAGA Transaction Coordination
author: Aidan
categories: []
tags: []
---

## What is a SAGA?

Distributed Transactions can require some complex coordination to avoid overwriting each others' changes, and can often run into special cases like deadlock which require even further coordination.
There are commonplace concurrency control algorithms such as Two-Phase Commit (2PC), and several Optimisitic Concurrency Control algorithms which partially solve this by coordinating between services, but this only works if all of the services are a part of the system, or if the system has exclusive control over that service's data.  

SAGA transactions are a way to avoid this by moving all coordination of the transaction to our system, and assuming that any external services will handle concurrency properly within the bounds of their own systems.  

The key to this is one of two approaches:
1. Build a centralised coordination service which handles the transactions and records their states, or
2. Enforce SAGA participation between each of the services.  

The latter of which may be abstracted by building local services which "wrap" interaction with an external service or microservice which doesn't normally participate in SAGA transactions.  

A SAGA transaction is a synchronous, atomic transaction which composes interactions between multiple hetrogenous services that aren't necessarily under the control of the system itself. It does so by simply performing all reversible transactions, then once those have been confirmed performing irreversible transactions en bloc. If the irreversible transactions fail then the entire transaction fails and compensating transactions are made for the reversible parts.


## The System
We built a simplified system as an exercise in implementing the SAGA transaction protocol, as follows:

### The Services

We modelled the system as 5 component parts, 
 - The Front-end
   A React.js web application to serve the user-interactible components of the application, and provide a framework for visualising the transactions.
 - SAGA Coordinator
   The transaction core, providing reliable coordination between the back-end services and implementing simple failure-recovery and transaction tracking.
 - Flights Service
   An API for booking a single (whole) flight, we didn't model seats on the flight service as this would have made implementation substantially more complicated and overstepped the scope of our project.
 - Hotels Service
   An API for booking a single (whole) hotel, very similarly to the flights service, but using an independent database and with a different naming scheme.
 - Payments Service

### Architecture

We built the system in Docker for easy development and task separation, with a "shared" CouchDB database backend. We only used a shared CouchDB container since it was logistically simpler than trying to deploy multiple databases, when CouchDB can provide separation by using different document databases for each service.

Each "service" modelled as a part of the system was a web service written in either Node.js (JavaScript) or Flask (Python), with the front-end service written in React (JavaScript).

 - Web services
 - Docker for development
 - Deployable with a docker-compose script
 - Database - CouchDB

## Analysis

 - Coordinator-based transactions might not be the most efficient
 - We ran our transactions in sequence instead of in parallel
 - 