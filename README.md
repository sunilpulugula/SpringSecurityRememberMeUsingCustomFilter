# Introduction :

Remember-me or persistent-login authentication refers to web sites being able to remember the identity of a principal between sessions. This is typically accomplished by sending a cookie to the browser, with the cookie being detected during future sessions and causing automated login to take place. Spring Security provides the necessary hooks for these operations to take place, and has two concrete remember-me implementations. One uses hashing to preserve the security of cookie-based tokens and the other uses a database or other persistent storage mechanism to store the generated tokens.

Spring provides an Interface for remember me, that is RememberMeServices.This interface has two implementations 

1. TokenBasedRememberMeServices
2. PersistentTokenBasedRememberMeServices.

RememberMeServices is hooked in UsernamePasswordAuthenticationFilter, which will invoke a concrete RememberMeServices at the appropriate times.This interface therefore provides the underlying remember-me implementation with sufficient notification of authentication-related events, and delegates to the implementation whenever a candidate web request might contain a cookie and wish to be remembered.

In this application we provide a login form with remember me enabled and we use TokenBasedRememberMeServices for remember me token management.


# TokenBasedRememberMeServices :

This approach uses hashing to achieve a useful remember-me strategy. In essence a cookie is sent to the browser upon successful interactive authentication, with the cookie being composed as follows:

base64(username + ":" + expirationTime + ":" + md5Hex(username + ":" + expirationTime + ":" password + ":" + key))

here key is A private key to prevent modification of the remember-me token.

When user logs in,TokenBasedRememberMeServices generates a RememberMeAuthenticationToken token with above macanism.This token is sent back to user in response.
Further requests, remember-me token will be associated in each request cookies.RememberMeServices will decode this token and token is validated by comparing with expected TokenSignature.If tokens are same then a new token is generated and sent back to user else InvalidCookieException is thrown.

In addition, TokenBasedRememberMeServices requires A UserDetailsService from which it can retrieve the username and password for signature comparison purposes, and generate the RememberMeAuthenticationToken to contain the correct GrantedAuthority[]s.
