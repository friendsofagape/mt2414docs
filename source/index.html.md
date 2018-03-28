---
title: AutographaMT API Reference

language_tabs:
  - shell

toc_footers:
  - <a href="https://autographamt.com">Sign Up for a Developer Key</a>
  - <a href="https://github.com/lord/slate">Documentation Powered by Slate</a>

includes:
  - errors

search: true
---

# Introduction

Welcome to the AutographaMT API Documentation! You can use our API to access AutographaMT API endpoints to accelerate Bible translations. The AutographaMT APIs are accessed through a AutographaMT client.

The documentation on how to use the AutographaMT client application is provided on [it's website](https://autographamt.com). 

# New user registration
Currently anyone with a valid email address can register and access the resources on the AutographaMT server.

> To register a new user account:

```shell
# If you have curl installed, you can directly access the APIs listed here. Alternativeley, you can use the Postman plugin (Firefox and Chrome).

curl -X POST 
  -d "username=<email_id>" 
  -d "password=<password>" 
  "https://api.autographamt.com/v1/registration"

> Server will send a verification email to the email_id provided by the user, if the given email_id is not used for registering any other user accounts.

> It will also return a message `{"success":true, "message":"Verification Email Sent"}` if it accepts the credentials provided by the user. If the email_id is already used for registering a user account, it will return a message, `{"success":false, "message":"Email Already Exists"}`
```
## Verification
When the user clicks on the verification link sent to the email id, the verification request is submitted to the /verification endpoint.

```shell
curl "https://api.autographamt.com/v1/verification/<verification_code>"
  -X GET

> After the succesfful verification, the user will be directed to the homepage of the client app.
```
# Authentication Overview
Authentication is the process of determining a client's identity. Authorization refers to the process of determining what permissions an authenticated user has on the system. In other words, the authentication process will identify a user where in authorization determines what he/she can do on the system.

## Authentication

> To authorize, use this code:

```shell
curl "https://api.autographamt.com/v1/auth"
  -X POST 
  -d "username=<email_id>" 
  -d "password=<password>"

> This will return the `secretkey` to access the API server.
```
AutographaMT uses API keys to authenticate and authorize clients to provide access to the APIs. You can register a new AutographaMT API key at our [site](https://augtographamt.bridgeconn.com). To authorize a user on the API side, we use JWT tokens. These have to be added to all requests using the following header.

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
curl "https://api.autographamt.com/v1/resetpassword"
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
curl "https://api.autographamt.com/v1/forgotpassword"
  -X POST 
  -d "temp_password=<temp_password>" 
  -d "password=<new_password>"
``` 
# Keys
The /v1/keys will permit a client application to interact with the system without having to call the /auth all the time. 

## Authorization
There are 3 roles in AutographaMT. The top level role is `superadmin` whose role is assigned manually during the installation of the server. Every new user on the system is a `member`. A `superadmin` can optionally change the role of a user from `member` to `admin`. 
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
  "https://api.autographamt.com/v1/language"

> Example
curl
  -X POST
  -H "Authorization:bearer <YOUR ACCESS TOKEN>" 
  "https://api.autographamt.com/v1/language"

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
  "https://api.autographamt.com/v1/get_languages"

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
  "https://api.autographamt.com/v1/get_books"

> Example
curl 
  -X POST 
  -H "Authorization:bearer <YOUR ACCESS TOKEN>" 
  -d '{"language":"ori", "version":"GL-ORYA-NT"}' 
  "https://api.autographamt.com/v1/get_books"

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
  "https://api.autographamt.com/v1/version"

> Example
curl 
  -X 
  -H "Authorization:bearer <YOUR ACCESS TOKEN>" 
  -d '{"language":"ori"}' 
  "https://api.autographamt.com/v1/version"

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
  "https://api.autographamt.com/v1/revision"

> Example
curl 
  -X 
  -H "Authorization:bearer <YOUR ACCESS TOKEN>" 
  -d '{"language":"ori", "version":"ULB"}' 
  "https://api.autographamt.com/v1/revision"

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
  -d '{"sourcelang":"sourcelang", "<version>":"<version>", "revision":"revision", "books":["book1", "book2"...], "nbooks":["book1", "book2"...], "targetlang":"<targetlang>"}' "https://api.autographamt.com/v1/getbookwiseautotokens"

> Example
curl
  -X POST
  -H "Authorization:bearer <YOUR ACCESS TOKEN>"
  -d '{"sourcelang":"ori", "version":"GL-ORYA-NT", "revision":"1", "books":["PHM", "2JN"], "nbooks": ["3JN"], "targetlang":"hin"}'
  "https://api.autographamt.com/v1/getbookwiseautotokens"

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
Once the translation of the tokens is completed, user can upload it back to the server. The translated tokens must be in either `xls` or in `xlsx` (preferred) format.

In order to upload the translated tokens, user must specify the [source language](./#get-source-language-list), [version](./#version) and [revision](./#revision) number along with the target language. 

## List of target languages
To get the list of currently available target languages, use `/languagelist` api. 

```shell
> CURL
curl 
  -H "Authorization:bearer <access token>" 
  "https://api.autographamt.com/v1/languagelist"

> Example
curl 
  -H "Authorization:bearer <YOUR ACCESS TOKEN>" 
  "https://api.autographamt.com/v1/languagelist"

> Response 
'{"English": "en", "Malayalam": "ml", "Hindi": "hi", ...}'
```

### Update language list
AutographaMT lists all the Indian languages listed in [`translationDatabase`]("http://td.unfoldingword.org/exports/langnames.json") which is maintained by the unfoldingword.org team. To update the list on the AutographaMT server, user can call the `/updatelanguagelist` api.

```shell
> CURL to Update the target language list
curl 
  -H "Authorization:bearer <access token>" 
  "https://api.autographamt.com/v1/updatelanguagelist"

> Example
curl 
  -H "Authorization:bearer <YOUR ACCESS TOKEN>" 
  "https://api.autographamt.com/v1/updatelanguagelist"

> Response 
{"success":true, "message":"Language List updated."}
```
## Upload Translated Tokens
To submit the translated tokens back to the server, `/uploadtokentranslation` api can be called. This API accepts the translated tokens as a spreadsheet file along with other metadata such as the source language, version, revision and the target language.

```shell

> CURL
curl 
  -X POST 
  -H "Authorization: bearer <your access token>" 
  -F "language=<3_letter_iso_language_code>" 
  -F "version=<version_name>" 
  -F "revision=<rev_number" 
  -F "targetlang=<3_letter_iso_language_code>" 
  -F "tokenwords=@path/to/file" 
  "https://api.mt2414.in/v1/uploadtokentranslation"

>Example
curl 
  -X POST 
  -H "Authorization: bearer <YOUR ACCESS TOKEN>" 
  -F "language=guj" 
  -F "version=GL-GUJ-NT" 
  -F "revision=1" 
  -F "targetlang=ori" 
  -F "tokenwords=@./gj.xlsx" 
  "https://api.mt2414.in/v1/uploadtokentranslation"

> Response
The server will respond with the message `server message` if the file is uploaded sucessfully
```

Condition | Response 
-----------------|----------
The source doesn't exist on the server | '{"success":false, "message":"Unable to locate the language, version and revision number specified"}'
Invalid file format | {"success":false, "message":"Invalid file format. Upload correct format of xls/xlsx files."}
No new translated tokens | {"success":false, "message":"No Changes. Existing token is already up-to-date."}
If tokens are not translated | {"success":false, "message":"Tokens have no translation"}
If the file upload completed succesfully | {"success":true, "message":"Token translation have been uploaded successfully"}


<aside class="warning">
The `curl` command for this api is not tested using curl. The file submission was tested using postman.
</aside>

## Update Tokens
When a user submit a translated token set through `/uploadtokentranslation` api, the server ignores any words that already have a translation. This is to prevent accidental any accidental changes in the translation. However if there are some tokens that need to be updated, an `admin` user can use the `/updatetranslatedtokens` api to do so. 

```shell
> CURL

curl 
  -X POST 
  -H "Authorization: bearer <access token>" 
  -F "language=<IETF_language_code>" 
  -F "version=<version_name>" 
  -F "revision=1" 
  -F "targetlang=ori" 
  -F "tokenwords=@/home/benjamin/Desktop/gj.xlsx" 
  "https://api.mt2414.in/v1/updatetokentranslation"

> Example
curl 
  -X POST 
  -H "Authorization: bearer <access token>" 
  -F "language=guj" 
  -F "version=GL-GUJ-NT" 
  -F "revision=1" 
  -F "targetlang=ori" 
  -F "tokenwords=@/home/benjamin/Desktop/gj.xlsx" 
  "https://api.mt2414.in/v1/updatetokentranslation"

> Response
If the update token completed succesfully, the server send the response `{"success":true, "message":"Token translation have been updated"}`
```
# Translations
The translation APIs will help user see the progress of the translation and also to download the draft translation. The translation APIs requires the following information.
[source language](./#get-source-language-list), [version](./#version), [revision](./#revision) and the [target language](./#list-of-target-languages)

## Book
The `/book` api will give the available books for the selected pair of languages.

```shell

> CURL
curl 
  -X POST 
  -H "Authorization: bearer <api token>" 
  -d '{"language":"<IETF_language_code>", "version":"<version_name>", "revision":"<revision_number>", "targetlang":"<IETF_langauge_code_of_target_language>"}' 
  "https://api.autographamt.com/v1/book"

> Example
curl 
  -X POST 
  -H "Authorization: bearer <YOUR API TOKEN>" 
  -d '{"language":"guj", "version":"GL-GUJ-NT", "revision":"1", "targetlang":"ori"}' 
  "https://api.autographamt.com/v1/book"

> Response
["REV", "JUD", "3JN", "2JN", "1JN", "2PE", "1PE", "JAS", "HEB", "PHM", "TIT", "2TI", "1TI", "2TH", "1TH", "COL", "GAL", "2CO", "ROM", "EPH", "PHP", "1CO", "ACT", "JHN", "LUK", "MRK", "MAT"]
```
## Download Draft
To download the draft translation.

```shell

> CURL
curl
  -X POST
  -H "Authorization: secretkey"
  -d '{"sourcelang": "<IETF_code_of_source_language>", "version": "version_name", "revision": "<Revision_Number>" , "targetlang": "<IETF_code_of_target_language>", "books": "[3_letter_codes_of_books_as_list]"}'
  "https://api.autographamt.com"

>Example
curl 
  -X POST 
  -H "Authorization: bearer <YOUR ACCESS TOKEN>" 
  -d '{"sourcelang":"guj", "version":"GL-GUJ-NT", "revision":"1", "targetlang":"ori", "books":["JUD", "2JN"]}' "https://api.mt2414.in/v1/translations"

> Response
If success, the server will return the requested books in a JSON structure, with book id as Key and the draft in usfm format as value. 

{"2JN": "2JN draft in USFM" 
 "JUD": "JUD Draft in USFM"}
```

Condition | Response 
-----------------|----------
No books selected for translation | {"success":false, "message":"Select the books to be Translated."}
The source doesn't exist on the server | '{"success":false, "message":"Source is not available. Upload it"}'
If some books requested are not available on server | {"success":false, "message":"' + ", " <unavailable book ids> + ' not available. Upload it to generate draft"}'

### HTTP Request

`POST https://api.mt2414.in/v1/translations`
