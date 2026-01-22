## OAuth Integration Written Reflection

1. CSRF and the state Parameter: In your own words, explain how an attacker could perform a Cross-Site Request Forgery (CSRF) attack on an OAuth flow. How does using the state parameter, as recommended, prevent this specific attack?

When a user is already logged into a trusted site like GitHub, their browser automatically sends cookies with requests to that site. An attacker can take advantage of this by sending the user a malicious link that starts part of an OAuth login flow. If the user clicks the link while logged in, GitHub may think the request is valid and continue the authorization process.

In an OAuth CSRF attack, the attacker is not trying to change anything on GitHub itself. Instead, they are trying to trick another application into connecting the attacker’s GitHub account to the victim’s session on that application. This can happen if the app does not properly check that the OAuth response is one it actually started.
The state parameter helps prevent this by adding a random value to the OAuth request. The application saves this value and checks it when GitHub redirects the user back. If the value does not match, the app knows something is wrong and rejects the request. This helps make sure the login response belongs to the correct user and request.

2. Redirect URI Attacks: The article mentions that validating a redirect_uri by simply checking the domain or allowing subdomains is a common mistake. Describe a hypothetical scenario where a “leaky” redirect_uri validation (e.g., one that allows any path on a valid domain) could be exploited to steal an authorization code.

If a developer validates the redirect_uri by only checking the domain and allows any path on that domain, an attacker could take advantage of this by using a page on the same domain that they can control or abuse. For example, the application may allow example.com as a valid redirect domain, but not restrict the exact callback path.

An attacker could use a URL like https://example.com/redirect?url=https://attacker.com as the redirect_uri. When the user logs in and approves access, the authorization server redirects the user to that URL and includes the authorization code in the query string. The redirect page then sends the user to the attacker’s site, leaking the authorization code. The attacker can use that code to get access tokens.
This happens because the authorization server trusted the domain but did not strictly validate the full redirect URI, allowing the authorization code to be exposed.

3. User Experience vs. Security: Adding a third-party login option like “Login with Google” is a significant user experience improvement. However, it also introduces complexity and new potential security risks. Based on the article and your own thoughts, describe one key trade-off a development team must consider when deciding to implement OAuth. (For example, think about the balance between convenience for the user and the responsibility of the application to protect user data).

Adding a third-party login option like “Login with Google” makes things easier for users because they don’t need to create or remember another password. However, it also adds more security responsibility for the development team. Instead of just handling their own login system, they now have to correctly implement OAuth and protect the authorization flow from attacks like CSRF.

For example, if the application does not properly use and validate the state parameter, an attacker could manipulate the OAuth flow and link the wrong account to a user’s session. This means the team must carefully follow OAuth best practices, such as using unique and unpredictable state values and validating redirect responses. The trade-off is that while users get a smoother login experience, developers take on more complexity and risk if the OAuth flow is implemented incorrectly.