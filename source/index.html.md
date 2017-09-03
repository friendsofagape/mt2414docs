---
title: AutographaMT API Reference

language_tabs:
  - shell

toc_footers:
  - <a href="https://mt2414.in">Sign Up for a Developer Key</a>
  - <a href="https://github.com/lord/slate">Documentation Powered by Slate</a>

includes:
  - errors

search: true
---

# Introduction

Welcome to the AutographaMT API Documentation! You can use our API to access AutographaMT API endpoints to accelerate Bible translations. The AutographaMT APIs are accessed through a AutographaMT client.

The documentation on how to use the AutographaMT client application is provided on [it's website](https://autographamt.bridgeconn.com). 

# New user registration
Currently anyone with a valid email address can register and access the resources on the AutographaMT server.

> To register a new user account:

```shell
# If you have curl installed, you can directly access the APIs listed here. Alternativeley, you can use the Postman plugin (Firefox and Chrome).

curl -X POST 
  -d "email=<email_id>" 
  -d "password=<password>" 
  https://api.autographamt.bridgeconn.com/v1/registration

> Server will send a verification email to the email_id provided by the user, if the given email_id is not used for registering any other user accounts.

> It will also return a message `{"success":true, "message":"Verification Email Sent"}` if it accepts the credentials provided by the user. If the email_id is already used for registering a user account, it will return a message, `{"success":false, "message":"Email Already Exists"}`
```

# Authentication

> To authorize, use this code:

```shell
curl "https://api.autographamt.bridgeconn.com/v1/auth>"
  -X POST 
  -d "email=<email_id>" 
  -d "password=<password>"

> This will return the `secretkey` to access the API server.
```
AutographaMT uses API keys to allow access to the APIs. You can register a new AutographaMT API key at our [site](https://augtographamt.bridgeconn.com).

AutographaMT expects for the API key to be included in all API requests to the server in a header that looks like the following:

`Authorization: secretkey`

<aside class="notice">
You must replace <code>secretkey</code> with your personal API key.
</aside>

# Recovering passwords

Recovering of the forgotten passwords is done in two stages.

## Request temporary password

User requests to reset the password by providing the email id to reset the password along with a link to the /forgotpassword endpoint.

If the email Id submitted is on the server and is verified the system will generate a temporary password and sends it to the user's email id.

It will also return `{"success":true, "message":"Link to reset password has been sent to the registered mail ID"}`
    
If no such email registered on the server, 

it will return `{"success":false, "message":"Email has not yet been registered"}`

> To send the request for a temporary password

```shell
curl 
  -X POST
  # TODO
``` 
## Reset the password
User can then submit a new password, along with the temporary password sent by the system.

If the temporary password matches the password sent by the server, the hash + salt of the new password is stored as the new password. The server will return a message `{"success":true, "message":"Password has been reset. Login with the new password."}`


If the temporary password doesn't match with the server's temporary password, it will return an error message `{"success":false, "message":"Invalid temporary password."}`

> To send the request for a temporary password

```shell
curl 
  -X POST
  # TODO
``` 


# Token Words

## Get Token Words

```shell
curl "https://api.mt2414.in/v1/tokenwords/<source-lang>"
  -H "Authorization: secretkey"
```

> The above command returns JSON structured like this:

```json
{
  "tokenwords" : [
    {
      "msgid": "word1",
      "msgstr": ""
    },
    {
      "msgid": "word2",
      "msgstr": ""
    }]
}
```

This endpoint retrieves all token words.

### HTTP Request

`GET https://api.mt2414.in/v1/tokenwords/<source-lang>`

# Translations

## Create new translation

```shell
curl "https://api.mt2414.in/v1/translations"
  -X POST
  -H "Authorization: secretkey"
  -d '{
  "tokenwords" : [
    {
      "msgid": "word1",
      "msgstr": "translation1"
    },
    {
      "msgid": "word2",
      "msgstr": "translation2"
    }]
}'
```

> The above command returns JSON structured like this:

```json
{
"GEN": "USFM text for Genesis",
"EXO": "USFM text for Exodus"
}
```

### HTTP Request

`POST https://api.mt2414.in/v1/translations`

# Corrections

## Update corrections

```shell
curl "https://api.mt2414.in/v1/corrections"
  -X POST
  -H "Authorization: secretkey"
  -d '{
    "book.chapter.verse" : "New verse text",
    "book.chapter.verse" : "New verse text",
    "book.chapter.verse" : "New verse text",
}'
```

> The above command returns JSON structured like this:

```json
{
"GEN": "USFM text for Genesis",
"EXO": "USFM text for Exodus"
}
```

### HTTP Request

`POST https://api.mt2414.in/v1/corrections`

# Suggestions

## Get suggestions

```shell
curl "https://api.mt2414.in/v1/suggestions/<book-name>"
  -H "Authorization: secretkey"
```

> The above command returns JSON structured like this:

```json
{
"GEN": "USFM text for Genesis",
"EXO": "USFM text for Exodus"
}
```

### HTTP Request

`GET https://api.mt2414.in/v1/suggestions/<book-name>`
