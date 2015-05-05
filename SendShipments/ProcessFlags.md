The Process element in the Shipment XML format accepts a number of keywords which impact the handling of your submission. You can specify multiple keywords by delimiting with the plus sign (+).

Current flag keywords:

* `FULL` - Shipment will be flagged as "customer processed". Username and password must be valid, and user must have access to create and process orders with the controller and bill-to specified.
* `NOW` - Shipment will be handled immediately and a tracking number will be provided.
* `LABEL` - Must correspond with `NOW`. Label printer code will be returned with shipment acknowledgment data. The default language is Datamax.
* `LABEL_DATAMAX` - Must correspond with `LABEL`. Indicates the label printer code will be in the Datamax label printing language.
* `LABEL_ZEBRA` - Must correspond with `LABEL`. Indicates the label printer code will be in the Zebra label printing language.
* `LABEL_EPL2` - Must correspond with `LABEL`. Indicates the label printer code will be in the EPL2 (some Zebra, formerly Eltron printers, especially those used by UPS and FedEx) label printing lanaguage.
* `LABEL_COGNITIVE` - Must correspond with `LABEL`. Indicates the label printer code will be in the Cognitive label printing language.
* `CHARGES` - Response will contain summary charge information (total charges). Must correspond with `NOW`. Username and password must be valid, and user must have access to create and process orders with the controller and bill-to specified.
* `NONAMESWAP` - The default behavior is to swap the Name and Contact fields of any address if the Name field is blank and the Contact field is not. (The system requires a non-blank Name to process a shipment, so it's convenient to the user to swap these values if the name has been placed in the Contact field instead of the Name field.) Specifying this flag suppress this default swapping behavior.
* `CLEANVIEW` - Only relevant if ShipmentFeeder.asp is used. Acknowledgment page will be formatted in a user-friendly manner. Must NOT correspond with `LABEL`.
* `BOLDRREDIR` - Only relevant if ShipmentFeeder.asp is used. Acknowledgment page will automatically redirect to shipment's Delivery Receipt. Must correspond with `NOW`. Must correspond with `FULL`. Must correspond with `CLEANVIEW`.
* `MAKERETURN` - We will create a Return shipment in the opposite direction of the original, linked as part of an Advance Exchange (AX) group.
* `CANCEL` - Shipment will be canceled. Shipment must not be processed, nor in transit. This flag will preempt most other flags. The MFSTextID or MFSID must be supplied. Username and password must be valid, and user must have access to create and process orders with the controller and bill-to specified.
* `DELAGENT` - Must correspond with `NOW`. Delivery Agent information will be returned with shipment acknolwedgement data. This will extend processing time as the system calculates and applies the appropriate routing. Username and Password security is enforced.
* `CUSTCARRIER` - Last Mile Style shipment will be updated with customer-controlled carrier tracking information. This flag will preempt most other flags. The MFSTextID or MFSID must be supplied. Username and password must be valid, and user must have access to create and process orders with the controller and bill-to specified.
* `INVENTORY` - Shipment will be integrated with inventory management system. Several other flags are incompatible with this, such as `FULL`, `NONAMESWAP`, `DELAGENT`, `CUSTCARRIER`, and `MAKERETURN`.
