===========
Bugzilla
===========

Bugzilla exposes its api through xmlrpc protocol

Sync Calls only
-------------------

1. Run an external program to query Bugzilla api, check the changes, sync to your application.
	- This approach requires you to implement the logic that the issue has been updated (Bugzilla allows you to retrieve history of a bug) 

Callback Support
-------------------

1. REDHAT REST Api doesnot support event notification:
        https://bugzilla.redhat.com/show_bug.cgi?id=978953


Query
-------------------

1. Bugzilla supports MYSQL queries.

2. You can save the search as Remembered search and query it. 

3. You can query using keyword/text.

Individual lookup
-------------------

+------------------+-----------------------------------------------+
|User              |Yes. through user id, real name, email address |
+------------------+-----------------------------------------------+
|Issue             |Yes. through issue id or alia                  |
+------------------+-----------------------------------------------+
|Comment           |Yes.                                           |
+------------------+-----------------------------------------------+
|Version/Branch    |Yes.                                           |
+------------------+-----------------------------------------------+
|Product/Component |Yes.                                           |
+------------------+-----------------------------------------------+      

Collect sample data
--------------------

::

	    >>curl --silent --insecure https://bugzilla.redhat.com/xmlrpc.cgi -H "Content-Type: text/xml" -d "<?xml version='1.0' encoding='UTF-8'?><methodCall><methodName>Bug.get</methodName> <params><param><value><struct><member><name>ids</name><value>575745</value></member></struct></value></param> </params> </methodCall>"

        <?xml version="1.0" encoding="UTF-8"?><methodResponse><params><param><value><struct><member><name>faults</name><value><array><data /></array></value></member><member><name>bugs</name><value><array><data><value><struct><member><name>priority</name><value><string>unspecified</string></value></member><member><name>cf_category</name><value><string>---</string></value></member><member><name>blocks</name><value><array><data /></array></value></member><member><name>creator</name><value><string>Marc Schoenefeld</string></value></member><member><name>last_change_time</name><value><dateTime.iso8601>20150819T08:44:27</dateTime.iso8601></value></member><member><name>cf_ovirt_team</name><value><string>---</string></value></member><member><name>is_cc_accessible</name><value><boolean>1</boolean></value></member><member><name>keywords</name><value><array><data><value><string>Security</string></value></data></array></value></member><member><name>cc</name><value><array><data><value><string>Andrew John Hughes</string></value><value><string>Andrew Haley</string></value><value><string>Deepak Bhole</string></value><value><string>Jiri Pechanec</string></value><value><string>Red Hat Product Security</string></value></data></array></value></member><member><name>url</name><value><string /></value></member><member><name>assigned_to</name><value><string>Red Hat Product Security</string></value></member><member><name>groups</name><value><array><data /></array></value></member><member><name>see_also</name><value><array><data /></array></value></member><member><name>id</name><value><int>575745</int></value></member><member><name>creation_time</name><value><dateTime.iso8601>20100322T10:21:00</dateTime.iso8601></value></member><member><name>whiteboard</name><value><string>impact=none,source=sun,public=20100330,reported=20100319</string></value></member><member><name>qa_contact</name><value><string /></value></member><member><name>depends_on</name><value><array><data /></array></value></member><member><name>cf_regression_status</name><value><string>---</string></value></member><member><name>docs_contact</name><value><string /></value></member><member><name>cf_story_points</name><value><string>---</string></value></member><member><name>resolution</name><value><string /></value></member><member><name>classification</name><value><string>Other</string></value></member><member><name>alias</name><value><array><data /></array></value></member><member><name>cf_doc_type</name><value><string>Bug Fix</string></value></member><member><name>op_sys</name><value><string>Linux</string></value></member><member><name>cf_crm</name><value><string /></value></member><member><name>target_release</name><value><array><data><value><string>---</string></value></data></array></value></member><member><name>status</name><value><string>NEW</string></value></member><member><name>cf_type</name><value><string>---</string></value></member><member><name>cf_documentation_action</name><value><string>---</string></value></member><member><name>summary</name><value><string>OpenJDK ThreadGroup finalizer allows creation of false root ThreadGroups (6639665)</string></value></member><member><name>cf_mount_type</name><value><string>---</string></value></member><member><name>is_open</name><value><boolean>1</boolean></value></member><member><name>platform</name><value><string>All</string></value></member><member><name>severity</name><value><string>unspecified</string></value></member><member><name>cf_environment</name><value><string /></value></member><member><name>version</name><value><array><data><value><string>unspecified</string></value></data></array></value></member><member><name>component</name><value><array><data><value><string>vulnerability</string></value></data></array></value></member><member><name>cf_fixed_in</name><value><string /></value></member><member><name>is_creator_accessible</name><value><boolean>1</boolean></value></member><member><name>product</name><value><string>Security Response</string></value></member><member><name>target_milestone</name><value><string>---</string></value></member><member><name>is_confirmed</name><value><boolean>1</boolean></value></member><member><name>cf_release_notes</name><value><string /></value></member><member><name>cf_verified_branch</name><value><string /></value></member></struct></value></data></array></value></member></struct></value></param></params></methodResponse>


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

+----------------------------------------+-------------------------------------------------------+
|who have "editbugs" priviledges         |can edit all bug fields                                |
+----------------------------------------+-------------------------------------------------------+
|who have "editkeywords" priviledges     |can create, destroy, edit keywords                     |
+----------------------------------------+-------------------------------------------------------+
|who have "editusers" priviledges        |can create, update, block user profiles                |
+----------------------------------------+-------------------------------------------------------+
|who have "canconfirm" priviledges       |can confirm a bug or mark it as duplicate              |
+----------------------------------------+-------------------------------------------------------+
|who have "admin" priviledges            |can edit fields, groups -> total control over Bugzilla |
+----------------------------------------+-------------------------------------------------------+

3. Groups:
	- restrict visibility of bugs to a set of specific users.
	- Types:
		* Generic group
		* Product-based group
