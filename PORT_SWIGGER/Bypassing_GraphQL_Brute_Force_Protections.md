
# Lab: Bypassing GraphQL Brute Force Protections

Hi everyone\! Today, I am solving the **Lab: Bypassing GraphQL Brute Force Protections**.

## Description (by Burp Suite)

The user login mechanism for this lab is powered by a GraphQL API. The API endpoint has a rate limiter that returns an error if it receives too many requests from the same origin in a short period of time.

To solve the lab, brute-force the login mechanism to sign in as `carlos`. Use the list of authentication lab passwords as your password source.

## Technique

- Bypass the rate limit by sending multiple GraphQL requests within a single HTTP request.
- Find the correct password and log in as `carlos`.

## Steps

### Step 1: Capture the Login Request

I tried to log in using an incorrect username and password. I received a `POST` request containing a GraphQL query.

### Step 2: Send the Request to Repeater

I sent the captured request to **Repeater** and used the **GraphQL introspection** feature.
<img width="1920" height="1080" alt="screenshot_20261027_054743" src="https://github.com/user-attachments/assets/045d7ba1-b6e2-4cff-96b3-d81cca889899" />

### Step 3: Explore the GraphQL Sitemap

I saved the output in the GraphQL sitemap. There, I found a new GraphQL request that sends only the username and password. This was the endpoint we needed for the lab.

### Step 4: Find the Password Wordlist

If you look at the lab description, there is a **Tips** option containing a complete wordlist. We can use this wordlist as our password source.

### Step 5: Bypass the Rate Limit

However, the API has rate-limiting protection, so we need to bypass it using the first technique: sending multiple GraphQL requests within a single HTTP request.

### Step 6: Prepare the GraphQL Requests

I used ChatGPT to help format the passwords from the wordlist into multiple GraphQL login requests for the user `carlos`.

Each request contained the same username but a different password, for example:

- Request 1: Username `carlos`, password `password1`
- Request 2: Username `carlos`, password `password2`
- Request 3: Username `carlos`, password `password3`
- And so on...
<img width="1920" height="1080" alt="screenshot_20261027_060320" src="https://github.com/user-attachments/assets/3070b289-ebb6-4523-88f8-0e3603fd68dd" />

I combined the requests into a single HTTP request to test multiple passwords while bypassing the rate limit.

### Step 7: Find the Correct Password

After sending the request, I checked the responses and found one with:

json

```
{
  "success": true
}
```
<img width="649" height="330" alt="screenshot_20261027_060501" src="https://github.com/user-attachments/assets/51430f25-6593-4bff-a99c-3f331e34f445" />

This indicated that I had found the correct password '123456789'.

### Step 8: Log In as Carlos

Finally, I used the correct password to log in as `carlos`, and the lab was successfully solved\!
<img width="1920" height="1080" alt="screenshot_20261027_060532" src="https://github.com/user-attachments/assets/8fb58d14-b167-4068-9467-c940024f1d5b" />

