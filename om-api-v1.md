# Ops Mage API v1.0 ::: DEPRECATED
# THIS DOCUMENTATION IS NO LONGER SUPPORTED. Please see Data Direct Api
## Description

This is the official API for Ops Mage server-to-server communications. It is developed for the purpose of CMS integration or server-side programmatic platform enrichment. Ahead of integration consult your integration documents and Ops Mage representative to ensure classification systems are prepared for your domain(s).

## Base URL

The base URL for all API requests is:

`https://context.opsmage.io/api/v1/opsmage`

## Authentication
Autentication of requests is done via an API key that is appended to the base URL with the api_key= parameter. For an API key contact your Ops Mage representative.

## Actions

### `GET ?url`

Returns the relevant classified information json for a given URL

### Parameters

- `api_key` (required): The provisioned API for your account or domain.
- `url` (required): The unique identified for the URL or classification target in base64 encoded form.

### Response

Returns a JSON object with the following properties:

- `url_hash`: the md5hash of the non-base64 encoded URL.
- `unclassifed_values`: an array of any values that do not fit into standard IAB classification, but the AI still deemed relevant.
- `unlcassified_ids`: an array of unique IDs for each of the unlcassified values in Ops Mage for the given article.
- `classified_values`: an array of IAB classification values. Returns the top level and nested values, where nested values will have their parents prepended with a '|' character.
- `classified_ids`: an array of the unique IDs for each IAB classification as stored in Ops Mage (typically takes on the form of the IAB classification value), each ID maps to respective value in order found in the classified_value array.
- `sentiment`: an array of the media or article sentiment: negative, neutral, positive.
- `source`: a string description of the classification source type: text, audio, video
- `brands`: an array of any brand names identified in the source content.

### Example

Request:

```
GET /?url=aHR0cHM6Ly93d3cubmF0dXJhbGx5Y3VybHkuY29tL2N1cmxyZWFkaW5nL2hvdy10b3Mtc3R5bGVzL2hvdy10by1wcmVzZXJ2ZS1hLWZsYXQtdHdpc3Qtb3V0LXdpdGhvdXQtcmV0d2lzdGluZy1zaQ==&api_key=<api_key>",
```

Response:

```
{
"url_hash": "13b7bc4b6769824cc45a582918f1389b",
"unclassified_values": [],
"sentiment": "['positive']",
"unclassified_ids": [],
"brands": "['As I Am', 'Jane Carter Solution']",
"source": "text", "url": "https://www.naturallycurly.com/curlreading/how-tos-styles/how-to-preserve-a-flat-twist-out-without-retwisting-si",
"classified_ids": ["552", "553", "554", "577", "opsmage_count", "opsmage"],
"classified_values": ["Style & Fashion", "Style & Fashion|Beauty", "Style & Fashion|Beauty|Hair Care", "Style & Fashion|Fashion Trends"]
}

```

## Errors

This API uses the following error codes:

- `400 Bad Request`: The request was malformed or missing required parameters.
  The 400 response will containe a default json respons: `{'error_type':'unknown','reason':'Error type unknown, contact Ops Mage}`
- `500 Internal Server Error`: An unexpected error occurred on the server.
