# API Development Guide

The ReaDI-Watch API is a REST based interface centered around JSON-encoded requests and responses. It uses standard HTTP response codes and authentication.

A dedicated test/integration environment is not currently available for the ReaDI-Watch API. If needed, please contact your Customer Success contact to set up a dedicated Account for sandboxed API testing.

Full API Reference[https://app.readi-watch.com/api-docs/reference]

## Authentication

The ReaDI-Watch API uses Bearer Tokens to authenticate requests.

You can generate an API token for your account by visiting the Admin portal, which can be accessed by clicking on the profile picture in the top right corner and clicking Admin. Only Account Administrators have access to this page. Then, open the Integration menu and generate an API token.

IMAGE GOES HERE

Your API keys carry many privileges, so be sure to keep them secure! Do not share your secret API keys in publicly accessible areas such as GitHub, client-side code, and so forth.

## Errors 

ReaDI-Watch uses conventional HTTP response codes to indicate the success or failure of an API request. In general, codes in the `2xx` range indicate success while codes in the `4xx` range indicate an error that failed given the information provided. Codes in the `5xx` range indicate an error with ReaDI-Watch’s servers - these are rare.

Even when a `2xx` code is returned, an error object may be included (often alongside data) to indicate a partial or complete error in the requested action. These types of errors usually relate to an invalid condition in the data requested from the API, and will contain an error message. An example is shown below.

``` json
{
   "error": {
       "code": "INVALID_PARAMETERS",
       "message": "The request contained invalid parameters. Please check your request and try again.. The count requested is greater than the maximum value (100)"
   }
}
```

A `4xx` range code will include a further error status and message code if available, but the body structure of a 4xx range code is not guaranteed to be standardized.

```json
{
   "statusCode": 404,
   "body": {
       "status": "NOT_FOUND",
       "message": "Workflow not found projecs"
   }
}
```

## Versioning

ReaDI-Watch considers the following to be backwards-compatible changes which don’t require a new API version:

- Adding new API resources
- Adding optional request parameters to existing API methods
- Adding new properties to existing API responses
- Changing the order of properties in existing API responses
- Changing the length or format of strings, object IDs, and error messages

In the future, ReaDI-Watch may introduce conventions for introducing breaking API changes; such a convention will itself be backwards compatible. 

## Pagination

If pagination is applicable for an API request (i.e. it returns a list of things), you can manipulate URL parameters `offset` and `count` to return the desired results. In these cases, a pagination object will be returned in the response indicating the offset used in the current API call, the total number of applicable results you could retrieve, and a URL suggestion to use for the next API call. 

```json
   "pagination": {
       "offset": 50,
       "total": 316,
       "next": "https://app.readi-watch.com/api/1.1/wf/projects?count=5&offset=55"
   }
```
   
