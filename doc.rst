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

Authentication
-------------------

- There are 3 authentication models:
	* basic authentication		
	* cookie based
	* oauth
		

- Basic authentication
	* Basic access authentication is a method to authenticate on HTTP using username and password
	* Usage example:
	curl -D- -u <username:password> -X GET -H "Content-Type: application/json" <url>

	* If you simply specify the username, curl will prompt for password

	* JIRA permits a default level of access to anonymous users. It doesnot supply a typical authentication challenge.

	* CAPTCHA:
		+ It is triggered after several consecutive failed log in attempts 
		+ user is supposed to interpret a distorted picture of word and type that word into a text field
		+ If captcha has been triggered, you cannot use Jira's REST api to authenticate with the JIRA site.
		+ how to check if captcha has been triggered?
			You will get an error response from JIRA with the header "X-Seraph-LoginReason" with value 			"AUTHENTICATION_DENIED"

- Cookie based authentication
	* If you are accessing Jira without logging in, you are accessing it anonymously
	* If you do not have permission to view something, you wont be able to view it using Jira REST Api either
	* Working:
		+ client creates a new session via JIRA REST API
		+ JIRA returns session object. Session object contains:
			- information about this session
			- session cookie
		+ Client stores this session object
		+ Client sets the cookie in header for all subsequent request to JIRA API
	* benefits:
		+ performance -> not having to make multiple authentication calls
	* disadvantage:
		+ security risks
			cookies can be hijacked if handled improperly
	* working:
		+ create a new session
