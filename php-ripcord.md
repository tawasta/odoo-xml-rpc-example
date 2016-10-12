# Odoo PHP XML-RPC Example

This example is using Ripcord XML-RPC library

* Ripcord git: https://github.com/poef/ripcord

## Login Information
```php
// Login information
$url = 'https://demo.futural.fi';
$url_auth = $url . '/xmlrpc/2/common';
$url_exec = $url . '/xmlrpc/2/object';

$db = 'demo';
$username = 'demo';
$password = 'demo';

// Ripcord can be cloned from https://github.com/poef/ripcord
require_once('ripcord/ripcord.php');
```

## Login
```php
// Login
$common = ripcord::client($url_auth);
$uid = $common->authenticate($db, $username, $password, array());

print("<p>Your current user id is '${uid}'</p>");

$models = ripcord::client($url_exec);
```
 
## Creating search queries

The search query is constructed as follows
```php
$models					// The (Ripcord) client
	->execute_kw(		// Execute command
	'table.reference'	// Referenced model, e.g. 'res.partner' or 'account.invoice'
	'search',			// Search method of the referenced model
	array(),		    // Search domain
)
```

## Search (customers)

All "partners" in Odoo are located in the table res_partner.

This includes customers, contacts and suppliers. Partner types
are differentiated with boolean fields:

```php
is_company 			// (True = is a company, False = is a contact or an employee)
customer 			// (True = is a customer, will be shown in "customers")
supplier 			// (True = is a supplier, will be shown in "suppliers").
```

A partner can be both customer and supplier (or neither)

```php
$customer_ids = $models->execute_kw(
    $db, // DB name
    $uid, // User id, user login name won't work here
    $password, // User password
    'res.partner', // Model name
    'search', // Function name
    array( // Search domain
        array( // Domain operators
            array('is_company', '=', true), // Query condition
            array('customer', '=', true)) // Query condition
        )
 );
```
$customer_ids will now contain a list of partner ids

## Read (customer) information

Read works similiar to search, but instead of domain operators,
we will support the read with record ids

```php
$customers = $models->execute_kw($db, $uid, $password, 'res.partner',
    'read',  // Function name
    array($customer_ids), // An array of record ids
    array('fields'=>array('name', 'businessid')) // Array of wanted fields
);
```

Output example
```php   
print("<p><strong>Found customers:</strong><br/>");
foreach ($customers as &$customer){
    print("${customer[name]} ${customer[businessid]}<br/>");
}
print("</p>");
```



## Searching a spesific customer with business id

Because read() always needs to know datatabase ids,
there is a combined function for search and read, which
combines the parameters and functionality of both methods.

```php
$customer = $models->execute_kw($db, $uid, $password, 'res.partner',
    'search_read', // Note the different function here, so we don't have to search AND read
    array( // Search domain
        array(
            array('businessid', '=', '1935052-9')
        )
    ),
    array('fields'=>array('name')) // Array of wanted fields
);
```

 
## Creating a (customer) record

When creating a record, some fields are required (usually at least 'name'-field).
Some required fields are filled in automatically, if they are left empty.

NOTE: Odoo doesn't prevent creating companies with same name.
Business id, however, is required to be unique (unless the company is a child of
a company with the same business id).

```php 
$partner_name = "Test Partner";
$new_partner_id = $models->execute_kw($db, $uid, $password,
    'res.partner',
    'create', // Function name
    array( // Values-array
        array( // First record
            'name'=>$partner_name,
            'businessid'=>"1234567-1",
            'businessid_shown'=>True, // Show business id in the customer card
            'customer'=>True,
            'is_company'=>True,
            'street'=>"Street 123",
            'street2'=>"Floor 7",
            'city'=>"Tampere",
            'zip'=>"33101",
            'phone'=>"123456780",
            'email'=>"mail@example.com",
            'comment'=>"A free comment",
        )
    )
);
```

Output example
```php
if(is_int($new_partner_id)){
    print("Partner '${partner_name}' created with id '${new_partner_id}'");
}
else{
    print("<p>Error: ");
    print($new_partner_id['faultString']);
    print("</p>");
}
```

 
## Creating an invoice

### Create the invoice (header)
```php 
$invoice_model = 'account.invoice';
$invoice_line_model = 'account.invoice.line';

// Create invoice
$invoice_id = $models->execute_kw($db, $uid, $password,
    $invoice_model,
    'create',
    array(
        array(
            'partner_id'=>$customer_ids[0],
            'account_id'=>219, // Account "1701 Myyntisaamiset
        )
    )
);
``` 

Output example
```php 
if(is_int($invoice_id)){
    print("<p>Invoice created with id '${invoice_id}'</p>");
}
else{
    print("<p>Error: ");
    print($invoice_id['faultString']);
    print("</p>");
}
```

### Create invoice lines
```php 
// Create invoice line(s)
$invoice_line_id = $models->execute_kw($db, $uid, $password,
    $invoice_line_model,
    'create',
    array(
        array(
            'invoice_id'=>$invoice_id, // Reference to the invoice itself
            'name'=>"Product description", // Invoice line description
            'product_id'=>1, // Products can be found from product_product
        )
    )
);
```

Output example
```php
if(is_int($invoice_line_id)){
    print("<p>Invoice line created with id '${invoice_line_id}'</p>");
}
else{
    print("<p>Error: ");
    print($invoice_line_id['faultString']);
    print("</p>");
}
```
