# casper-account-info-standard

### Table of Contents

- [Introduction](#introduction)
- [JSON Schema](#json-schema)
  - [Schema Specification](#schema-specification)
- [Usage as an Account Owner](#usage-as-an-account-owner)
  - [How does it work?](#how-does-it-work)
  - [What do I do?](#what-do-i-do)

## Introduction

The Casper Account Info Standard provides a JSON schema and a mechanism for Casper Network accounts to
self identify and provide additional metadata for their owners, their other resources, services and policies.

## JSON Schema

The [JSON Schema](schema.json) provided in this project is compatible with
[JSON schema Draft 2019-09](https://json-schema.org/specification-links.html#2019-09-formerly-known-as-draft-8)
and can be validated with compatible schema validators and IDEs. 
The provided template [account-info.json](account-info.json) implements the schema and can be validated against 
it using online tools such as [JsonSchemaValidator.net](https://www.jsonschemavalidator.net/s/2tXkGGvK).

### Schema Specification

- **`owner`** *(object)*: More detailed information about the Owner of the Account.
    - **`name`** *(string)*: The name of the entity or person that owns the Account.
    - **`description`** *(string)*: An optional description of the Owner of the Account.
    - **`type`** *(array)*: Indicates the type of entity of the owner. One Owner can have multiple types.
        - **Items** *(string)*: Must be one of: `['validator', 'exchange', 'dapp', 'individual', 'corporation', 'dao']`.
    - **`identity`** *(object)*: Information about the ownership of the Account.
        - **`ownership_disclosure_url`**: Link to a page outlining entity ownership and/or governance. 
        - **`casper_association_kyc_url`**: Reserved for a link to a Casper Association provided verified identity document, as applicable to Validator members of the Association.
        - **`casper_association_kyc_onchain`** *(string)*: Reserved for Casper Associated on-chain resource that provides for a verified identity.
    - **`resources`** *(object)*: Additional online resources provided by the Account owner.
        - **`code_of_conduct_url`**: A link to a Code of Conduct document.
        - **`terms_of_service_url`**: A link to the Terms of Service applicable to the use of Owner's services, such as network validation and staking.
        - **`privacy_policy_url`**: A link to a Privacy Policy, as may be applicable to personal information provided to the Account Owner.
        - **`other`** *(array)*: Links to other relevant resources.
            - **Items** *(object)*: Additional URL based resources
                - **`name`** *(string)*: The name of the resource, e.g. "About Us" or "Careers"
                - **`url`** *(string)*: The URL to the resource
    - **`affiliated_accounts`** *(array)*: A list of additional accounts owned by the same owner. Declaring additional accounts provides transparency with respect to Exchange-owned wallets, or self-staked funds belonging to Validators. In order for an associated account o be considered valid, it needs to register the same base URL with the Casper Account Info Standard contract as the Owner.
        - **Items** *(object)*: 
            - **`public_key`** *string*: the public key (hexedecimal representation) of the associated account
    - **`website`**: A link to the Owner's website.
    - **`email`** *(string)*: E-mail address for the Owner.
    - **`branding`** *(object)*: Owner Branding.
        - **`logo`** *(object)*: Graphical assets to visually identify the Owner.
            - **`png_256`**: Link to Owner logo [PNG format, 256x256]. 
            - **`png_1024`**: Link to Owner logo [PNG format, 1024x1024].
            - **`svg`**: Link to Owner logo [SVG format].
    - **`location`** *(object)*: The location of the Owner of the Account.
        - **`name`** *(string)*: Free-form location in human-readable format [for example: City, State]
        - **`country`** *(string)*: ISO 3166-1 alpha 2 country code [XX]
        - **`latitude`** *(number)*: Latitude in decimal degrees
        - **`longitude`** *(number)*: Longitude in decimal degrees
    - **`social`** *(object)*: Links to the Owner's social media profiles.
        - **`keybase`**: username only, e.g. 'john'.
        - **`telegram`**: username only, e.g. '@john'. 
        - **`twitter`**: username only, e.g. '@john'.
        - **`github`**: username only, e.g. 'john'. 
        - **`youtube`**: channel address only, e.g. 'john'. 
        - **`facebook`**: Facebook Page or Facebook Group address only, not the full URL, e.g. 'john'.
        - **`medium`**: username only, e.g. '@john'. 
        - **`reddit`**: username only, e.g. 'r/john' or 'u/john'. 
        - **`wechat`**: WeChat ID only, e.g. 'john'. 
- **`nodes`** *(array)*: List of nodes operated by the Account Owner.
    - **Items** *(object)*: 
        - **`public_key`** *(string)*: Casper Public Key (hexadecimal representation)
        - **`description`** *(string)*: A description of the use and functionality of this node.
        - **`location`** *object*: The location of this node.
          - **`name`** *(string)*: Free-form location in human-readable format [for example: City, State]
          - **`country`** *(string)*: ISO 3166-1 alpha 2 country code [XX]
          - **`latitude`** *(number)*: Latitude in decimal degrees
          - **`longitude`** *(number)*: Longitude in decimal degrees         
        - **`functionality`** *(array)*: Reserved to describe the functionality provided by this node.
            - **Items** *(string)*: Must be one of: `['validator', 'rpc-proxy', 'sse-proxy', 'read-only', 'other']`.

## Usage as an Account Owner

### How does it work?

The Account Owner will cryptographically sign a deploy of a smart contract on the Casper Network, specifying that a specific domain name 
(e.g. https://example.com) is theirs. They will then place a JSON file in a specific location on that domain.
Once done, Block Explorers, dApps, and other clients on the Network can simply query the smart contract for the domain name 
by passing the Account's public key. This will allow them to retrieve the metadata file and display the detailed 
information that the Account Owner provided.
Examples to perform these queries will be provided in a separate repository.

### What do I do?

* create a file called `account-info.<chainspec-name>.json`, where `<chainspec-name>` represents the network the data file
  is intended for. For example, for Casper Mainnet, create: `account-info.casper.json` whereas for Casper Testnet, create
  `account-info.casper-test.json`. 
* Provide all required and desired details
* validate your resulting JSON against the `schema.json` file
* following [IETF RFC 8615](https://datatracker.ietf.org/doc/html/rfc8615) proposal for Well-Known URIs, place your
file in the following directory relative to the root of your domain name: `/.well-known/casper/`. In the Casper Mainnet example,
  your file would thus be available at `https://yourdomain.com/.well-known/casper/account-info.casper.json`
* Follow the instructions here [LINK TO FOLLOW]() to link your Account to your domain name. Perform this step for every
one of your `affiliated_accounts` that you declare in your `account-info.json`.
