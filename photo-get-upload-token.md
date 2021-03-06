# API Method: /api/photo/get-upload-token
# flags=comments

Pre-authenticate an upload, allowing your client to upload directly to 23's servers from a web browser.

This method can be used to pre-authenticated uploads to a site without proxying them through your servers and the API. The flow is described in detail in the <a href="browser-based-uploads">Browser-based uploads</a> section of the API documentation, but in general terms this method will return a token which can be included in a standard HTML form (or even a more advanced Flash uploader, see [cross-domain policies](crossdomain)) to upload photos or videos directly to 23's servers.


### Parameters

<table class="pretty">
  <tr><th>Parameter</th><th>Description</th></tr>
  <tr><td><tt>return_url</tt></td><td>The URL where the user is return after both upload success and failure. See <a href="#flow">the Flow section</a> for more information. The callback is a GET request when permitted (when the return data is less than 1500 chars) and a POST in all other cases; so your callback handler needs to accept both GET and POST. If <tt>return_url</tt> is empty or omitted, the request will return a simple HTTP 200 OK mesage. </td></tr>
  <tr><td><tt>background_return_p</tt></td><td>Return to the callback url in the background. If this parameter is set to <tt>1</tt> the upload request will not redirect the client directly back to <tt>return_url</tt>. Instead, a request is made to <tt>return_url</tt> from the 23 servers and <a href="photo-redeem-upload-token">a plain text document in returned</a> containing status information. This option is design for uploading through clients wuch as Adobe Flash, where a client-side redirected might not be honoured. (The background callback takes the form of a HTTP GET request with a maximum length of 1500 chars.)</td></tr> 
  <tr><td><tt>user_id</tt></td><td>The user's ID. If no user ID is given, the objects will be uploaded anonymously. Requires <tt>super</tt>, otherwise files are uploaded using the authenticated user.</td></tr>
  <tr><td><tt>album_id</tt></td><td>A album (or channel) ID. Multiple values should be comma-separated.</td></tr>
  <tr><td><tt>title</tt></td><td>A title for the upload. All HTML tags will be stripped away from the title.</td></tr>
  <tr><td><tt>description</tt></td><td>A description of upload. The value can include save HTML tags such as &lt;b&gt; or &lt;p&gt;; however, if any disallowed tags are included, all html with be stripped from the description.</td></tr>
  <tr><td><tt>tags</tt></td><td>Space-separated list of tags to attach to the uploaded file.</td></tr>
  <tr><td><tt>publish</tt></td><td>Should the file be published in the site immediately?<br/>Default is <tt>1</tt>.<br/><i>Valid values:</i> <tt>0</tt> or <tt>1</tt></td></tr>
  <tr><td><tt>absolute_url</tt></td><td>If the uploaded photo or video is to be associated with a different canonical URI from the on assigned by default, set it here.</td></tr>
  <tr><td><tt>valid_minutes</tt></td><td>How many minutes should the upload token be valid for. The default is <tt>180</tt> minutes (or 3 hours). The minimum value is 15 minutes and the maximum is 24 hours.</td></tr>
  <tr><td><tt>max_uploads</tt></td><td>How many files can be uploaded using the returned token. The default is <tt>1</tt> upload; the maximum is 100</td></tr>
</table>

In addition to the standard parameters, any custom variable configured for `photo` objects can be set using the variable `key` as a parameter.

Any extra parameters sent to this method will be repeated in the callback request to `return_url` (if given).
    

### Permission level 

The minimum required [permission level](index#permission-level) is:

    write

### Example XML Response

    <response status="ok" permission_level="write" 
      message="The upload token is ready to use" cached="0">
      <upload_token>022fe6e3fb42758d6147c539727c5358af3524b1</upload_token>
      <title>A title for the new upload</title>
      <description>... and a prelimiary description</description>
      <publish>1</publish>
      <tags>product:1234, testemonial</tags>
      <user_id>17488</user_id>
      <album_id></album_id>
      <max_uploads>1</max_uploads>
      <valid_minutes>180</valid_minutes>
      <valid_until>1266711372</valid_until>
      <return_url>http://www.example.com/upload/callback?theme=green</return_url>
    </response>

### Example JSON Response

    {
      "status": "ok", 
      "message":"The upload token is ready to use",
      "permission_level":"write",
      "cached":"0",
      "uploadtoken":{
        "valid_minutes": "180", 
        "description": "... and a prelimiary description", 
        "upload_token": "022fe6e3fb42758d6147c539727c5358af3524b1", 
        "max_uploads": "1", 
        "album_id": "", 
        "return_url": "http://www.example.com/upload/callback?theme=green", 
        "title": "A title for the new upload", 
        "valid_until": "1266713254", 
        "publish": "1", 
        "tags": "product:1234, testemonial",
        "user_id": "17488"
      },
      "p": "1",
      "size": "1",
      "site": { ... },
      "endpoint": "/api/photo/get-upload-token"
    }

### The callback

The callback URL is defined by the `return_url` parameter, and the callback request will always take the form of a HTTP 301 redirect to a GET-style address. 

The request will always include the <tt>upload_token</tt> and <tt>domain</tt> as a parameter. If the upload succeeded, the parameters <tt>photo_id</tt>, <tt>token</tt>, <tt>tree_id</tt> will be included. On upload failure, the callback includes an <tt>error_message</tt> parameter. [See flow details](browser-based-uploads).
