#Browser-friendly format for EPUB 3.1

EPUB as it exists today is not directly usable by a web browser. The web-friendly content files are inside a zip package, which also contains container and package files expressed in a custom XML vocabulary. 

The goal of a browser-friendly format (henceforth EPUB-BFF) is to make it easier for web developers to display EPUB content by [1] allowing an unzipped ("exploded") publication, and [2] by providing an alternative JSON serialization of the information in container.xml and the package document(s). 

##Packaging

Ordinary EPUBs must be packaged in an EPUB Container as defined in [OCF31]. EPUB-BFF is not defined in a packaged state (although this may change in the future). The EPUB-BFF JSON package file may be included in an ordinary packaged EPUB if referenced properly, but reading systems have no obligation to read the JSON package file.

##The JSON Package Document

###Introduction

A single JSON package document replaces both container.xml and the package file(s). Document metadata, the manifest, the spine, rendition metadata, and collections can all be serialized this way. 

###Data Model

The JSON package file consists of a metadata object, followed by one or more collection objects. EPUB 3.0.1 defines a collection as "a related group of resources." We are extending this term so that a rendition of a publication can be described as a collection.


A collection consists of metadata and links. The link array describes both the required publication resources ("manifest") and their sequence ("spine"). Note that this avoids the duplication inherent in the manifest/spine model of EPUB, as well as the need for id and idrefs.

```json
{
  "metadata": {
    "title": "Moby Dick",
    "language": "en",
    "identifier": {
      "type": "unique-identifier",
      "value": "9780000000001",
      "modified": "2015-09-29T17:00:00Z"
    }
  },

  "rendition": {

    "links": [{
      "href": "cover.jpg",
      "type": "image/jpeg",
      "properties": "cover-image"
    }, {
      "href": "c001.html",
      "type": "text/html"
    }, {
      "href": "c002.html",
      "type": "text/html"
    }, {
      "href": "toc.html",
      "type": "text/html",
      "properties": "nav"
    }, {
      "href": "style.css",
      "type": "text/css"
    }]
  }

}
```

The package assumes that resources of type "text/html" are in the "spine" unless otherwise specified.

```json
{
  "href": "toc.html",
  "type": "text/html",
  "properties": "nav",
  "sequence": "false"
}
```

####Link objects

Each publication component is described by a link object. The `href` and `type` attributes are required. 

| Name  | Value | Format | Required? |
| ------------- | ------------- | ------------- | ------------- |
| href  | link location  | URI  | Yes  |
| type  | MIME type of resource  | MIME media type  | Yes  |
| sequence  | is the linked resource part of the linear reading order?  | boolean  | No  |
| rel  | relationship  | TK  | No  |
| properties  | properties associated with the linked resource  | (see spec)  | No  |
| templated  | indicates linked resource is a URI template  | boolean  | No  |


####Types of collections

