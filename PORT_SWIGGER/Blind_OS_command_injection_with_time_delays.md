
# Blind OS Command Injection with Time Delays - PortSwigger Academy

Today, I am solving the **Blind OS Command Injection with Time Delays** lab in the PortSwigger Academy.
<img width="1920" height="1080" alt="screenshot_20260729_104507" src="https://github.com/user-attachments/assets/8f901827-7206-4049-8131-8cd946e8eb32" />

This lab contains a blind OS command injection vulnerability in the feedback function.  
The application executes a shell command containing the user-supplied details. The output from the command is not returned in the response.  
To solve the lab, exploit the blind OS command injection vulnerability to cause a **10-second delay**.

I opened the website in Burp Browser and tested blindly with many payloads:
<img width="1920" height="1080" alt="screenshot_20260729_105154" src="https://github.com/user-attachments/assets/68b4377a-6c7d-431a-97f9-b9c669aa9cb7" />

```
& sleep 10 &
; sleep 10;
| sleep 10
& ping -n 11 127.0.0.1 &
& timeout /T 10 &
```


I tested all of these payloads in the **message** section, but none of them worked. Then, I tested them in the **email** field.

```
x; sleep 10;
```

I used this payload in the email address field, and it worked.
<img width="1920" height="1080" alt="screenshot_20260729_110145" src="https://github.com/user-attachments/assets/7f6a0782-81fd-43ee-9c4d-4d03c3f1330c" />


