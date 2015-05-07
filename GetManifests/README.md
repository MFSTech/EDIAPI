GetManifests
============

EDI (API) Documentation for the Standard, XML-Based, Vendor Shipment Data Transfer

Overview
--------

Manna Freight Systems, Inc. can be configured to generate specially-formatted XML documents which contain shipment information for our suppliers. This information can be transferred to an HTTP endpoint or put into a file which can be transferred via FTP-- either uploaded to an FTP server hosted by the supplier or made available for download on an FTP server hosted by us.

We recommend beginning the integration by creating a system to receive our transmissions and then requesting that we enable them. You would start to collect real-world examples for your development and testing. Make your request by emailing Support@MFSCorporate.com.

Transmission
------------

The transmissions are generated by our system based on various triggers. Each transmission contains the complete shipment information as of the time of generation. The system does not filter out transmissions which would be practically identical to previous transmissions. The triggering mechanism also does not ensure that a change actually took place-- rather, it triggers based on an activity which might result in a change of shipment information. Also, the system does not, by default, stop transmissions for a particular shipment based on its life cycle. It will continue to send transmissions for a shipment whenever relevant activity occurs, even if the shipment has long since been completed.

When the data is transferred via HTTP, the XML itself will appear in the body of an HTTP POST request. It will not be like a normal web form POST from a browser, which would have name-value pairs with a media type of `application/x-www-form-urlencoded`. Rather, it will be raw XML, with an effective media type of `application/xml`.

When the data is transferred via HTTP, our system can be configured to expect an HTTP response with specially-formatted XML content which describes whether your interpretation of our transmission was successful. This allows our system to inform our operations staff if there is a problem with you receiving the information. 

Business Concepts
-----------------

When our customers request that we move product (see also [SendUsShipments](/SendUsShipments)), we create a new shipment in our system. (That shipment is also called a Bill of Lading.) We then aggregate these shipments into groups which are appropriate for a single engagement with a supplier. We call that grouping a manifest. From a contractual or legal perspective, we relate to our suppliers at that aggregate level. We expect to be charged in aggregate, and we expect that the entire group move together and be treated as a single set of pieces. In effect, officially, it is not a group at all but simply a larger "shipment".

That said, the operational reality is that it is still a group, and it's valuable for our suppliers to understand the nature of the group-- to have information about the shipments inside the manifest. Toward that end, our XML format includes both aggregate, manifest-level information and information about each shipment within the manifest. The most important piece of shipment-level information is the shipment identifier (ID). It is also known as the tracking number or BOL number. It's generally the number our customers use to refer to the shipment, and it's the number generally used by suppliers when interacting with shippers and consignees.

Example Files
-------------

* [Sample Outbound Transmission](ManifestXMLOutbound-Sample.xml) (From Us to You)
* Sample Response (You Responding to Us)
 * [Success](ManifestXMLOutbound-Response-Success-Sample.xml)
 * [Failure](ManifestXMLOutbound-Response-Failure-Sample.xml) 

Transmission Content
--------------------

The root XML element is `ManifestTransmission`; it contains header information and the manifest information itself. Each transmission will contain information about *only one* manifest.

### Header Information

The header elements are grouped into a header element, but rather hang off the root. They consist of:

* `TransmissionID` - A unique integer for the current transmission. Multiple transmissions of the same manifest information would each have a different transmission ID. The value is unique across all of our transmissions, not just transmissions for a particular supplier. This value is useful for debugging transmission problems.
* `TransmissionGeneratedUTC` and `TransmissionGeneratedLocal` - The date the transmission was generated in UTC and our time (US Central time zone, where daylight savings is observed).
* `Authentication` - This section can be populated with a `UserName` and `Password` of your choosing. This could be used by you to authenticate our transmission. Instead of a password, a `PasswordHash` can be sent, which is a dynamically salted MD5 or SHA1 hash of this constructed value:
	> `UserName` + `|` + `TransmissionID` + `|` + `TransmissionGeneratedUTC` + `|` + `Password`

### Manifest Information

The manifest information is hangs off the root inside of `Manifest` and is broken into various sections:

* `ManifestInfo`
* `ManifestControlNumbers`
* `ManifestKeyDates`
* `ManifestOrigin`
* `ManifestDestination`
* `ManifestPieceSummary`
* `Shipments`

#### ManifestInfo

* `ManifestVendorID` - This is our primary, unique textual identifier for the company responsible for the manifest. The identifier is only unique for a particular Vendor Type.
* `ManifestVendorType` - We generally categorize our suppliers as either a "Carrier" or "Agent". A MAWB must have a "Carrier" as the responsible company. Carriers are generally used to move freight from one market to another (often called a "line haul"). An "Agent" typically operates within a market and is the responsible entity on Pickup Manifests, Delivery Manifests, and most Transfer Manifests (though a Carrier can be the responsible company on a Transfer Manifest).
* `ManifestType` - The primary Manifest Types are "Pickup", "Delivery", "MAWB", and "Transfer".
	 * A Pickup Manifest involves our supplier retrieving products from our customer's designated shipping location and then transferring that product to another supplier, typically the Carrier on one or more MAWBs. Usually, all of the shipments on the Pickup Manifest will belong to the same customer of ours, though it is possible that a variety of our customers might have shipments originating at the same distribution center or "drop shipper". While a Pickup manifest should always have a single origin location, various groups of the underlying shipments might have different Carriers for their next manifest. Therefore, the Pickup Manifest supplier (a.k.a. "Pickup Agent") might need to drop groups of the products at multiple Carriers. Unfortunately, this XML EDI system does not provide detail beyond the first drop location at this time.
	 * A MAWB can be thought of as a Line-Haul Manifest. The term MAWB stands for Master Air WayBill which is how the air freight industry typically refers to shipments of cargo on air carriers. Typically, Carriers receive shipments at their terminals (which is usually at or near an airport and therefore represented by 3-character IATA airport codes such as MSP) and hold them for pickup at the destination terminal. Some carriers perform more complete service, retrieving product from or delivering product to a location. Whether the carrier is bound to their terminals or travel to an address to pickup or drop off the orders varies by MAWB and is indicated in the `ManifestOrigin\ManifestOriginType` and `ManifestDestination\ManifestDestType` elements.
	 * A Delivery Manifest typically involves our supplier retrieving products from a carrier terminal and dropping products off at our customer's designated destination location; that destination location is known as the BOL Consignee. The vast majority of Delivery Manifests are of a single shipment; they are often destined for a residence. However, some are "returns", that is, a collection of shipments of damaged or broken products being "returned" to our customer's warehouse. In those cases, multiple shipments will be combined onto the same Delivery Manifest, and often they will need to be retrieved from multiple carriers. Unfortunately, this XML EDI system does not provide detail beyond the first retrieval location at this time.
	 * A Transfer Manifest is a designed to accommodate those supplier engagements which do not fit one of the primary patterns (Pickup, MAWB, Delivery). The responsible company is permitted to be either an Agent or Carrier. It could represent a responsible company moving product from one supplier to another, or it could involve performing an installation or assembly service at a residence. Unfortunately because of the wide variety of use cases, the XML EDI system probably will not provide sufficient detail on the handling of these manifests at this time.
* `ManifestSpecialSide` - Many manifests involve simply moving product from one business with a dock to another. However, Delivery Manifests (especially) often involve special services such as "White Glove" treatment (where the supplier brings product inside a residence, often with two people, unpacks the product, and removes the shipping materials). For Delivery Manifests, this should always be on the destination side. For Pickup Manifests, this should always be on the origin side. For MAWBs, it could be the origin or destination. It is not a well-defined concept for Transfer Manifests. If a special side is indicated on a MAWB, that side should not be an `ManifestOriginType` or `ManifestDestinationType` of Terminal; rather, it should be address. Internally, Pickup manifests where the special side is on the origin and Delivery manifests where the special side is on the destination are referred to as the "operative" shipment segments.
* `ManifestShipType` - This is the textual ID of the special services to be performed on the side specified in `ManifestSpecialSide`. Examples include "WG" for "White Glove" and "TP" for "Threshold Plus". Additional services required will be listed in the `ExtraServices` element under the `Shipment` element referenced below.
* `ManifestShipTypeName` - This is the textual name related to the special services ID specified in `ManifestShipType`.
* `ManifestShipTypeBilling` - Not all suppliers have defined rates for all special services. This billing description text is designed to explain the special services in more standardized terms for which the supplier is more likely to have rates.
* `ManifestShipTypeDesc` - This is a more lengthy description of the special services to be performed on the side specified in `ManifestSpecialSide`.
* `ManifestSpecInst` - These are custom instructions from our operations staff to our suppliers.

#### ManifestControlNumbers

* `ManifestID` - This is the textual transaction identifier for the manifest in our system.
 * In the case of a MAWB, it is referred to as the "MAWB number". For Pickups, Deliveries, and Transfers, it is generally referred to as the "PRO Number". 
 * The intent of this field is to house the supplier's primary reference number for the manifest. This is not generally known at time of manifest creation, so a default value is constructed. The default is a letter ("P" for a Pickup, "M" for a MAWB, "D" for a Delivery, and "T" for a Transfer) followed by the numeric transaction identity (see `ManifestTranID`).
 * If the supplier provides this number, it replaces the default number in our system and in future transmissions. The supplier can provide the number in the response to this transmission-- in the `VendorManifestControlNumber` element-- and the system will perform the replacement automatically. Alternatively, the supplier could communicate the value for our Ops staff to manually update.
 * For some Carrier suppliers, we have secured ranges of valid numbers to use for this identifier on their MAWB manifests. We draw from this pool upon creation of the manifest, so even the earliest transmissions of that MAWB manifest will contain the valid, pre-assigned supplier reference number. 
 * In order for any transmission to us of [status](https://github.com/MFSTech/SendStatuses/) or [invoice](https://github.com/MFSTech/SendInvoices/) information to succeed, it must contain a `ManifestID` or `ManifestTranID` which matches a manifest transaction to which the transmitter is authorized.
* `ManifestTranID` - This is the unique numeric transaction identity for the manifest in our system. It is inviolate for a particular transaction, unlike the textual `ManifestID`.  
* `ManifestSecCode` - This is a code used for primitive security, primarily for document retrieval. For example, in order to access some printable representations of manifest information, the security code must be provided in the URL. They are typically a four-digit random number which does not usually change through the live of a manifest. The security system is designed for prevention of casual snooping of other suppliers' documents simply by trying other `ManifestTranID` values in the document retrieval URLs.

#### ManifestKeyDates

* `ManifestReadyDateTime`
 * For MAWB manifests with a `ManifestOriginType` of "Terminal", the Ready Date is the date and time we expect the shipment to be available for the carrier to begin transportation; internally, we refer to this as the ETD (estimated time of departure). We use that date together with the carrier's transit time matrix (if we have it in our system) to determine the Deliver Date, which we refer to internally as the ETA (estimated time of arrival).
 * For other manifests, the Ready Date has less official meaning. It is labeled as "Open Time" in our system and was originally intended to store the start of the allowable window for the agent supplier to perform the pickup or delivery. It defaults to the morning of the shipment's ready date for Pickup manifests and the morning of the shipment's delivery date (the date by which we are obligated to our customer to deliver the freight) for Delivery manifests. When the manifest is the "operative segment" (that is, a Pickup manifest with a "Pickup" `ManifestSpecialSide` or a Delivery manifest with a "Delivery" `ManifestSpecialSide`) and an associated shipment is scheduled, this date and time will be set to the start of the scheduled window.
* `ManifestDeliverDateTime`
 * For MAWB manifests with a `ManifestDestinationType` of "Terminal", the Deliver Date is the date and time we expect the carrier to have made the shipment available for the next supplier; internally, we refer to this as the ETA (estimated time of arrival).
 * For other manifests, the Deliver Date has less official meaning. It is labeled as "Close Time" in our system and was originally intended to store the end of the allowable window for the agent supplier to perform the pickup or delivery. It defaults to the evening of the shipment's ready date for Pickup manifests and the evening of the shipment's delivery date (the date by which we are obligated to our customer to deliver the freight) for Delivery manifests. When the manifest is the "operative segment" (that is, a Pickup manifest with a "Pickup" `ManifestSpecialSide` or a Delivery manifest with a "Delivery" `ManifestSpecialSide`) and an associated shipment is scheduled, this date and time will be set to the end of the scheduled window.
* `ManifestScheduleDateTimeStart` - For a manifest with a specified `ManifestSpecialSide`, the represents the start of the allowable window for performance of the services identified by `ManifestShipType`. 
* `ManifestScheduleDateTimeStop` - For a manifest with a specified `ManifestSpecialSide`, the represents the start of the allowable window for performance of the services identified by `ManifestShipType`.

#### ManifestOrigin

Most of these elements are self-explanatory.

* `ManifestOriginType` - This is either "Terminal" or "Address". "Terminal" is only used for MAWB manifests. When the carrier receives the manifest at their origin terminal rather than retrieving it from an address, the "Terminal" origin type is used. Similarly, when the carrier holds the manifest for pickup at their destination terminal, rather than delivering it to an address, the "Terminal" destination type is used.
* `ManifestOriginCode` - When the `ManifestOriginType` or `ManifestDestType` is "Terminal", this will contain the terminal's code (often an airport code such as "MSP"). When the `ManifestType` is "Pickup" this will be the terminal code for the origin of the adjoining MAWB. When the `ManifestType` is "Delivery", this will be the terminal code for the destination of the adjoining MAWB.
* `ManifestOriginLocationType` - This is one of an enumerated list of location types.
 * Business
 * Convention
 * Hospital
 * Hotel
 * MilitaryBase
 * Residential
 * School
* `ManifestOriginName` - This is the primary name. It could be a business name or an individual's name. It generally should not be blank. The system will automatically move the contact name to the primary name if the primary name is left blank and the contact name is non-blank. Occasionally, customers will enter the same name in the primary and contact name fields. 
* `ManifestOriginContact`
* `ManifestOriginAdd1`
* `ManifestOriginAdd2`
* `ManifestOriginCity`
* `ManifestOriginState`
* `ManifestOriginRegion`
* `ManifestOriginZIP`
* `ManifestOriginCountryCode`
* `ManifestOriginPhone`
* `ManifestOriginFax`
* `ManifestOriginHomePhone`
* `ManifestOriginOtherPhone`

#### ManifestDestination

Most of these elements are self-explanatory. See the analogous origin element above for any special notes.

* `ManifestDestType`
* `ManifestDestCode`
* `ManifestDescLocationType`
* `ManifestDestName`
* `ManifestDestContact`
* `ManifestDestAdd1`
* `ManifestDestAdd2`
* `ManifestDestCity`
* `ManifestDestState`
* `ManifestDestRegion`
* `ManifestDestZIP`
* `ManifestDestCountryCode`
* `ManifestDestPhone`
* `ManifestDestFax`
* `ManifestDestHomePhone`
* `ManifestDestOtherPhone`

#### ManifestPieceSummary

* `ManifestPieceCount` - This represents the total number of pieces on the manifest as a whole (across all of our customers' shipments).
 * While seemingly obvious, it isn't always clear what constitutes a piece. Generally, we view it as a separable, labeled box. Several boxes piled on (but not secured to) a pallet are generally considered separate pieces. However, if they are banded to a pallet and surrounded with black shrink wrap, then the pallet itself is considered a single piece. It's less clear for configurations in between.
 * Usually, this will be the sum of the number of pieces listed in each of our customers' shipments. However, sometimes the freight is reconfigured (e.g. banded to pallets) and shipped as larger, bulk units instead of the individual pieces supplied by the customer. In those cases, the adjusted piece count (known internally as "override pieces") will be displayed here. 
* `ManifestWeightActual` - This represents the total actual weight of all pieces on the manifest. It is not the "dimensional weight".

#### Shipments

This element contains one or more `Shipment` elements for each of our customers' shipments on the manifest.

##### ControlNumbers

* `ShipmentID` - This is the primary textual identifier for a customer shipment. Duplicates in our system are discouraged but not impossible. This can change over time for an individual shipment, though that is quite rare. It is also known as the BOL (Bill of Lading) number and the tracking number. As a shorthand, for manifests with only a single BOL, our operations staff will generally refer to the manifest using the `ShipmentID`.  
* `ShipmentTranID` - This is the unique numeric transaction identity for the shipment in our system. It is inviolate for a particular transaction, unlike the textual `ShipmentID`. It is in the same numeric scope as `ManifestTranID` values, meaning no `ShipmentTranID` can be used as a `ManifestTranID`, and vice versa.
* `Ref` - Reference Number. This is one of four primary textual control numbers customers are allowed to specify. It is merely informational.
* `PO` - Purchase Order Number. This is one of four primary textual control numbers customers are allowed to specify. It is merely informational.
* `RMA` - Return Merchandise Authorization Number. This is one of four primary textual control numbers customers are allowed to specify. It is merely informational.
* `SO` - Sales Order Number. This is one of four primary textual control numbers customers are allowed to specify. It is merely informational.
* `ShipperManifest` - A shipper might place a group of shipments together on a Shipper Manifest. Each shipment will share the same `ShipperManifest` number. This element is only present if authorized.

##### Pieces

One or more `Piece` elements with the following information.

A `Piece` represents a piece record in our system. Typically, multiple, like pieces are grouped together in a single piece record, though that is not required. Therefore, a shipment might have multiple pieces but only one piece line. A shipment with two different types of pieces (TVs and stands) will usually have multiple piece lines.

* `PieceID` - This is the unique numeric identity for the piece line in our shipment. It would allow you to specify updated dimension information for a particular piece. However, there is no correlation between this number and a physical piece (it doesn't appear on any label), so that system is not ready for use.
* `PieceCount` - If there are multiple pieces of the type represented by this piece line, that will be represented here by a quantity greater than 1. A quantity of 0 indicates that a piece has been converted into a pallet with other pieces (the pallet being represented by a different piece line). 
* `PieceLength` - The length, in inches, of a a piece represented by this line.
* `PieceWidth` - The width, in inches, of a a piece represented by this line.
* `PieceHeight` - The height, in inches, of a a piece represented by this line.
* `PieceWeightActual` - The weight, in pounds, of a piece represented by this line. This is the *per piece* weight. A piece line with 5 TVs weighing 100 pounds each would have the value 100 in this element. 
* `PieceDecVal` - The value, in US dollars, of a piece represented by this line. This is the *per piece* value. A piece line with 5 TVs valued at $1000 each would have the value 1000 in this element. This element is only present with special authorization.
* `ProdType` - One of a list of official product type IDs. Technically, these vary by customer, though there is general consistency across customers. Examples include TV product types (such as "PDP-042", "LED-050", which stand for 42" Plasma TV and 50" LED LCD TV, respectively); mattress sizes (such as "Queen-M"); and generally classifications (such as "Case" and "Soft" for "Case goods" and "Soft goods", in the context of furniture). 
* `ProdID` - This is a customer-specified product ID which is often a model or SKU number.
* `ProdName` - This is a customer-specified product name.
* `PieceCommodity` - This is one of an specific list of commodity types. This element is available upon request.
 * Appliances
 * Exercise Equipment
 * Furniture
 * Mattress/Foundation
 * Misc Freight
 * Other
 * TV
 * TV Accessories
* `PieceSubCommodity` - This is one of a specific list of commodity types, with a different list for each commodity type. Examples include "Range" and "Grill" for "Appliances"; "LCD" and "Plasma" for "TV"; "Bookcase" and "Buffet" for "Furniture"; and "Mattress" and "Boxspring" for "Mattress/Foundation". This element is available upon request.
* `PieceNMFC` - The National Motor Freight Classification for the pieces represented by this line, if known. This is mostly for future expansion as it is rarely populated. This element is available upon request. 

##### Other Elements

* `SpecInst` - Special instructions provided by the customer such as "beware of dog".
* `PkgDesc` - Package description provided by the customer such as "dining room set with table and chairs". 

#### ExtraServices

This element falls under the `Manifest` element. All of the extra services required by the various customer shipment requests are aggregated into a single, unified manifest list of "extra services" such as Deluxing (where the provider removes packaging, inspects contents, and ensures product is pristine for delivery). The `ExtraServices` element has one or more `ExtraService` elements beneath it with the below contents.

* `ExtraServiceTypeID` - A textual identifier for the extra service. (e.g. "Deluxing" for Deluxing)
* `ExtraServiceTypeName` - A name for the required extra service. 
* `ExtraServiceTypeDesc` - A description of the required extra service. (e.g. "At Local Delivery Carrier location, remove product from packaging, inspect and process the product so that it is delivered in a pristine condition.")

Response Content
----------------

When using synchronous HTTP transmission as a transport, we recommend responding with XML as defined below. We are able to mine this response for errors and your preferred control number. This is most useful when you synchronously, fully process the transmission-- that is, you actually create shipment records in your system-- so we confirm a semantic comprehension rather than a mere receipt acknowledgment.

At this time, we do not support response handling for asynchronous transport such as FTP.

The root element is `ManifestTransmissionResponse`.

* `MFSTransmissionID` - This should be used to echo back our transmission ID. This would not be strictly necessary for synchronous communication, as it must correspond with the active transmission, but we include this as an extra check for validity and for future expansion to asynchronous processing.  
* `VendorTransmissionID` - This should be a unique number you create for this transmission-- not for the shipment we are transmitting (as we might transmit the same shipment multiple times in the case of updates). This number is valuable for troubleshooting the result of particular transmissions.
* `ResponseGeneratedUTC` - This is a time stamp in UTC time.
* `ResponseGeneratedLocal` - This is a time stamp in your local time.
* `VendorManifestControlNumber` - This should be used for your preferred primary identifier for this shipment. We will set our `ManifestID` to this value and use it in future update transmissions. 
* `ErrorID` - This should contain a numeric representation of any processing error. It is not currently used, but for future expansion, we envision it to be used to classify problems for better reporting and potential automatic remediation. 
* `ErrorText` - This should be human comprehensible text describing any issues you had interpreting or handling the data we provided. It is made available for our operations staff and can be used by them to fix any data issues for future transmissions. 

Miscellany
----------

* Vendor and Supplier are used interchangeably in this documentation. Generally, the terms refer to any company which we pay to provide us service. Typically, they are transportation providers (truck fleet operators) or assembly and installation service providers.
* The term "element" is used to refer to an XML node.
* The presence of elements which are "available upon request" or otherwise restricted are controlled by EDI configuration settings which should be coordinated with our technical staff. 