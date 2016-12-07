Base Classes
============

BaseFragment.java
-----------------

.. index:: BaseFragment.java

``BaseFragment.java`` class extends ``android.support.v4.app.Fragment.Fragment`` class and implements ``IBaseFragment`` interface which contains two methods:

.. code-block:: java

	public interface IBaseFragment {
	    public List<ODrawerItem> drawerMenus(Context context);

	    public <T> Class<T> database();
	}

We must have to create one file that extends ``BaseFragment.java`` which will be registered in ``Addons.java`` 

These two methods are used by framework for creating database and drawer menu. Here, ``<T>`` indicate your model class type. We will see it in ``OModel.java``

**BaseFragment.java** contains some of usefull methods we can used in our fragment. As below:

.. index:: setTitle()

``setTitle()`` 
**************

**Syntax:**

``Void setTitle(String title)``

Used for setting actionbar title:

.. code-block:: java

	setTitle("My Title");

.. index:: db()

``db()``
********

**Syntax:**

``OModel db()``

Used to get current db object returned in ``database()`` method which implemented in our fragment class.

.. code-block:: java

	db().select();

	OValues values = new OValues();
	...
	...
	db().insert(values);

.. index:: user()

``user()``
*************

**Syntax:**

``OUser user()``

Used to get current active user object. It is easy when you want to do some operation with current user.

.. code-block:: java

	user().getName();
	user().getPartner_id();
	user().getUser_id();
	...

.. index:: parent()

``parent()``
*************

**Syntax:**

``OdooActivity parent()``

Returns parent activity (``i.e., getActivity()``). 
It will automatically cast Activity object to ``OdooActivity`` So you can easyliy access public methods of ``OdooActivity``

.. code-block:: java

	parent().closeDrawer();
	parent().refreshDrawer();
	parent().setOnActivityResultListener(callback);

We will see more methods of ``parent()`` method in ``OdooActivity.java`` 

.. index:: inNetwork()

``inNetwork()``
***************

**Syntax:**

``boolean inNetwork()``

Returns ``true`` if device in network, otherwise ``false``

.. code-block:: java

	if(inNetwork()){
		// Do some stuff
	}else{
		// Ignore stuff
	}

.. index:: startFragment()

``startFragment()``
*******************

**Syntax:**

``Void startFragment(Fragment fragment, boolean addToBackState)``

``Void startFragment(Fragment fragment, boolean addToBackState, Bundle data)``

Used to start another fragment from current fragment. You can also specify backstate when starting another fragment.

.. code-block:: java

	startFragment(new MyNewFragment(), true); // Starting fragment with backstate

	Bundle data = new Bundle();
	...
	...
	startFragment(new MyNewFragment(), false, data); // Starting new fragment without backstate but with some of bundle data

.. index:: setHasFloatingButton()

``setHasFloatingButton()``
**************************

**Syntax:**

``void setHasFloatingButton(View view, int viewId, ListView listViewObj, View.OnClickListener callback)``

By default floating button is hidden. You need to activate floating button to use. It will auto add callback method. Also you need to add ListView object as parameter so when you scroll your listview FAB will automatically hide/visible on listview scroll.

.. code-block:: java

	setHasFloatingButton(view, R.id.fabButton, listViewObj, this);

	// this will implement onClick(View v) method

	@Override
	public void onClick(View v) {
	    switch (v.getId()) {
	        case R.id.fabButton:
	            // Do your stuff
	            break;
	    }
	}

.. index:: showFab() hideFab()

``hideFab()`` and ``showFab()``
********************************

**Syntax:**

``void hidFab()``

``void showFab()``

After setting fab button you can call ``hideFab()`` and ``showFab()`` as per your requirements

.. code-block:: java

	if(inNetwork()){
		showFab();
	}else{
		hideFab();
	}

.. index:: setHasSearchView()

``setHasSearchView()``
**********************

**Syntax:**

``void setHasSearchView(IOnSearchViewChangeListener callback, Menu menu, int menu_id)``

If there is a menu with search option. You can directly set ``setHasSearchView()`` and framework will work for you. It will give you callback on search text changed and search view close. 

It takes callback to its first paramenter of ``IOnSearchViewChangeListener`` interface which has following methods:

.. code-block:: java

	public interface IOnSearchViewChangeListener {
	    public static final String TAG = IOnSearchViewChangeListener.class.getSimpleName();

	    public boolean onSearchViewTextChange(String newFilter);

	    public void onSearchViewClose();
	}

To apply search view callback just call method when you create your menu:

.. code-block:: java

	@Override
	public void onCreateOptionsMenu(Menu menu, MenuInflater inflater) {
	    super.onCreateOptionsMenu(menu, inflater);
	    menu.clear();
	    inflater.inflate(R.menu.menu_partners, menu);
	    setHasSearchView(this, menu, R.id.menu_partner_search);
	}

It will implement two methods:

.. code-block:: java

	@Override
	public boolean onSearchViewTextChange(String newFilter) {
	    mCurFilter = newFilter;
	    // Do any other stuff when change filter text
	    return true;
	}

	@Override
	public void onSearchViewClose() {
	    // Any stuff when user close search view
	}

.. index:: setHasSwipeRefreshView()

.. _swipe-refresh-view:

``setHasSwipeRefreshView()``
****************************

**Syntax:**

``void setHasSwipeRefreshView(View parentView, int resourceId, SwipeRefreshLayout.OnRefreshListener callback)``

When using swipe refresh view, you can easyly set it's call back by calling ``setHasSwipeRefreshView``

.. code-block:: java

	setHasSwipeRefreshView(view, R.id.swipe_container, this);

It will implement one method for calling swipe event:

.. code-block:: java

	@Override
	public void onRefresh() {
	    if (inNetwork()) {
	        // Do your stuff
	    } else {
	    	// Do your stuff
	    }
	}

.. index:: setSwipeRefreshing() hideRefreshingProgress()

``setSwipeRefreshing()`` and ``hideRefreshingProgress()``
**********************************************************

**Syntax:**

``void setSwipeRefreshing(boolean refreshing)``

``void hideRefreshingProgress()``

When using swipe refresh view you can use these method for hiding and showing refreshing operation.

.. code-block:: java

	@Override
	public void onRefresh() {
	    if (inNetwork()) {
	        setSwipeRefreshing(true);
	    } else {
	        hideRefreshingProgress();
	    }
	}

.. index:: setHasSyncStatusObserver()

.. _sync-status-observer:

``setHasSyncStatusObserver()``
*******************************

**Syntax:**

``void setHasSyncStatusObserver(String menuKEY, ISyncStatusObserverListener callback, OModel model)``

Used when any of your data are synchronizing in background and you need to notify when sync finished or data set update. By calling this method it is easy to notify on dataset change.

.. code-block:: java

	setHasSyncStatusObserver(KEY, this, db());

1. It takes drawer KEY or TAG which passed when creating drawer menu
2. Callback for data set change. Implement ``onStatusChange()`` method
3. database object on which you need to set observer

.. code-block:: java

	@Override
	public void onStatusChange(Boolean changed) {
		if(changed){
			getLoaderManager().restartLoader(0, null, this); // Updating listview
		}
	}

.. index:: _s() _c() _dim()

``_s()``, ``_c()`` and ``_dim()``
**********************************

**Syntax:**

``String _s(int resource_id)``

``int _c(int resource_id)``

``int _dim(int resource_id)``

Used to get quick string, color and dimention.

.. code-block:: java

	String name = _s(R.string.app_name);
	int color = _c(R.color.theme_primary);
	int height = _dim(R.dimen.statusBarHeight);

Sample class using ``BaseFragment``
***********************************

.. code-block:: java

	public class Messages extends BaseFragment {
	    public static final String TAG = Messages.class.getSimpleName();


	    @Override
	    public View onCreateView(LayoutInflater inflater, ViewGroup container, 
	                             Bundle savedInstanceState) {
	        return inflater.inflate(R.layout.common_listview, container,false);
	    }

	    @Override
	    public void onViewCreated(View view, Bundle savedInstanceState) {
	        super.onViewCreated(view, savedInstanceState);
			setTitle(_s(R.string.title_messages));
	    }

	    @Override
	    public Class<MailMessage> database() {
	        return MailMessage.class;
	    }

	    @Override
	    public List<ODrawerItem> drawerMenus(Context context) {
	        return null;
	    }

	}

OColumn.java
-------------

.. index:: OColumn.java

Used to create column for model.

**Syntax:**

.. code-block:: java

	OColumn(String label, Class<?> type);
	OColumn(String label, Class<?> type, RelationType relationType);

Here,

1. ``label`` indicate column label. It auto load in form control as control title.
2. ``type`` type of datatype. (Basic type + Relation type)
3. ``relationType`` [optional] if ``type`` is related to another model class 
	- Possible Types
		- OneToMany
		- ManyToOne
		- ManyToMany

**Some chaining methods**

`each method returns, OColumn object with updated value.`

``setName()``
*************

.. index:: OColumn.setName()

**Syntax:**

``OColumn setName(String name)``

Used to set column name. Generally variable name is considered as column name but if you want to change its name runtime you can change it.

.. code-block:: java
	
	OColumn dummy_column = new OColumn("Name", OVarchar.class).setName("name");

.. danger::
	Remeber that, You can not change column name after model's constructor call finish. If you trying to change outside of constructor, it will affect your local database.

``setRecordSyncLimit()``
************************

.. index:: OColumn.setRecordSyncLimit()

**Syntax:**

``OColumn setRecordSyncLimit(int limit)``

Limiting syncing record for **OneToMany** and **ManyToMany**. 

.. code-block:: java
	
	OColumn tag_ids = new OColumn("Tags", NoteTag.class, RelationType.ManyToMany)
		.setRecordSyncLimit(10);

.. note:: 
	Not all records are synced for OneToMany and ManyToMany if you set record sync limit. 

``setLabel()``
**************

.. index:: OColumn.setLabel()

**Syntax:**

``OColumn setLabel(String label)``

Sets label for column name. Used as title for column. 

.. code-block:: java
	
	OColumn name = new OColumn("Dummy Name", OVarchar.class).setLabel("Name");


``setRelatedColumn()``
**********************

.. index:: OColumn.setRelatedColumn()

**Syntax:**

``OColumn setRelatedColumn(String related_column)``

Used when you have created ``OneToMany`` relation column. OneToMany required ``related column`` to maintain relation.

.. code-block:: java
	
	OColumn child_ids = new OColumn("Contacts", ResPartner.class, RelationType.OneToMany)
		.setRelatedColumn("parent_id");

``setSize()``
*************

.. index:: OColumn.setSize()

**Syntax:**

``OColumn setSize(int size)``

Used to set column size. Takes integer value.

.. code-block:: java
	
	OColumn title = new OColumn("Blog Title", OVarchar.class).setSize(100);

``setDefaultValue()``
*********************

.. index:: OColumn.setDefaultValue()

**Syntax:**

``OColumn setDefaultValue(Object value)``

Sets default value for column. Will store into database if user not pass value for column

.. code-block:: java
	
	OColumn published = new OColumn("Published", OBoolean.class).setDefaultValue(false);

``setRequired()``
*****************

.. index:: OColumn.setRequired()

**Syntax:**

``OColumn setRequired()``

Sets column value required. OForm will automatically show validation error if column is required.

.. code-block:: java

	OColumn name = new OColumn("Name", OVarchar.class).setRequired();

``setLocalColumn()``
********************

.. index:: OColumn.setLocalColumn()

**Syntax:**

``OColumn setLocalColumn()``

Sometime you need some local column that will not available on server. You can make any column local only.

.. code-block:: java
	
	OColumn total_amount = new OColumn("Total Amount", OInteger.class).setLocalColumn();

``setType()``
*************

.. index:: OColumn.setType()

**Syntax:**

``OColumn setType(Class<?> type)``

Sets data type for column. You can change data type for column as runtime but only in constructor. 
In some cases, such as different type for different odoo version. 

.. code-block:: java

	OColumn date = new OColumn("Date", ODate.class);

	public ResPartner(Context context, OUser user) {
	    super(context, "crm.lead", user);
	    if(getOdooVersion().getVersion_number() > 7){
	        date.setType(ODateTime.class);
	    }
    }

``addDomain()``
***************

.. index:: OColumn.addDomain()

Adds default filter domain for column. Basically you need ``ManyToOne`` column to be filter on some conditions.

**Syntax:**

.. code-block:: java

	addDomain(String column_name, String operator, Object value);
	addDomain(String conditional_operator);

**Example:**

.. code-block:: java

	OColumn parent_id = new OColumn("Related Company", ResPartner.class, RelationType.ManyToOne)
		.addDomain("is_company","=",true);

``addSelection()``
*******************

.. index:: OColumn.addSelection()

**Syntax:**

``OColumn addSelection(String key, String value)``

Used to add key value selection pair. Used with ``OSelection`` data type.

.. code-block:: java

	OColumn state = new OColumn("State", OSelection.class)
		.addSelection("draft","Draft")
		.addSelection("confirm","Confirmed")
		.addSelection("close","Canceled")
		.addSelection("done","Done");

Column annotations
******************

``@Odoo.api.v7``, ``@Odoo.api.v8``, ``@Odoo.api.v9``, ``@Odoo.api.v10`` and ``@Odoo.api.v11alpha``
..................................................................................................

.. index:: @Odoo.api.v7, @Odoo.api,v8, @Odoo.api.v9, @Odoo.api.v10, @Odoo.api.v11alpha

api annotations are used when your column name is different in odoo versions. Or may be it is possible that some of column not present in older version and newer version. Framework column annotation provide feature for making your model compitible for different odoo versions.

You need to just add annotation on column with your supported version.

.. code-block:: java

	@Odoo.api.v9
	OColumn to_read = new OColumn("To Read", OBoolean.class);

	@Odoo.api.v10
	OColumn is_read = new Column("Is read", OBoolean.class);

Here, ``api.v9`` column will created only if connected odoo server is version 9.0, same as for ``api.v10``

``@Odoo.SyncColumnName()``
..........................

Some time you need to create column name that is not supported in SQLite (such as ``limit``) or some variable name are not allowed in ``java`` such as ``class``

By using ``SyncColumnName`` annotation framework will treat that column in different behaviour. For example, just create ``_class`` column and add annotation named with ``class``. 

Synchronization will done with ``class`` column name but stored in ``_class`` also you can treat it with ``_class`` name locally.

.. code-block:: java

	@Odoo.SyncColumnName("class")
	OColumn _class = new OColumn("Class", OVarchar.class);

	@Odoo.SyncColumnName("limit")
	OColumn _limit = new OColumn("Limit", OInteger.class);


``@Odoo.onChange()``
....................

.. index:: @Odoo.onChange()

**Compitable with OForm control only**

Used when column value changed. It takes method name as first parameter and boolean value as second parameter if you want to execute onchange task in background process.

.. code-block:: java

	@Odoo.onChage(method="onParentIdChange")
	OColumn parent_id = new OColumn("Company", ResPartner.class, RelationType.ManyToOne);
	OColumn city = new OColumn("City", OVarchar.class);

	public ODataRow onParentIdChange(ODataRow parent_id){
		ODataRow newValues = new ODataRow();
		newValues.put("city", parent_id.getString("city")); // get city from parent_id and returning new data row.
		return newValues;
	}

``OForm`` call it automatically and fill the values in form object. 

``@Odoo.Functional()``
......................

.. index:: @Odoo.Functional()

One can define a field whose value is computed instead of simply being read from the database. 

Takes three parameters:

	1. ``method``, name of method 
	2. ``store``, boolean flag for storing value in database (if true, database will create column)
	3. ``depends``, array of string, depended column names

.. code-block:: java

	OColumn first_name = new OColumn("First name", OVarchar.classs);
	OColumn last_name = new OColumn("Last name", OVarchar.class);

	@Odoo.Functional(method="storeDisplayName", depends = {"first_name","last_name"}, store = true)
	OColumn display_name = new OColumn("Display name", OVarchar.class).setLocalColumn();

	public String storeDisplayName(OValues values){
		String displayName = "";

		displayName = values.getString("first_name");
		displayName += " " + values.getString("last_name");

		return displayName;
	}

**Info:** 

For ManyToOne, ManyToMany and OneToMany values will be different. 

*ManyToOne*

.. code-block:: java

	public String storeManyToOne(OValues values){
		String manyToOne = "";
		if(!values.getString("parent_id").equals("false")){
			List<Object> parent_id = (ArrayList<Object>) value.get("parent_id");
			// Here, list index 0 contain record id (server id), and
			// list index 1 contains record name
			manyToOne = parent_id.get(1).toString();
		}
		return manyToOne;
	}

*ManyToMany* or *OneToMany*

.. code-block:: java

	public int storeChildCount(OValues values){
		if(!values.getString("child_ids").equals("false")){
			// Contains list of ids (server ids)
			return ((ArrayList<Object>) values.get("child_ids")).size();
		}
		return 0;
	}

``@Odoo.Domain()``
...........................

.. index:: @Odoo.Domain()

In some cases, you need to filter your record depended on some value change at runtime. For example, by changing country, states are loaded related to country. 

By using ``Domain`` annotation with your domain string in valid format you can deal with it. 

Add column domain as annotation value. Framework will apply domain with annotation and also include column domains (static domains with hard coded values) and applied to related column at runtime. 
**Note: it works with OForm control only**

**Syntax:**

.. code-block:: java
	
	@Odoo.Domain("[['related_column', '=', @related_column],'and', ['','',@??], 'or', .....]")
	OColumn state_id = new OColumn("State", ResCountryState.class, RelationType.ManyToOne);

Here ``@related_column`` indicate that from ``OForm`` fields the dynamic value is from @related_column,
It will be replaced with value at runtime.

.. note::
	Use columns that are available in model with ``@Odoo.Domain()`` 

.. code-block:: java

	OColumn country_id = new OColumn("Country", ResCountry.class, RelationType.ManyToOne);

	@Odoo.Domain("[['country_id', '=', @country_id]]")
	OColumn state_id = new OColumn("State", ResStates.class, RelationType.ManyToOne);


.. _model-class:

OModel.java
-----------

.. index:: OModel.java

All the model (database class) extends ``OModel`` class. 
It contains all database required methods. Also allow you to add column easily by declaring as member variable type ``OColumn``

- It automatically create relation tables and maintain relations for records. 
- Works with ``ContentProvider`` so faster performance for loading data from SQLite database. 
- Properly maintain local relation. 

OModel is binded with own ORM. It easy and fast.

.. index:: Datatypes

OModel support different datatypes which will create dynamic table with its type and return records as per its column type.

Basic Data Types
****************

``OVarchar``
............

.. index:: OVarchar

A string of limited length. Default length : 64

.. code-block:: java

	OColumn name = new OColumn("Name", OVarchar.class).setSize(100).setRequired();

``OInteger``
............

.. index:: Type: OInteger

An integer

.. code-block:: java

	OColumn counter = new OColumn("Counter", OInteger.class);


``OBoolean``
............

.. index:: Type: OBoolean

A boolean (true, false). Default false

.. code-block:: java

	OColumn is_active = new OColumn("Active", OBoolean.class);

``OFloat``
..........

.. index:: Type: OFloat

A floating point number.

.. code-block:: java

	OColumn weight = new OColumn("Weight", OFloat.class);

``OText``
.........

.. index:: Type: OText

A text field with no limit in length.

.. code-block:: java

	OColumn body = new OColumn("Message body", OText.class);

``OHtml``
.........

.. index:: Type: OHtml

A html (actual text) field with no limit in length.

.. code-block:: java

	OColumn body = new OColumn("Message body", OHtml.class);

``ODate``
.........

.. index:: Type: ODate

A date. Stores ``yyyy-MM-dd`` formatted date or ``false`` if value not set

.. code-block:: java

	OColumn date = new OColumn("Date", ODate.class);

``ODateTime``
.............

.. index:: Type: ODateTime

Allows to store a date and the time of day in the same field. Stores ``yyyy-MM-dd HH:mm:ss`` formatted date or ``false`` if value not set

.. code-block:: java

	OColumn date = new OColumn("Date", ODateTime.class);

``OBlob``
.........

.. index:: Type: OBlob

Allows to store a base64 data in database. Generally used by ir.attachment

.. code-block:: java

	OColumn image = new OColumn("Avatar", OBlob.class);

``OSelection``
..............

.. index:: Type: OSelection

Allows to store a string value (i.e., key for selection). Used selection for parsing Label for stored key.

.. code-block:: java

	OColumn state = new OColumn("State", OSelection.class)
		.addSelection("draft","Draft")
		.addSelection("confirm","Confirmed")
		.addSelection("close","Canceled")
		.addSelection("done","Done");

``OTimestamp``
..............

.. index:: Type: OTimestamp

Stores current date time to column.

.. code-block:: java

	OColumn order_date = new OColumn("Order date", OTimestamp.class);

Relation tyeps
**************

``OneToMany``
..............

.. index:: Type:: OneToMany

One2many field; the value of such a field is the recordset of all the records in comodel_name such that the field inverse_name is equal to the current record.

It required Type as another model's class type and also required realted column (as ManyToOne in related model)

.. code-block:: java
	
	OColumn parent_id = new OColumn("Company", ResPartner.class, RelationType.ManyToOne);
	OColumn child_ids = new OColumn("Contacts", ResPartner.class, RelationType.OneToMany).
		setRelatedColumn("parent_id");

``ManyToOne``
.............

.. index:: Type:: ManyToOne

The value of such a field is a recordset of size 0 (no record) or 1 (a single record).

.. code-block:: java
	
	OColumn parent_id = new OColumn("Company", ResPartner.class, RelationType.ManyToOne);

``ManyToMany``
..............

.. index:: Type:: ManyToMany

Many2many field; the value of such a field is the recordset.

.. code-block:: java
	
	OColumn tag_ids = new OColumn("Tags", NoteTag.class, RelationType.ManyToMany);


Base structure of class
***********************

- extends ``OModel`` class
- Contains columns, methods (custom method used for model)

.. code-block :: java

	class ResPartner extends OModel{

		public ResPartner(Context context, OUser user){
			super(context,"res.partner",user);
		}

	}

- Constructor with ``Context`` and ``OUser`` parameter only. Pass **model name** in super.
- This will create table with some base columns ``_id, id, create_date, write_date`` and more..

.. note::
	Note that database is created when you first time run your application, or when you clean your data from app setting. You need to clean application data everytime when you update your database column.

Adding some columns
...................

.. code-block:: java

	class ResPartner extends OModel{

		OColumn name = new OColumn("Name", OVarchar.class);
		OColumn parent_id = new OColumn("Company", ResPartner.class, RelationType.ManyToOne);

		public ResPartner(Context context, OUser user){
			super(context,"res.partner",user);
		}

	}

Note that, if you pass second parameter ``null`` while creating model object. It will take current active user object and treat all operation to current user database only.

You can add columns as your requirement. Framework will create each relation column table automatically. But if there is no any relation column for specific model and you need to create that table. You need to register it in ``BaseModels.java`` @See :ref:`ref-base-models`

Methods
*******

``setDefaultNameColumn()``
..........................

.. index:: setDefaultNameColumn()

**Syntax:**

``void setDefaultNameColumn(String nameColumn)``

Used when default **name** column is different. Default takes ``name``. Used for storing name column when ManyToOne record arrive.

.. code-block:: java

	public class ResPartner extends OModel {

		OColumn display_name = new OColumn("Name", OVarchar.class);

		public ResPartner(Context context, OUser user){
			super(context, "res.partner", user);
			setDefaultNameColumn("display_name");
		}

	}

``getDefaultNameColumn()``
..........................

.. index:: getDefaultNameColumn()

**Syntax:**

``String getDefaultNameColumn()``

Alternative of ``setDefaultNameColumn()`` override ``getDefaultNameColumn()`` method for return default name column.

.. code-block:: java

	public class ResPartner extends OModel {

		OColumn display_name = new OColumn("Name", OVarchar.class);

		public ResPartner(Context context, OUser user){
			super(context, "res.partner", user);
		}

		@Override
		public String getDefaultNameColumn(){
			return "display_name";
		}
	}

``setModelName()``
..................

.. index:: setModelName()

**Syntax:**

``void setModelName(String modelName)``

In some cases, you need to change model name (before just creating database table) depends on odoo version.

.. code-block:: java

	public class CalendarEvent extends OModel {
		...
		...

		public CalendarEvent(Context context, OUser user){
			super(context, "calendar.event", user);

			// Model name different for calendar.event in odoo version 7.0
			if(getOdooVersion().getVersionNumber() ==7){
				setModelName("crm.meeting");
			}
		}
	}

``getTableName()``
..................

.. index:: getTableName()

**Syntax:**

``String getTableName()``

Returns, table name for model. (Generally, ``res.partner`` become ``res_partner``)

.. code-block:: java

	ResPartner partner = new ResPartner(mContext, null);
	String tableName = partner.getTableName();

``setHasMailChatter()``
.......................

.. index:: setHasMailChatter()

**Syntax:**

``void setHasMailChatter(boolean hasChatter)``

Used to enable mail chatter below ``OForm`` view. takes boolean parameter 

.. code-block:: java

	public class ResPartner extends OModel {
		...
		...

		public ResPartner(Context context, OUser user){
			super(context, "res.partner", user);

			setHasMailChatter(true);
		}
	}

``getUser()``
.............

.. index:: getUser()

**Syntax:**

``OUser getUser()``

Used to get current active User object. returns ``OUser`` object

.. code-block:: java

	ResPartner partner = new ResPartner(mContext, null);
	OUser user = partner.getUser();

	String userName = user.getName();
	int userId = user.getUserId();

``getOdooVersion()``
....................

.. index:: getOdooVersion()

**Syntax:**

``OdooVersion getOdooVersion()``

Used to get current user's odoo version information. 

.. code-block:: java

	ResPartner partner = new ResPartner(mContext, null);

	OdooVersion odooVersion = partner.getOdooVersion();

	int versionNumber = getOdooVersion().getVersionNumber();
	String versionType = getOdooVersion().getVersionType();


``getColumns()``
................

.. index:: getColumns()

**Syntax:**

``List<OColumn> getColumns()``

``List<OColumn> getColumns(boolean local)``

Used to get list of models column. returns, ``ArrayList<OColumn>``

Takes one optional parameter boolean, If you want to get only local column or server columns

- ``local`` boolean

.. code-block:: java
	
	ResPartner partner = new ResPartner(mContext, null);

	// Getting all columns
	for(OColumn column: partner.getColumns()){
		Log.i(column.getName() , column.getLabel());
	}

	// Getting local columns
	for(OColumn column: partner.getColumns(true)){
		Log.i(column.getName() , column.getLabel());
	}

	// Getting server columns
	for(OColumn column: partner.getColumns(false)){
		Log.i(column.getName() , column.getLabel());
	}

``getRelationColumns()``
........................

.. index:: getRelationColumns()

**Syntax:**

``List<OColumn> getRelationColumns()``

Used when you need to get all relation columns, ``ManyToMany``, ``ManyToOne`` and ``OneToMany``

.. code-block:: java

	ResPartner partner = new ResPartner(mContext, null);

	for(OColumn column : partner.getRelationColumns()){
		Log.i(column.getName(), column.getLabel());
	}

``getColumn()``
...............

.. index:: getColumn()

**Syntax:**

``OColumn getColumn(String columnName)``

Used to get ``OColumn`` object by using its name

.. code-block:: java

	ResPartner partner = new ResPartner(mContext, null);

	OColumn display_name = partner.getColumn("display_name");

Note, if annotations applied and version not compitable with that column, it will returns ``null``

``getFunctionalColumns()``
..........................

.. index:: getFunctionalColumns()

**Syntax:**

``List<OColumn> getFunctionalColumns()``

Returns all functional columns (with annotation ``@Odoo.Functional``)

.. code-block:: java

	ResPartner partner = new ResPartner(mContext, null);

	for(OColumn column : partner.getFunctionalColumns()){
		Log.i(column.getName(), column.getLabel());
	}	

``getManyToManyColumns()``
..........................

.. index:: getManyToManyColumns()

**Syntax:**

``List<OColumn> getManyToManyColumns(OModel reationModel)``

Returns list of ``OColumn`` for many to many table.
Takes relation model object as parameter 

.. code-block:: java

	ResPartner partner = new ResPartner(mContext, null);

	for(OColumn col: partner.getManyToManyColumns(new Tags(mContext, null))){
		Log.i(col.getName(), col.getLabel());
	}

``createInstance()``
....................

.. index:: createInstance()

**Syntax:**

``OModel createInstance(Class<?> type)``

Used to create ``OModel`` object related to model class type.

.. code-block:: java

	ResPartner partner = new ResPartner(mContext, null);

	Tags tags = (Tags)partner.createInstance(Tags.class);

It will take ``Context`` and ``User`` from ``ResPartner`` object


``OModel.get()``
................

.. index:: OModel.get()

**Syntax:**

``OModel OModel.get(Context context, String modelName, String userAndroidName)``

Used to create ``OModel`` object for particular model by its model name. 

Takes, three parameters:
	
	1. ``Context`` context object
	2. ``String`` model name (i.e, res.partner or mail.message)
	3. ``String`` user account name (You can see it under account of android settings). Made of ``username`` and ``database`` E.g., username : ``admin``, database: ``production`` => account name : ``admin[production]`` or you can get it by user object. ``OUser.getAndroidName()``

.. code-block:: java

	OUser user = OUser.current(mContext);
	ResPartner partner = (ResPartner) OModel.get(mContext, "res.partner", user.getAndroidName());

``authority()``
...............

.. index:: authority()

**Syntax:**

``String authority()``

returns, default authority. Used by ``BaseModelProvider``.
can be change if you have custome ``ContentProvider``

.. code-block:: java
	
	ResPartner partner = new ResPartner(mContext, null);
	String authority  = partner.authority();

``uri()``
.........

.. index:: uri()

**Syntax:**

``Uri uri()``

Returns, model ``Uri`` object. Works with ``BaseModelProvider`` 

.. code-block:: java
	
	ResPartner partner = new ResPartner(mContext, null);
	
	Cursor cr = getContentResolver().query(
		partner.uri(), // URI
		null, // Projection
		null, // Selection
		null, // Selection arguments
		null // sort order
	);

``buildURI()``
..............

.. index:: buildURI()

**Syntax:**

``Uri buildURI(String authority)``

Used to create custom uri with different authority and path segments.

.. code-block:: java

	public class ResPartner extends OModel {

		public static final String CUSTOMER_FILTER = "com.odoo.base.addons.res.res_partner";
		...
		...

		public ResPartner(Context context, OUser user){
			super(context, "res.partner", user);
		}
		
		@Override
	    public Uri uri() {
	        return buildURI(CUSTOMER_FILTER);
	    }

	    public Uri indianCustomers() {
	        return uri().buildUpon().appendPath("in_customer_filter").build();
	    }
	}

``projection()``
................

.. index:: projection()

**Syntax:**

``String[] projection()``

``String[] projection(boolean onlyServerColumns)``

Returns string array of columns used as projection to ``ContentResolver`` ``query()`` method.

Optional parameter for getting only local column projection or server column projection.

.. code-block:: java

	ResPartner partner = new ResPartner(mContext, null);
	
	Cursor cr = getContentResolver().query(
		partner.uri(), // URI
		partner.projection(), // Projection
		null, // Selection
		null, // Selection arguments
		null // sort order
	);

Database Operations
*******************

``getLabel()``
..............

.. index:: getLabel() Selection type

Returns label for selection value. Works with ``OSelection`` type

.. code-block:: java

	CRMLead leads = new CRMLead(mContext, null);

	ODataRow row = leads.browse(1);

	String stateLabel = leads.getLabel("state", row.getString("state"));

``browse()``
............

.. index:: browse()

Returns ``DataRow`` object for record. ``null`` if no record found

.. code-block:: java

	ResPartner partner = new ResPartner(mContext, null);
	ODataRow row = partner.browse(2); // Here, 2 is belong to SQLite local auto incremented id i.e, _id

``browse()`` with projection:

.. code-block:: java

	ResPartner partner = new ResPartner(mContext, null);
	ODataRow row = partner.browse(new String[]{
			"name", "parent_id"
		}, 2);
	
``browse()`` with projection, selection and selection arguments

.. code-block:: java

	ResPartner partner = new ResPartner(mContext, null);
	
	List<ODataRow> rows = partner.browse(
		new String[]{"name","parent_id"}, 	// Projection
		"city = ?",				// Selection
		new String[]{"Gandhinagar"} 		// Selection arguments
		);

``getServerIds()``

.. index:: getServerIds()

returns list of server ids.

.. code-block:: java

	ResPartner partner = new ResPartner(mContext, null);

	List<Integer> serverIds = partner.getServerIds();

``isEmptyTable()``
..................

.. index:: isEmptyTable()

returns true, if table is empty.

.. code-block:: java

	ResPartner partner = new ResPartner(mContext, null);

	if(partner.isEmptyTable()){
		// Do synchronization stuff
	}

``select()``
............

.. index:: select()

Select all records from database

returns, list of ``ODataRow``

.. code-block:: java

	ResPartner partner = new ResPartner(mContext, null);
	List<ODataRow> rows = partner.select();
	for(ODataRow row: rows){

		// code of block

	}

``select()`` with projection

.. code-block:: java

	ResPartner partner = new ResPartner(mContext, null);
	
	List<ODataRow> rows = partner.select(
			new String[]{"name","parent_id","city"}
		);
	
	for(ODataRow row: rows){

		// code of block

	}

``select()`` with projection, selection and selection arguments

.. code-block:: java

	ResPartner partner = new ResPartner(mContext, null);
	
	List<ODataRow> rows = partner.select(
			new String[]{"name","parent_id","city"},
			"city = ?",
			new String[]{"Gandhinagar"}
		);
	
	for(ODataRow row: rows){

		// code of block

	}

``select()`` with projection, selection, selection arguments and sortOrder

.. code-block:: java

	ResPartner partner = new ResPartner(mContext, null);
	
	List<ODataRow> rows = partner.select(
			new String[]{"name","parent_id","city"},
			"city = ?",
			new String[]{"Gandhinagar"},
			"name DESC"
		);
	
	for(ODataRow row: rows){

		// code of block

	}


``insertOrUpdate()``
....................

.. index:: insertOrUpdate()

**Syntax:**

``int insertOrUpdate(int serverId, OValues values)``

``int insertOrUpdate(String selection, String[] selectionArgs, OValues values)``


Creates new record if not exists or update if exists

.. code-block:: java

	ResPartner partner = new ResPartner(mContext, null);

	OValues values = new OValues();
	values.put("id",1);
	values.put("name", "Dharmang Soni");

	int newId = partner.insertOrUpdate(1, values);

``insertOrUpdate()`` **with selection and selection arguments**

.. code-block:: java

	ResPartner partner = new ResPartner(mContext, null);

	OValues values = new OValues();
	values.put("id",1);
	values.put("name", "Dharmang Soni");

	int newId = partner.insertOrUpdate("id = ?", new String[]{"1"}, values);

``insert()``
............

.. index:: insert()

create new record with values. returns new created id if successfull, otherwise ``OModel.INVALID_ROW_ID`` ie., ``-1``

.. code-block:: java

	ResPartner partner = new ResPartner(mContext, null);

	OValues values = new OValues();
	values.put("id",1);
	values.put("name", "Dharmang Soni");

	int newId = partner.insert(values);

``delete()``
............

.. index:: delete()

Delete record from local. Server record will be deleted when synchronization done.

**Syntax:**

``delete(int row_id)``

``delete(String selection, String[] selectionArgs)``

retuns number of record deleted.

.. code-block:: java

	ResPartner partner = new ResPartner(mContext, null);

	int count = partner.delete(5);

	// or

	int count = partner.delete(OColumn.ROW_ID +" = ?", new String[]{"5"});


``selectServerId()``
....................

.. index:: selectServerId()

**Syntax:**

``int selectServerId(int row_id)``

returns server id for local record. 

If record not found, returns ``OModel.INVALID_ROW_ID`` i.e., ``-1``

.. code-block:: java

	ResPartner partner = new ResPartner(mContext, null);

	int serverId = partner.selectServerId(2);

``selectManyToManyRecords()``
.............................

.. index:: selectManyToManyRecords()

**Syntax:**

``List<ODataRow> selectManyToManyRecords(String[] projection, String column_name, int row_id)``

Returns list of many to many relation records for column and row.

.. code-block:: java

	ResPartner partner = new ResPartner(mContext, null);

	List<ODataRow> tags = partner.selectManyToManyRecords(new String[]{"name"}, "tag_ids", 2);

Here, **projection** is for related table (i.e., tags).

``count()``
...........

.. index:: count()

**Syntax:**

``int count(String selection, String[] selectionArgs)``

Returns number or record affecting selection.

.. code-block:: java

	ResPartner partner= new ResPartner(mContext, null);
	int total = partner.count("is_company = ?", new String[]{"true"});

``update()``
............

.. index:: update()

**Syntax:**

``int update(String selection, String[] args, OValues values)``

``int update(int row_id, OValues values)``

Update record value.

.. code-block:: java

	ResPartner partner = new ResPartner(mContext, null);

	OValues values =new OValues();
	values.put("name","Parth Gajjar");

	int updated = partner.update(5, values);

``query()``
...........

.. index:: query()

**Syntax:**

``List<ODataRow> query(String sql)``

``List<ODataRow> query(String sql, String[] args)``

Returns list of record generated by query.

.. code-block:: java

	ResPartner partner = new ResPartner(mContext, null);

	String sql = "SELECT _id, name, city FROM res_partner WHERE country_id = ?";

	List<ODataRow> records = partner.query(sql, new String[]{"4"});

``executeRawQuery()``
.....................

.. index:: executeRawQuery()

**Syntax:**

``Cursor executeRawQuery(String sql, String[] args)``

Used to execute raw queries. 

.. code-block:: java

	ResPartner partner = new ResPartner(mContext, null);

	Cursor cr = partner.executeRawQuery("select * from res_partner where customer = ?", new String[]{"true"});

``executeQuery()``
..................

.. index:: executeQuery()

**Syntax:**

``void executeQuery(String sql)``

Execute queries. DROP TABLE, CREATE TABLE, etc...


``getName()``
.............

.. index:: getName()

**Syntax:**

``String getName(int row_id)``

Returns ``name`` column value for record. `(@See setDefaultNameColumn() and getDefaultNameColumn())`

.. code-block:: java

	ResPartner partner = new ResPartner(mContext, null);

	String name = partner.getName(3);

``countGroupBy()``
..................

.. index:: countGroupBy()

**Syntax:**

``ODataRow countGroupBy(String column, String group_by, String having, String[] args)``

Returns ``ODataRow`` object with ``total`` column contains total number of records

.. code-block:: java

	ResPartner partner = new ResPartner(mContext, null);

	int total = partner.countGroupBy("parent_id", "parent_id", "parent_id != ?", new String[]{"false"});

Synchronization related methods
*******************************

``getLastSyncDateTime()``
.........................

.. index:: getLastSyncDateTime()

**Syntax:**

``String getLastSyncDateTime()``

Returns last synchronized date time for model.

.. code-block:: java

	ResPartner partner = new ResPartner(mContext, null);

	String lastSyncDateTime = partner.getLastSyncDateTime();


``defaultDomain()``
...................

.. index:: defaultDomain()

**Sytanx:**

``ODomain defaultDomain()``

Returns, default domain for model. called when synchronization occured. Default it return only blank object ``new ODomain()`` with no domain filter. You need to ``override`` this method for return you default domain filter for model.

.. code-block:: java

	class ResPartner extends OModel {

		....
		....
		....

		@Override
		public ODomain defaultDomain(){
			ODomain domain = new ODomain();
			domain.put("customer","=",true);
			return domain;
		}

	}

``checkForCreateDate()``
........................

.. index:: checkForCreateDate()

**Syntax:**

``boolean checkForCreateDate()``

Return true if you need to check for create date on synchronization. It will filter ``create_date`` with sync limit from setting. 

If return false, all the data are re-synchronized every time.

Default is ``True``

.. code-block:: java

	class ResPartner extends OModel {
		...
		...
		...

		@Override
		public boolean checkForCreateDate(){
			return true; 
		}
	}

``checkForWriteDate()``
.......................

.. index:: checkForWriteDate()

**Syntax:**

``boolean checkForWriteDate()``

Return true if you need to compare each of record with ``write_date``. It will reduce the trafic for requests. Only different write_date records are syncronized. 

If return false, nothing will checked with ``write_date``

Default ``True``

.. code-block:: java

	class ResPartner extends OModel {
		...
		...
		...

		@Override
		public boolean checkForWriteDate(){
			return true;
		}
	}


``allowUpdateRecordOnServer()``
...............................

.. index:: allowUpdateRecordOnServer()

**Syntax:**

``boolean allowUpdateRecordOnServer()``

If true, framework will update record on server if local record dirty and ``write_date`` is newer that server's ``write_date``

If false, framework will never update record on server with locally changed values.

Default ``True``

.. code-block:: java

	class ResPartner extends OModel {
		...
		...
		...

		@Override
		public boolean allowUpdateRecordOnServer(){
			return true;
		}
	}

``allowCreateRecordOnServer()``
...............................

.. index:: allowCreateRecordOnServer()

**Syntax:**

``boolean allowCreateRecordOnServer()``

If true, framework will create record on server if it found ``0`` value to ``id`` column. (i.e., locally created records)

If false, framework will never create record on server.

Default ``True``

.. code-block:: java

	class ResPartner extends OModel {
		...
		...
		...

		@Override
		public boolean allowCreateRecordOnServer(){
			return true;
		}
	}

``allowDeleteRecordOnServer()``
...............................

.. index:: allowDeleteRecordOnServer()

**Syntax:**

``boolean allowDeleteRecordOnServer()``

If true, framework will delete record from server if it locally ``inactive`` (deleted by user locally)

If false, framework will never delete record from server

Default ``True``

.. code-block:: java

	class ResPartner extends OModel {
		...
		...
		...

		@Override
		public boolean allowDeleteRecordOnServer(){
			return true;
		}
	}

``allowDeleteRecordInLocal()``
..............................

.. index:: allowDeleteRecordInLocal()

**Syntax:**

``boolean allowDeleteRecordInLocal()``

If true, framework will remove local record if server record not exist.

If false, framework will not remove any local record if server record not exist.

Default ``True``

.. code-block:: java

	class ResPartner extends OModel {
		...
		...
		...

		@Override
		public boolean allowDeleteRecordInLocal(){
			return true;
		}
	}

``onSyncStarted()`` and ``onSyncFinished()``
.............................................

.. index:: onSyncStarted() onSyncFinished()

**Syntax:**

``void onSyncStarted()``

``void onSyncFinished()``

Used when you need to perform any operation on sync start and finish.

.. code-block:: java

	class ResPartner extends OModel {
		...
		...
		...

		@Override
		public void onSyncStarted(){
			// Code of block
		}

		@Override
		public void onSyncFinished(){
			// code of block
		}
	}

``quickSyncRecords()``
......................

.. index:: quickSyncRecords()

**Syntax:**

``void quickSyncRecords(ODomain domain)``

Used when you need to synchronize some of records depends on some domain. You need to run this method in ``AsynTask`` or any background service.

.. code-block:: java

	new AsyncTask<Void,Void,Void>{

		public Void doInBackground(Void...args){
			ResPartner partner = new ResPartner(mContext, null);

			ODomain domain = new ODomain();
			domain.add("is_company", "=", true);

			partner.quickSyncRecords(domain);

			return null;
		}
	}.execute();

``quickCreateRecord()``
.......................

.. index:: quickCreateRecord()

**Syntax:**

``void ODataRow quickCreateRecord(ODataRow row)``

Return fully synced record data row object. You need to pass datarow object which contain ``id`` column with its server id value. Method will sync full record and return updated object.

.. code-block:: java

	new AsyncTask<Void,Void,Void>{

		public Void doInBackground(Void...args){
			ResPartner partner = new ResPartner(mContext, null);

			ODataRow row = new ODataRow();
			row.put("id", 49);

			row = partner.quickCreateRecord(row);
			return null;
		}
	}.execute();
	

Other
******

``isInstalledOnServer()``
.........................

.. index:: isInstalledOnServer()

**Syntax:**

``void isInstalledOnServer(String module_name, IModuleInstallListener callback)``

Check for module installed on server or not.

.. code-block:: java
	
	ResPartner partner = new ResPartner(mContex, null);
	partner.isInstalledOnServer("notes", new IModuleInstallListener() {
            @Override
            public void installedOnServer(boolean isInstalled) {
          		// isInstalled ?
            }
        });


``getServerDataHelper()``
.........................

.. index:: getServerDataHelper()

**Syntax:**

``ServerDataHelper getServerDataHelper()``

Used to perform some of server operations (works with live network only).

**Contains following methods:**

- ``getOdoo()`` : Returns ``Odoo`` object
- ``nameSearch()`` : Name search on server
- ``read()`` : Read record from server
- ``searchRecords()``  : search records with fields, domain and limit
- ``executeWorkFlow()`` : execute server workflow with signal
- ``callMethod()`` : call model's custom methods
- ``createOnServer()`` : quick create record on server (not create locally)
- ``updateOnServer()`` : quick update record on server (not update locally)

.. _base-model-provider:

BaseModelProvider.java
----------------------

.. index:: BaseModelProvider.java

Provide base database operation with ``ContentResolver``, 

extends ``ContentProvider`` and works with ``Uri``

We required to use ``BaseModelProvider`` when creating custom sync service for model. 

.. code-block:: java

	public class CustomersSyncProvider extends BaseModelProvider {

	    @Override
	    public String authority() {
	        return ResPartner.AUTHORITY;
	    }
	}

.. code-block:: xml

	<provider
            android:name="com.odoo.addons.customers.providers.CustomersSyncProvider"
            android:authorities="com.odoo.core.provider.content.sync.res_partner"
            android:label="@string/sync_label_customers"
            android:multiprocess="true" />

Adding custom Uri
*****************

- Register New Uri in model:

.. code-block:: java

	class MailMesage extends OModel {
		public static final String AUTHORITY = "your.custom.authority";
		...
		...
		...

		@Override
	    public Uri uri() {
	        return buildURI(AUTHORITY);
	    }

	    public Uri inboxURI(){
	    	return uri().buildUpon().appendPath(MailProvider.KEY_INBOX_MESSAGES).build();
	    }
	}

- Adding Uri to ``MailProvider`` 

.. code-block:: java

	public class MailProvider extends BaseModelProvider {
	    public static final String TAG = MailProvider.class.getSimpleName();
	    public static final int INBOX_MESSAGES = 234;
	    public static final String KEY_INBOX_MESSAGES = "inbox_messages";

	    @Override
	    public boolean onCreate() {
	        String path = new MailMessage(getContext(), null).getModelName()
	                .toLowerCase(Locale.getDefault());
	        matcher.addURI(authority(), path + "/" + KEY_INBOX_MESSAGES, INBOX_MESSAGES);
	        return super.onCreate();
	    }

	    @Override
	    public void setModel(Uri uri) {
	        super.setModel(uri);
	        mModel = new MailMessage(getContext(), getUser(uri));
	    }

	    @Override
	    public String authority() {
	        return MailMessage.AUTHORITY;
	    }

	    @Override
	    public Cursor query(Uri uri, String[] base_projection, String selection, String[] selectionArgs,
	                        String sortOrder) {
	        int match = matcher.match(uri);
	        if (match != INBOX_MESSAGES)
	            return super.query(uri, base_projection, selection, selectionArgs, sortOrder);
	        else {
	        	MailMessage mail = new MailMessage(getContext(), getUser(uri));
	        	return mail.executeRawQuery("select * from mail_message", null);
	        }
	    }
	}	

.. _sync-service-class:

OSyncService.java
-----------------

.. index:: OSyncService.java

Provide support for managing your sync requests and perform operation with your model data.. When uses sync adapter for custom sync service. Use ``OSyncService`` We need to extends ``OSyncService`` class which implement two methods:

1. getSyncAdapter()
2. performDataSync()

``getSyncAdapter()``
********************

.. index:: getSyncAdapter()

**Syntax:**

``OSyncAdapter getSyncAdapter(OSyncService serviceObj, Context context)``

Used to return intial sync adapter object. (do not use chaining methods in this method)

.. code-block:: java

	public class CustomerSyncService extends OSyncService {

	    @Override
	    public OSyncAdapter getSyncAdapter(OSyncService service, Context context) {
	        return new OSyncAdapter(context, ResPartner.class, this, true);
	    }

	    @Override
	    public void performDataSync(OSyncAdapter adapter, Bundle extras, OUser user) {
	        
	    }
	}

``performDataSync()``
**********************

.. index:: performDataSync()

Called just before data sync start. You can put some filters (domains), limiting data requiest in this method. Also you can specify the next sync operation after its sync finish.

.. code-block:: java

	public class CustomerSyncService extends OSyncService {
	    public static final String TAG = CustomerSyncService.class.getSimpleName();

	    @Override
	    public OSyncAdapter getSyncAdapter(OSyncService service, Context context) {
	        return new OSyncAdapter(context, ResPartner.class, service, true);
	    }

	    @Override
	    public void performDataSync(OSyncAdapter adapter, Bundle extras, OUser user) {
	        ODomain domain = new ODomain();
	        domain.add("active","=",true);
	        adapter.syncDataLimit(80).setDomain(domain);
	    }
	}


**Specify next sync operation for different model**

.. code-block:: java

	public class CustomerSyncService extends OSyncService implements ISyncFinishListener {
	    public static final String TAG = CustomerSyncService.class.getSimpleName();
	    private Context mContext;
	    @Override
	    public OSyncAdapter getSyncAdapter(OSyncService service, Context context) {
	        mContext = context;
	        return new OSyncAdapter(context, ResPartner.class, service, true);
	    }

	    @Override
	    public void performDataSync(OSyncAdapter adapter, Bundle extras, OUser user) {
	        if(adapter.getModel().getModelName().equals("res.partner")) {
	            ODomain domain = new ODomain();
	            domain.add("active", "=", true);
	            adapter.syncDataLimit(80).setDomain(domain);
	            adapter.onSyncFinish(this);
	        }
	    }

	    @Override
	    public OSyncAdapter performNextSync(OUser user, SyncResult syncResult) {
	        return new OSyncAdapter(mContext, ResCountry.class, this, true);
	    }
	}


Here, You can see we have checked for model name. Each time when you have chaining sync adapters you need to check for model name in ``performDataSync``. Otherwise, your service will go in infinite loop. 

``onSyncFinish()`` will tell service to perform next operation and service will continue to complete all the task in chain. 

Note: You have to check everytime for each of model to pass domain and filters by getting its model name.

If you dont wont to continue with next sync operation but you need to do some operation after sync finish. You can do it in two ways:

1. Just add sync finish call back and return ``null``. Do your operation before returning ``null`` or
2. Just override ``onSyncFinished()`` method in your model. Where you can perform your operations.