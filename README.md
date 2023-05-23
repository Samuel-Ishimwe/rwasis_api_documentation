> **Warning**
> **The RWASIS API is under development.** This documentation is subjected to change; We recommend readers to visit the documentation page more frequently.

# RWASIS  API documentation

This repository contains the documentation for [RWASIS](https://rwasis.rab.gov.rw)’s API.

#### Contents

- [Overview](#1-overview)
- [Authentication](#2-authentication)
  - [Registration-and-access](#21-registration-and-access)
  - [Access tokens](#22-access-tokens)
- [Resources](#3-resources)
  - [Erosion](#31-erosion)
  - [Fertilizer](#32-fertilizer)
  - [Crop type](#33-crop-type)
- [Testing](#4-testing)

## 1. Overview

This API documentation will provide exhaustive information about Soil, and crop information, erosion occurrence, as well as fertilizer and lime recommendations for planting and wedding such that evidence based decisions can be made to implement policies for improving agricultural productivity and land use. 

The API will follow REST architecture. The data type to be exchanged will be in application/json format while the access token will follow OAuth 2.0 with a token bear that will be generated from authentication process.

All requests are made to endpoints beginning:
`https://rwasis.rab.gov.rw/v1/api`


## 2. Authentication

In order to get access to the RWASIS resources, you will need an access token. An access token grants access to the services and information exposed by RWASIS to 3rd parties integrators. 

### 2.1. Registration and access 

For using RWASIS  API, your organization must have an agreement with RAB who is the owner of the RWASIS. For more information contact `eric.nsabimana@rab.gov.rw`.

To use this API you must have valid credentials (email and passowrd) from the admin of the system.

###  2.2. Access tokens

Access tokens are explicitly obtained using the authentication process. We assume that user is already registered in the system and have obtained the credentials(email and password) from the system admin administrator.


```
POST /v1/api/login HTTP/1.1
Host: rwasis.rab.gov.rw
Content-Type: application/x-www-form-urlencoded
Accept: application/json
Accept-Charset: utf-8

email={{email}}&password={{password}}
```


With the following parameters:

| Parameter       | Type     | Required?  | Description                                     |
| -------------   |----------|------------|-------------------------------------------------|
| `email`         | string   | required   | The email that is registered on you that identifies your integration. |
| `password`      | string   | required   | The password that you are given during registration process |


If successful, you will receive back an access token response:

```
HTTP/1.1 201 OK
Content-Type: application/json; charset=utf-8

{
  "token_type": "Bearer",
  "access_token": {{access_token}},
  "refresh_token": {{refresh_token}},
  "expires_at": {{expires_at}}
}
```

With the following parameters:

| Parameter       | Type         | Required?  | Description                                     |
| -------------   |--------------|------------|-------------------------------------------------|
| `token_type`    | string       | required   | The literal string "Bearer"                     |
| `access_token`  | string       | required   | A token that is valid for 7 days and may be used to perform authenticated requests on behalf of the user. |
| `refresh_token` | string       | required   | A token that does not expire which may be used to acquire a new `access_token`.                            |
| `expires_at`    | int64        | required   | The timestamp in unix time when the access token will expire |

Each access token is valid for 7 days. When an access token expires, you may request a new token using the refresh token. Refresh tokens do not expire. Both access tokens and refresh tokens may be revoked by the user at any time. **You must treat both access tokens and refresh tokens like passwords and store them securely.**

Both access tokens and refresh tokens are consecutive strings of hex digits, like this:

```
181d415f34379af07b2c11d144dfbe35d
```

If NOT successful, it means that your credentials are not valid. Either the password does not match the email or your account is not yet registed. You will receive response containing error message:

```
HTTP/1.1 401 Unauthorized
Content-Type: application/json; charset=utf-8

{
  "message": "invalid credentials",
}
```

To acquire a new access token using a refresh token, make the following form-encoded request:

```
POST /v1/tokens HTTP/1.1
Host: rwasis.rab.gov.rw
Content-Type: application/x-www-form-urlencoded
Accept: application/json
Accept-Charset: utf-8

refresh_token={{refresh_token}}
```

With the following parameters:

| Parameter       | Type     | Required?  | Description                                     |
| -------------   |----------|------------|-------------------------------------------------|
| `refresh_token` | string   | required   | A valid refresh token.                          |


## 3. Resources

The API is RESTful and arranged around resources. All requests must be made with an access token. All requests must be made using `https`.

### 3.1. Erosion

#### Getting the authenticated user’s details
Returns details of the erosion on farmer's land on specific location provided by land UPI coordinate (Point geographically represented by X{latitude},Y{longitude}).

```
GET Accept: application/json Authorization: Bearer `accessToken` https://rwasis.rab.gov.rw/v1/api/erosion?latitude={{X}}&longitude={{Y}}
```

Example request:

```
GET /v1/api/erosion HTTP/1.1
Host: rwasis.rab.gov.rw
Authorization: Bearer 181d415f34379af07b2c11d144dfbe35d
Content-Type: application/json
Accept: application/json
Accept-Charset: utf-8
```

The response contians the updated information about the land.

Example response:

```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
  "data": {
    "risk": "HIGH",
    "landcover": "Bare Land",
    "recommended": "Agroforest",
    "coordinates": [43434, 64366]
    "province":"North",
    "district":"Gakenke",
    "sector":"Rusasa",
    "cell":"Nyundo",
    "village":"kabuye",
  }
}
```

Where a erosion object is:

| Field         | Type   | Description                                     |
| --------------|--------|-------------------------------------------------|
| risk          | string | Erosion risk at the provided location.          |
| landcover     | string | Landcover at the provided location.             |
| recommended   | string | Recommended risk at the provided location.      |
| coordinates   | string | Coordinates risk at the provided location.      |
| province      | string | Province of the provided location.              |
| district      | string | District of the provided location.              |
| sector        | string | Sector of the provided location.                |
| cell          | string | Cell of the provided location.                  |
| village       | string | Village of the provided location.               |

Possible errors:

| Error code           | Description                                     |
| ---------------------|-------------------------------------------------|
| 401 Unauthorized     | The `accessToken` is invalid or has been revoked. |


### 3.2. Fertilizer

#### Getting the lime and fertilizer recommendation

Returns the recommendation of the erosion on farmer's land on specific location provided by land UPI coordinate (Point geographically represented by X{latitude},Y{longitude}).

```
GET Accept: application/json Authorization: Bearer `accessToken` https://rwasis.rab.gov.rw/v1/api/fertilizer/?latitude={{X}}&longitude={{Y}}
```

Example response:

```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
  "data": {
    "fertilizer_type": "Uree",
    "fertilizer_quantity": "23.44",
    "lime_type": "calitic",
    "lime_quantity": "234",
    "investment_cost":"123",
    "coordinates": [43434, 64366]
    "province":"North",
    "district":"Gakenke",
    "sector":"Rusasa",
    "cell":"Nyundo",
    "village":"kabuye",
  }
}
```

Where lime/fertilizer recommendation data is:

| Field                   | Type   | Description                                                                |
| ------------------------|--------|----------------------------------------------------------------------------|
| fertilizer_type         | string | Type of fertilizer recommended at the provided location.                   |
| fertilizer_quantity     | string | Quantity of fertilizer recommended at the provided location in kg per Are. |
| lime_type               | string | Type of lime recommended at the provided location.                         |
| lime_quantity           | string | Quantity of lime recommended at the provided location in kg per Are.       |
| coordinates             | string | Coordinates risk at the provided location.                                 |
| province                | string | Province of the provided location.                                         |
| district                | string | District of the provided location.                                         |
| sector                  | string | Sector of the provided location.                                           |
| cell                    | string | Cell of the provided location.                                             |
| village                 | string | Village of the provided location.                                          |

Possible errors:

| Error code           | Description                                                                           |
| ---------------------|---------------------------------------------------------------------------------------|
| 401 Unauthorized     | The `accessToken` is invalid                                                          |

### 3.3. Crop type

#### Getting the crop type recommendation

Returns the recommendation of the crop tyoe on farmer's land on specific location. The crop type recommendation is provided by RAB researcher.

```
GET Accept: application/json Authorization: Bearer `accessToken` https://rwasis.rab.gov.rw/v1/api/crop/?latitude={{X}}&longitude={{Y}}
```

Example response:

```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
  "data": {
    "crop_type": "potatoes",
    "coordinates": [43434, 64366]
    "province":"North",
    "district":"Gakenke",
    "sector":"Rusasa",
    "cell":"Nyundo",
    "village":"kabuye",
  }
}
```

Where crop recommendation data is:

| Field                   | Type   | Description                                                                |
| ------------------------|--------|----------------------------------------------------------------------------|
| crop_type               | string | Type of crop recommended at the provided location.                         |
| coordinates             | string | Coordinates risk at the provided location.                                 |
| province                | string | Province of the provided location.                                         |
| district                | string | District of the provided location.                                         |
| sector                  | string | Sector of the provided location.                                           |
| cell                    | string | Cell of the provided location.                                             |
| village                 | string | Village of the provided location.                                          |

Possible errors:

| Error code           | Description                                                                           |
| ---------------------|---------------------------------------------------------------------------------------|
| 401 Unauthorized     | The `accessToken` is invalid                                                          |




## 4. Testing

We do not have a sandbox environment yet. We will update this sections after deploying the API.

