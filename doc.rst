=========
Jira 
=========

Sync calls
-------------

1. Run an external program to query Jira using REST, check the changes, sync to your application.
	- This approach requires you to implement the logic that the issue has been updated
	- load on Jira for each polling.

2. Jira Command Line Interface
	- good functionality
	- launching the process every time can be a bit of a burden
	- easy to debug and maintain
	- recommended approach	

Callback support
-----------------

1. done through the use of jira webhooks which can be created using rest api

2. When webhook is triggered, it sends a JSON callback containing:
	- event id
	- timestamp
	- information about entity(issue, project etc) associated with event
	- additional information depending on type of event 

Query
---------

1. Jira REST API allows you to send a JQL query and recieve a subset of issues.

3. We can:
	- perform text search
	- run saved search

Individual lookup
------------------

1. User: 
	-There is no direct method to get all users in Jira REST Api.

	- You can search for a particular user using string:
	
	Example:
	https://servername/rest/api/2/user/search?username=a
	this returns all users that have "a" somewhere in their name

	- Workaround:
		* You can get list of all users belonging to a group (although you cannot get list of all groups)
		* You can get list of all users which can be assigned to a project
	
Collect sample data
-------------------

::

	>>curl -u username:password -X GET -H "Content-Type: application/json" https://issues.jboss.org/rest/api/2/issue/12406319?fields=status
	{"expand":"renderedFields,names,schema,transitions,operations,editmeta,changelog","id":"12406319","self":"https://issues.jboss.org/rest/api/2/issue/12406319","key":"ARQ-88","fields":{"status":{"self":"https://issues.jboss.org/rest/api/2/status/6","description":"The issue is considered finished, the resolution is correct. Issues which are not closed can be reopened.","iconUrl":"https://issues.jboss.org/images/icons/statuses/closed.png","name":"Closed","id":"6","statusCategory":{"self":"https://issues.jboss.org/rest/api/2/statuscategory/3","id":3,"key":"done","colorName":"green","name":"Done"}}}


Authentication
-------------------

1. There are 3 authentication models:
	- basic authentication		
	- cookie based
	- oauth
		

2. Basic authentication
	- Authenticate on HTTP using username and password
	- Usage example:
::

	curl -D- -u <username:password> -X GET -H "Content-Type: application/json" <url>

	- If you simply specify the username, curl will prompt for password. It doesnot give error

	- JIRA permits a default level of access to anonymous users. It doesnot supply a typical authentication challenge.

	- CAPTCHA:
		* It is triggered after several consecutive failed log in attempts 
		* user is supposed to interpret a distorted picture of word and type that word into a text field
		* If captcha has been triggered, you cannot use Jira's REST api to authenticate with the JIRA site.
		* how to check if captcha has been triggered?
			You will get an error response from JIRA with the header "X-Seraph-LoginReason" with value 			"AUTHENTICATION_DENIED"

3. Cookie based authentication
	- If you are accessing Jira without logging in, you are accessing it anonymously
	- If you do not have permission to view something, you wont be able to view it using Jira REST Api either
	- benefits:
		* performance -> not having to make multiple authentication calls
	- disadvantage:
		* security risks
			cookies can be hijacked if handled improperly
	- working:
		* create a new session
			+ Post user credentials to session resource
				Example of session resource:
::

					http://jira.example.com:8090/jira/rest/auth/1/session

				Example credentials:
::

					{ "username": "myuser", "password": "mypassword" }
		
		
			+ JIRA returns session object.
			+ Session object contains:
                        	information about this session
                        	session cookie
		* store session object on client
		* Add cookie name and value in 'cookie' field in header of your request
			Example:
::

				headers: {cookie: JSESSIONID=6E3487971234567896704A9EB4AE501F}

	- cookie expiration:
		* If cookie has expired, it gives 401 error response
		
	- This authentication model should be used when:
		* script involving REST API calls runs only for a few minutes
	
4. OAuth
	- Open standard of Authorization
	- A way through which users can log in using third party websites
	- Working:
		* obtain request token from JIRA
		* authorize this token
		* swap request token with access token
		* make authentication request to a REST end point

Authorization Levels
---------------------

+----------------------------+-----------------------------------------------------------------------------+
|Browse projects and issues  |Anyone                                                                       |                         
+----------------------------+-----------------------------------------------------------------------------+
|View commit information     |Anyone                                                                       |
+----------------------------+-----------------------------------------------------------------------------+
|Create issues	             |Registered user                                                              |
+----------------------------+-----------------------------------------------------------------------------+
|Add comments                |Registered user                                                              |
+----------------------------+-----------------------------------------------------------------------------+
|lookup                      |Anyone                                                                       | 
+----------------------------+-----------------------------------------------------------------------------+
|Query                       |Anyone. No field level security.                                             |
+----------------------------+-----------------------------------------------------------------------------+
|Callback                    |Register via JIRA REST API. User must have administrators global permission. |
+----------------------------+-----------------------------------------------------------------------------+
