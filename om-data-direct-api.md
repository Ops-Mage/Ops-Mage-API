Below is the API documentation for the provided code, explaining the functionality for GET and POST requests, including required data and responses.

---

# API Documentation

## Overview
This API allows for Ops Mage customers to submit data directly for contextualization and brand suitability, as well as retrieve data that has been contextualized. 

### Base URL
- `https://opsmage-api.io`

## Authentication
- The API uses an `api_key` for authentication, passed as a query parameter.

## Endpoints

### 1. GET /context

#### Description
Retrieves a context item from DynamoDB based on either `content_id` or `opsmage_id`.

#### Parameters

- **Query Parameters:**
  - `api_key` (string, required): The API key to authenticate the request.
  - `content_id` (string, optional): The ID of the content to retrieve as initially provided for during contextualization.
  - `opsmage_id` (string, optional): The unique opsmage ID of the content to retrieve.

> Note: Either `content_id` or `opsmage_id` must be provided.

#### Request Example

```http
GET {BASE_URL}/context?api_key=<your_api_key>&content_id=<unique_presisted_content_id>&ops_mage_id=<unqiue_ops_mage_identifer>
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
            'iab_cats': ['Books & Literature', 'Entertainment', 'Technology'],
            'iab_cat_ids': ['42', 'JLBCU7', '89'],
            'brands': [],
            'brand_ids': [],
            'restricted_cat': [],
            'restricted_cat_id': [],
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
    - **Code:** 400 Not FOund
    - **Content:**
    ```json
    {
      "error": "<more detailed error reporting not yet documented>"
    }
    ```
    
### 2. POST /context

#### Description
Initiates classification of content. Classification typically occures in under a minute.

#### Parameters

- **Query Parameters:**
  - `api_key` (string, required): The API key to authenticate the request.

- **Body Parameters:**
  - `content_id` (string, required): The content provider's persisted unique ID of the content for future requests about the contextualization. If no unique ID, the unique URL of the content (webpage) should be used. 
  - `content` (string, required): The content string to be contextualized.
  - `domain` (string, required): The domain associated with the content.
  - `image_id` (string, optional): The publisher's associated ID of the image, if no ID, you can use the URL to the image. * Currently in closed Beta
  - `image_url` (string, optional): The URL to the image. * Currently in closed Beta
  

#### Request Example

```http
POST {BASE_URL}/context?api_key=your_api_key
Content-Type: application/json

{
  "content_id": "12345",
  "content": "Lorem ipsum dolor sit amet, consectetur adipiscing elit. Fusce maximus commodo odio. Integer quam diam, pulvinar ornare lorem non, laoreet aliquam diam. Vivamus pellentesque sapien vel sodales fermentum. Donec lacinia imperdiet nibh a mollis. Praesent sed pretium velit, sed accumsan nulla.",
  "image_id": "img_123",
  "image_url": "https://imgs.xkcd.com/comics/api.png",
  "domain": "xkcd.com"
}
```

#### Response

- **Success Response:**
  - **Code:** 200 OK
  - **Content Example:**
  ```json
  {
    "content_id": "12345",
    "ops_mage_id": "827ccb0eea8a706c4c34a16891f84e7b", //unique id for content generated by opsmage
    "image_id": "img_123",
    "ops_mage_image_id": "6142b5473b532149b34e5c29a14cb762", //unique id for image, generated by opsmage
    "content_status": "processing",
    "image_status": "processing"
  }
  ```

- **Error Responses:**
  - **Invalid API Key:**
    - **Code:** 401 Unauthorized
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
      "error": "content type or media processing error: {error message}"
    }
    ```


## Support

For further assistance leif@opsmage.io