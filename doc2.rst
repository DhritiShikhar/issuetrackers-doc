===========
Bugzilla
===========

Bugzilla exposes its api through xmlrpc protocol

Sync Calls only
-------------------

1. REST Api doesnot support event notification:
	https://bugzilla.redhat.com/show_bug.cgi?id=978953 

Callback Support
-------------------

Query
-------------------

1. You can save the query as Remembered Query

2. Advanced querying is done by Boolean chart

3. When you run a search, list of all matching bugs will be returned. Default search is to return all open bugs on the system -> not advised. 

4. Quicksearch:
	- single text box query
	- uses metacharacters to indicate what is to be searched
	- foo|bar will search foo OR bar in summary and status of bug
	- :Bazproduct will search only in that product

5. Querying can be done using MySQL 

6. You can get information about a particular bug using its:
	- id
	- alias

Example:
	- GET /rest/bug/(id_or_alias)
	- GET /rest/bug?product=Foo&product=Bar

7. You can get history of a specific bug:
	- GET /rest/bug/(id)/history
	- GET /rest/bug/(id)/history?new_since=YYYY-MM-DD

8. Advanced Search:
	use query parameters in the URL as your query for REST call

9. To get all comments:
	GET /rest/bug/(id_or_alias)/comment

Individual lookup
-------------------

1. You can search a user based on:
	- ID
	- real name
	- email address
	- match


Collect sample data
--------------------

Auth models
-------------------

1. Basic Authentication
	- send a HTTP get request with proper credentials to the endpoint:
		https://bugzilla.mozilla.org/rest/login?login=username&password=password
	- You will get an id and a token
	- Use the token to get authenticated search results
		https://bugzilla.mozilla.org/rest/bug?token=272345-L1KydUNCwq&f1=cf_tracking_firefox31&o1=equals&v1=%2B&include_fields=id

2. Two factor authentication
	- If you have two-factor authentication on your bugzilla account, then create a new API key and pass it to each request
		requests.get('https://bugzilla.mozilla.org/rest/bug', params={'id': '1214433', 'api_key': 'xyz'})

3. cookie authentication is not accepted.

Authorization Level
--------------------

1. Permission to do stuff in Bugzilla are defined by groups and Bugzilla users get membership to certain groups manually or automatically

2. Administrators:
	- who have "editbugs" priviledges can:
		edit all bug fields
	- who have "editkeywords" priviledges can:
		create, destroy, edit keywords
	- who have "editusers" priviledges can:
		create, update, block user profiles
	- who have "canconfirm" priviledges can:
		confirm a bug or mark it as duplicate
	- who have "admin" priviledges can:
		edit fields, groups -> total control over Bugzilla

3. Groups restrict visibility of bugs to a set of specific users.

4. There are two types of groups:
	- Generic group
	- Product Based group

5. There are two anchors in Bugzilla:
	- Assignee -> person who will fix the bug
	- Reporter -> person who added the bug

