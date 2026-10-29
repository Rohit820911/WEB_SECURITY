# Lab: Excessive Trust in Client-Side Controls

## Overview

This lab does not properly validate user input. You can exploit a logic flaw
in its purchasing workflow to buy items at an unintended price.

**Goal:** Buy a "Lightweight l33t leather jacket".

**Credentials:** wiener:peter



### Steps

1. Log in with the credentials wiener:peter.
2. Open Burp Suite and route your browser traffic through the Burp proxy.
3. Turn Intercept on (Proxy > Intercept > Intercept is on).
4. Open the "Lightweight l33t leather jacket" page and click Add to cart.
5. Burp intercepts a POST /cart request with a body like:
   productId=1&redir=PRODUCT&quantity=1&price=133700
   <img width="1920" height="1080" alt="screenshot_20261029_163056" src="https://github.com/user-attachments/assets/99722f49-7dee-44a4-b89b-ebc47c84ce75" />

   
6. Change the price value to something low, e.g. price=1.
 <img width="1920" height="1080" alt="screenshot_20261029_163119" src="https://github.com/user-attachments/assets/7b4e71d4-0033-46a3-9bc9-ae03e6e97e9c" />

7. Click Forward to send the modified request.
8. Turn Intercept off.
9. Go to the cart and click Place order.<img width="1920" height="1080" alt="screenshot_20261029_163327" src="https://github.com/user-attachments/assets/a02a7e5f-bdd2-47cc-9c69-615d3a733241" />

10. The jacket is purchased at the lowered price — lab solved.
    <img width="1920" height="1080" alt="screenshot_20261029_163349" src="https://github.com/user-attachments/assets/7c70a1d0-ae74-4bea-a74c-613fac126e46" />


## Why This Works

The server trusts the price sent by the client instead of validating it
against its own records. Prices must be checked server-side.

