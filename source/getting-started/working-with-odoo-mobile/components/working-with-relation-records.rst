Working with relation records
=============================

.. index:: Relation Records

*Added in v2.2.0*

**Why we need to handle relation record at runtime ?**

In a case, when you need to create one record, and it contains reference to another record that actually does not exists in the database. In manual case, you need to first create that refering master record and than pass it's reference to the record you are creating. 

But, It's easy if we can directly pass the new record values to the reference column and ORM will create that reference record and link it with your newly created record.

In odoo, It is possible through ORM. As in odoo mobile it is possible with ``RelValues``; which holds your new values for reference record and also it can hold the record ids that actaully already created.


``RelValues``
-------------

.. index:: RelValues

.. note:: 
	``RelValues`` is used with **OneToMany** and **ManyToMany** only. For **ManyToOne** you can directly pass ``OValues`` object.

**Syntax:**

.. code-block:: java

	// Constructor
	new RelValues()

	// Chaining Methods
	RelValues append([List<Object> values, [Object...values]]);
	RelValues replace([List<Object> values, [Object...values]]);
	RelValues delete([List<Integer> ids, [Integer...ids]]);
	RelValues unlink([List<Integer> ids, [Integer...ids]]);


``append()``
************

.. index:: RelValues::append()

**Syntax:**

``RelValues append([List<Object> values, [Object...values]]);``

Append referece records to your record or create new reference and link to your record. You can pass multiple records.

**For OneToMany:**

.. code-block:: java

	OValues values = new OValues();
	values.put("name", "Odoo");
	values.put("is_company", true);
	values.put("email", "odoo@example.com");

	// Contact 1
	OValues dpr = new OValues();
	dpr.put("name", "Dharmang Soni");
	dpr.put("email", "dpr@example.com");

	// Contact 2
	OValues pga = new OValues();
	pga.put("name", "Parth Gajjar");
	pga.put("email", "pga@example.com");

	// Appending new contact to company and also passing already created contact id.
	// Passing rel values to child_ids (i.e. OneToMany column)
	values.put("child_ids", new RelValues().append(dpr, pga, 10));

	// Creating record in Local database
	db().insert(values);


**For ManyToMany:**

.. code-block:: java

	OValues values = new OValues();
	values.put("name", "Dharmang Soni");
	values.put("email", "dpr@example.com");

	// Tag 1
	OValues employee = new OValues();
	employee.put("name", "Employee");

	// Tag 2
	OValues rd = new OValues();
	rd.put("name", "Research & Development");

	// Appending tags to record with already created id
	// Passing rel values to tag_ids (i.e. ManyToMany column)
	values.put("tag_ids", new RelValues().append(employee, rd, 45));

	// Creating record in Local database
	db().insert(values);

For update, insert, The way is same.

.. code-block:: java
	
	...
	...
	values.put("tag_ids", new RelValues().append(30, 45, newTag));

	db().update(34, values);
	
``replace()``
*************

.. index:: RelValues::replace()

**Syntax:**

``RelValues replace([List<Object> values, [Object...values]]);``

Removes old reference to record and link with newly given references.

Works with **OneToMany** and **ManyToOne**

.. code-block:: java
	
	OValues newContact = new OValues();
	...
	values.put("child_ids", new RelValues().replace(30, 45, newContact));
	...
	OValues newTag = new OValues();
	...
	values.put("tag_ids", new RelValues().replace(30, 45, newTag));

	db().update(34, values);

``delete()``
************

.. index:: RelValues::delete()

**Syntax:**

``RelValues delete([List<Integer> values, [Integer...values]]);``

Delete relation between record and also remove referece master record. Takes ``ids`` of record[s]

Works with **OneToMany** and **ManyToOne**

.. code-block:: java
	
	values.put("tag_ids", new RelValues().delete(30, 45));
	db().update(34, values);

It will remove master record i.e, **30** and **45** (in example)


``unlink()``
************

.. index:: RelValues::unlink()

**Syntax:**

``RelValues unlink([List<Integer> values, [Integer...values]]);``

Delete relation between record and but keeps master record. Takes ``ids`` of reference record[s]

Works with **OneToMany** and **ManyToOne**

.. code-block:: java
	
	values.put("tag_ids", new RelValues().unlink(30, 45));
	db().update(34, values);

It will remove reference between **34** (main record) and records **30** and **45** (in example) but does not delete record as ``delete()`` does.

Using all together:
--------------------

Yes, you can use all of these together in single update/insert request.

.. code-block:: java

	OValues values = new OValues();
	values.put("name", "Odoo");
	values.put("is_company", true);
	values.put("email", "odoo@example.com");

	// Contact 1
	OValues dpr = new OValues();
	dpr.put("name", "Dharmang Soni");
	dpr.put("email", "dpr@example.com");

	// Contact 2
	OValues pga = new OValues();
	pga.put("name", "Parth Gajjar");
	pga.put("email", "pga@example.com");

	// Tag 1
	OValues employee = new OValues();
	employee.put("name", "Employee");

	// Tag 2
	OValues rd = new OValues();
	rd.put("name", "Research & Development");

	values.put("child_ids", new RelValues().append(dpr, pga).unlink(48));

	values.put("tag_ids", new RelValues().append(employee, rd, 45).unlink(56).delete(78));

	// Creating record in Local database
	db().update(values);


For ManyToOne:
--------------

For ManyToOne, You can pass reference record id or ``OValues`` object directly.

.. code-block:: java

	OValues values = new OValues();
	values.put("name", "Ankit Sathvara");
	values.put("email", "asa@example.com");

	OValues company = new OValues();
	company.put("name", "Odoo");
	company.put("email", "odoo@example.com");
	company.put("is_company", true);

	values.put("parent_id", company);

	db().insert(values);
