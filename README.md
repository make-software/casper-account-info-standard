# casper-account-info-standard

## Introduction

The Casper Account Info Standard provides a JSON schema and a mechanism for Casper Network accounts to
self identify and provide additional metadata for their owners, their other resources, services and policies.

## JSON Schema

The [JSON Schema](schema.json) provided in this project is compatible with
[JSON schema Draft 2019-09](https://json-schema.org/specification-links.html#2019-09-formerly-known-as-draft-8)
and can be validated with compatible schema validators and IDEs. 
The provide template [account-info.json](account-info.json) implements the schema and can be validated against 
it using online tools such as [JsonSchemaValidator.net](https://www.jsonschemavalidator.net/s/mesjGb8t).

### Schema Specification

- **`owner_name`** *(string)*: The name of the entity or person that owns the Account.
- **`owner_description`** *(string)*: An optional description of the Owner of the Account.
- **`owner_type`** *(array)*: Indicates the type of entity of the Owner. One Owner can have multiple types.
    - **Items** *(string)*: Must be one of: `['validator', 'exchange', 'dapp', 'individual', 'corporation', 'dao']`.
- **`owner`** *(object)*: More detailed information about the Owner of the Account.
    - **`identity`** *(object)*: Information about the ownership of the Account. 
        - **`ownership_disclosure`**: Link to a page outlining entity ownership and/or governance.
        - **`casper_association_kyc_web`**: Reserved for a link to a Casper Association provided verified identity document, as applicable to Validator members of the Association.
        - **`casper_association_kyc_onchain`** *(string)*: Reserved for Casper Associated on-chain resource that provides for a verified identity.
    - **`resources`** *(object)*: Additional online resources provided by the Account owner. 
        - **`code_of_conduct`**: A link to a Code of Conduct document. 
        - **`terms_of_service`**: A link to the Terms of Service applicable to the use of Owner's services, such as network validation and staking. 
        - **`privacy_policy`**: A link to a Privacy Policy, as may be applicable to personal information provided to the Account Owner. 
        - **`other`** *(array)*: Links to other relevant resources.
            - **Items**: *(string)* URLs
    - **`associated_accounts`** *(array)*: A list of additional accounts owned by the same owner. Declaring additional accounts provides transparency with respect to Exchange-owned wallets, or self-staked funds belonging to Validators. In order for an associated account o be considered valid, it needs to register the same base URL with the Casper Account Info Standard contract as the Owner.
        - **Items**: *(string)* Casper Public Keys
    - **`website`**: A link to the Owner's website.
    - **`email`** *(string)*: E-mail address for the Owner.
    - **`branding`** *(object)*: Graphical assets to visually identify the Owner.
        - **`logo_256`**: Link to Owner logo [PNG format, 256x256].
        - **`logo_1024`**: Link to Owner logo [PNG format, 1024x1024].
        - **`logo_svg`**: Link to Owner logo [SVG format].
    - **`location`** *(object)*: The location of the Owner of the Account.
        - **`name`** *(string)*: Location in human readable format [City, State]
        - **`country`** *(string)*: ISO 3166-1 alpha 2 country code [XX]
        - **`latitude`** *(number)*: Latitude in decimal degrees
        - **`longitude`** *(number)*: Longitude in decimal degrees
    - **`social`** *(object)*: Links to the Owner's social media profiles. 
        - **`keybase`**: username only. 
        - **`telegram`**: username only.
        - **`twitter`**: username only.
        - **`github`**: username only.
        - **`youtube`**: channel address only. 
        - **`facebook`**: Facebook Page or Facebook Group address only, not the full URL.
        - **`medium`**: username only.
        - **`reddit`**: username only.
        - **`wechat`**: username only.
- **`nodes`** *(array)*: List of nodes operated by the Account Owner.
    - **Items** *(object)*
        - **`node_account_address`**: *(string)* Casper Public Key
        - **`node_description`** *(string)*: A description of the use and functionality of this node.
        - **`location`** *(object)*: The location of this node.
          - **`name`** *(string)*: Location in human readable format [City, State]
          - **`country`** *(string)*: ISO 3166-1 alpha 2 country code [XX]
          - **`latitude`** *(number)*: Latitude in decimal degrees
          - **`longitude`** *(number)*: Longitude in decimal degrees
        - **`features`** *(array)*: Reserved to describe the features provided by this node.
            - **Items** *(string)*: Must be one of: `['validator', 'rpc-proxy', 'sse-proxy', 'read-only', 'other']`.

## Usage as an Account Owner

### How does it work?

The Account Owner will cryptographically sign a deploy of a smart contract on the Casper Network, specifying that a specific domain name 
(e.g. https://example.com) is theirs. They will then place a JSON file in a specific location on that domain.
Once done, Block Explorers, dApps and other clients on the Network can simply query the smart contract for the domain name 
by passing the Account's public key. This will allow them to retrieve the metadata file, and display the detailed 
information that the Account Owner provided.
Examples to perform these queries will be provided in a separate repository.

### What do I do?

* create a file called `account-info.json` and provide all required and desired details
* validate your resulting JSON against the `schema.json` file
* following [IETF RFC 8615](https://datatracker.ietf.org/doc/html/rfc8615) proposal for Well-Known URIs, place your
`account-info.json` file in the following location relative to the root of your domain name: 
  `/.well-known/casper/account-info.json`   
* Follow the instructions here [LINK TO FOLLOW]() to link your Account to your domain name. Perform this step for every
one of your `associated_accounts` that you declare in your `account-info.json`.

## Overriding Data for specific networks

An Account Owner may want to provide different information for different networks, for example "main net" vs. "test net". 
To enable this, it's possible to create a network specific JSON file next to the base version. For example, to provide
different date for the `casper-test` network, create a `account-info.casper-test.json` file alongside your `account-info.json` file.
The two files will be merged, and any property in the `casper-test` specific file will override the corresponding 
property in the base version of the file.
