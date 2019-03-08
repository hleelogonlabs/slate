---
title: API Reference

language_tabs:
  - { javascript: .js }
  - { php: PHP }
  - { java: Java }
  - { csharp: .NET }

toc_footers:
  - <a href='#'>Sign Up for a Developer Key</a>
  - <a href='https://github.com/lord/slate'>Documentation Powered by Slate</a>

search: true
---

# Introduction

The LogonLabs API is based on REST. The API provides redirection workflows as well as standard JSON-encoded requests and responses.

# API Libraries

We have created a set of API libraries to available in several languages.

### JS

This is a client only library. This library is used to manage the authorization workflows only. 

`Link to repo: <LINK>`

### PHP

This is a client/server library. This library is used to manage the authorization workflows and can also be used to facilitate the authorization callback workflows.

`Link to repo: <LINK>`

### Java

This is a client/server library. This library is used to manage the authorization workflows and can also be used to facilitate the authorization callback workflows.

`Link to repo: <LINK>`

### .NET

This is a client/server library. This library is used to manage the authorization workflows and can also be used to facilitate the authorization callback workflows.

`Link to repo: <LINK>`

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

# Basic Workflows

>Load SDK

```javascript
window.logonAsync = function() {
    Logon.configure({
        app_id: 'YOUR_APP_ID'
    });
};

(function(d, s, id){
    var js, fjs = d.getElementsByTagName(s)[0];
    if (d.getElementById(id)) {return;}
    js = d.createElement(s); js.id = id;
    js.src = "https://logon-javascript-sdk.herokuapp.com/dist/logon.min.js";
    fjs.parentNode.insertBefore(js, fjs);
}(document, 'script', 'logon-js'));


```
```php
require(PATH_TO_SDK . 'vendor/autoload.php');
use Logon\Api\Client as Logon;

Logon::initialize(array(
   'app_id' => 'YOUR_APP_ID',
   'auto_redirect' => true //Boolean
));
```

Describe the basic workflow

- Call Authorize providing the following information

    - app_id → the identifier used by LogonLabs to correctly identify your application
    
    - identify_provider → the identity provider that the user is redirected to for authentication.
    
    - email_address → when provided it hints the email to Identity Provider so the user does not need to enter their email again. It also restricts this logon attempt to this email address.
    
    - client_data → this is a dynamic object that can be used by the client to send pass through data at the start of the authorization workflow that will be passed to the callback at the end of the workflow.
    
- LogonLabs proceeds to redirect the user to the desired identity provider and brokers the identity confirmation process

- Upon completion, the User is redirected back to the URI (configured in your app settings) with a token to securely retrieve the authentication information

- The Client now calls GetResponseData with the provided token and the result of identity confirmation process is returned. You now have confirmed the users identity and proceed with your applications workflow (ie, assigning the user session, etc).

<aside class="notice">
You -> LogonLabs brokers logon with identity provider -> You
</aside>

# Authentication

The LogonLabs API acts a broker for identity providers. As a result, there is no authentication required. To initiate a logon request you just need to post to our authorization interface with the required information.

For a subset of the API calls, like AuditLogin, you need to pass your AppId and a secret for authentication. These secrets are managed in the LogonLabs app settings in the LogonLabs portal.

<aside class="success">
You must replace <code>YOUR_APP_ID</code> with your personal APP ID.
</aside>

# Authorization API


## Ping

```javascript
Logon.ping(callback);
```

```php
Logon::ping();
```

Ping is a basic API to check that you have the correct URL and to confirm the current version of the LogonLabs Authorization API.

## Authorize


```javascript
Logon.authorize(Logon.idp.GOOGLE, 'consumer@example.com');
```

```php
Logon::authorize(Logon::GOOGLE, 'consumer@example.com');
```

>Identity Providers

```javascript
Logon.idp.GOOGLE
Logon.idp.O365
Logon.idp.FACEBOOK
Logon.idp.LINKEDIN
Logon.idp.OKTA
```

```php
Logon::GOOGLE
Logon::O365
Logon::FACEBOOK
Logon::LINKEDIN
Logon::OKTA
```

Authorize is a redirection interface that can be called as either a POST or a GET depending on your requirements. Once called it will validate the parameters and redirect the user user to the identity provider specified. After confirmation from the identity provider, we complete custom validations based on your app settings. Once that is completed we redirect back to the URI for your app with a token to securely retrieve the ResponseData.


Parameter | Type | Description
--------- | ------- | -----------
app_id | string | Unique identifier for your application. Provided by LogonLabs.
identity_provider | enum (office365, google, facebook, linkedin, okta) | The identity provider that the user is redirected to for authentication.
email_address | string (optional) | When provided it hints the email to Identity Provider so the user does not need to enter their email again. It also restricts this logon attempt to this email address.
client_data | string (JSON object) | This is a dynamic object that can be used by the client to send pass through data at the start of the authorization workflow that will be passed to the callback at the end of the workflow.

## GetAuthorizationData

```php
Logon::getAuthorizationData(TOKEN)
```

This call allows your back end code to retrieve all the logon information. This includes information from the identity provider as well as LogonLabs specific validation information.

### REQUEST

Parameter | Type | Description
--------- | ------- | -----------
token | string | The unique identifier provided by LogonLabs to retrieve the response data package.

### RESPONSE
Parameter | Type | Description
--------- | ------- | -----------
app_id | string | Unique identifier for your application. Provided by LogonLabs.
email_address | string | Email address of the user authorized by the Identity Provider.
ip_address | string | IP of the user authorized by the Identity Provider.
country_code | enum (ISO country code) | Country of the user authorized by the Identity Provider.
validation_success | boolean | The unique identifier provided by LogonLabs to retrieve the response data package.
validation_details | Validations (object) | This object contains all validations on the Logon. This includes both authorization from the Identity Provider as well as other validations performed by LogonLabs. Details are provided below.
provider_data | ProviderData (object) | This object contains the data that was returned from the Identity Provider authorization.
client_data | string | This is a dynamic object that can be used by the client to send pass through data at the start of the authorization workflow that will be passed to the callback at the end of the workflow.

### ProviderData

Parameter | Type | Description
--------- | ------- | -----------
identity_provider | enum (office365, google, facebook, linkedin, okta) | The identity provider that the user is redirected to for authentication.
uid | string | This is a unique identifier created for the user by the Identity Provider.
first_name | string | The first name of the user provided by the Identity Provider.
last_name | string | The last name of the user provided by the Identity Provider.

### Validations

Parameter | Type | Description
--------- | ------- | -----------
provider_auth | boolean | The identity provider that the user is redirected to for authenThis validation confirmed that the Identity Provider has authorized the user.
email_mismatch | boolean | This validation confirms that the optional email_address passed in the initial Authentication request was the user that was authorized by the Identity Provider.
ip_valid | boolean | This validation confirms that the IP address of the user passes IP filters based on the app settings (You can manage these settings the LogonLabs app settings page in the LogonLabs portal.)
geo_valid | boolean | This validation confirms that the Geo fence address of the user passes Geo fence filters based on the app settings (You can manage these settings the LogonLabs app settings page in the LogonLabs portal.)


## AuditLogon

This API call provides the client the ability to validate and audit logons based on your existing local password workflow. When confirming a users credentials you can use this API to validate the user based on your LogonLabs app settings and also add a record in your LogonLabs audit logs. This allows for a complete reporting on all the logons within your application.
  
Authentication for this API is based on the AppId and secret created under your app in the LogonLabs settings page.

### REQUEST

For this request you need to pass the app_id and secret in the header

Parameter | Type | Description
--------- | ------- | -----------
email_address | string | The email address of the user that is attempting to log in locally.
ip_address | string | The ip_address of the user that is attempting to log in locally.

### RESPONSE

Parameter | Type | Description
--------- | ------- | -----------
country_code | enum (ISO country code) | Country of the user authorized by the Identity Provider.
validation_success | boolean | This value lets you know if the Logon attempt was success. It represents a combination of the Identity Provider authorization and LogonLabs custom validations.
validation_details | Validations (object) | This object contains all validations on the Logon. This includes both authorization from the Identity Provider as well as other validations performed by LogonLabs. Details are provided below.