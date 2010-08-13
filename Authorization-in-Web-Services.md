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