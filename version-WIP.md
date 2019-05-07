# Versions

## Description

??? Mention that they are Paging, sorting and filtering compatible

Repositories retain a list of versions of assets. Versions Resources are used by the Platform API to store the set of all available versions of an asset.

For [files](link.goes.here) a new version is created when new content is uploaded for the primary resource. For [composites](link.goes.here), a new version is created when the manifest is uploaded. Directories are not version specific and the rest of this section does not apply to them.

When new content is uploaded, a new set of [rendition resources](link.goes.here) become available, corresponding to the new version. A new metadata resource is also available for each new content version. Each _version_ of an asset logically consists of this set of content and data resources.

There is only one versions resource associated with each asset, regardless of how many versions of that asset there may be. The versions resource can be discovered via the `link:` header returned with each resource of each version of an asset.

Versions form a linear sequence. There is no branching or forking of version histories. Branching and forking is tracked in embedded asset metadata. Version order can be determined only from the order in which versions are listed in the versions resource.

Note about garbage collection: Repositories may implement garbage collection of old versions, after a certain number of days, or when a certain number of versions is reached, for example. If a repository implements garbage collection of old versions, they must allow a limited number of specific versions to be exempted from garbage collection by setting their [milestone](link.goes.here) property; see below for details.

<br/><br/>
[](*****************************************************)

## Properties

Each version of an asset has the following associated properties:

* **created:** The time at which this version of the asset was created.
* **created_by:** An identifier of the user who created this version of the asset.
* **version:** An identifier unique to this version of an asset. Repositories may use a monotonically increasing number for this value (0, 1, 2...). Other implementations are possible so long as they guarantee each version_id is unique for a given asset. This identifier says nothing about order, and should only be used to compare equality. Version_ids may appear as a parameter in templated URIs.
* **milestone:** An object that, when set, contains a human-readable label for this version (required) and a description (optional). The presence of a milestone on a version excludes that version from garbage collection. The milestone property may be added, removed, and modified by user agents (see [Operations]() for details). Implementations may impose a restriction on the number of milestones per asset.

Each version may also have links of the following relation types:

* **`api:primary`** A version-specific link to the content of this asset version.
* **`describedBy`** A version-specific link to the metadata of this asset version.
* **`api:rendition`** Zero or more version-specific links to renditions of this asset version. Rendition links may have width and height properties in addition to standard link properties.
* **`api:manifest`** A version-specific link to the manifest for this asset, if it is a composite.
* **`api:component`** A version-specific template link to the components in this asset, if it is a composite.

<br/><br/>
[](*****************************************************)
 
## Operations
## Get a Versions Resource
### Description

This request is used to retrieve the Versions Resource of an asset. The body of the HTTP response will contain the versions in a JSON format.

### Request

#### Link

Versions Resources are retrieved using a link to the asset's `<version-history>` link. This link can be found in the Link header of associated resources, or the Directory Resources.

#### Parameters

|Parameter|Description|
|--------------------------|-----------|
|orderBy|Specifies the comma-separated list of properties by which the resource should be sorted.|
|start|Specifies where the list of items should begin.|
|limit|Specifies a positive integer as a _hint_ to the max number of items that should be returned (depends on `start` parameter).|
|property|???|

#### Method

__`GET`__, __`HEAD`__

#### Headers

|Name|Required|Description|
|----|--------|-----------|
|Authorization|Yes|Bearer token representing an authenticated user.|
|x-api-key|Yes|API Key of the calling client or service.|
|x-request-id|No|An identifier for the request.|
|if-none-match|No|List of ETag's. Versions Resource retrieved only if the ETag does not match any of the values listed.|

#### Body

Empty

### Response

#### Status Codes

|Status Code|Description|
|-----------|-----------|
|200 OK|The Versions Resource was successfully retrieved.|
|400 Bad Request|The request was invalid or the response was too large. Use [page-by-page](link.goes.here) access to avoid the latter.|

#### Headers

|Name|Description|
|------------|-----------|
|ETag|The entity tag of the Versions Resource.|

#### Body

Versions resource (JSON)

### Example: Read a Version Resource for /images/pets/dog.jpg

Prerequisite: Read the link header for /images/pets/dog.jpg. This will contain a URL to the Versions Resource. This is referred to as `<versions-history>` in the example below.

#### Request

```
GET <version-history> 
Authorization: bearer Xes...2scls
X-Api-Key: 9fns0engnsSd
If-None-Match: "*"
...


Body: Empty
```

#### Response

```
HTTP/1.1 200 OK
ETag: <image_etag>
...


Body: Versions resource content (JSON)
```
<br/>
[](*****************************************************)

## Get a Versions Resource Page
### Description

Version histories may be retrieved page-at-a-time. This is done by issuing a `GET` or `HEAD` to the `<api:page>` link of an asset. This method bypasses any risk of a `400 Bad Request` caused by the response being too large.

### Request
#### Link

Versions Resources are retrieved using a link to the asset's `<api:create>` link. This link can be found in the Link header of associated resources, or the Directory Resources.

#### Parameters

|Parameter|Description|
|--------------------------|-----------|
|resource|Must be "version-history".|
|orderBy|When accessing version via pages, they are always sorted by the `create` property.|
|start|Specifies where the list of items should begin.|
|limit|Specifies a positive integer as a _hint_ to the max number of items that should be returned (depends on `start` parameter).|
|property|milestone.label|

#### Method

__`GET`__, __`HEAD`__

#### Headers

|Name|Required|Description|
|----|--------|-----------|
|Authorization|Yes|Bearer token representing an authenticated user.|
|x-api-key|Yes|API Key of the calling client or service.|
|x-request-id|No|An identifier for the request.|
|if-none-match|No|List of ETag's. Versions Resource page retrieved only if the ETag does not match any of the values listed.|

#### Body

Empty

### Response
#### Status Codes

|Status Code|Description|
|-----------|-----------|
|200 OK|The Versions Resource page was successfully retrieved.|
|400 Bad Request|The request was invalid.|

#### Headers

|Name|Description|
|------------|-----------|
|ETag|The entity tag of the Versions Resource.|

#### Body

Versions resource (JSON)

<br/>

[](*********************************************)

## Update a Versions Resource
### Description

Version Resources can update the `milestone` property by issuing a `PATCH` request against the resource. Any number of version records may be updated in a single `PATCH` request. You are not able to issue `PUT` requests because most of the data in a Versions Resource is read-only. Users are also not able to issue `DELETE` requests to the Versions Resource.

### Request
#### Link

Each Versions Resource has its own `Etag`. You _must_ use an `if-match` header to apply `PATCH` to a Versions Resource. Using the `Etag`, you can detect conflicts with competing changes to the Versions Resource.

#### Parameters

#### Method

__`PATCH`__

#### Headers

#### Body

Empty

### Response
#### Status Codes

#### Headers

#### Body

