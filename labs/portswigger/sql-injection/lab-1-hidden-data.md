# Lab 1: SQL Injection – Hidden Data Retrieval

## Platform
PortSwigger Web Security Academy

## Lab Name
SQL injection vulnerability in WHERE clause allowing retrieval of hidden data

## Entry Point
GET /filter?category=

## Payload Used
' OR 1=1--

## Exploitation Steps
1. Selected a product category on the shop page.
2. Identified the `category` parameter in the URL.
3. Injected `' OR 1=1--` into the parameter.
4. Application returned all products including hidden ones.

## Root Cause
User input was directly concatenated into a SQL query without proper sanitization.

## Impact
Attackers can bypass intended access controls and retrieve unauthorized data.

## Proof of Exploitation
Lab successfully solved (all products visible).

## Status
Solved

