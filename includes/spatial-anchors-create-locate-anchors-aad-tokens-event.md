Like with access tokens, if an Azure AD token isn't set, you must handle the TokenRequired event, or implement the tokenRequired method on the delegate protocol.

You can handle the event synchronously by setting the property on the event arguments.
