**[ DRAFT REVISION ]**

# Universal Off-Chain Token Metadata Standard

- **Authors:** [Brad Bayliss](mailto:brad@bayliss.co.uk), ... (placeholder)
- **Status:** Draft
- **Created:** 2022-XX-XX (placeholder)
- **Reference Implementation:** undefined (placeholder)

## Abstract

This document defines the structure of a Metadata Resource File which is used to represent the details of a token. It also describes how an application will interpret the Metadata Resource File by defining how it's parsed and rendered.

## Motivation

_[[ describe details about The Metaverse ]]_ (placeholder)

The issue that currently plagues both token creators and application developers is the way in which the Metadata Resource File is structured. A lack of a well-defined standard means that token creators are left to interpret how these files should be formatted, and this is often done by referencing existing documentation materials or existing tokens. The lack of a standard also leaves application developers struggling to natively support all tokens, which leads to complications towards working to the goal of _The Metaverse_. This standard details the specifics required to ensure universal compatibility for all applications, and so that token creators can be reassured their tokens will be presented to the end-user in a manner that they expect.

This standard also focuses on allowing **everyone** to partake in _The Metaverse_ by ensuring that accessibility and internationalisation are equally well defined from the very start.

Finally, the concept of _The Metaverse_ is one that changes day-by-day. It is for that reason that this standard is upgradeable to allow for the standard to periodically be upgraded to meet the everchanging demands of _The Metaverse_.

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

`group_key: String` An optional, string, that will be used to group tokens with the same value.
* Resource MAY supply this property to indicate to a Client how to group similar tokens for a given collection.
* Client SHOULD NOT display this property. Client SHOULD group tokens of the same collection if they have a matching group key. Client MUST NOT group tokens, irrespective of group key, if the collection differs.

`keywords: Array<String>` An array of unique keywords to describe this token.
* Resource MAY contain this property to help Clients with search functionality better locate their token. It is RECOMMENDED to keep the number of unique keywords to no mare than 20 as Clients are solely responsible for whether keywords may be used and how many are referenced.
* Client MAY use this property to determine the most appropriate match for a given search term.

`fallback_image: URL` An image that the Client should respect in the event it is unable to load another media resource, this is in favour of a generic 'broken image' that the Client may otherwise have displayed.
* Resource MAY contain this property. The value MUST correspond to an `image/*` type and is RECOMMENDED to be either `image/bmp`, `image/jpeg`, `image/png` or `image/gif` to ensure compatibility with all Clients.
* Client SHOULD display this image only as a fallback in the event it fails to load the requested media file(s) and/or if all media file(s) are that of an unsupported type.

`media: Array<Object>` An array of objects containing media (image; video; and/or model) files that are used to represent this token. The order of this array is important as the Client should present any supported media resources in that order. 
* Resource SHOULD provide a list of media resources.
* Client SHOULD display at least one media resource. The Client MAY ignore media types that it is unable to support. The Client SHOULD present media in the order they're listed within the array.

`media[n].url: URL` The URL of the media resource.
* Resource MUST contain this property.
* Client MUST use this property to retrieve the media associated with this token.

`media[n].type: MediaType` A valid IANA Media Type used to indicate the type of media resource.
* Resource SHOULD supply the corresponding [IANA Media Type](https://www.iana.org/assignments/media-types/media-types.xhtml) associated with the media supplied within the `media[n].url` property.
* Client MAY use this property to filter media unsupported by the Client or to determine how best to render the media.

`media[n].alt: String` Alternative text used to describe the media.
* Resource MAY supply a string that describes the media file. It is RECOMMENDED that this property is specified to improve accessibility.
* Client SHOULD use this property as a text alternative to the media file.

`attributes: Object<String,Object>` An object containing custom attributes that may be interpreted by the Client.
* Resource MAY provide a list of attributes that can be optionally displayed by the Client and is a simply key/value pair. The value MAY be an object that itself MUST contain a key named `value`, additional data may be contained within this object.
* Client MAY parse the attributes to provide a richer experience to the end user.

`attributes[key].display_name: String` A custom display name to be used instead of the key.
* Resource MAY supply this property to override the representation of the key for the attribute.
* Client SHOULD respect this value, when supplied, in favour of the key used for the attribute.

`attributes[key].display_value: String` A custom display value to be used instead of the actual value provided.
* Resource MAY supply this property to override the representation of the value for the attribute.
* Client SHOULD respect this value, when supplied, in favour of the value used for the attribute.

`attributes[key].value: String` The value associated with this attribute.
* Resource MUST supply this property.
* Client MAY display this property.

`attributes[key].type: String` The type of attribute.
* Supported Values:
  * `string` (default)
  * `integer`
  * `float`
  * `boolean`
  * `datetime` ([ISO 8601](https://en.wikipedia.org/wiki/ISO_8601))
  * `date` ([ISO 8601](https://en.wikipedia.org/wiki/ISO_8601))
  * `time` ([ISO 8601](https://en.wikipedia.org/wiki/ISO_8601))
  * `secret`
  * `url` ([RFC 3986](https://datatracker.ietf.org/doc/html/rfc3986))
  * `hidden`
* Resource SHOULD provide this property to instruct the Client how to appropriately render this attribute.
* Client MAY render values for values that it supports. Client MAY determine what attribute types are supported, though MUST  support `string` (as it is the default value if this proeprty is omitted) as well as the `hidden` type. Client SHOULD NOT show the attribute if the type is `hidden`.

`meta: Object` Meta about this Resource.
* Resource MUST supply this property as it serves as a hint that the Resource is compliant with the Token Metadata Standard.
* Client MUST parse this property.

`meta.version: Float` The version of the standard that this Resource conforms to.
* Supported Values:
  * `1.0` is the current standard.
* Resource MUST contain this property which indicates the standard that this Resource conforms to.
* Client MUST use this property to determine how to parse and render this Resource.

`meta.rating: String` Identifies this Resource as being potentially sensitive, and under which clasification.
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

`meta.alternate: Object<Language,URL>` An object containing alternate Resource files for different languages.
* Resource MAY contain this property to identify alternate versions of this Resource in various languages.
* Client SHOULD identify the Resource that closest matches the end user's language settings and retrieve the most specific Resource for rendering. Refer to [Alternate Resources (Internationalisation)](#metadata-alternate-resources-internationalisation) for more information.
 
### Alternate Resources (Internationalisation)

The `meta.alternate` property, defined within the top-level Resource, indicates an exhaustive list of alternate Resources that correspond to different localizations of the given Resource. This enables tokens to natively be translated into multiple different languages without the need for using a third-party translator.

The Client is responsible for determining the best language for the end-user who is viewing the Resource. This MAY come in the form of automatically detecting the best language based on request information such as a header; and/or geolocation data, or it MAY come in the form of a manual interaction such as user selection.

If the Client is aware of a better suited Resource for the end-user, the alternate Resource SHOULD be retrieved; parsed; and rendered instead. In order to prevent a potential Denial of Service (DoS) attack, the Client MUST NOT follow more than one-level deep from the top-level Resource.

## Data Types

`URL` [Uniform Resource Locator (RFC 1738)](https://datatracker.ietf.org/doc/html/rfc1738): any compatible URL (this includes relative URLs) as defined within RFC 1738 is considered a valid input. The Client is solely responsible for determining whether the scheme is supported and MAY omit any values that are unsupported. Clients SHOULD support the `http` and `https` schemes as a minimum. It is also RECOMMENDED that Clients support the `ipfs` and `data` schemes.

`Language` [Tags for Identifying Languages (RFC 5646)](https://datatracker.ietf.org/doc/html/rfc5646): any compatible Language as defined within RFC 5646 is considered a valid input.

`MediaType`: any assigned [Media Type](https://www.iana.org/assignments/media-types/media-types.xhtml) by the Internet Assigned Numbers Authority (IANA).

## Examples

You can use the [Metadata Debugger](https://metadatadebugger.com) (placeholder url) tool to view, manage, and create metadata that conforms with this standard.

<details>
  <summary>Basic Sword: A very simple metadata file.</summary>
  
  [View in Debugger](https://metadatadebugger.com/examples/basic-sword) (placeholder url)

  ```json
{
	"name": "Basic Sword",
	"media": [
		{
			"uri": "https://metadatadebugger.com/token/1/basic-sword.jpg",
			"type": "image/jpeg"
		}
	],
	"meta": {
		"version": 1
	}
}
  ```
</details>
 
<details>
  <summary>Excalibur: A richly formatted metadata file.</summary>
  
  [View in Debugger](https://metadatadebugger.com/examples/excalibur) (placeholder url)
  
  ```json
{
	"name": "Excalibur",
	"description": "A legendary sword wielded by King Arthur, entitling rightful sovereignty of Britain.",
	"fallback_image": "https://metadatadebugger.com/token/2/excalibur-fallback.jpg",
	"media": [
		{
			"uri": "https://metadatadebugger.com/token/2/excalibur-sword.png",
			"alt": "Image of the excalibur sword",
			"type": "image/jpeg"
		},
		{
			"uri": "https://metadatadebugger.com/token/2/excalibur-sword-swing.mp4",
			"alt": "Video of the excalibur sword being swung",
			"type": "video/mp4"
		},
		{
			"uri": "https://metadatadebugger.com/token/2/excalibur-sword-swing.mp3",
			"alt": "Audio of the excalibur sword whoosh",
			"type": "audio/mpeg"
		},
		{
			"uri": "ipfs://ipfs/resource-hash",
			"alt": "3d-model of the excalibur sword, modeled to scale.",
			"type": "model/stl"
		}
	],
	"attributes": {
		"id": {
			"value": "90057b9c-0541-4ff7-971a-054a0f769bb6",
			"visible": false
		},
		"required_lvl": {
			"display_name": "Level Requirement",
			"value": 100
		},
		"dps": {
			"display_name": "Damage Per Second",
			"display_value": "1,000",
			"value": 1000
		},
		"Hint": {
			"value": "It's just a sword."
		}
	},
	"meta": {
		"version": 1,
		"language": "en",
		"alternate": {
			"en": "https://metadatadebugger.com/token/2.json",
			"en-GB": "https://metadatadebugger.com/token/2/en_gb.json",
			"es": "https://metadatadebugger.com/token/2/es.json",
			"es-MX": "https://metadatadebugger.com/token/2/es_mx.json",
			"jp": "https://metadatadebugger.com/token/2/jp.json",
			"it": "https://metadatadebugger.com/token/2/it.json",
			"fr": "https://fr.metadatadebugger.com/actif/2.json",
			"de": "https://de.metadatadebugger.com/anlage/2.json",
			"zh": "https://cn.metadatadebugger.com/资产/2.json"
		}
	}
}
  ```
</details>
 
<details>
  <summary>Reaper: A dense metadata file, containing localization.</summary>
  
  [View in Debugger](https://metadatadebugger.com/examples/reaper) (placeholder url)
  
  <details>
   <summary>en.json</summary>
   
   ```json
   {}
   ```
  </details>
  
  <details>
   <summary>es.json</summary>
   
   ```json
   {}
   ```
  </details>
</details>

## References

placeholder

## Copyright

_Current version is **properietary**._

(placeholder - needs waiving once we're ready to publish the draft)

**[ DRAFT REVISION ]**
