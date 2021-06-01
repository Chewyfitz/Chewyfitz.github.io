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

 - Transaction coordination
 - Multiple services
 - Distrubted system
 - Synchronous
 - Atomic

## The System

### Architecture

 - Web services
 - Docker for development
 - Deployable with a docker-compose script
 - Database - CouchDB

### The Services

 - Front-end
 - SAGA Coordinator
 - Flights Service
 - Hotels Service
 - Payments Service

## Analysis

 - Coordinator-based transactions might not be the most efficient
 - We ran our transactions in sequence instead of in parallel
 - 