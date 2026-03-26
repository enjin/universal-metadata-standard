# Off-Chain Metadata Standard

- **Authors:** [Brad Bayliss](mailto:brad@bayliss.co.uk)
- **Status:** Final
- **Created:** 2025-10-13

## Abstract

This document defines the structure of a Metadata Resource File which is used to represent the details of a token. It also describes how an application will interpret the Metadata Resource File by defining how it's parsed and rendered.

## Motivation

The Metaverse is a network of linked virtual worlds where you can take your avatar, friends, and digital items anywhere you go. Because these worlds run on open blockchains, you own your identity and belongings, can trade or build with anyone, and help set the rules together, letting people connect and create on equal terms across the globe.

The issue that currently plagues both token creators and application developers is the way in which the Metadata Resource File is structured. A lack of a well-defined standard means that token creators are left to interpret how these files should be formatted, and this is often done by referencing existing documentation materials or existing tokens. The lack of a standard also leaves application developers struggling to natively support all tokens, which leads to complications towards working to the goal of _The Metaverse_. This standard details the specifics required to ensure universal compatibility for all applications, and so that token creators can be reassured their tokens will be presented to the end-user in a manner that they expect irrespective of the device, browser, or application used to view the tokens.

This standard also focuses on allowing **everyone** to partake in _The Metaverse_ by ensuring that accessibility and internationalisation are equally well defined from the very start, something current attempts have failed to achieve.

Finally, the concept of _The Metaverse_ is one that changes day-by-day. It is for that reason that this standard is upgradeable to allow for the standard to periodically be upgraded to meet the everchanging demands of _The Metaverse_. This is achieved through incrementing the `meta.version` property, defined later herein.

## Terminology

* `Resource` refers to a Metadata Resource File.
* `Client` refers to an application responsible for the parsing and rendering of a Resource.

## Specification

The key words “MUST”, “MUST NOT”, “REQUIRED”, “SHALL”, “SHALL NOT”, “SHOULD”, “SHOULD NOT”, “RECOMMENDED”, “MAY”, and “OPTIONAL” in this document are to be interpreted as described in [RFC 2119](https://datatracker.ietf.org/doc/html/rfc2119).

### Metadata Format

All Resources MUST consist solely of a valid JSON payload, as defined in [RFC 8259](https://datatracker.ietf.org/doc/html/rfc8259).

### Metadata Structure

`name: String` This display name associated with this token.
* Resource MUST supply this property.
* Client MUST display this property and reference the token as such.

`description: String` An optional description used to describe this token.
* Resource MAY contain this property as an aid to describe this token.
* Client MAY display this property.

`external_url: URL` An optional, external URL, that is related to this token in an official capacity.
* Resource MAY supply this property to hint at an external URL related to this token.
* Client MAY provide a link to this URL.

`keywords: Array<String>` An array of unique keywords to describe this token.
* Resource MAY contain this property to help Clients with search functionality better locate their token. It is RECOMMENDED to keep the number of unique keywords to no mare than 20 as Clients are solely responsible for whether keywords may be used and how many are referenced.
* Client MAY use this property to determine the most appropriate match for a given search term.

`fallback_image: URL` An image that the Client should respect in the event it is unable to load another media resource, this is in favour of a generic 'broken image' that the Client may otherwise have displayed.
* Resource MAY contain this property. The value MUST correspond to an `image/*` type and is RECOMMENDED to be either `image/bmp`, `image/jpeg` or `image/png` to ensure compatibility with all Clients.
* Client SHOULD display this image only as a fallback in the event it fails to load the requested media file(s) and/or if all media file(s) are that of an unsupported type. Client MAY support animated image formats.

`banner_image: URL` An image that the Client may use to render a banner-like image (4:1 ratio).
* Resource MAY contain this property. The value MUST correspond to an `image/*` type and is RECOMMENDED to be either `image/bmp`, `image/jpeg` or `image/png` to ensure compatibility with all Clients.
* Client MAY display the image. Client MAY support animated image formats.

`media: Array<Object>` An array of objects containing media (image; video; audio; and/or model) files that are used to represent this token. The order of this array is important as the Client should present any supported media resources in that order. 
* Resource SHOULD provide a list of media resources.
* Client SHOULD display at least one media resource. The Client MAY ignore media types that it is unable to support. The Client SHOULD present media in the order they're listed within the array.

`media[n].url: URL` The URL of the media resource.
* Resource MUST contain this property.
* Client MUST use this property to retrieve the media associated with this token.

`media[n].type: MediaType` A valid IANA Media Type used to indicate the type of media resource.
* Resource SHOULD supply the corresponding IANA Media Type associated with the media supplied within the `media[n].url` property.
* Client MAY use this property to filter media unsupported by the Client or to determine how best to render the media.

`media[n].alt: String` Alternative text used to describe the media supplied within the `media[n].url` property.
* Resource MAY supply a short string that describes the media file. It is RECOMMENDED that this property is specified to improve accessibility.
* Client SHOULD use this property as a text alternative to the media file.

`attributes: Object<String,Object<String,String|Float|Integer|Boolean|Array|null>>` An object containing custom attributes that may be interpreted by the Client.
* Resource MAY provide a list of attributes that can be optionally displayed by the Client and is a simple key/value pair. The value MAY be an object that itself MUST contain a key named `value`, additional data may be contained within this object.
* Client MAY parse the attributes to provide a richer experience to the end-user.

`attributes[key].display_name: String` A custom display name to be used instead of the key.
* Resource MAY supply this property to override the representation of the key for the attribute.
* Client SHOULD respect this value, when supplied, in favour of the key used for the attribute.

`attributes[key].display_value: String` A custom display value to be used instead of the actual value provided.
* Resource MAY supply this property to override the representation of the value for the attribute.
* Client SHOULD respect this value, when supplied, in favour of the value used for the attribute.

`attributes[key].value: String|Float|Integer|Boolean|Array|null` The value associated with this attribute.
* Resource MUST supply this property, if an attribute is supplied. If `type` is set to `array`, this property MUST be a valid JSON Array.
* Client MAY display this property.

`attributes[key].type: String` The type of attribute.
* Supported Values:
  * `string` (default)
  * `integer`
  * `float`
  * `boolean`
  * `array`
  * `datetime` ([ISO 8601](https://en.wikipedia.org/wiki/ISO_8601))
  * `date` ([ISO 8601](https://en.wikipedia.org/wiki/ISO_8601))
  * `time` ([ISO 8601](https://en.wikipedia.org/wiki/ISO_8601))
  * `url` ([RFC 3986](https://datatracker.ietf.org/doc/html/rfc3986))
  * `hidden`
* Resource SHOULD provide this property to instruct the Client how to appropriately render this attribute. Custom types may be used, but they should be prefixed by `x-` (such as `x-progress-bar`) to ensure they don't conflict with the behaviours of future attribute types that may be standardised.
* Client MAY render values for any type that it supports. Client MAY determine what attribute types are supported, though MUST support `string` (as it is the default value if this proeprty is omitted) as well as the `hidden` type. Client SHOULD NOT show the attribute if the type is `hidden`. If `type` is `array`, the Client SHOULD render the individual elements of the array as discrete items (e.g., tags or badges).

`meta: Object` Meta about this Resource.
* Resource MUST supply this property as it serves as a hint that the Resource is compliant with the Token Metadata Standard.
* Client MUST parse this property althought it is OPTIONAL whether it is rendered.

`meta.version: Float` The version of the standard that this Resource conforms to.
* Supported Values:
  * `1.0` is the current standard.
* Resource MUST contain this property which indicates the standard that this Resource conforms to.
* Client MUST use this property to determine how to parse and render this Resource.

`meta.rating: String` Identifies this Resource as being potentially sensitive to certain audiences, and under which clasification.
* Supported Values:
  * `nudity` denotes that this Resource contains nudity-related content.
  * `violence` denotes that this Resource contains violence-related content.
  * `drugs` denotes that this Resource contains drugs-related content.
  * `alcohol` denotes that this Resource contains alcohol-related content.
  * `gambling` denotes that this Resource contains gambling-related content.
  * `other` denotes that some viewers may find this Resource sensitive for one reason or another, that's not necessarily better defnied by an alternative - more specific - value.
* Resource MAY include this property to indicate the contents of this Resource are potentially sensitive to certain audiences. It is RECOMMENDED to include this whenever the creator is aware that the Resource may be deemed sensitive.
* Client SHOULD take appropriate steps to safeguard their end-users when a token has identified itself as being potentially sensitive. The Client MAY determine which themes warrant safeguarding based on their target audience.

`meta.language: Language` The language associated with this Resource.
* Resource SHOULD contain this property to indicate the language of this Resource.
* Client MAY display this property.

`meta.alternate: Object<Language,URL>` An object containing alternate variants of the Resource, specifically for different languages.
* Resource MAY contain this property to identify alternate versions of this Resource in various languages.
* Client SHOULD identify the Resource that closest matches the end-user's language settings and retrieve the most specific Resource for rendering. Refer to [Alternate Resources (Internationalisation)](#metadata-alternate-resources-internationalisation) for more information.
 
### Alternate Resources (Internationalisation)

The `meta.alternate` property, defined within the top-level Resource, indicates an exhaustive list of alternate Resources that correspond to different localizations of the given Resource. This enables tokens to natively be translated into multiple different languages without the need for using a third-party translator.

The Client is responsible for determining the best language for the end-user who is viewing the Resource. This MAY come in the form of automatically detecting the best language based on request information such as a header; and/or geolocation data, or it MAY come in the form of a manual interaction such as user selection.

If the Client is aware of a better suited Resource for the end-user, the alternate Resource SHOULD be retrieved; parsed; and rendered instead. In order to prevent a potential Denial of Service (DoS) attack, the Client MUST NOT follow more than one-level deep from the top-level Resource. The entirety of the Resource should be inferred exclusively from the Resource that is best suited for the end-user.

## Data Types

`URL` [Uniform Resource Locator (RFC 1738)](https://datatracker.ietf.org/doc/html/rfc1738): any compatible URL (this includes relative URLs) as defined within RFC 1738 is considered a valid input. The Client is solely responsible for determining whether the scheme is supported and MAY omit any values that are unsupported. Clients SHOULD support the `http` and `https` schemes as a minimum. It is also RECOMMENDED that Clients support the `ipfs` and `data` schemes.

`Language` [Tags for Identifying Languages (RFC 5646)](https://datatracker.ietf.org/doc/html/rfc5646): any compatible Language as defined within RFC 5646 is considered a valid input.

`MediaType`: any assigned [Media Type](https://www.iana.org/assignments/media-types/media-types.xhtml) by the Internet Assigned Numbers Authority (IANA).

## Examples

<details>
  <summary>Basic Sword: A very simple metadata file containing the absolute minimum.</summary>

  Featuring only a `name`, this is the absolute minimum a metadata resource file complying with this standard can contain.
  
  [View Example Token](https://nft.io/asset/4319-1) · [View JSON](https://platform.production.enjinusercontent.com/enterprise/enjin/assets/metadata/metadata-standard-examples/basic-sword.json)
  

  ```json
{
	"name": "Basic Sword",
	"meta": {
		"version": 1.0
	}
}
  ```
</details>

<details>
  <summary>Bronze Sword: A simple metadata file containing a name, description and image.</summary>

  This would be the most typical form of metadata resource file. It features not just a name and a brief description, but also an image.
  
  [View Example Token](https://nft.io/asset/4319-2) · [View JSON](https://platform.production.enjinusercontent.com/enterprise/enjin/assets/metadata/metadata-standard-examples/bronze-sword.json)
  

  ```json
{
	"name": "Bronze Sword",
	"description": "A simple bronze sword.",
	"media": [
		{
			"url": "https://platform.production.enjinusercontent.com/enterprise/enjin/assets/media/metadata-standard-examples/bronze-sword.png",
			"type": "image/png"
		}
	],
	"meta": {
		"version": 1.0
	}
}
  ```
</details>

<details>
  <summary>Iron Sword: Inclusion of a custom fallback image.</summary>

  A fallback image is optionally rendered by the client. This allows the resource creator to define how the token should be display whilst it's loading or upon the failure to load any media.
  
  [View Example Token](https://nft.io/asset/4319-3) · [View JSON](https://platform.production.enjinusercontent.com/enterprise/enjin/assets/metadata/metadata-standard-examples/iron-sword.json)
  

  ```json
{
	"name": "Iron Sword",
	"description": "A robust sword, made out of iron.",
	"fallback_image": "https://platform.production.enjinusercontent.com/enterprise/enjin/assets/media/metadata-standard-examples/iron-sword.fallback.jpg",
	"media": [
		{
			"url": "https://platform.production.enjinusercontent.com/enterprise/enjin/assets/media/metadata-standard-examples/iron-sword.png",
			"type": "image/png",
			"alt": "A sword made out of iron."
		}
	],
	"meta": {
		"version": 1.0
	}
}
  ```
</details>

<details>
  <summary>Steel Sword: Introducing attributes and keywords.</summary>

  The introduction of attributes allows richer data to be presented by the Client. Further, adding keywords can assist in searching for the token. Also, the inclusion of `meta.language` assists in identifying the source language of the metadata file, helpful for automatic translations.
  
  [View Example Token](https://nft.io/asset/4319-4) · [View JSON](https://platform.production.enjinusercontent.com/enterprise/enjin/assets/metadata/metadata-standard-examples/steel-sword.json)
  

  ```json
{
	"name": "Steel Sword",
	"description": "A sharp and incredibly strong sword, forged out of steel.",
	"fallback_image": "https://platform.production.enjinusercontent.com/enterprise/enjin/assets/media/metadata-standard-examples/steel-sword.fallback.jpg",
	"keywords": ["weapon", "sword", "steel", "steel sword", "steel weapon"],
	"media": [
		{
			"url": "https://platform.production.enjinusercontent.com/enterprise/enjin/assets/media/metadata-standard-examples/steel-sword.png",
			"type": "image/png",
			"alt": "A sword made out of steel."
		}
	],
	"attributes": {
		"type": {
			"value": "Weapon",
			"type": "string"
		},
		"subtype": {
			"value": "Sword",
			"type": "string"
		},
		"material": {
			"value": "Steel",
			"type": "string"
		}
	},
	"meta": {
		"version": 1.0,
		"language": "en"
	}
}
  ```
</details>

<details>
  <summary>Multiverse Shield: Introducing array attributes for interoperability.</summary>

  The use of the `array` type allows a token to specify multiple values for a single attribute. This is specifically useful for lists or tagging systems that Clients can use for dynamic filtering and richer UI rendering (e.g., displaying values as badges).
  
  ```json
{
  "name": "Multiverse Shield",
  "description": "A high-tech energy shield compatible with multiple virtual worlds.",
  "attributes": {
    "usable_in": {
      "display_name": "Compatible Games",
      "type": "array",
      "value": ["Space MMO", "Fantasy Realm", "Cyber Racers"]
    },
    "rarity": {
      "value": "Legendary",
      "type": "string"
    }
  },
  "meta": {
    "version": 1.0,
    "language": "en"
  }
}
  ```
</details>

<details>
  <summary>Tungsten Sword: Custom display names and values for attributes, different attribute types and reference to an external website.</summary>

  The use of `external_url` allows the resource creator to define a custom link that the Client may render to allow users to find out more details about a token. The use of custom attribute types allows for better Client handling and providing string values for `display_name` and `display_value` within the attribute allows overriding how the attribute appears.
  
  [View Example Token](https://nft.io/asset/4319-5) · [View JSON](https://platform.production.enjinusercontent.com/enterprise/enjin/assets/metadata/metadata-standard-examples/tungsten-sword.json)
  

  ```json
{
	"name": "Tungsten Sword",
	"description": "A sharp and incredibly strong sword, forged out of tungsten.",
	"fallback_image": "https://platform.production.enjinusercontent.com/enterprise/enjin/assets/media/metadata-standard-examples/tungsten-sword.fallback.jpg",
	"external_url": "https://github.com/enjin/universal-metadata-standard",
	"keywords": ["weapon", "sword", "tungsten", "tungsten sword", "tungsten weapon"],
	"media": [
		{
			"url": "https://platform.production.enjinusercontent.com/enterprise/enjin/assets/media/metadata-standard-examples/tungsten-sword.png",
			"type": "image/png",
			"alt": "A sword made out of tungsten."
		}
	],
	"attributes": {
		"type": {
			"value": "weapon",
			"type": "hidden"
		},
		"subtype": {
			"display_name": "Weapon Type",
			"display_value": "Sword",
			"value": "sword",
			"type": "string"
		},
		"material": {
			"display_name": "Weapon Material",
			"display_value": "Weapon",
			"value": "tungsten",
			"type": "string"
		},
		"grade": {
			"display_name": "Weapon Grade",
			"display_value": "IV",
			"value": 4,
			"type": "integer"
		},
		"is_broken": {
			"display_name": "Broken",
			"value": false,
			"type": "boolean"
		},
		"forged_at": {
			"display_name": "Forged",
			"value": "2018-06-22T10:08:51",
			"type": "datetime"
		}
	},
	"meta": {
		"version": 1.0,
		"language": "en"
	}
}
  ```
</details>

<details>
  <summary>Elven Sword: A localized metadata resource file.</summary>

  By specifying `meta.alternate`, this seemingly simply file now supports internationalization (i18n), allowing for the first time a metadata resource file to be rendered by the Client in multiple languages. This includes also customizing the attributes and media files that are displayed.
  
  [View Example Token](https://nft.io/asset/4319-6)
  

`en` English ([View JSON](https://platform.production.enjinusercontent.com/enterprise/enjin/assets/metadata/metadata-standard-examples/elven-sword.en.json))
  ```json
{
	"name": "Elven Sword",
	"description": "A magical blade, rumoured to hold enchanted powers, forged by the Elven Gods.",
	"fallback_image": "https://platform.production.enjinusercontent.com/enterprise/enjin/assets/media/metadata-standard-examples/elven-sword.fallback.jpg",
	"external_url": "https://github.com/enjin/universal-metadata-standard",
	"keywords": ["weapon", "sword", "elven", "elven sword", "elven weapon"],
	"media": [
		{
			"url": "https://platform.production.enjinusercontent.com/enterprise/enjin/assets/media/metadata-standard-examples/elven-sword.en.png",
			"type": "image/png",
			"alt": "FOR GLORY FOR GOD"
		},
		{
			"url": "https://platform.production.enjinusercontent.com/enterprise/enjin/assets/media/metadata-standard-examples/elven-sword.png",
			"type": "image/png",
			"alt": "A magic sword crafted by the elves."
		}
	],
	"attributes": {
		"type": {
			"value": "weapon",
			"type": "hidden"
		},
		"subtype": {
			"display_name": "Weapon Type",
			"display_value": "Sword",
			"value": "sword",
			"type": "string"
		},
		"material": {
			"display_name": "Weapon Material",
			"display_value": "Weapon",
			"value": "elven",
			"type": "string"
		},
		"grade": {
			"display_name": "Weapon Grade",
			"display_value": "X",
			"value": 10,
			"type": "integer"
		},
		"is_broken": {
			"display_name": "Broken",
			"value": false,
			"type": "boolean"
		},
		"forged_at": {
			"display_name": "Forged",
			"value": "2018-06-22T10:08:51",
			"type": "datetime"
		}
	},
	"meta": {
		"version": 1.0,
		"language": "en",
		"alternate": {
			"en": "https://platform.production.enjinusercontent.com/enterprise/enjin/assets/metadata/metadata-standard-examples/elven-sword.en.json",
			"en-US": "https://platform.production.enjinusercontent.com/enterprise/enjin/assets/metadata/metadata-standard-examples/elven-sword.en_us.json",
			"it": "https://platform.production.enjinusercontent.com/enterprise/enjin/assets/metadata/metadata-standard-examples/elven-sword.it.json",
			"ja": "https://platform.production.enjinusercontent.com/enterprise/enjin/assets/metadata/metadata-standard-examples/elven-sword.ja.json"
		}
	}
}
  ```
  

`en` English (US) ([View JSON](https://platform.production.enjinusercontent.com/enterprise/enjin/assets/metadata/metadata-standard-examples/elven-sword.en_us.json))
  ```json
{
    "name": "Elven Sword",
    "description": "A magical blade, rumored to hold enchanted powers, forged by the Elven Gods.",
    "fallback_image": "https://platform.production.enjinusercontent.com/enterprise/enjin/assets/media/metadata-standard-examples/elven-sword.fallback.jpg",
    "external_url": "https://github.com/enjin/universal-metadata-standard",
    "keywords": ["weapon", "sword", "elven", "elven sword", "elven weapon"],
    "media": [
		{
			"url": "https://platform.production.enjinusercontent.com/enterprise/enjin/assets/media/metadata-standard-examples/elven-sword.en.png",
			"type": "image/png",
			"alt": "FOR GLORY FOR GOD"
		},
		{
			"url": "https://platform.production.enjinusercontent.com/enterprise/enjin/assets/media/metadata-standard-examples/elven-sword.png",
			"type": "image/png",
			"alt": "A magic sword crafted by the elves."
		}
    ],
    "attributes": {
        "type": {
            "value": "weapon",
            "type": "hidden"
        },
        "subtype": {
            "display_name": "Weapon Type",
            "display_value": "Sword",
            "value": "sword",
            "type": "string"
        },
        "material": {
            "display_name": "Weapon Material",
            "display_value": "Weapon",
            "value": "elven",
            "type": "string"
        },
        "grade": {
            "display_name": "Weapon Grade",
            "display_value": "X",
            "value": 10,
            "type": "integer"
        },
        "is_broken": {
            "display_name": "Broken",
            "value": false,
            "type": "boolean"
        },
        "forged_at": {
            "display_name": "Forged",
            "value": "2018-06-22T10:08:51",
            "type": "datetime"
        }
    },
    "meta": {
        "version": 1.0,
        "language": "en-US",
        "alternate": {
            "en": "https://platform.production.enjinusercontent.com/enterprise/enjin/assets/metadata/metadata-standard-examples/elven-sword.en.json",
            "en-US": "https://platform.production.enjinusercontent.com/enterprise/enjin/assets/metadata/metadata-standard-examples/elven-sword.en_us.json",
            "it": "https://platform.production.enjinusercontent.com/enterprise/enjin/assets/metadata/metadata-standard-examples/elven-sword.it.json",
            "ja": "https://platform.production.enjinusercontent.com/enterprise/enjin/assets/metadata/metadata-standard-examples/elven-sword.ja.json"
        }
    }
}
  ```

`it` Italian ([View JSON](https://platform.production.enjinusercontent.com/enterprise/enjin/assets/metadata/metadata-standard-examples/elven-sword.it.json))
```json
{
    "name": "Spada Elfica",
    "description": "Una lama magica, si dice che possieda poteri incantati, forgiata dagli Dei Elfici.",
    "fallback_image": "https://platform.production.enjinusercontent.com/enterprise/enjin/assets/media/metadata-standard-examples/elven-sword.fallback.jpg",
    "external_url": "https://github.com/enjin/universal-metadata-standard",
    "keywords": ["arma", "spada", "elfica", "spada elfica", "arma elfica"],
    "media": [
        {
            "url": "https://platform.production.enjinusercontent.com/enterprise/enjin/assets/media/metadata-standard-examples/elven-sword.it.png",
            "type": "image/png",
            "alt": "PER LA GLORIA DI DIO"
        },
		{
			"url": "https://platform.production.enjinusercontent.com/enterprise/enjin/assets/media/metadata-standard-examples/elven-sword.png",
			"type": "image/png",
			"alt": "Una spada magica forgiata dagli elfi."
		}
    ],
    "attributes": {
        "type": {
            "value": "arma",
            "type": "hidden"
        },
        "subtype": {
            "display_name": "Tipo di Arma",
            "display_value": "Spada",
            "value": "spada",
            "type": "string"
        },
        "material": {
            "display_name": "Materiale dell'Arma",
            "display_value": "Elfica",
            "value": "elfica",
            "type": "string"
        },
        "grade": {
            "display_name": "Grado dell'Arma",
            "display_value": "X",
            "value": 10,
            "type": "integer"
        },
        "is_broken": {
            "display_name": "Rottura",
            "value": false,
            "type": "boolean"
        },
        "forged_at": {
            "display_name": "Forgiata il",
            "value": "2018-06-22T10:08:51",
            "type": "datetime"
        }
    },
    "meta": {
        "version": 1.0,
        "language": "it",
        "alternate": {
            "en": "https://platform.production.enjinusercontent.com/enterprise/enjin/assets/metadata/metadata-standard-examples/elven-sword.en.json",
            "en-US": "https://platform.production.enjinusercontent.com/enterprise/enjin/assets/metadata/metadata-standard-examples/elven-sword.en_us.json",
            "it": "https://platform.production.enjinusercontent.com/enterprise/enjin/assets/metadata/metadata-standard-examples/elven-sword.it.json",
            "ja": "https://platform.production.enjinusercontent.com/enterprise/enjin/assets/metadata/metadata-standard-examples/elven-sword.ja.json"
        }
    }
}
```

`ja`  Japanese ([View JSON](https://platform.production.enjinusercontent.com/enterprise/enjin/assets/metadata/metadata-standard-examples/elven-sword.ja.json))
```json
{
    "name": "エルフの剣",
    "description": "魔法の力を秘めていると噂される、エルフの神々によって鍛えられた魔剣。",
    "fallback_image": "https://platform.production.enjinusercontent.com/enterprise/enjin/assets/media/metadata-standard-examples/elven-sword.fallback.jpg",
    "external_url": "https://github.com/enjin/universal-metadata-standard",
    "keywords": ["武器", "剣", "エルフ", "エルフの剣", "エルフの武器"],
    "media": [
        {
            "url": "https://platform.production.enjinusercontent.com/enterprise/enjin/assets/media/metadata-standard-examples/elven-sword.ja.png",
            "type": "image/png",
            "alt": "神の栄光のために"
        },
        {
            "url": "https://platform.production.enjinusercontent.com/enterprise/enjin/assets/media/metadata-standard-examples/elven-sword.png",
            "type": "image/png",
            "alt": "エルフによって鍛えられた魔法の剣。"
        }
    ],
    "attributes": {
        "type": {
            "value": "武器",
            "type": "hidden"
        },
        "subtype": {
            "display_name": "武器の種類",
            "display_value": "剣",
            "value": "剣",
            "type": "string"
        },
        "material": {
            "display_name": "武器の材質",
            "display_value": "エルフ製",
            "value": "エルフ",
            "type": "string"
        },
        "grade": {
            "display_name": "武器の等級",
            "display_value": "X",
            "value": 10,
            "type": "integer"
        },
        "is_broken": {
            "display_name": "破損状態",
            "value": false,
            "type": "boolean"
        },
        "forged_at": {
            "display_name": "鍛造日",
            "value": "2018-06-22T10:08:51",
            "type": "datetime"
        }
    },
    "meta": {
        "version": 1.0,
        "language": "ja",
        "alternate": {
            "en": "https://platform.production.enjinusercontent.com/enterprise/enjin/assets/metadata/metadata-standard-examples/elven-sword.en.json",
            "en-US": "https://platform.production.enjinusercontent.com/enterprise/enjin/assets/metadata/metadata-standard-examples/elven-sword.en_us.json",
            "it": "https://platform.production.enjinusercontent.com/enterprise/enjin/assets/metadata/metadata-standard-examples/elven-sword.it.json",
            "ja": "https://platform.production.enjinusercontent.com/enterprise/enjin/assets/metadata/metadata-standard-examples/elven-sword.ja.json"
        }
    }
}
```
</details>

## Copyright

Copyright and related rights waived via [CC0](https://creativecommons.org/publicdomain/zero/1.0/legalcode.txt).
