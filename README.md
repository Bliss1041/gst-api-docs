# GoSystem Tax Return API Documentation

## Table of Contents

- [Introduction](#introduction)
- [Getting Started](#getting-started)
  - [Obtaining API Credentials](#obtaining-api-credentials)
  - [Authentication Flow](#authentication-flow)
  - [Making Your First API Call](#making-your-first-api-call)
- [Using Postman](#using-postman)
  - [Setting Up Postman](#setting-up-postman)
  - [Importing the Collection](#importing-the-collection)
  - [Configuring Environment Variables](#configuring-environment-variables)
  - [Testing API Calls](#testing-api-calls)
- [Authentication](#authentication)
- [API Rate Limits](#api-rate-limits)
- [Tax Return Operations](#tax-return-operations)
  - [Opening a Tax Return](#opening-a-tax-return)
  - [Saving Fields](#saving-fields)
  - [Retrieving Fields](#retrieving-fields)
  - [Working with Groups](#working-with-groups)
  - [Saving Field Attributes](#saving-field-attributes)
  - [Getting Diagnostics](#getting-diagnostics)
  - [Getting Overrides](#getting-overrides)
  - [Full Compute](#full-compute)
  - [Closing a Tax Return](#closing-a-tax-return)
- [Metadata Operations](#metadata-operations)
  - [User Accounts](#user-accounts)
  - [Tax Returns](#tax-returns)
  - [Fields](#fields)
  - [Forms](#forms)
- [E-file Attachment Operations](#e-file-attachment-operations)
  - [Adding Attachments](#adding-attachments)
  - [Updating Attachments](#updating-attachments)
  - [Deleting Attachments](#deleting-attachments)
  - [Getting Attachment Metadata](#getting-attachment-metadata)
  - [Getting Jurisdiction Codes](#getting-jurisdiction-codes)
  - [Getting Jurisdiction Details](#getting-jurisdiction-details)
- [Error Handling](#error-handling)
- [Best Practices](#best-practices)

## Introduction

The GoSystem Tax Return API allows developers to programmatically interact with GoSystem Tax Returns. This API provides capabilities to open, edit, and close tax returns, as well as manage e-file attachments and retrieve metadata.

The API follows RESTful principles and uses JSON for request and response payloads. The base URL for all API endpoints is:

```
https://api.thomsonreuters.com/gosystem/tax-returns
```

## Getting Started

Before using the GoSystem Tax Return API, you need to obtain the necessary credentials and understand the authentication flow.

### Obtaining API Credentials

You'll need two sets of credentials to use the API:

#### 1. API Token from GoSystem Tax RS

1. Log in to GoSystem Tax RS at [https://gosystemrs.fasttax.com/](https://gosystemrs.fasttax.com/)
2. Click on the **Options** menu in the upper right corner
3. Select **API Token** from the left-side navigation panel
4. You'll see your API token along with your login ID, firm code, and location code

Example:
```
API Token: XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
Login Id: [Your Login ID]
Firm: [Your Firm Code]
Location: [Your Location Code]
```

#### 2. Consumer Key and Secret from Thomson Reuters Developer Portal

1. Log in to the Thomson Reuters Developer Portal at [https://developers.thomsonreuters.com/](https://developers.thomsonreuters.com/)
2. Navigate to the GoSystem API section
3. Click on **My Keys** in the top navigation
4. Find your application in the list
5. View your Consumer Key and Secret by clicking the "eye" icon

### Authentication Flow

GoSystem API uses OAuth 2.0 for authentication. The most common method is the Authorization Code Grant (3-legged OAuth):

#### Step 1: Obtain an Authorization Code

```http
POST https://api.thomsonreuters.com/gosystem/oauth2/v1/auth-code
Content-Type: application/x-www-form-urlencoded

client_id=[Your Consumer Key]
login_id=[Your GoSystem Login ID]
firm=[Your Firm Code]
center=[Your Location/Center Code]
state=[Random Unique Identifier]
api_token=[Your API Token from GoSystem]
```

The response will contain an authorization code that is valid for 60 seconds.

#### Step 2: Exchange for an Access Token

```http
POST https://api.thomsonreuters.com/gosystem/oauth2/v1/token
Content-Type: application/x-www-form-urlencoded

client_id=[Your Consumer Key]
client_secret=[Your Consumer Secret]
code=[Authorization Code from Step 1]
grant_type=authorization_code
scope="urn:tr:gosystem:auth:api:taxreturn"
```

The response will include an access token (valid for 60 minutes) and a refresh token (valid for 24 hours).

### Making Your First API Call

Once you have obtained an access token, you can make your first API call by retrieving the list of available tax returns:

```http
GET https://api.thomsonreuters.com/gosystem/tax-returns/beta/tax-returns?year=2022&account=12AB&taxType=P
Authorization: Bearer [Your Access Token]
```

For more detailed authentication instructions, refer to the [full authentication documentation](../Authorize/README.md).

## Using Postman

[Postman](https://www.postman.com/) is a popular API testing tool that makes it easy to test and explore APIs. This section covers how to set up Postman to work with the GoSystem Tax Return API.

### Setting Up Postman

1. Download and install Postman from [postman.com](https://www.postman.com/downloads/)
2. Create a free account or sign in to your existing account
3. Create a new workspace for your GoSystem API testing

### Importing the Collection

The GoSystem Tax Return API provides a Postman collection with pre-configured requests for all API endpoints:

1. Request the postman_collection.json from your GST API rep.
2. In Postman, click on "Import" in the upper left corner
3. Drag and drop the collection file or browse to select it
4. Click "Import" to add the collection to your workspace

The imported collection contains organized folders for all available API endpoints, including authorization, tax return operations, and e-file attachments.

### Configuring Environment Variables

To make the most of the Postman collection, set up environment variables to store your credentials and reuse them across requests:

1. Click on "Environments" in the left sidebar
2. Click the "+" icon to create a new environment (e.g., "GoSystem API")
3. Add the following variables:
   - `client_id`: Your Consumer Key from the Thomson Reuters Developer Portal
   - `client_secret`: Your Consumer Secret from the Developer Portal
   - `account`: Your GoSystem firm/account code
   - `api_token`: Your API token from GoSystem Tax RS
   - `base64token`: Leave this empty (will be populated automatically)
   - `token`: Leave this empty (will be populated by the authentication response)
   - `refreshtoken`: Leave this empty (will be populated by the authentication response)
   - `authToken`: Leave this empty (will be populated by the open tax return response)
   - `taxReturnSessionToken`: Leave this empty (will be populated by the open tax return response)
4. Click "Save"
5. Select your new environment from the environment dropdown in the upper right corner

### Testing API Calls

Now you're ready to start testing API calls:

1. **Authentication**: 
   - Navigate to the "Authorize" > "GST Authorize" folder
   - Run the "/oauth2/v1/auth-code" request first (this generates an authorization code)
   - Then run the "/oauth2/v1/token" request (this exchanges the code for an access token)
   - The authentication scripts will automatically save the token to your environment variables

2. **Opening a Tax Return**:
   - Navigate to the "GoSystem Tax Return Live API" folder
   - Find and run the "Use to open a Tax Return for editing" request
   - Modify the request body with your tax return details
   - The scripts will automatically save the session tokens to your environment variables

3. **Working with Tax Return Data**:
   - After opening a tax return, you can use the other requests in the collection
   - Try the "Use to retrieve fields" request to fetch data
   - Or the "Use to save fields" request to update data

4. **Closing the Tax Return**:
   - Always remember to close your tax return when finished using the "Use to close an already opened Tax Return" request

The Postman collection includes pre-configured request bodies and authentication helpers, making it much easier to explore and learn the API.

**Tips for Using Postman**:
- Use the "Pre-request Script" and "Tests" tabs to see how authentication and token handling are implemented
- Check the "Console" tab (at the bottom) to debug request/response details
- Save your successful requests to reuse them later
- Use Postman's "Code" button to generate code snippets in your preferred programming language

## Authentication

The GoSystem Tax Return API uses OAuth 2.0 for authentication. For detailed instructions on obtaining credentials and authenticating with the API, please refer to the [Authentication Documentation](../Authorize/README.md).

In summary, you'll need:
1. An API Token from GoSystem Tax RS
2. Consumer Key and Secret from the Thomson Reuters Developer Portal

After authentication, you'll receive access tokens that must be included in the `Authorization` header of all API requests.

## API Rate Limits

To ensure optimal performance, prevent server overload, and promote fair use among all API customers, the GoSystem Tax Return API enforces rate limits. Understanding and respecting these limits is essential for building reliable applications.

### Rate Limit Policies

- **Hourly Limit**: 10,000 requests per hour for each GoFileRoom firm
- **Reset Interval**: The request count resets at the top of each hour (e.g., 1:00, 2:00)
- **Spike Arrest Policy**: Maximum of 250 calls per second, which protects against traffic surges and prevents performance issues

### When Rate Limits Are Exceeded

When you exceed the rate limit, the server will respond with:
- HTTP Status Code: `429 Too Many Requests`

### Managing API Requests

To effectively work within these rate limits:

1. **Monitor your request rates** and optimize your applications to stay within the allocated limits
2. **Implement error handling for 429 status codes** to gracefully manage request throttling
3. **Incorporate retry logic** with exponential backoff for failed requests
4. **Batch operations** where possible instead of making multiple individual requests
5. **Cache responses** when appropriate to reduce the number of API calls needed

### Example: Handling Rate Limiting

Here's an example of how to handle rate limiting in your code:

```javascript
async function makeApiRequest(url, options, maxRetries = 3) {
  let retries = 0;
  
  while (retries < maxRetries) {
    try {
      const response = await fetch(url, options);
      
      if (response.status === 429) {
        // Calculate backoff time (exponential with jitter)
        const backoffTime = Math.min(Math.pow(2, retries) * 1000 + Math.random() * 1000, 60000);
        console.log(`Rate limited. Retrying in ${backoffTime}ms`);
        await new Promise(resolve => setTimeout(resolve, backoffTime));
        retries++;
      } else {
        return response;
      }
    } catch (error) {
      console.error("API request failed:", error);
      throw error;
    }
  }
  
  throw new Error("Maximum retries exceeded");
}
```

## Tax Return Operations

### Opening a Tax Return

Before performing any operations on a tax return, you must first open it using the `/beta/open` or `/v1/open` endpoint.

**Endpoint:** `POST /beta/open`

**Request Body:**
```json
{
  "account": "1234",
  "taxYear": "2021",
  "taxReturnId": "3948RT",
  "taxType": "P",
  "subTaxType": "P"
}
```

**Response:**
```json
{
  "taxReturnSessionToken": "taxReturnSessionToken=3e4dff4f-8467-4bfa-9f2e-eeeea784c864~~~BIGipServerSANDBOX-OTWSVC-9100=564121098.35875.0000; path=/; Httponly; Secure",
  "authToken": "eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9...",
  "encodedLocatorDetails": "aXNBdXRoZW50aWNhdGVkPUZhbHNlJmlkbGVUaW1lb...",
  "locatorToken": "f1f9e413-016f-4566-b432-f1e48ae3cf9b",
  "apiServerCookie": "564121098.35875.0000"
}
```

**Note:** The `taxReturnSessionToken` and `authToken` from the response are required for subsequent API calls.

### Saving Fields

To save field values in an open tax return:

**Endpoint:** `POST /beta/save-fields`

**Headers:**
```
authToken: [value from open response]
taxReturnSessionToken: [value from open response]
```

**Request Body:**
```json
{
  "fieldsRequest": [
    {
      "groupField": null,
      "fields": [
        {
          "fieldId": "RS.ENTITYNAME",
          "value": "Entity Name"
        },
        {
          "fieldId": "BASEIN",
          "value": "12-1234567"
        }
      ]
    }
  ]
}
```

### Retrieving Fields

To retrieve field values from an open tax return:

**Endpoint:** `POST /beta/retrieve-fields`

**Headers:**
```
authToken: [value from open response]
taxReturnSessionToken: [value from open response]
```

**Request Body:**
```json
{
  "fieldsRequest": [
    {
      "groupField": null,
      "fields": [
        {
          "fieldId": "RS.ENTITYNAME"
        },
        {
          "fieldId": "BASEIN"
        }
      ]
    }
  ]
}
```

**Response:**
```json
{
  "fieldsResponse": [
    {
      "groupField": null,
      "fields": [
        {
          "fieldId": "RS.ENTITYNAME",
          "value": "Thomson Reuters",
          "responseStatus": null
        },
        {
          "fieldId": "BASEIN",
          "value": "12-1234567",
          "responseStatus": null
        }
      ]
    }
  ]
}
```

### Working with Groups

Groups are used for repeating sections in tax returns such as beneficiaries, 1099s, and K-1s. Working with these elements requires special handling in the API.

#### Group Index Management

When working with groups, follow these rules:
- Indices start at 0
- Indices must be sequential
- **Important:** There is currently no way to delete items using the API - to delete items, you must use the GoSystem UI

#### Example: Adding a Beneficiary

Here's an example of adding a beneficiary as a group field:

**Request Body:**
```json
{
  "fieldsRequest": [
    {
      "groupField": {
        "index": 0,
        "fieldId": "F16.BNAME",
        "value": "John Doe",
        "child": null
      },
      "fields": [
        {
          "fieldId": "F16.OSSN",
          "value": "123-45-6789"
        }
      ]
    }
  ]
}
```

#### Example: Retrieving Group Fields

To retrieve fields from a group, specify the group field with the appropriate index:

**Request Body:**
```json
{
  "fieldsRequest": [
    {
      "groupField": {
        "index": 0,
        "fieldId": "F16.BNAME",
        "value": "John Doe"
      },
      "fields": [
        {
          "fieldId": "F16.OSSN"
        }
      ]
    }
  ]
}
```

#### Example: Adding Multiple Elements to a Group

To add multiple elements to a group (like multiple other income items), include multiple fields with the same field ID but increment the index:

**Request Body:**
```json
{
  "fieldsRequest": [
    {
      "groupField": {
        "fieldId": "INCOME.GROUP",
        "value": "1"
      },
      "fields": [
        {
          "index": 0,
          "fieldId": "OTHER.INCOME.DESC",
          "value": "Rental Income",
          "isGroup": true
        },
        {
          "index": 0,
          "fieldId": "OTHER.INCOME.AMOUNT",
          "value": "2500",
          "isGroup": true
        },
        {
          "index": 1,
          "fieldId": "OTHER.INCOME.DESC",
          "value": "Investment Income",
          "isGroup": true
        },
        {
          "index": 1,
          "fieldId": "OTHER.INCOME.AMOUNT",
          "value": "1800",
          "isGroup": true
        }
      ]
    }
  ]
}
```

### Saving Field Attributes

To save field attributes in an open tax return:

**Endpoint:** `POST /beta/save-field-attributes`

**Headers:**
```
authToken: [value from open response]
taxReturnSessionToken: [value from open response]
```

**Request Body:**
```json
{
  "fieldAttributesRequests": [
    {
      "groupField": null,
      "fieldAttributes": [
        {
          "fieldId": "FIELD_ID",
          "fieldAttributes": {
            "locked": true
          }
        }
      ]
    }
  ]
}
```

### Getting Diagnostics

To retrieve diagnostics for an open tax return:

**Endpoint:** `GET /beta/diagnostics`

**Headers:**
```
authToken: [value from open response]
taxReturnSessionToken: [value from open response]
```

**Response:**
```json
[
  {
    "typeDescription": "Severe",
    "jurisdictionId": 90,
    "jurisdiction": "AllCities",
    "jurisdictiontype": "City",
    "description": "State Return: The City of Philadelphia requires that the State of Pennsylvaia return be active in order to file the city return.",
    "isSuppressed": false,
    "fieldId": ""
  }
]
```

### Getting Overrides

To retrieve overrides for an open tax return:

**Endpoint:** `GET /beta/overrides`

**Headers:**
```
authToken: [value from open response]
taxReturnSessionToken: [value from open response]
```

**Response:**
```json
[
  {
    "computedValue": "",
    "overrideValue": "800-123-4567",
    "selector": "Paid Preparer Information",
    "fieldId": "FTEL1"
  }
]
```

### Full Compute

To trigger a full compute for an open tax return:

**Endpoint:** `POST /beta/fullcompute`

**Headers:**
```
authToken: [value from open response]
taxReturnSessionToken: [value from open response]
```

**Response:**
```
true
```

### Closing a Tax Return

To close a tax return that was previously opened:

**Endpoint:** `POST /beta/close`

**Headers:**
```
authToken: [value from open response]
taxReturnSessionToken: [value from open response]
```

## Metadata Operations

### User Accounts

To retrieve accounts for the logged-in user:

**Endpoint:** `GET /beta/user-accounts`

**Response:**
```json
{
  "userAccounts": [
    {
      "account": "12AB"
    },
    {
      "account": "34CD"
    }
  ]
}
```

### Tax Returns

To retrieve tax returns for a specific firm, year, and tax type:

**Endpoint:** `GET /beta/tax-returns`

**Query Parameters:**
```
year=2019
account=12AB
taxType=P
```

**Response:**
```json
{
  "taxReturns": [
    {
      "year": 2019,
      "taxReturnId": "0123PT",
      "firm": "FT",
      "account": "AB12",
      "taxType": "P",
      "taxPayerName": "Tax Payer 1",
      "clientCode": "111-2222-333"
    }
  ]
}
```

### Fields

To retrieve field definitions for a specific tax year and return type:

**Endpoint:** `GET /beta/fields`

**Query Parameters:**
```
TaxYear=2021
ReturnType=1065
FormName=SCHEDULE K
IncludeStringValues=true
```

**Response:**
```json
[
  {
    "taxYear": "2019",
    "returnType": "1065",
    "fieldId": "C8865.217.7",
    "formName": "8865 3468 ORGANIZER",
    "fieldName": "POST OCT 3 ENERGY CRED 1",
    "fieldType": "O",
    "description": "POST OCT 3 ENERGY CRED 1",
    "screenName": "",
    "screenLongName": "",
    "dataType": "Dollar",
    "dataLength": "15",
    "dataPrecision": "0",
    "isGroup": true,
    "required": false,
    "stringValues": []
  }
]
```

### Forms

To retrieve form definitions for a specific tax year and return type:

**Endpoint:** `GET /beta/forms`

**Query Parameters:**
```
TaxYear=2021
ReturnType=1065
```

**Response:**
```json
[
  {
    "formName": "3468 INVEST CRED"
  },
  {
    "formName": "7004 8004 EXTENSION"
  }
]
```

## E-file Attachment Operations

### Adding Attachments

To attach a file to a tax return:

**Endpoint:** `POST /beta/efile-attachments`

**Headers:**
```
authToken: [value from open response]
taxReturnSessionToken: [value from open response]
```

**Request Body (multipart/form-data):**
```
fileName: File1.pdf
description: Form 3115 - IRS approved advance consent
jurisdictionAttachmentCode: FE
isNewFile: true
file: [binary file data]
```

### Updating Attachments

To update an existing attachment:

**Endpoint:** `PUT /beta/efile-attachments/{jurisdictionAttachmentCode}/{attachmentName}`

**Headers:**
```
authToken: [value from open response]
taxReturnSessionToken: [value from open response]
```

### Deleting Attachments

To delete an attachment:

**Endpoint:** `DELETE /beta/efile-attachments/{jurisdictionAttachmentCode}/{attachmentName}`

**Headers:**
```
authToken: [value from open response]
taxReturnSessionToken: [value from open response]
```

### Getting Attachment Metadata

To retrieve metadata for all attachments:

**Endpoint:** `GET /beta/efile-attachments-metadata`

**Headers:**
```
authToken: [value from open response]
taxReturnSessionToken: [value from open response]
```

**Response:**
```json
[
  {
    "AttachmentName": "P9069JC0_FE_My_Federal_Return_1120.pdf",
    "FileName": "My_Federal_Return_1120.pdf",
    "Description": "Form 3115 - IRS approved advance consent",
    "JurisdictionAttachmentCode": "FE",
    "Size": "98892",
    "LastModified": "05/13/2021 03:50 PM"
  }
]
```

### Getting Jurisdiction Codes

To retrieve available jurisdiction attachment codes:

**Endpoint:** `GET /beta/efile-jurisdictions-attachment-codes`

**Headers:**
```
authToken: [value from open response]
taxReturnSessionToken: [value from open response]
```

**Response:**
```json
[
  {
    "Name": "Federal",
    "Code": "FE",
    "FileType": []
  },
  {
    "Name": "California",
    "Code": "CA",
    "FileType": [
      "PDF"
    ]
  }
]
```

### Getting Jurisdiction Details

To retrieve detailed information for a specific jurisdiction code:

**Endpoint:** `GET /beta/efile-jurisdiction-attachment-details/{jurisdictionAttachmentCode}`

**Headers:**
```
authToken: [value from open response]
taxReturnSessionToken: [value from open response]
```

**Response:**
```json
{
  "PredefinedDescriptions": [
    {
      "Description": "8453 Signature Document",
      "IsReusable": false
    }
  ],
  "HasAdditionalInfo": true,
  "CanAttachMultiple": true,
  "MustCheckFormName": true,
  "HasFormInfo": true,
  "HasStateRequiredFormNames": true,
  "AdditionalInfos": [
    {
      "Text": "Form and line number to which attachment is linked:",
      "MaxLength": 50
    }
  ],
  "FileTypes": [],
  "Name": "Federal",
  "Code": "FE"
}
```

## Error Handling

The API returns standard HTTP status codes to indicate the success or failure of an API request:

- **200 OK**: The request was successful
- **207 Multi Status**: Some fields were processed successfully, but others had validation errors
- **400 Bad Request**: The request was invalid or malformed
- **401 Unauthorized**: Authentication credentials are missing or invalid
- **403 Forbidden**: The authenticated user does not have access to the requested resource
- **404 Not Found**: The requested resource does not exist
- **429 Too Many Requests**: Rate limit exceeded
- **503 Service Unavailable**: The service is temporarily unavailable

For error responses, the API returns a JSON object with additional details:

```json
{
  "responseType": "Error",
  "statusCode": "400",
  "message": "Error message",
  "fieldErrors": [
    {
      "fieldId": "FIELD_ID",
      "message": "Error message for the field",
      "errorCode": "E1003"
    }
  ]
}
```

## Best Practices

1. **Always close tax returns** after you're done with them using the `/beta/close` endpoint to release resources.

2. **Handle authentication properly**:
   - Store access tokens securely
   - Refresh tokens before they expire
   - Do not share tokens between applications

3. **Use proper error handling**:
   - Check for error responses
   - Implement retry logic for transient errors
   - Log detailed error information for troubleshooting

4. **Optimize field operations**:
   - Batch field operations where possible
   - Only retrieve the fields you need
   - Only save fields that have changed

5. **Manage API rate limits**:
   - Implement backoff strategies for rate limiting
   - Distribute requests evenly over time
   - Cache responses where appropriate

6. **Testing**:
   - Test your integration thoroughly in a test environment before moving to production
   - Create test data that represents real-world scenarios