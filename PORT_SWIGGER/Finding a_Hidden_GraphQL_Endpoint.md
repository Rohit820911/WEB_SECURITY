
# Lab: Finding a Hidden GraphQL Endpoint

Hi, today I am solving a new GraphQL lab **: Finding a Hidden GraphQL Endpoint**

First, I learned this:

We can bypass the defense system of GraphQL:
- Changing the request from POST to GET and adding it to the sitemap to see what is working and what is not.
- Imagine a developer may block `__schema{`, but we can write a comma (`,`), a space (` `), or a new line (`\n`) — maybe it can work.

## Description Given by Burp Suite

The user management functions for this lab are powered by a hidden GraphQL endpoint. You won't be able to find this endpoint by simply clicking pages on the site. The endpoint also has some defenses against introspection.

To solve the lab, find the hidden endpoint and delete Carlos.

Learn more about Working with GraphQL in Burp Suite.

## Steps

- We need to find the hidden GraphQL endpoint. For that, we will use the first and second techniques mentioned above.
- We also need to delete Carlos. Maybe we can find the admin functionality and then delete him, or maybe there is a direct endpoint for deleting users.

## Steps to Solve the Lab

### 1. Finding the GraphQL Endpoint

- We need to find a correct endpoint that has a GraphQL query.
- I tried to find one, but I couldn't find any direct GraphQL query.
- I tried writing the following query in a GET request:

  ```graphql
  query {
    __schema {
      queryType {
        name
      }
    }
  }
`

However, I couldn't get the expected result.

- Then, I tried `/api` and got a `Query not present` message and a `BAD REQUEST` error. I thought there was no query, so I wondered why it returned `400 Bad Request` instead of `404 Not Found`. We need to investigate this.
- I sent this request:
  http
  ```
  GET /api?query=query{name}
  ```
- I got the following error in the response:
  text
  ```
  'name' in type 'Query' is undefined
  ```
- Now, we can confidently say that there is a GraphQL endpoint. Next, we need to find the hidden endpoint, so we will try some introspection techniques.

### 2. Bypassing the Introspection Defense

- I used Burp Suite's "Set introspection query" feature and sent the request. I got the following error:
  json
  ```
  {
    "errors": [
      {
        "locations": [],
        "message": "GraphQL introspection is not allowed, but the query contained __schema or __type"
      }
    ]
  }
  ```
- From this error, we have proof that a simple introspection query is not allowed. There is a defense mechanism, and we need to bypass it.
- In the request, we have:
  text
  ```
  __schema+%7b
  ```
  We need to add a new line or a space:
  text
  ```
  __schema%0A%7b
  ```
  Here, `%0A` adds a new line. I am trying the first technique to bypass the defense.
- As a result, we get all the endpoints\! We have successfully bypassed the defense mechanism in this lab.

### 3. Saving the GraphQL Queries to the Sitemap

- Now, we need to find the endpoint that deletes users or the endpoint that provides user details. We can use the user details endpoint to find the user IDs and usernames.
- For this, we send our results to the sitemap so we can easily see what is working and what is not. We use "Save GraphQL to sitemap" in Burp Suite.

### 4. Finding the User and Delete Endpoints

After saving the GraphQL queries to the sitemap, we find two endpoints: the first is for finding a user's details, and the second is for deleting a user.

<img width="1581" height="93" alt="image" src="https://github.com/user-attachments/assets/db54b94c-bd20-403d-bb87-3b887c456526" />

Endpoint 1: Find User Details

http

```
GET /api?query=query(%24id%3a+Int%21)+%7b%0a++getUser%28id%3a+%24id%29+%7b%0a++++id%0a++++username%0a++%7d%0a%7d&variables=%7b%22id%22%3a0%7d
```

Endpoint 2: Delete a User

http

```
GET /api?query=mutation%28%24input%3a+DeleteOrganizationUserInput%29+%7b%0a++deleteOrganizationUser%28input%3a+%24input%29+%7b%0a++++user+%7b%0a++++++id%0a++++++username%0a++++%7d%0a++%7d%0a%7d&variables=%7b%22input%22%3a%7b%22id%22%3a0%7d%7d
```

### 5. Finding Carlos's User ID

- Send both requests to Burp Suite Repeater and change the `id` variable to `0`, `1`, `2`, and `3` to check the corresponding usernames.
- The results are:
  - `1` → Admin
    <img width="1191" height="522" alt="screenshot_20261026_145439" src="https://github.com/user-attachments/assets/2ad90323-ad07-4006-b88f-f688f67b4c05" />

  - `2` → Winner
  - `3` → Carlos

### 6. Deleting Carlos

- Now, go back to the delete request and change the user ID to `3`.
- Send the request to delete Carlos.
 <img width="1204" height="538" alt="screenshot_20261026_145522" src="https://github.com/user-attachments/assets/8dd820f1-9c24-4095-b432-589c23b91470" />

- The lab will be solved\!
<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/365b7253-b1f4-46ce-9178-82e59344105f" />

