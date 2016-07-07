=========
Jira 
=========

Sync calls
-------------

1. It means that code execution will wait for API call to return before continuing. Until a response is returned by API, your application will not execute further.

2. Example Usage:
	- getting a list of changes such as
		* issue has been added, updated, deleted
		* comment has been added

3. sync calls can be:
	- unidirectional:
		GET/POST
	- bidirectional:
		GET+POST

4. activity stream:
	- list of events
	- recent activities by current user
	- we can add activity stream using rest api

Query
-----------

1. There are two ways:
	- Basic Search 
		* filter your search results using a user-friendly interface
	- Advanced Search
		* build structured queries using Jira Query Language (JQL)
		* you can save your search
		* Any query has 3 basic parts:
			+ field:
				like priority, fixVersion, issue type
			+ Operator:
				they relate field to value. 
				can be =, !=, <, >
			+ Value:
				actual data in query.
				item for which we are looking.
			+ Keyword:
				specific words that have special meaning
				like AND, OR
Authentication
-------------------

1. There are 3 authentication models:
	- basic authentication		
	- cookie based
	- oauth
		

2. Basic authentication
	- Basic access authentication is a method to authenticate on HTTP using username and password
	- Usage example:

	curl -D- -u <username:password> -X GET -H "Content-Type: application/json" <url>

	- If you simply specify the username, curl will prompt for password

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
					http://jira.example.com:8090/jira/rest/auth/1/session
				Example credentials:
					{ "username": "myuser", "password": "mypassword" }
		
		
			+ JIRA returns session object.
			+ Session object contains:
                        	information about this session
                        	session cookie
		* store session object on client
		* Add cookie name and value in 'cookie' field in header of your request
			Example:
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
