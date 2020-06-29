# Errors

MFIT API adheres to returning standard HTTP codes - Successful API call returns "200".

MFIT API uses the following error codes:


Error Code | Meaning
---------- | -------
400 | Bad Request -- Your request is invalid (pls check for password and other query parameters).
401 | Unauthorized -- Your API key is invalid/expired.
500 | Internal Server Error -- We had a problem with our server. Try again later.
501 | Not Implemented - Unrecognized file
440 | Session Timeout - Please login again (this is applicable for portal users and not direct API calls)
412 | Precondition Failed - Unverified User: Most likely someone outside is using your API Key. 



