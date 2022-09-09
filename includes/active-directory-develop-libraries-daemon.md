| Language / framework | Project on<br/>GitHub                                                                 | Package                                                                                | Getting<br/>started                           | Sign in users                                            | Access web APIs                                                 | Generally available (GA) *or*<br/>Public preview<sup>1</sup> |
|----------------------|---------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------|:---------------------------------------------:|:--------------------------------------------------------:|:---------------------------------------------------------------:|:------------------------------------------------------------:|
| .NET                 | [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)    | [Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client/) | [Quickstart](../articles/active-directory/develop/quickstart-v2-netcore-daemon.md) | ![Library cannot request ID tokens for user sign-in.][n] | ![Library can request access tokens for protected web APIs.][y] | GA                                                           |
| Java                 | [MSAL4J](https://github.com/AzureAD/microsoft-authentication-library-for-java)        | [msal4j](https://javadoc.io/doc/com.microsoft.azure/msal4j/latest/index.html)          | —                                             | ![Library cannot request ID tokens for user sign-in.][n] | ![Library can request access tokens for protected web APIs.][y] | GA                                                           |
| Node               | [MSAL Node](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node) | [msal-node](https://www.npmjs.com/package/@azure/msal-node)  | [Quickstart](../articles/active-directory/develop/quickstart-v2-nodejs-console.md)  | ![Library cannot request ID tokens for user sign-in.][n] | ![Library can request access tokens for protected web APIs.][y] | GA  |
| Python               | [MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python) | [msal-python](https://github.com/AzureAD/microsoft-authentication-library-for-python)  | [Quickstart](../articles/active-directory/develop/quickstart-v2-python-daemon.md)  | ![Library cannot request ID tokens for user sign-in.][n] | ![Library can request access tokens for protected web APIs.][y] | GA                                                           |
<!--
|PHP| [The PHP League oauth2-client](https://oauth2-client.thephpleague.com/usage/) | [League\OAuth2](https://oauth2-client.thephpleague.com/) | ![Green check mark.][n] | ![X indicating no.][n] | ![Green check mark.][y] | -- |
-->

<sup>1</sup> [Supplemental terms of use for Microsoft Azure Previews][preview-tos] apply to libraries in *Public preview*.

<!--Image references-->

[y]: ../articles/active-directory/develop/media/common/yes.png
[n]: ../articles/active-directory/develop/media/common/no.png

<!--Reference-style links -->

[preview-tos]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/