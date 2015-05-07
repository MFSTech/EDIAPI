Send Us Shipments
=================

EDI (API) Documentation for the Standard, XML-Based, Customer Shipment HTTP Endpoint

Overview
--------

Manna Freight Systems, Inc. has in place an electronic data transmission system capable of accepting and responding to shipment requests in near real time. It receives data in XML format using the same communication technology as web pages. It is flexible, extensible, human-readable, easy to use through firewalls, and can be initiated on-demand rather than merely at specified intervals.

This is intended for our customers to send us the information required for us move product for them. We call this movement a shipment. The basic requirements for a shipment are the origin, destination, speed, level of service, and some information about what we are moving (i.e. the pieces).

We are configured to receive this data as specially-formatted XML in a standard HTTP POST. However, it is not the same as a POST from an HTML page (that puts data in URL format-- strictly speaking, Internet media type "application/x-www-form-urlencoded"-- not XML). You must directly control the content of the POST (e.g. using a toolset such as Microsoft's XML over HTTP-- MSXML2.ServerXMLHTTP).

The body of the HTTP response will be an XML acknowledgment, except in cases of unhandled errors or system failures. The same XML elements regardless of the success or failure of the transmission, though some elements are only populated in some situations.

* The status code of the HTTP response to a successful POST will be either 200 or 202, depending on whether you choose to process the shipment information synchronously (immediately) or asynchronously (after the submission, for submission performance purposes). The choice is controlled using flags in your XML submission (see [ProcessFlags.md](ProcessFlags.md)).
	* If you choose to process the submission synchronously, the status code will be 200, and the response will contain tracking numbers and other optional information.
	* If you choose to process the submission asynchronously, the status code will be 202, and you will receive only a basic acknowledgment indicating that the submission passed basic (fast) syntactical checks. In that case, further information can optionally be sent to you via POST to a web resource (a mirror of your submission process). If you choose to process the submission synchronously, the response will contain tracking numbers and other optional information. Few customers choose this method. If you choose this method:
		* You must establish an HTTP endpoint of your own to accept these responses.
		* You will not know the true success or failure of your transmission until you receive the response transmission from us.
* In any case, if you get a response other than 200 or 202, likely something was wrong with your request or our servers. In anything but a serious error, you should get some information back in the XML which will tell you what was wrong with your request. Sometimes, there is something wrong with your shipment information which prevents it from fully processing in our system, but it is sufficient to create a shipment record. In this case, you will get a 200 status and more complete XML information, but there would still be some indication of the problem in the XML variables.

Note that the XML spec presumes that you will use certain codes for service requests and product types, among other things. We will be able to provide you those codes once we verify with our sales or operations departments which are applicable to your relationship with us.

We will update the XML system from time to time but the goal is to maintain as much backward compatibility as possible.

If you experience problems using our XML system you contact our Help Desk at 651-994-7535 or email Support@MFSCorporate.com.

Endpoint Addresses
------------------

All URLs which have a host name of "demo" are for testing. They are served by a sandbox system which can be but is not generally accessed by our operations staff. We recommend that you begin your development using these test URLs. New customers are not created immediately in this system, though do get migrated over time. You should coordinate with us to ensure your customer information is configured in the new system.

All URLs which have a host name of "EDI" are for production. Unless you specially coordinate with us in advance, if you send shipment data to these addresses and it is successfully processed, we will act on the shipment and you will be subject to charges. Using fake names, addresses, or labeling it as "test" are insufficient protection. We have many automated processes which will not recognize the shipment as a test.

All URLs are case insensitive.

### Transmission Testing Addresses

On either the test or production servers, you can test the transmission process without worrying about the XML content by using these URLs:

* Test: [http://demo.MFSClarity.com/EDI/XMLInTest.asp](http://demo.MFSClarity.com/EDI/XMLInTest.asp)
* Production: [http://EDI.MFSClarity.com/EDI/XMLInTest.asp](http://EDI.MFSClarity.com/EDI/XMLInTest.asp)

They gather XML from the HTTP POST body and echo it back out as part of a simplified XML acknowledgment.

### Content Addresses

These URLs receive shipment data in the official XML format and create shipments in our system.
* Test: [http://demo.MFSClarity.com/EDI/Cust/Shipment.asp](http://demo.MFSClarity.com/EDI/Cust/Shipment.asp)
* Production: [http://EDI.MFSClarity.com/EDI/Cust/Shipment.asp](http://EDI.MFSClarity.com/EDI/Cust/Shipment.asp)

### Feeder Addresses

If you have sample XML you wish to test without sending it through your system as an actual POST, you can use our testing "feeder" harness to send XML to the content or transmission testing pages.

* Transmission Test, Test: [http://demo.MFSClarity.com/EDI/XMLInTestFeeder.asp](http://demo.MFSClarity.com/EDI/XMLInTestFeeder.asp)
* Transmission Test, Production: [http://EDI.MFSClarity.com/EDI/XMLInTestFeeder.asp](http://EDI.MFSClarity.com/EDI/XMLInTestFeeder.asp)
* Content, Test: [http://demo.MFSClarity.com/EDI/Cust/ShipmentFeeder.asp](http://demo.MFSClarity.com/EDI/Cust/ShipmentFeeder.asp)
* Content, Production: [http://EDI.MFSClarity.com/EDI/Cust/ShipmentFeeder.asp](http://EDI.MFSClarity.com/EDI/Cust/ShipmentFeeder.asp)

### Clarity Addresses

Our customers often use our Clarity web application to manually enter shipment requests and track shipments. Each system, test and production, has a connected Clarity web application which can be used to review your EDI submissions. It can also be used to adjust your credentials (username and password).

* Test: [http://demo.MFSClarity.com/Clarity/](http://demo.MFSClarity.com/Clarity/)
* Production: [http://www.MFSClarity.com/Clarity/](http://www.MFSClarity.com/Clarity/)

Key Files
---------

* [Shipment.dtd](Shipment.dtd) - The Document Type Definition which defines the XML format for the shipment transmission (sent to Shipment.asp). This is also hosted next to the EDI processing end point. For example, it is located at these locations:
	* [http://demo.MFSClarity.com/EDI/Cust/Shipment.dtd](http://demo.MFSClarity.com/EDI/Cust/Shipment.dtd)
	* [http://EDI.MFSClarity.com/EDI/Cust/Shipment.dtd](http://EDI.MFSClarity.com/EDI/Cust/Shipment.dtd)
* [Shipment.xml](Shipment.xml) - A sample submission of a new shipment request.
* [Shipment-Commented.xml](Shipment-Commented.xml) - A sample submission of a new shipment request, with comments on the various elements. 
* [ShipAck.xml](ShipAck.xml) - A sample response to a successful transmission.
* [ShipAck-Commented.xml](ShipAck-Commented.xml) - A sample response to a successful transmission, with comments on the various elements.
* [ProcessFlags.md](ProcessFlags.md) - A list of all keywords which can be used in the `Process` element

Special Cases
-------------

### Cancellation

The standard DTD and format is designed for new and updated shipment information. When canceling a shipment request, much less information is required, and the standard DTD would be too onerous. A revised DTD and associated example is provided here:

* [ShipmentCancel.dtd](ShipmentCancel.dtd) - Specifying the DTD is optional.
* [ShipmentCancel.xml](ShipmentCancel.xml) - Sample

### Carrier Update

One type of service we provide involves the customer transporting the product to our "last mile" provider, which then makes the final delivery. For this style of shipment, the customer is required to specify the tracking information for the other carrier which will be delivering the product to our provider. Because this is a small subset of shipment information, we created a separate, simplified format.

* [ShipmentCarrierUpdate.dtd](ShipmentCarrierUpdate.dtd) - Specifying the DTD is optional.
* [ShipmentCarrierUpdate.xml](ShipmentCarrierUpdate.xml) - Sample

Helpful Hints
-------------

* After this document, [Shipment-Commented.xml](Shipment-Commented.xml) should be the next document you review. 
* If you get a 202 status code, you don't know whether you have really been successful.
* You probably want to send `NOW` in the `Process` element to engage synchronous processing and avoid getting a 202 status code.
* Even when you don't get a 200 or 202 status code, you should still mine the response for the XML, as it will usually contain information in the ParseErrors section which explain what went wrong.
* If you navigate directly to the endpoint URLs, you will receive an XML response which says, among other things, "XML document must have a top level element.". That is because you have not POSTed any XML.
* You cannot pass the endpoint your XML in a named parameter as you would with a normal web form. (You can pass the XML as a parameter named XML to the "feeder" pages, but that is intended only for testing and is not supported for automated use.)
* Having trouble getting a success out of our system? See if it's your transmission mechanism or the XML itself by posting the XML into our "feeder" page (see above).
* The sample files won't necessarily process without error. For example, if you put `NOW` into the `Process` element of the sample, you will get an error because `FULL` is not specified though the `ScheduleDate` element is populated. If you resolve that issue, you will often find other errors, such as the control number type "CustomControlNumber" not existing. The sample is designed to show a variety of features of the XML format; it is not designed to be a simple, successful transmission.
* Theoretically, you could begin using this system without assistance from us using your existing Clarity credentials. There are no special authorizations or settings required. However, you will likely need our help to make sure your customer information is configured in the sandbox environment and for the lists of codes required to represent service types and product types (for example).

Technical Notes
---------------

* You do not need to specify a content-type. It is not enforced.
* The DTD reference is optional. When specified, our server will use the DTD to validate the XML and reject it if invalid (even if our lower level systems could still manage to interpret it).
* The order of XML elements is irrelevant once the XML makes its way through the DTD-based validation (or if you simply omit the DTD reference).
* The format of the acknowledgment XML sent in the body of the HTTP response will evolve. We will strive never to remove or alter existing elements-- and indeed have not to date-- but we have and will continue to add new elements.
* We won't act on a shipment unless it's fully processed, which requires you to send `FULL` in the `Process` element (see also [ProcessFlags.md](ProcessFlags.md)).
* You can only modify shipments until they are fully processed. You cannot cancel nor can you edit a shipment once it has been fully processed.
