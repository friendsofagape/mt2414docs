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

# Source text information
The following APIs can be called to access the information of source langauges, version and texts available on the server.

## Get source language list
To get the list of available source langauges on the server, a user can call the api `/language`. In response, the server will return the list of language ISO codes available on the server.

If the server couldn't find any languages, the response will be `{"success":false, "message":"No Languages"}`.

```shell

> CURL
curl
  -X POST
  -H "Authorization:bearer <access token>"
  "https://api.autographamt.bridgeconn.com/v1/language"

> Example
curl
  -X POST
  -H "Authorization:bearer <YOUR ACCESS TOKEN>" 
  "https://api.autographamt.bridgeconn.com/v1/language"

> Repsonse (list of 3 letter ISO codes of the lanagues available)
["guj", "tel", "khn", "ori", "mar", "hin"]
``` 

## Get source text list
To get the list of available source languages *with the versions name*, a user can call the api `/get_languages`. In response, the server will return the list of language iso codes and the corresponding version names paired as a list.

If the server couldn't find any text matching the language-version name, the response will be `{"success":false, "message":"No Languages"}`.

```shell

> CURL
curl
  -X POST
  -H "Authorization:bearer <access token>"
  "https://api.autographamt.bridgeconn.com/v1/get_languages"

> Examples
curl 
  -X POST 
  -H "Authorization:bearer <YOUR ACCESS TOKEN>" 
  "https://api.autographamt.brideconn.com/v1/get_languages"

> Response (3 letter ISO code of the language + version name)
[["ori", "GL-ORYA-NT"], ["mar", "GL-MAR-NT"], ["guj", "GL-GUJ-NT"], ["ori", "OBS"], ["tel", "GL-TEL-NT"], ["hin", "GL-HIN-NT"], ["khn", "OA-KHN-NT"], ["hin", "GL-HIN-PSA-PRO"]]
``` 
## Get the book list
To get the list of books and it's revision number of the a given version of a source language, a user can call the `/get_books` api. In response, the server will return a list of all the books as 3 letter book id and revision number paired as a list.

If the server couldn't find the given combination it will send ```{"success":false, "message":"No books available"}``` as the response.


```shell
> CURL

curl
  -X POST
  -H "Authorization:bearer <access token>"
  -d '{"language":"<language_id>", "version":"<version_name>"}'
  "https://api.autographamt.bridgeconn.com/v1/get_books"

> Example
curl 
  -X POST 
  -H "Authorization:bearer <YOUR ACCESS TOKEN>" 
  -d '{"language":"ori", "version":"GL-ORYA-NT"}' 
  "https://api.autographamt.bridgeconn.com/v1/get_books"

> Response (3 letter ISO code + revision number)
[["MRK", "1"], ["GAL", "1"], ["MAT", "1"], ["ROM", "1"], ["JHN", "1"], ["LUK", "1"], ["ACT", "1"], ["EPH", "1"], ["PHP", "1"], ["COL", "1"], ["TIT", "1"], ["PHM", "1"], ["HEB", "1"], ["JAS", "1"], ["REV", "1"], ["JUD", "1"], ["1CO", "1"], ["2CO", "1"], ["1TH", "1"], ["2TH", "1"], ["1TI", "1"], ["2TI", "1"], ["1PE", "1"], ["2PE", "1"], ["1JN", "1"], ["2JN", "1"], ["3JN", "1"]]
``` 
# Download Tokens
As soon as a source text is submitted to the server, it will generate the tokens save it in a database. 

In order to download the tokens, user have to specify the source from where (s)he want to download the tokens for. User must specify the language, version and the revision of the source along with the book(s) (s)he wanted to download the tokens for.

## List existing sources
The following APIs can be called to find the existing source text information.

##  - Languages
Please see the [section above](./#get-source-language-list)

##  - Version
A language can have more than one version of the Bible, and each version can be uploaded to the server using a separate unique version name. To list all the versions available for a specific language, call the `/versions` api.

Response in case no versions available for the given langauge, `{"success":false, "message":"No version"}`.

```shell

> CURL
curl 
  -X 
  -H "Authorization:bearer <access token>" 
  -d '{"language":"<language>"}' 
  "https://api.autographamt.bridgeconn.com/v1/version"

> Example
curl 
  -X 
  -H "Authorization:bearer <YOUR ACCESS TOKEN>" 
  -d '{"language":"ori"}' 
  "https://api.autographamt.bridgeconn.com/v1/version"

> Response (List of versions)
["GL-ORYA-NT", "OBS"]
```

##  - Revision
In many cases the source text itself is dynamic in nature. It will undergo several changes in course of time. In order to keep track of this, AutographaMT uses revision numbers. Every time a text is submitted it is automatically assigned a revision number. If anybody submit an updated version of an existing book, a new revision is created and the files is saved as a new revision. This will enable the engine to generate a translation even after the text gets revised between the token download and the tokens submission.

The `/revision` api takes the language name and the version number as input and will send a list of revisions available as a response.

In case no records matches the language name and the version number submitted the user, a response will be sent to the user/client with the message `{"success":false, "message":"No books available"}`.

```shell

> CURL
curl 
  -X 
  -H "Authorization:bearer <access token>" 
  -d '{"language":"<language>", "version":"<version>"}' 
  "https://api.autographamt.bridgeconn.com/v1/revision"

> Example
curl 
  -X 
  -H "Authorization:bearer <YOUR ACCESS TOKEN>" 
  -d '{"language":"ori", "version":"ULB"}' 
  "https://api.autographamt.bridgeconn.com/v1/revision"

> Response (List of versions)
["1"]
```
## Download Tokens
There are two different ways a user can download tokens. A user can download the entire set of tokens for a specific set book(s) or the tokens for a set of book(s) excluding the tokens already included in a previous set.

##  - Tokens Complete Set
If the complete set of tokens are to be downloaded, a user can send the list of book(s) along with the language name, version and revision to the  `/bookwiseautotokens`. Server in response will send the complete set of tokens for the books specified, if they are available on the server. In case if the books are not present, server will respond with one of the following messages.

  - If Source is not found `{"success":false, "message":"Source is not available. Upload source."}`

  - If no books are selected `{"success":false, "message":"Select any books from include books"}`

  - If no books are selected to be include list, but some books are selected in the exclude list `{"success":false, "message":"Select any books from include books"}`.

```shell
> CURL

curl 
  -X POST
  -H "Authorization:bearer <access token>" 
  -d '{"sourcelang":"sourcelang", "<version>":"<version>", "revision":"revision", "books":["book1", "book2"...], "nbooks":["book1", "book2"...], "targetlang":"<targetlang>"}' "https://api.mt2414.in/v1/getbookwiseautotokens"

> Example
curl
  -X POST
  -H "Authorization:bearer <YOUR ACCESS TOKEN>"
  -d '{"sourcelang":"ori", "version":"GL-ORYA-NT", "revision":"1", "books":["PHM", "2JN"], "nbooks": ["3JN"], "targetlang":"hin"}'
  "https://api.mt2414.in/v1/getbookwiseautotokens"

> Response (Tokens of the selected books as an Excel Sheet)

```

##  - Excluded Set
The exclude books option can come handy when a user wanted to split the translation job between multiple tranlsators. The tokens from the books listed in the `nbooks` list will be removed after creating the tokens for the `books` selected. In this way a team can avoid the duplication of the work.

```shell
> CURL

curl 
  -X POST
  -H "Authorization:bearer <access token>" 
  -d '{"sourcelang":"sourcelang", "<version>":"<version>", "revision":"revision", "books":["book1", "book2"...], "nbooks":["book1", "book2"...], "targetlang":"<targetlang>"}' "https://api.autographamt.bridgeconn.com/v1/getbookwiseautotokens"

> Example
curl
  -X POST
  -H "Authorization:bearer <YOUR ACCESS TOKEN>"
  -d '{"sourcelang":"ori", "version":"GL-ORYA-NT", "revision":"1", "books":["PHM", "2JN"], "nbooks": ["3JN"], "targetlang":"hin"}'
  "https://api.autographamt.bridgeconn.com/v1/getbookwiseautotokens"

> Response 
Tokens of the selected books as an Excel Sheet
```
<aside class="notice">
Supply the books to be included in the token generation as a value of "books" and the books to be excluded in the "nbooks".
</aside>

<aside class="warning">
If you use the exclude tokens, the translation can't be complete until the user uploads the first sets of tokens generated.
</aside>

# Submit Translated Tokens
To submit the translated tokens back to the server, `/uploadtokentransltion` api can be called. The translated tokens must be in either `xls` or in `xlsx` (preferred) format.

In order to upload the translated tokens, user must specify the target language. 

## List of target languages
To get the list of currently available target languages, use `/languagelist` api. 

```shell
> CURL
curl 
  -H "Authorization:bearer <access token>" 
  "https://api.autographamt.bridgeconn.com/v1/languagelist"

> Example
curl 
  -H "Authorization:bearer <YOUR ACCESS TOKEN>" 
  "https://api.autographamt.bridgeconn.com/v1/languagelist"

> Response 
'{"English": "en", "Malayalam": "ml", "Hindi": "hi", ...}'
```

## Update language list
AutographaMT lists all the Indian languages listed in [`translationDatabase`]("http://td.unfoldingword.org/exports/langnames.json") which is maintained by the unfoldingword.org team. To update the list on the AutographaMT server, user can call the `/updatelanguagelist` api.

```shell
> CURL
curl 
  -H "Authorization:bearer <access token>" 
  "https://api.autographamt.bridgeconn.com/v1/updatelanguagelist"

> Example
curl 
  -H "Authorization:bearer <YOUR ACCESS TOKEN>" 
  "https://api.autographamt.bridgeconn.com/v1/updatelanguagelist"

> Response 
{"success":true, "message":"Language List updated."}
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
