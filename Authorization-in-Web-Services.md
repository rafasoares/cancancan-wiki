If your web application provides a web service which returns XML responses then you will likely want to handle Authorization properly with a 403 response. You can do so by rendering an XML response when rescuing from the exception.

```ruby
rescue_from CanCan::AccessDenied do |exception|
  respond_to do |format|
    format.html { redirect_to root_url }
    format.xml { render :xml => "...", :status => :forbidden }
  end
end
```

**Note: I'm not certain what XML is conventionally returned here, if someone wants to fill this out more that would be great.**

Example from [[Amazon S3|http://doc.s3.amazonaws.com/proposals/copy.html]]

HTTP/1.1 403 Forbidden
x-amz-request-id: E4CA6F6767D6685C
x-amz-id-2: BHzLOATeDuvN8Es1wI8IcERq4kl4dc2A9tOB8Yqr39Ys6fl7N4EJ8sjGiVvu6wLP
Content-Type: application/xml
Date: Wed, 20 Feb 2008 23:19:01 +0000
Connection: close
Server: AmazonS3

```xml
<?xml version="1.0" encoding="UTF-8"?>
  <Error>
   <Code>AccessDenied</Code>
   <Message>Access Denied</Message>
   <RequestId>E4CA6F6767D6685C</RequestId>
   <HostId>BHzLOATeDuvN8Es1wI8IcERq4kl4dc2A9tOB8Yqr39Ys6fl7N4EJ8sjGiVvu6wLP</HostId>
  </Error>
```
