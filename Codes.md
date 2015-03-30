MFS EDI API Codes
=================

Many of our [EDI API formats](README.md) rely on particular codes which represent one of various options. Examples include the service type (speed in transit) and the shipment type (bundle of services). 

Characteristics
---------------

### Service Types

* Service Types are used to select the speed in transit.
* Examples:
	* "R" - "Ground"
	* "1" - "Overnight"
	* "2" - "Second Day"
	* "3" - "Three-Day Service"

### Shipment Types

* Shipment Types are used to select the bundle of services to perform on the special side of the shipment (typically the delivery side).
* Examples:
	* "WG" - "White Glove"
		* This includes placement of a product in a home, including the removal of packaging. 
	* "TP" - "Threshold Plus"
		* Placement of product just across a home's threshold.
* URL to retrieve all current shipment type options:
	* [http://edi.mfsclarity.com/edi/xmlin.aspx?Process=GetShipmentTypes](http://edi.mfsclarity.com/edi/xmlin.aspx?Process=GetShipmentTypes)

Events
------

### Status Types

* Status Types are generally used to represent physical activity related to freight movement.
* Examples:
	* "Pickup" - "Proof of Pickup"
	* "Delivery" - "Proof of Delivery"
* URL to retrieve all current status types:
	* [http://edi.mfsclarity.com/EDI/XMLIn.aspx?Process=GetStatusTypes](http://edi.mfsclarity.com/EDI/XMLIn.aspx?Process=GetStatusTypes)

### Service Log Types

* Service Log Types, also known as Exception types, are generally used to represent unusual events which have occurred, such as a weather delay.
* Examples:
	*  "D0" - "MISC MANNA FAULT 1 DAY LATE - SVC FAILURE"
	*  "Z03" - "CUSTOMER REQUESTS DELIVERY AT LATER TIME"
	*  "D6" - "DAMAGED FREIGHT - OSD"
	*  "Z30" - "Compliance Call Completed"
*  URL to retrieve all current service log types:
	*  [http://edi.mfsclarity.com/edi/xmlin.aspx?Process=GetServiceLogTypes](http://edi.mfsclarity.com/edi/xmlin.aspx?Process=GetServiceLogTypes)