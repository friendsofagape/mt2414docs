---
title: API Reference

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

Welcome to the MT2414 API! You can use our API to access MT2414 API endpoints to accelerate Bible translations.

# Authentication

> To authorize, use this code:

```shell
# With shell, you can just pass the correct header with each request
curl "api_endpoint_here"
  -H "Authorization: secretkey"
```

> Make sure to replace `secretkey` with your API key.

MT2414 uses API keys to allow access to the API. You can register a new MT2414 API key at our [site](https://mt2414.in).

MT2414 expects for the API key to be included in all API requests to the server in a header that looks like the following:

`Authorization: secretkey`

<aside class="notice">
You must replace <code>secretkey</code> with your personal API key.
</aside>

# Token Words

## Get Token Words

```shell
curl "https://api.mt2414.in/v1/tokenwords/mal"
  -H "Authorization: secretkey"
```

> The above command returns JSON structured like this:

```json
{
  "tokenwords" : [
    {
      "msgid": "Some text",
      "msgstr": ""
    },
    {
      "msgid": "Some text",
      "msgstr": ""
    }]
}
```

This endpoint retrieves all token words.

### HTTP Request

`GET https://api.mt2414.in/v1/tokenwords/sourcelang`

# Translations

## Create new translation


# Corrections

# Suggestions
