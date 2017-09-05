---
title: AutographaMT API Reference

language_tabs:
  - shell

toc_footers:
  - <a href="https://autographamt.bridgeconn.com">Sign Up for a Developer Key</a>
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
  -d "username=<email_id>" 
  -d "password=<password>" 
  https://api.autographamt.bridgeconn.com/v1/registration

> Server will send a verification email to the email_id provided by the user, if the given email_id is not used for registering any other user accounts.

> It will also return a message `{"success":true, "message":"Verification Email Sent"}` if it accepts the credentials provided by the user. If the email_id is already used for registering a user account, it will return a message, `{"success":false, "message":"Email Already Exists"}`
```
## Verification
When the user clicks on the verification link sent to the email id, the verification request is submitted to the /verification endpoint.

```shell
curl "https://api.autographamt.bridgeconn.com/v1/verification/<verification_code>"
  -X GET

> After the succesfful verification, the user will be directed to the homepage of the client app.
```
# Authentication

> To authorize, use this code:

```shell
curl "https://api.autographamt.bridgeconn.com/v1/auth"
  -X POST 
  -d "username=<email_id>" 
  -d "password=<password>"

> This will return the `secretkey` to access the API server.
```
AutographaMT uses API keys to allow access to the APIs. You can register a new AutographaMT API key at our [site](https://augtographamt.bridgeconn.com). To authorize a user on the API side, we use JWT tokens. These have to be added to all requests using the following header.

`Authorization: Bearer <jwt>`

<aside class="notice">
You must replace <code>secretkey</code> with your personal API key. JSON Web Tokens (JWT) are an open, industry standard RFC 7519 method for representing claims securely between two parties.
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
curl "https://api.autographamt.bridgeconn.com/v1/resetpassword"
  -X POST 
  -d "email=<email>" 

> Server will send a temporary password with the link to the /forgotpasword
``` 
## Reset the password
User can then submit a new password, along with the temporary password sent by the system.

If the temporary password matches the password sent by the server, the hash + salt of the new password is stored as the new password. The server will return a message `{"success":true, "message":"Password has been reset. Login with the new password."}`


If the temporary password doesn't match with the server's temporary password, it will return an error message `{"success":false, "message":"Invalid temporary password."}`

> To send the request for a temporary password

```shell
curl "https://api.autographamt.bridgeconn.com/v1/forgotpassword"
  -X POST 
  -d "temp_password=<temp_password>" 
  -d "password=<new_password>"
``` 
# Keys
The /v1/keys will permit a client application to interact with the system without having to call the /auth all the time. 

### TODO

# Create Source Text
To create a new source language and upload text in the source langugage to the server.

## 1 Create Source Language
An administrator can select the name of the language and supply the version name to create a source langauge.

```shell
curl
  -X
  -H "Authorization:bearer <access token>"
  -d '{"language":"<language>", "<version>":"<version>"}' 
  "https://api.mt2414.in/v1/createsources"

> You can call the /v1/sourceid to get the id of a source language

curl 
  -X POST 
  -H "Authorization:bearer <access token>" 
  -d '{"language":"<language>", "<version>":"<version>"}' 
  "https://api.mt2414.in/v1/sourceid"

```

## 2 Upload Source Text
Once a language and version is assigned to be used as a source, user can upload the source text for that language. You can call the /sources api to upload the content.

```shell
curl 
  -X POST 
  -H "Authorization:bearer <access token>" 
  -d "source_id=<source_id>" 
  -f "content=<content>" 
  "https://api.mt2414.in/v1/sources"

```
<aside class="notice">
The content submitted must be in valid usfm format. At a minimum the file should contain \id followed by a valid 3 letter Book id. Each book must be a separate file.
</aside>

# Language information
The following APIs can be called to access the information of source langauges, version and texts available on the server.

## Get source language list
To get the list of available source langauges on the server, a user can call the api /language. In response, the server will return the list of language ISO codes available on the server.

```shell
curl
  -X POST
  -H "Authorization:bearer <access token>"
  "https://api.autographamt.bridgeconn.com/v1/language"
``` 

## Get source text list
To get the list of available source languages *with the versions name*, a user can call the api /get_languages. In response, the server will return the list of language iso codes and the corresponding version names paired as a list.

```shell
curl
  -X POST
  -H "Authorization:bearer <access token>"
  "https://api.autographamt.bridgeconn.com/v1/get_languages"
``` 
## Get the book list
To get the list of books and it's revision number of the a given version of a source language, a user can call the /get_books api. In response, the server will return a list of all the books as 3 letter book id and revision number paired as a list.

If the server couldn't find the given combination it will send ```{"success":false, "message":"No books available"}``` as the response.

```shell
curl
  -X POST
  -H "Authorization:bearer <access token>"
  -d '{"language":"<language_id>", "version":"<version_name>"}'
  "https://api.autographamt.bridgeconn.com/v1/get_books"
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
