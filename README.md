# Documentation on how to use the Savvy SNBL
To use the Savvy SNBL, you have to first get access to your access key and secret key. Request your SPOC for these credentials. Staging credentials will be given first, and then production credentials will be issued once a round of testing has been performed. The API urls will also be shared via your SPOC. 

## SNBL integration types
1) [SNBL for tech partners](#SNBL-for-tech-partners)
2) [SNBL for non-tech partners](#SNBL-for-non-tech-partners)

## SNBL for tech partners
This option is for partners with technical expertise, this requires server to server integration to generate your user and store it in your backend for later use, if you are looking for non-tech integration check out [SNBL for non-tech partners](#SNBL-for-non-tech-partners)

The secret key must be kept secret. Please make sure this key is not on the client side, or anywhere in your database or permanent storage. It must be kept in your live environment as a env. variable or use other similar key storage mechanisms like AWS KMS. Leakage of your secret key could compromise all your users and could lead to very bad things.

### User creation
Every request from the partner to our endpoint must be authenticated using your access key (which identifies the partner making the request) and an IDENTITY_TOKEN (which identifies the user making the request). A user must be created via a server-to-server call using your access and secret key. In return, an expiring token is passed back. Please store this token SAFELY, preferably in your database or other similar storage mechanism and pass it in our url via query params.

**User create call**

**POST** ($BASE_URL)**/partners/users**

Headers:
| name | type | example |
| ---- | ---- |:-------:|
| X-PARTNER-ACCESS-KEY | string | abcde |
| X-PARTNER-SECRET-KEY | string | xyzab |

Params (root key must be user: `user: { phone_number.... }`):
| name | type | example |
| ---- | ---- |:-------:|
| phone_number | string | 9876543210 OR +919876543210 |
| email | string | test@example.com |
| first_name | string | Foo |
| last_name | string | Bar |

_Response:_

Headers:
| name | type | example |
| ---- | ---- |:-------:|
| X-USER-IDENTITY-TOKEN | string | abcd.efg.hijk |
| Content-Type | string | application/json |

Body:
| name | type | example |
| ---- | ---- |:-------:|
| uuid | string | abcd-efg-hijk-xyz |

The identity token expires every 24 hours, so please make sure to have a refresh mechanism built in.

**Token refresh call**

**POST** ($BASE_URL)**/partners/users/new_token**

Headers:
| name | type | example |
| ---- | ---- |:-------:|
| X-PARTNER-ACCESS-KEY | string | abcde |
| X-PARTNER-SECRET-KEY | string | xyzab |

Params (root key must be user: `user: { uuid.... }`):
| name | type | example |
| ---- | ---- |:-------:|
| uuid | string | abcd-efg-rf-rrrr |

_Response:_

Headers:
| name | type | example |
| ---- | ---- |:-------:|
| X-USER-IDENTITY-TOKEN | string | abcd.efg.hijk |
| Content-Type | string | application/json |

Body:
| name | type | example |
| ---- | ---- |:-------:|
| uuid | string | abcd-efg-hijk-xyz |

### Usage
**STAGING ENDPOINT**: https://partners.thesavvyapp.in

**PRODUCTION ENDPOINT**: https://partners.savvyapp.in

From your application redirect to **https://partners.{{ENVIRONMENT}}.in** along with **partner_access_key**, **user_identity_token**, **uuid** and **product_id** as query params.

**Example:** `https://{{ENVIRONMENT}}.savvyapp.in?partner_access_key=xxxx&user_identity_token=xxxxx&uuid=xxxxx&product_id=xxxx`

| param | example | mandatory| description |
| ---- | ---- | -- |:-------:|
| partner_access_key | abcd-efg-hijk-xyz | Yes | Will be provided by SPOC |
| user_identity_token | abcd-efg-hijk-xyz | Yes | Should be generate using server to server api call |
| uuid | abcd-efg-hijk-xyz | Yes | Should be generated using server to server api call |
| product_id | xxxxx | Yes | Will be provided by SPOC for your internal product |
| other data | reference=xxxx&.... | Optional | Any other additional params which you need to get it back as the callback params after the SNBL fulfllment |

### Fullfillment
Once your user is redirected to our SNBL web app users will be able to view the product details, choose payment method, pay the upfront payment (if any), complete their kyc and set up the auto debit payment. Upon successfully completion of the flow users will be redirected to the url which will be provided by you to our SPOC during onboarding.

**Example Callback redirection:**
`https://yourwebsite.com?status={{status}}&message={{message}}&saving_goal_id={{saving_goal_id}}&product_id={{product_id}}`

| param | description 
| ---- |:-------:|
| status | Status of the fulfillment **(success/error)** |
| messsage | Explanatory message for success or error  |
| saving_goal_id | unique id for the goal created in our system |
| product_id | product catalog id stored in our system |

## SNBL for non-tech partners

This option is for partners whom require minimal to no technical integration, if you are looking for advanced technical integration please go to [SNBL for tech partners](#SNBL-for-tech-partners)

### Usage
**STAGING ENDPOINT**: https://partners.thesavvyapp.in
**PRODUCTION ENDPOINT**: https://partners.savvyapp.in

From your application redirect to **https://partners.{{ENVIRONMENT}}.in** along with **partner_access_key** and **product_id** as query params.

**Example:** `https://partners.{{ENVIRONMENT}}.in?partner_access_key=xxxx&product_id=xxxx`

| param | example | mandatory| description |
| ---- | ---- | -- |:-------:|
| partner_access_key | abcd-efg-hijk-xyz | Yes | Will be provided by SPOC |
| product_id | xxxxx | Yes | Will be provided by SPOC for your internal product |

### Fullfillment
Once your user is redirected to our SNBL web app users will be able to view the product details, choose payment method, pay the upfront payment (if any), complete their kyc and set up the auto debit payment. Upon successfully completion of the flow users will be redirected to the url which will be provided by you to our SPOC during onboarding.

**Example Callback redirection:**
`https://yourwebsite.com?status={{status}}&message={{message}}&saving_goal_id={{saving_goal_id}}&product_id={{product_id}}`

| param | description 
| ---- |:-------:|
| status | Status of the fulfillment **(success/error)** |
| messsage | Explanatory message for success or error  |
| saving_goal_id | unique id for the goal created in our system |
| product_id | product catalog id stored in our system |
