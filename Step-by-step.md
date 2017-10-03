# Abstract
BLOCKv’s protocol augments blockchains like Bitcoin, Ethereum, EOS and others with powerful, revenue-generating consumer-facing experiences, such as interactive 2D and 3D graphics, sounds, music, animation, location, combinability, retail redemption, and real-time state changes. BLOCKv enables the rapid development and distribution of interactive smart objects -- “experiential currency” called vAtoms. 

This article will describe, at a technical level, how a developer would create vAtoms and abstract blockchain networks for the purpose of building applications.

# vAtoms, Template Variations and Templates

The BLOCKv platform is built around the concept of vAtoms. Each vAtom is a virtual object that is ‘emitted’ from a template variation. It helps to think of a vAtom as an artist’s silkscreen print (e.g. 34 of 100) which is collectable, rare, unchanging and authentic. 

To help understand Templates and Template Variations, one can extend the silkscreen analogy: A Template is like the screen used to create the artist’s print and the Template Variation is the set of colors that are used with the screen to make the prints. 

Putting the analogy aside, the vAtom inherits the properties and behaviors of the Template Variation and Template including ‘Actions’ and ‘Faces’.

Actions are behaviors ascribed to a vAtom such as ‘Drop’, ‘Pickup’, ‘Acquire’, ‘Transfer’ that allow it to interact with users and other vAtoms.

Faces are used to display the vAtom on a specific device or platform by referencing the appropriate code.

# The BLOCKv APIs

The BLOCKv APIs allow you to create and manipulate vAtoms on the platform. There are currently 2 different sets of APIs:

* The BLOCKv Viewer API - this API is typically used by front-end devices that cater to the end-user. The API allows you to build a wallet app, register users, manipulate and view vAtoms, call Actions and interact with other users.
* The BLOCKv Publisher API - this API is used by vAtom creators (e.g. Advertising Agencies) to create the Templates, Template Variations and vAtoms.

You can find the API documentation at XXX.

# Publishing vAtoms on Blockchain

The power of BLOCKv lies in allowing developers to specify a relationship between vAtoms and a blockchain network in the Template and Template Variation. Each vAtom implements this behavior and the platform executes all the complex transactions on the blockchain network transparently for the developer.
The easiest way to start creating vAtoms is to leverage an existing Template in the system. There are several public Templates such as:

* 3D Object
* Image
* Media Player
* Web View

Each of these templates (and many others) are already supported by the SDK for iOS and Android. Nothing is stopping you from creating your own Templates that are unique to your own custom viewer, but using an existing viewer will help you get started.

### 1) First log in with your user account:

```
curl --request POST \
  --url 'https://{{BLOCKv-Server}}/api/v1/user/login' \
  --header 'X-BLOCKv-App-Id: {{App-ID}}' \
  --data '{
  "email" : "admin@vatom_publisher.com",
  "password": "mysecret"
}'
```

### 2) Retrieve the Image Template:

```
curl --request GET \
  --url 'https://{{BLOCKv-Server}}/spec/template/blockv.templates::Image::v1'
```

### 3) To use the Template on Ethereum, define the properties for the smart contract in the “eth” section shown below and simply re-save it under your own account.

The “ActivatedImage” and “CardImage” resources specified below are used by the viewer to display the vAtom. The ActivatedImage is shown in the inventory view and the CardImage is displayed when the vAtom is “opened”. 

As an example, imagine that the CardImage is a Rare Pepe image. The viewer has essentially become a Rare Pepe wallet!

```
curl --request POST \
  --url 'http://{{BLOCKv-Server}}/api/v1/currentuser/vatom/template' \
  --header 'X-BLOCKv-App-Id: {{App-ID}}' \
  --data '{
   "template":"{{publisher_name}}::Image::v1",
   "public":false,
   "cloneable":false,
   "eth": {
    "identifier": {
      "value": "",
      "type":"string"
    },
    "value": {
      "value": 0,
      "Type":"uint"
    }
  },
   "vatom":{
      "vAtomic::v1::vAtom::vAtomType":{
         "root_type":"vAtomic::v1::vAtom::vAtomType",
         "transferable":true,
         "acquireable":false,
         "title":"Image",
         "description":"This is a Template for a single image vAtom on blockchain",
         "category":"Miscellaneous",
         "commerce":{
            "redeemable":false,
            "pricing":{
               "v1::PricingType":"",
               "value":{
                  "currency":"",
                  "price":"",
                  "valid_from":"",
                  "valid_through":"",
                  "vat_included":false
               }
            }
         },
         "eth":{

         },
         "states":[
            {
               "name":"Activated",
               "value":{
                  "type":"boolean",
                  "value":"true"
               }
            }
         ],
         "resources":[
            {
               "name":"ActivatedImage",
               "v1::ResourceType":"",
               "value":{
                  "v1::ResourceValueType":"ResourceValueType::URI",
                  "value":"{{default_icon_path}}"
               }
            },
            {
               "name":"CardImage",
               "v1::ResourceType":"",
               "value":{
                  "v1::ResourceValueType":"ResourceValueType::URI",
                  "value":""
               }
            }
         ],
         "visibility":{
            "type":"owner",
            "value":"*"
         }
      },
      "private":{

      }
   }
}'
```

### 4) Register the Faces for your Template

This essentially informs the iOS viewer to use a native image renderer to display the ‘ActivatedImage’ and ‘CardImage’

```
curl --request POST \
  --url 'http://{{BLOCKv-Server}}/api/v1/publisher/face' \
  --header 'X-BLOCKv-App-Id: {{App-ID}}' \
  --data '{
    "template": "{{publisher_name}}::Image::v1",
    "display_url": "native://image",
    "package_url": "native://image",
    "constraints": {
        "bluetooth_le": false,
        "contact_list": false,
        "gps": false,
        "view_mode": "icon",
        "platform": "ios",
        "three_d": false,
        "quality": "high"
    },
    "resources": ["ActivatedImage"]
}'
```

```
curl --request POST \
  --url 'http://{{BLOCKv-Server}}/api/v1/publisher/face' \
  --header 'X-BLOCKv-App-Id: {{App-ID}}' \
  --data '{
    "template": "{{publisher_name}}::Image::v1",
    "display_url": "native://image",
    "package_url": "native://image",
    "constraints": {
        "bluetooth_le": false,
        "contact_list": false,
        "gps": false,
        "view_mode": "card",
        "platform": "ios",
        "three_d": false,
        "quality": "high"
    },
    "resources": ["CardImage"]
}'
```

### 5) Register the Transfer Action for your Template

There are many actions available, so check with the API documentation.

```
curl --request POST \
  --url 'http://{{BLOCKv-Server}}/api/v1/publisher/action' \
  --header 'X-BLOCKv-App-Id: {{App-ID}}' \
  --data '{
   "name":"{{publisher_name}}::Image::v1::Action::Transfer",
   "reactor":"vatomic://v1/Transfer",
   "wait":true,
   "rollback":false,
   "abort_on_pre_error":true,
   "abort_on_post_error":false,
   "abort_on_main_error":true,
   "timeout":10000,
   "guest_user":true,
   "state_impact":[
      "this.owner"
   ],
   "policy":{
      "pre":[

      ],
      "rule":"",
      "post":[

      ]
   },
   "params":{
      "input":[
         "this.id",
         "new.owner.email|new.owner.phone_number|new.owner.id"
      ],
      "output":[
         "vAtomic::v1::Error"
      ]
   },
   "config":{
      "auto_create_landing_page":"http://viewer.vatomic.io/land/#",
      "auto_create_mode":"claim",
      "auto_create_non_existing_recipient":true
   },
   "limit_per_user":0,
   "action_notification":{
      "on":true,
      "msg":"You received a vAtom",
      "custom":{

      }
   }
}'
```

### 6) Create a corresponding Template Variation specifying the Ethereum smart contract data.

```
curl --request POST \
  --url 'http://{{BLOCKv-Server}}/api/v1/currentuser/vatom/template/variation' \
  --header 'X-BLOCKv-App-Id: {{App-ID}}' \
  --data '{
   "template":"{{publisher_name}}::Image::v1",
   "template_variation":"{{publisher_name}}::Image::v1::BlockChainImage::v1",
   "eth":{
      "identifier":{
        "value":"ThisIs1234567890",
        "Type":"string"
      },
      "value":{
        "value":5,
        "Type":"uint"
      }
    },
   "public":false,
   "cloneable":false,
   "redeemable":{

   },
  "auto_create_on_acquire":false,
   "vatom":{
      "vAtomic::v1::vAtom::vAtomType":{
         "transferable":true,
         "acquireable":false,
         "title":"BlockChainImage",
         "category":"Miscellaneous",
         "description":"This is an image on blockchain",
         "commerce":{
            "redeemable":false,
            "pricing":{
               "v1::PricingType":"Fixed",
               "value":{
                  "currency":"",
                  "price":"0",
                  "valid_from":"*",
                  "valid_through":"*",
                  "vat_included":false
               }
            }
         },
         "eth":{

         },
         "resources":[
            {
               "name":"ActivatedImage",
               "v1::ResourceType":"ResourceTypes::Image::PNG",
               "value":{
                  "v1::ResourceValueType":"ResourceValueType::URI",
                  "value":"{{icon_path}}"
               }
            },
            {
               "name":"CardImage",
               "v1::ResourceType":"ResourceTypes::Image::JPEG",
               "value":{
                  "v1::ResourceValueType":"ResourceValueType::URI",
                  "value":"{{card_image_path}}"
               }
            }
         ],
         "visibility":{
            "type":"owner",
            "value":"*"
         }
      },
      "private":{

      }
   }
}'
```

# Emitting a vAtom

Once the Template Variation is created, you can now emit a vAtom:

```
curl --request POST \
  --url 'http://{{BLOCKv-Server}}/api/v1/currentuser/vatom' \
  --header 'X-BLOCKv-App-Id: {{App-ID}}' \
  --data '{
    "template_variation": "{{publisher_name}}::Image::v1::BlockChainImage::v1",
    "num": 1
}'
```

This returns a list of vAtom IDs, each of which is now on the blockchain network specified.

# Transferring vAtoms

You can now simply invoke the Transfer action to send the vAtom to someone based on their phone number or email address, which will also transfer the vAtom on the blockchain. 

```
curl --request POST \
  --url 'http://{{BLOCKv-Server}}/api/v1/currentuser/action/Transfer' \
  --header 'X-BLOCKv-App-Id: {{App-ID}}' \
  --data '{
    "this.id": "vAtom-Id",
    "new.owner.email": "you@publisher.com"
}'
```

Being able to transfer virtual goods (which can include tokens etc) on the blockchain using a phone number or email address greatly simplifies a highly complex process - making the blockchain networks much more accessible to developers. When transferring a vAtom on BlockV, the vAtom is simultaneously transferred from the publisher’s blockchain wallet to the target’s blockchain wallet.

# Listing vAtoms

As a user you can easily get an inventory of your vAtoms which allows you to easily build a wallet of virtual goods or tokens.

```
curl --request POST \
  --url 'http://{{BLOCKv-Server}}/api/v1/currentuser/inventory' \
  --header 'X-BLOCKv-App-Id: {{App-ID}}' \
  --data '{
    "parent_id": ".",
    "page_token": "",
    "page_amount": 2
}'
```

# Conclusion

I hope that this helps you understand how vAtoms are created and how they can abstract blockchain networks to build a myriad of applications. 

For more information please visit http://blockv.io.  As well, please take a look at our API documentation and participate in the discussion.

