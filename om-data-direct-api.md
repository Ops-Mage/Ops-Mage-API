# API Documentation

## Overview
This API allows for Ops Mage customers to submit data directly for contextualization and brand suitability, as well as retrieve data that has been contextualized. 

For efficiency we also return a bucketed score of 'unscored','low','medium','high' where 'low' is poor quality unsafe content, 'medium' may have some more adult content, emotional themes, or topical subjects that may be about sensitive content (for example, a review about an action movie that takes place in a war), 'high' quality is content that does not have any sensitive content in it, but may have a range of emotions.

The score bucketing is as follows:
- 'unscored' = content that has not been analyzed yet. This will always have a default score of 0
- 'low' = a score of .4 and lower
- 'medium' = a score of .41 to .67
- 'high' = a score greater than or equal to .68

### Cacheing
Content requests are cached for 1 minute intervals and cache based on the content_id. 
If you need to defeat the cache, contact Ops Mage.

### Base URL
- `https://opsmage-api.io`

## Authentication
- The API uses an `api_key` for authentication, passed as a query parameter.

## Endpoints

### GET /context/api

#### Description
Retrieves a context item based on either `content_id` or `opsmage_id`.

#### Parameters

- **Query Parameters:**
  - `api_key` (string, required): The API key to authenticate the request.
  - `content_id`  (base64 encoded string, required): The content provider's persisted unique ID of the content for future requests about the contextualization. If no unique ID, the unique URL of the content (webpage) should be used. ASCII characters only.
  - `opsmage_id` (string, optional): The unique opsmage ID of the content to retrieve.

> Note: Either `content_id` or `opsmage_id` must be provided (if you know the opsmage_id). If both are present, content_id will take priority.

#### Request Example

```http
GET {BASE_URL}/context/api?api_key=<your_api_key>&content_id=aHR0cHM6Ly93d3cueGtjZC5jb20vMTQ4MS8=&ops_mage_id=
```

#### Response

- **Success Response:**
  - **Code:** 200 OK
  - **Content Example:**
  ```json
  {
    "content_classificaiton": {
            'opmsage_data_id': "d66f3d757bf05d92e5c122c5770da0d3",
            'content_id': "https://www.xkcd.com/1481/",
            'media_type': "<text|image|video>",
            'publisher_domain': "xkcd.com",
            'res_score': .87,
            'res_score_bucket': 'high',
            'iab_cats': ['Books & Literature', 'Entertainment', 'Technology'],
            'iab_cat_ids': ['42', 'JLBCU7', '89'],
            'brands': [],
            'brand_ids': [],
            'restricted_cat': [],
            'restricted_cat_id': [],
            'content_status': 'processed|processing error',
            'emotions': ['Delight', 'Happiness', 'Satisfaction', 'Trust'],
            'emotion_ids': ['d829bb17','b4915e0a','92bfd7eb','cbc7c3aa'],
            'related_opsmage_ids': ['63d6d73c03fc61319ae403e076f4a95a']
    }
}


- **Error Responses:**
  - **Invalid API Key:**
    - **Code:** 400 Bad Request
    - **Content:**
    ```json
    {
      "error": "Invalid API Key"
    }
    ```
  - **Missing Parameters:**
    - **Code:** 400 Bad Request
    - **Content:**
    ```json
    {
      "error": "You must include an opsmage_id or content_id"
    }
    ```
  - **Item Not Found:**
    - **Code:** 400 Not Found
    - **Content:**
    ```json
    {
      "error": "No classified content with this ID, please try again later."
    }
    ```
- **Misc Errors:**
    - **Code:** 400 Not Found
    - **Content:**
    ```json
    {
      "error": "<more detailed error reporting not yet documented>"
    }
    ```
- **500 Errors:**
    - **Code:** 5XX <relevant message>
    
### POST /context/api

#### Description
Initiates classification of content. Text classification typically occures in under a minute.
Content scores are returned between 0.0 and 1.0


#### Parameters

- **Query Parameters:**
  - `api_key` (string, required): The API key to authenticate the request.
  - `content_id` (base64 encoded string, required): The content provider's persisted unique ID of the content for future requests about the contextualization. If no unique ID, the unique URL of the content (webpage) should be used.  ASCII characters only.

- **Body Parameters:**
  - `content` (string, required): The content string to be contextualized. If processing an image, the content string should be left as an empty string.
  - `domain` (string, required): The domain associated with the content. Domains are whitelisted per API key, thus must be accurate to your account, this is inclusive of sub domains.
  - `image_url` (string, optional): The URL of the image to be classified
  
*Note* If  content is present with the image_url, the image_url will be ignored. Future state of the API will consider include both content and image together for contextualization.

#### Text Content Request Example

```http
POST {BASE_URL}/context/api?api_key=your_api_key&content_id=aHR0cHM6Ly93d3cueGtjZC5jb20vMTQ4MS8=
Content-Type: application/json

{
  "content": "Lorem ipsum dolor sit amet, consectetur adipiscing elit. Fusce maximus commodo odio. Integer quam diam, pulvinar ornare lorem non, laoreet aliquam diam. Vivamus pellentesque sapien vel sodales fermentum. Donec lacinia imperdiet nibh a mollis. Praesent sed pretium velit, sed accumsan nulla.",
  "image_url": "",
  "domain": "xkcd.com"
}
```

#### *Image* Request Example
## Only jpg,gif(non-animated),png are currently supported ##

```http
POST {BASE_URL}/context/api?api_key=your_api_key&content_id=aHR0cHM6Ly91cGxvYWQud2lraW1lZGlhLm9yZy93aWtpcGVkaWEvY29tbW9ucy83Lzc5L0dhemVsbGFfZ2F6ZWxsYS5qcGc=
Content-Type: application/json

{
  "content": "",
  "image_url": "https://upload.wikimedia.org/wikipedia/commons/7/79/Gazella_gazella.jpg",
  "domain": "wikimedia.com"
}
```

#### Response

- **Success Response:**
  - **Code:** 200 OK
  - **Content Example:**
  ```json
  {"content_classificaiton":{
    "content_id": "https://upload.wikimedia.org/wikipedia/commons/7/79/Gazella_gazella.jpg", //the base54 decodedd content ID supplied
    "ops_mage_id": "827ccb0eea8a706c4c34a16891f84e7b", //unique id for content generated by opsmage
    "ops_mage_image_id": "", //unique id for image, generated by opsmage
    "content_status": "processing",
  }}
  ```

- **Error Responses:**
  - **Invalid API Key:**
    - **Code:** 400 Bad Request
    - **Content:**
    ```json
    {
      "error": "Invalid API Key"
    }
    ```
  - **Invalid Format:**
    - **Code:** 400 Bad Request
    - **Content:**
    ```json
    {
      "error": "POST body appears to be an invalid format"
    }
    ```
  - **Missing Parameters:**
    - **Code:** 400 Bad Request
    - **Content:**
    ```json
    {
      "error": "content and content_id are required"
    }
    ```
  - **Content Processing Error:**
    - **Code:** 400 Processing Error
    - **Content:**
    ```json
    {
      "error": "content type or media processing error"
    }
    ```
### POST /context/scanner/

#### Description
Instructs the Ops Mage Content Scanning Bot to consume content from a supplied URL.

- **Query Parameters:**
  - `api_key` (string, required): The API key to authenticate the request.
  - `content_id` (base64 encoded string, required): The content provider's persisted unique ID of the content for future requests about the contextualization. If no unique ID, the unique URL of the content (webpage) should be used.  ASCII characters only.

- **Body Parameters:**
  - empty JSON object (future expansion will have further functionality and customization)

```http
POST {BASE_URL}/context/scanner?api_key=your_api_key&content_id=aHR0cHM6Ly91cGxvYWQud2lraW1lZGlhLm9yZy93aWtpcGVkaWEvY29tbW9ucy83Lzc5L0dhemVsbGFfZ2F6ZWxsYS5qcGc=
Content-Type: application/json
{}
```

## Support

For further assistance leif@opsmage.io
