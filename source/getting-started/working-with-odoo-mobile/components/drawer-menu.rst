Drawer Menu
-----------

.. index:: drawer menu

The navigation drawer is a panel that transitions in from the left edge of the screen and displays the app’s main navigation options.

Odoo mobile framework provide feature to add dynamic menu to your application just by providing list of menus.

.. image:: images/drawer_menu_sample.png
   :width: 440px

By extending ``BaseFragment`` class you are allowed to implement ``drawerMenu()`` method. Which returns list of ``DrawerItem`` with key, title and your object of fragment or class type for Activity.

Here is simple example for loading drawer items:

.. code-block:: java

	class Customers extends BaseFragment {	
		...
		...

		@Override
		public List<ODrawerItem> drawerMenus(Context context) {
		    List<ODrawerItem> items = new ArrayList<>();
		    items.add(new ODrawerItem(KEY).setTitle("Customers")
		            .setIcon(R.drawable.ic_action_customers)
		            .setExtra(extra(Type.Customer))
		            .setInstance(new Customers()));
		    items.add(new ODrawerItem(KEY).setTitle("Suppliers")
		            .setIcon(R.drawable.ic_action_suppliers)
		            .setExtra(extra(Type.Supplier))
		            .setInstance(new Customers()));
		    items.add(new ODrawerItem(KEY).setTitle("Companies")
		            .setIcon(R.drawable.ic_action_company)
		            .setExtra(extra(Type.Company))
		            .setInstance(new Customers()));
		    return items;
		}

		public Bundle extra(Type type) {
		    Bundle extra = new Bundle();
		    extra.putString(EXTRA_KEY_TYPE, type.toString());
		    return extra;
		}

		...
		...	
	}


ODrawerItem.java
****************

.. index:: ODrawerItem.java

``ODrawerItem`` class contains information for your menu item.

**Syntax:**

``ODrawerItem(String key)``

This class contains chaining methods such as :

- setTitle()
- setGroupTitle()
- setCounter()
- setInstance()
- setExtra()
- setIcon()


``setTitle()``
..............

.. index:: setTitle()

Sets title for drawer menu.

**Sytanx:**

``ODrawerItem setTitle(String title)``

.. code-block:: java

	ODrawerItem item = new ODrawerItem("my_menu")
		.setTitle("Dashboard");

``setGroupTitle()``
...................

.. index:: setGroupTitle()

Works as seperator. 

**Syntax:**

``ODrawerItem setGroupTitle()``

.. code-block:: java

	ODrawerItem item = new ODrawerItem("my_menu")
		.setTitle("Dashboard")
		.setGroupTitle();


``setCounter()``
................

.. index:: setCounter()

Shows number of record at right of menu.

**Syntax:**

``ODrawerItem setCounter(int counter)``

.. code-block:: java

	ODrawerItem item = new ODrawerItem("my_menu")
		.setTitle("Dashboard")
		.setCounter(10);

``setInstance()``
.................

.. index:: setInstance()

Sets loading fragment instance or activity class.

**Syntax:**

``ODrawerItem setInstance(Fragment fragment)``

``ODrawerItem setInstance(Class<? extends Activity> classType)``

``ODrawerItem setInstance(Class<? extends ActionbarActivity> classType)``

``ODrawerItem setInstance(Class<? extends FragmentActivity> classType)``


.. code-block:: java

	ODrawerItem dashboard = new ODrawerItem("dashbaord")
		.setTitle("Dashboard")
		.setCounter(10)
		.setInstance(new Dashbaord());

	ODrawerItem settings  = new ODrawerItem("settings")
		.setTitle("Settings")
		.setInstance(SettingActivity.class);

``setExtra()``
..............

.. index:: setExtra();

Sets extra bundle passed with fragment or activity.

**Syntax:**

``ODrawerItem setExtra(Bundle data)``

.. code-block:: java

	Bundle data = new Bundle();
	data.putInt("record_id", 10);

	ODrawerItem item = new ODrawerItem("my_menu")
		.setTitle("Projects")
		.setInstance(new Projects())
		.setExtra(data);

``setIcon()``
.............

.. index:: setIcon()

Shows icon for menu

**Syntax:**

``ODrawerItem setIcon(int resource_id)``

.. code-block:: java

	ODrawerItem item = new ODrawerItem("my_key")
		.setTitle("Projects")
		.setInstance(new Projects())
		.setIcon(R.drawable.ic_action_projects);