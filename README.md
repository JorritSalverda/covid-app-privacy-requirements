# COVID-19 tracking app privacy requirements

With plans by multiple goverments to start tracking their population's whereabouts to alert and quarantine people that have been in proximity of a confirmed COVID-19 case it's extremely important to think about requirements to safeguard privacy of the users of such an app, to prevent any country from turning into a totalitarian state.

## Technical requirements

### General

* Access to any sensitive data is only allowed through applications that have a form of role-based access control (RBAC) and create an audit trail of whom accessed what data at what time.
* No single person should be allowed to gain access to the data directly, circumventing the applications mentioned above.
* The data is exclusively to be used for tracking people that have come into contact with a COVID-19 positive person. Governments have a habit of combining data for completely other purposes, this should be disallowed.

### Source code

* The code of the mobile applications, API and other components that are part of the platform and come in contact with sensitive data have to be open sourced so they can be peer reviewed.
* The open sourced code repository have to tag commits to indicate which revision of code is live.

### Mobile app

The mobile app is the part of the system that will be installed on a countries inhabitants and visitors phones. This will use a phones location API to track the whereabouts of the phone (and through that the user of the phone). To make the entire system scalable this application will likely send location information to a centralized server in batches. Until a batch of data is shipped this information lives on the phone, leading to a number of requirements to make sure this data can't be accessed by other applications and users of the phone and is securely shipped to the server.

* Authentication: will the application be linked to a social security number? This will not work for visitors of the country. And makes any stored data all the more sensitive. Should not be linked to any non-public identity provider like Google or Facebook though
* Communicate with API over https, validating host and validity of the API's SSL certificate
* Store location data on device until the batch of data is sent to the API and confirmed to be received. The duration of how long historical location data is stored depends on how frequent batches are sent to the API and whether the API can be reached at a given time.
* Application debug logs cannot contain location data nor other user data that can be used to identify the person. If location information is needed to debug an issue it has to be 'obfuscated' by lowering the precision of the location (for example by applying a 1km x 1km grid and using the grid cell location instead).

### API / data store

The system receiving all app data and applying logic to notify other app users that they've been in proximity of a confirmed case and have to self-quarantine will likely by formed by many separate applications, but in this document will be treated as a single entity.

* Given the incubation time of the SARS-Cov-2 virus and time until someone is likely to be confirmed to be COVID-19 positive a data retention of no more than 6 weeks seems more than enough to cover all use cases.
* Sensitive data in any data store has to be encrypted, only allowed to be decrypted by applications interfacing with that data; not just personal details of a user, also the location information, since it can be easily used to pinpoint where a person lives it needs to be treated as personal identifiable information (PII)
* Application debug logs: see _mobile app_; Do not store exact ip address of the mobile phone app, but mask it to lower the precision.

### Auditing

To ensure the safeguards of the system can be evaluated all interactions with sensitive data have to be logged and made insightful.

### Data exchange

To allow foreign travel it's likely that other goverments want to be able to gain access to the data. However this should not be allowed. Instead an anonymous (standardised) data format has to be defined to notify other countries of visits by a person confirmed to be COVID-19 positive, so that country can track who came into contact with this person and quarantine those persons, without ever knowing the identity of the foreign visitor.

For studying the spread of the virus this same (or similar) anonymous data exchange format can be used by epidemiologist, so they don't have access to any sensitive information.
