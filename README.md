# XML-RPC Examples for Futural ERP

[Futural ERP](http://tawasta.fi/palvelut/futural-erp) is based on the [Community version](https://odoo-community.org/) (OCA) of [Odoo](https://www.odoo.com) and most of the examples apply to the core version. 

Some of the fields, however, are only found in the Futural ERP or [OCA modules](https://github.com/OCA).

XML-RPC examples:
* [PHP Ripcord example](php-ripcord.md)
* TBD: PHP phpxmlrpc example
* TBD: Python xmlrpclib example
* TBD: Ruby XMLRPC example
* TBD: Java XmlRpcClient example


## Common Odoo XML-RCP info

### Official documentation
Odoo XML-RPC documentation:  
https://www.odoo.com/documentation/8.0/api_integration.html


### Search domains
Search domain is an array containing an array of domain rules, 
that contains array(s) of query conditions

Available domain operators (based on http://stackoverflow.com/a/29443027)

```
'=', '!=', '<=', '<', '>', '>=', '=?',
'=like', '=ilike', 'like', 'not like', 
'ilike', 'not ilike', 'in', 'not in', 'child_of'
```

### Model Examples
Database scheme (v8): http://useopenerp.com/v8

Common models:
* Partner - http://useopenerp.com/v8/model/res-partner
* Invoice - http://useopenerp.com/v8/model/account-invoice
* Invoice line - http://useopenerp.com/v8/model/account-invoice-line
* Product - http://useopenerp.com/v8/model/product_product

### Field examples
Fields documentation: https://www.odoo.com/documentation/8.0/reference/orm.html

Common fields:
* **name** - displayed name (e.g. partner name, invoice number)
* **partner_id** or **partner** - usually customer or supplier reference. Can be found in most records.
* **company_id** or **company** - to which company the record belongs to. Only relevant in multicompany-instances

Automatic fields (every record has these)
* **id** - database id
* **create_date** - when record is created
* **create_uid** - the user who created the record
* **write_date** - when record was last edited
* **write_uid** - the user who last edited the record
