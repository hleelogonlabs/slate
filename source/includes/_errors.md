# Error Handling

```json
{
  "error":{
    "message":"The API key provided is invalid.",
    "code":"invalid_api_key"
  }
}
```

LogonLabs uses HTTP response codes to indicate the success or failure of a request. Error codes in the 200 range indicate success. Error codes in the 400 range indicate an error based on the provided parameters. Codes in the 500 range indicate an error with the LogonLabs servers.


##HTTP Status Descriptions

 Code | Meaning
---------- | -------
200 - OK | All good
400 - Bad Request | Incorrect request. Missing incorrect parameter.
~~401 - Unauthorized~~ | 
402 - Request Failed |
404 - Not Found | Could not find requested resource.

<aside class="warning">
401 Not required for IDPX interface
</aside>

## Erros

Code | Meaning
---------- | -------
invalid_auth_token | Auth token passed is not valid
... | ...