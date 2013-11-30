SQLite3 Xenode
=============

Note: you will need the Xenograte Community Toolkit (XCT) to run this Xenode. Refer to the XCT repo [https://github.com/Nodally/xenograte-xct](https://github.com/Nodally/xenograte-xct) for more information.

**SQLite3 Xenode** listens for values to substitute into a SQL statement. This Xenode only works with SQLite3 and requires the "sqlite3" RubyGem. SQL Templates and a SQLite3 database are required to run this Xenode. There are sample templates and databases in their respective directories. The SQLite Xenode processes a message containing an array of hashes where each hash is a row. After executing the SQL statement, the xenode will write the output from the database to its children. If there was no output, it passes the message data to its children as it comes in. SQL Templates can have custom tokens in them which will get replaced by the message context and data that gets passed in. SQL Templates do not necessarily need to have the custom tokens; simply don't include the ~~ after the SQL statement.

###Configuration File Options:###
* loop_delay: defines the number of seconds the Xenode waits before running the Xenode process. Expects a float.
* enabled: determines if the Xenode process is allowed to run. Expects true/false.
* debug: enables extra debug messages in the log file. Expects true/false.
* sql_template: specifies the file path where the SQL templates are located. If custom tokens are used, put ~~ after the statement followed by the tokens. Seperate the tokens with commas. Variable expects a string value.
* db_path: specifies the file path where the local SQLite3 databases are located. Variable expects a string value.
* pass_through: determines if the Xenode will send the message it receives to its children Xenodes. Expects true/false.
* rel_path: specify the relative path for templates and databases to the default system defined temporary directory. If set to false, the Xenode will use db_path value to locate the database files.

###Example Configuration File:###
* loop_delay: 5
* enabled: true
* debug: true
* sql_template: xenode_lib/sqlite3_node/templates/insert_shipping_tokens.tmp
* db_path: databases/Shipping.db
* pass_through: true
* rel_path: true

### Example Input:###
* insert_shipping_tokens.tmp: INSERT INTO Shipping VALUES (?, ?, ?, ?, ?, ?, ?, ?, ?, ?)~~|ponum|,|odate|,|pnum|,|pname|,|qty|,|price|,|sdate|,|smethod|,|comment|,|adate|
* msg.context: {:sqlite3_node=>{"PurchaseOrderNumber"=>"|ponum|", "OrderDate"=>"|odate|", "PartNumber"=>"|pnum|", "ProductName"=>"|pname|", "Quantity"=>"|qty|", "USPrice"=>"|price|", "ShipDate"=>"|sdate|", "ShipMethod"=>"|smethod|", "Comment"=>"|comment|", "ArrivalDate"=>"|adate|"}}
* msg.data: [{"PurchaseOrderNumber"=>"99503", "OrderDate"=>"2013-03-10", "PartNumber"=>"872-AA", "ProductName"=>"Lawnmower", "Quantity"=>50, "USPrice"=>148.95, "ShipDate"=>"2013-03-11", "ShipMethod"=>"AIR", "Comment"=>"Confirm Order by 2013-03-10", "ArrivalDate"=>"2013-05-11"}, {"PurchaseOrderNumber"=>"23567", "OrderDate"=>"2013-03-14", "PartNumber"=>"17-A6-23", "ProductName"=>"Particle Cannon", "Quantity"=>3, "USPrice"=>1948628.95, "ShipDate"=>"2013-03-16", "ShipMethod"=>"SEA", "Comment"=>"Confirm Order by 2013-03-15", "ArrivalDate"=>"2013-03-16"}]

### Example Output:###
* SQL Statement: INSERT INTO Shipping VALUES (?, ?, ?, ?, ?, ?, ?, ?, ?, ?)  #Data: ["99503", "2013-03-10", "872-AA", "Lawnmower", 50, 148.95, "2013-03-11", "AIR", "Confirm Order by 2013-03-10", "2013-05-11"]
* SQL Statement: INSERT INTO Shipping VALUES (?, ?, ?, ?, ?, ?, ?, ?, ?, ?)  #Data: ["23567", "2013-03-14", "17-A6-23", "Particle Cannon", 3, 1948628.95, "2013-03-16", "SEA", "Confirm Order by 2013-03-15", "2013-03-16"]
* msg.data: [{"PurchaseOrderNumber"=>"99503", "OrderDate"=>"2013-03-10", "PartNumber"=>"872-AA", "ProductName"=>"Lawnmower", "Quantity"=>50, "USPrice"=>148.95, "ShipDate"=>"2013-03-11", "ShipMethod"=>"AIR", "Comment"=>"Confirm Order by 2013-03-10", "ArrivalDate"=>"2013-05-11"}, {"PurchaseOrderNumber"=>"23567", "OrderDate"=>"2013-03-14", "PartNumber"=>"17-A6-23", "ProductName"=>"Particle Cannon", "Quantity"=>3, "USPrice"=>1948628.95, "ShipDate"=>"2013-03-16", "ShipMethod"=>"SEA", "Comment"=>"Confirm Order by 2013-03-15", "ArrivalDate"=>"2013-03-16"}]
