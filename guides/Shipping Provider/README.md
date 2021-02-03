#asd
## GlossaryGlossary
- Carrier: It is the entity that represents the shipping company in Tiendanube. 
- Carrier option: Shipping service offered by a carrier. 
- Shipping rates: Set of shipping calculations that the carrier offers for each carrier option created in the store. 
- Merchant: Shop's owner. 
- Consumer: Person who buys in a store. 

## Introduction
This documentation provides an explanation of what a Shipping APP is and serves as a guide for the development process.
The construction process is divided into 5 steps:
1. **Initial setup** —> The first steps to create a shipping application 
○ Partner registration and APP creation
○ APP installation
○ Carrier creation
○ Creation of carrier options
2. **Shipping Rates ** —> Definition of business rules to perform shipment calculations. 
3. **Shipment management** —> Mechanisms to exchange information when a purchase occurs on Tiendanube.
○ Notification of new shipments
○ Get order data
○ Report the shipping code
○ Update shipment statuses
4. **Integration levels** —> Classification of the APP according to the functionalities developed by the partner.
5. **Testing Checklist **—> List to guide the functional test of the APP.


# Setting-upSetting-up
### Partner account and creation of the APP 
To interact with our APIs you have to create an APP. The APPs represent the products of our partners on our platform. Each APP has its own credentials, which are required to authenticate on our platform, as well as certain permissions to access the different scopes of our API. The steps to create a Shipping APP are as follows: 
1. If your company does not yet have an account on our partner portal, you must create one by entering the following link 
2. Once inside the partner portal, an APP must be created ensuring that the fields are completed with real information. How to set up an APP (Portuguese version)
3. It is important to pay close attention to the “Redirect URL” field, which is a key part of the APP installation process. Later it can be modified if necessary.
4. You will surely want to include a good description of the application and the services offered, this information will be displayed in our App Store and other sections of our platform. 
5. Ensure that the category of the APP is "Shipping". 
6. Very important: Once the APP is created, ensure that it has the following permission scopes enabled in the portal:
○ write_shipping 
○ Write_orders 
○ read_customers 

### APP installation
To obtain the authorization code. Use the following URL to start the installation flow in a store:
https://${store_name}.mitiendanube.com/admin/apps/${app_id}/authorize 
After the merchant approves the permissions, redirect the merchant to the URL redirect of the APP (defined in the configuration, along with the CODE in the URL)


### Get the access_token 


    curl --location --request POST 
    'https://www.tiendanube.com/apps/authorize/token' \ 
    --header 'Content-Type: application/x-www-form-urlencoded' \ --data-urlencode 'client_id=${client_id}' \ 
    --data-urlencode 'client_secret=${client_secret}' \ 
    --data-urlencode 'grant_type=authorization_code' \ 
    --data-urlencode 'code=${authorization_code}' 

###### Note 1: 
We use placeholders to: 
- ${code}: From the query string of the Redirect URL. 
- ${client_secret} 
- ${client_id} - 

###### Note 2: 
${client_id} y ${app_id} represent the same value. 

[More about authentication](https://github.com/tiendanube/api-docs/blob/master/resources/authentication.md "More about authentication")

### Creation of the Shipping Carrier
In order for stores to view, activate and configure the new shipping method, a Carrier must be created. The carrier is the entity that represents the shipping company in Tíanube and it is to which all services are added. Each integrator partner must generate only 1 (one) Shipping Carrier.
To create the carrier, the following information must be provided.
- name: The name of the logistics company.
- callback_url: It is the URL to which we are going to consult to obtain the shipping costs. 
- types: indicates if the carrier supports ship (home delivery) and / or pickup (delivery at pickup point). In case of being the options, place both separated with a comma.

###### Example: 
POST /shipping_carriers 

    { 
    "name": "My Shipping Company", 
    "callback_url": "https://example.com/rates", 
    "types": "ship,pickup" 
    } 
	
    HTTP/1.1 201 Created 
    
	{ 
    "id": 123, 
    "name": "My Shipping Company", 
    "active": true, 
    "callback_url": "https://example.com/rates", 
    "types": "ship,pickup", 
    "created_at": "2013-06-11T11:12:10-03:00", 
    "updated_at": "2013-06-11T11:12:10-03:00" 
    } 

[More on creating a carriers ](https://github.com/TiendaNube/api-docs/blob/master/resources/shipping_carrier.md#post-shipping_carriers "More on creating a carriers ")

### Create shipping services (carrier_option)
Once the carrier was created, the API returns the carrier's data set, among these is the ID of the carrier in Tiendanube. This ID should be used to create the shipping services, hereinafter carrier_option.
The carrier options are the shipping services (shipping and pickup) offered by the carrier. Each carrier option represents a service, or set of services with similar characteristics that the company offers.
These appear in the shipping carrier configuration and users will be able to make configurations such as: enable / disable the service, indicate if the service supports free shipping, add cost and / or days additional to the shipping rates obtained. 

###### Note 1: 
Each carrier option (service) must have a unique CODE. That means that there cannot be 2 carrier_option with the same CODE. 
###### Note 2: 
A carrier option will only be able to answer 1 rate per inquiry. Otherwise, only the first one will be taken.
###### Note 3: 
The configurations made by the user in Tiendanube should not affect the payload of the app. They will be applied exclusively by Tiedanube upon receiving the rates from the APP.
###### Note 4: 
If the shipping method adds more shipping options, to reflect them in the stores, they must create the corresponding carrier_options in the stores that are customers of the APP. And add it as a new carrier_option when installing the APP.
###### Note 5: 
The allow_free_shipping attribute must be created with the value False. The store owner will modify this configuration from the admin whenever he wishes.

###### Example: 
**POST /shipping_carriers/#{carrier_id}/options 
POST /shipping_carriers/123/options **



    { 
    "code": "standard", 
    "name": "Servicio de envío Estándar" 
    } 
	
    HTTP/1.1 201 Created 
    
    { 
    "id": 1, 
    "code": "standard", 
    "name": "Servicio de envío Estandard", 
    "additional_days": 0, 
    "additional_cost": 00.0, 
    "allow_free_shipping": false, 
    "active": true, 
    "created_at": "2013-04-12T10:15:10-03:00", 
    "updated_at": "2013-04-12T10:15:10-03:00" 
    } 
See more about [carrier options properties](https://github.com/TiendaNube/api-docs/blob/master/resources/shipping_carrier.md#shipping-carrier-options "carrier options properties") and [endpoints](https://github.com/TiendaNube/api-docs/blob/master/resources/shipping_carrier.md#post-shipping_carrierscarrier_idoptions "endpoints") 
Sequence diagram for creating an APP