# Lab: Accidental Exposure of Private GraphQL Fields

I am solving a new lab called **"Lab: Accidental Exposure of Private GraphQL Fields."** In this lab, we need to delete a user whose name is `carlos`. It is a GraphQL API lab.

## Step 1: Understanding the Vulnerability

First, we need to get access to the administrator account, and then we will delete the user.

This is called an **access control vulnerability**. It simply means that some users don't have permission to access certain features or perform specific actions, but they find a way to bypass these restrictions and change, edit, or remove things they are not supposed to.

This happens when an application fails to properly enforce restrictions on what authenticated users are allowed to do.

## Step 2: Exploring GraphQL Queries

I used Burp Suite's GraphQL query introspection feature, which helps retrieve the available GraphQL queries. After that, I got stuck and couldn't figure out how to proceed.

## Step 3: Finding the Solution

I was trying to solve the lab, but I couldn't do it. Then, I looked at the solution to understand what I was missing.

I found that I needed to send the queries to Burp Suite and save them in the sitemap. From there, I could go through the queries one by one and inspect them.

While exploring the queries, I found one that contained the login ID, username, and password fields, with a default value of `0`. I changed the value and was able to retrieve the administrator's password.
<img width="1920" height="1080" alt="screenshot_20261025_162916" src="https://github.com/user-attachments/assets/340d6211-c578-4675-b37b-9d50d47a45aa" />

## Step 4: Logging In as Administrator

Finally, I logged in using the administrator's credentials and deleted the user `carlos` through the admin panel.
<img width="832" height="519" alt="screenshot_20261025_162856" src="https://github.com/user-attachments/assets/6be50535-2cf5-40de-a3c4-7eadd1f0f309" />

**Lab completed\!**
<img width="1920" height="1080" alt="screenshot_20261025_162936" src="https://github.com/user-attachments/assets/376d5d42-125b-4d93-8169-59afa574d6f9" />
