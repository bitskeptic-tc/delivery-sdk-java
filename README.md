# Kentico Cloud Delivery Java SDK

[![Build Status](https://travis-ci.org/Kentico/delivery-sdk-java.svg?branch=master)](https://travis-ci.org/Kentico/delivery-sdk-java)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Javadoc](http://img.shields.io/badge/javadoc-1.0--SNAPSHOT-brightgreen.svg)](https://kentico.github.io/delivery-sdk-java/docs/javadoc/)
[![SonarQube](http://img.shields.io/badge/SonarQube-Results-blue.svg)](https://sonarcloud.io/dashboard?id=com.kenticocloud%3Adelivery-sdk-java)
[![MavenCentral](http://img.shields.io/badge/Maven_Central-1.0--SNAPSHOT-yellow.svg)](https://oss.sonatype.org/content/groups/public/com/kenticocloud/delivery-sdk-java/)

🚧 **This is currently a work in progress, and is only a prototype. Not ready for general consumption. Let us know if you want to contribute at DEVELOPERSCOMMUNITY@KENTICO.COM** 🚧 

## Summary

The Kentico Cloud Delivery Java SDK is a client library used for retrieving content from Kentico Cloud. You can use the SDK in the form of a [Maven dependency](https://oss.sonatype.org/content/repositories/snapshots/com/kenticocloud/).

You can add this to your Gradle project by the following:

```groovy

repositories {
	mavenCentral()
	maven { url 'https://oss.sonatype.org/content/repositories/snapshots/' }
}

dependencies {
    compile('com.kenticocloud:delivery-sdk-java:1.0-SNAPSHOT')
}
```

Or via your Maven POM:
```xml
<repositories>
...
    <repository>
        <id>snapshots</id>
        <url>
            https://oss.sonatype.org/content/repositories/snapshots/
        </url>
        <snapshots>
            <enabled>true</enabled>
        </snapshots>
        <releases>
            <enabled>false</enabled>
        </releases>
    </repository>
</repositories>

...

<dependency>
    <groupId>com.kenticocloud</groupId>
    <artifactId>delivery-sdk-java</artifactId>
    <version>1.0-SNAPSHOT</version>
</dependency>
```

## Prerequisites

To retrieve content from a Kentico Cloud project via the Delivery API, you first need to activate the API for the project. See our documentation on how you can [activate the Delivery API](https://developer.kenticocloud.com/docs/using-delivery-api#section-enabling-the-delivery-api-for-your-projects).

## Using the DeliveryClient

The `DeliveryClient` class is the main class of the SDK. Using this class, you can retrieve content from your Kentico Cloud projects.

To create an instance of the class, you need to provide a [project ID](https://developer.kenticocloud.com/docs/using-delivery-api#section-getting-project-id).

```java
// Initializes an instance of the DeliveryClient client
DeliveryClient client = new DeliveryClient("975bf280-fd91-488c-994c-2f04416e5ee3");
```

You can also provide the project ID and other parameters by passing the [`DeliveryOptions`](https://github.com/Kentico/delivery-sdk-java/blob/master/src/main/java/com/kenticocloud/delivery/DeliveryOptions.java) object to the class constructor. The `DeliveryOptions` object can be used to set the following parameters:

* `setPreviewApiKey(String)` – sets the Delivery Preview API key.
* `setProjectId(String)` – sets the project identifier.
* `setUsePreviewApi(boolean)` – determines whether to use the Delivery Preview API.
* `setWaitForLoadingNewContent(boolean)` – makes the client instance wait while fetching updated content, useful when acting upon [webhook calls](https://developer.kenticocloud.com/docs/webhooks#section-requesting-new-content).

Once you create a `DeliveryClient`, you can start querying your project repository by calling methods on the client instance. See [Basic querying](#basic-querying) for details.

### Filtering retrieved data

The SDK supports full scale of the API querying and filtering capabilities as described in the [API reference](https://developer.kenticocloud.com/reference#filtering-content-items).

```java
// Retrieves a list of the specified elements from the first 10 content items of
// the 'brewer' content type, ordered by the 'product_name' element value
ContentItemsListingResponse response = client.getItems(
    DeliveryParameterBuilder.params()
        .filterEquals("system.type", "brewer")
        .projection("image", "price", "product_status", "processing")
        .page(null, 10)
        .orderByAsc("elements.product_name")
        .build();
);
```

### Previewing unpublished content

To retrieve unpublished content, you need to create a `DeliveryClient` with both Project ID and Preview API key. Each Kentico Cloud project has its own Preview API key. 

```Java
// Note: Within a single project, we recommend that you work with only
// either the production or preview Delivery API, not both.
DeliveryClient client = new DeliveryClient("YOUR_PROJECT_ID", "YOUR_PREVIEW_API_KEY");
```

For more details, see [Previewing unpublished content using the Delivery API](https://developer.kenticocloud.com/docs/preview-content-via-api).

## Basic querying

Once you have a `DeliveryClient` instance, you can start querying your project repository by calling methods on the instance.

```java
// Retrieves a single content item
ContentItemResponse response = client.getItem("about_us");

// Retrieves a list of all content items
ContentItemsListingResponse listingResponse = client.getItems();
```

## Response structure

For full description of single and multiple content item JSON response formats, see our [API reference](https://developer.kenticocloud.com/reference#response-structure).

### Single content item response

When retrieving a single content item, you get an instance of the `ContentItemResponse` class. This class represents the JSON response from the Delivery API endpoint and contains the requested `ContentItem` as a property.

### Multiple content items response

When retrieving a list of content items, you get an instance of the `ContentItemsListingResponse`. This class represents the JSON response from the Delivery API endpoint and contains:

* `getPagination()` returns a `Pagination` object with information about the following:
  * `getSkip()`: requested number of content items to skip
  * `getLimit()`: requested page size
  * `getCount()`: the total number of retrieved content items
  * `getNextPage()`: the URL of the next page
* A list of the requested content items

### ContentItem structure

The `ContentItem` class provides the following:

* `getSystem()` returna a `System` object with metadata such as code name, display name, type, or sitemap location.
* `getElements()` returns a Map containing all the elements included in the response keyed by code names.

## Getting content item properties

You can access information about a content item (i.e., its ID, codename, name, location in sitemap, date of last modification, and its content type codename) by using the `System` object.

```java
// Retrieves name of an article content item
articleItem.getSystem().getName()

// Retrieves codename of an article content item
articleItem.getSystem().getCodename()

// Retrieves name of the content type of an article content item
articleItem.getSystem().getType()
```

## Getting element values

The SDK provides methods for retrieving content from content elements such as Asset, Text, Rich Text, Multiple choice, etc.

## Further information

For more developer resources, visit the Kentico Cloud Developer Hub at <https://developer.kenticocloud.com>.

### Building the sources

Prerequisites:

**Required:**
Java 8 SDK (Oracle & OpenJDK both tested and supported)
