# Security and SSO

## Background Information
This is the result of an effort to add SSO functionality to the CVS Accelerators.

 -- Evan Gryska

 A long time ago in a galaxy far far away... We were using implicit flow and PCF SSO tile. At the time, there was some tricky stuff going on with validation of the user token in the Java app. We had to do this every time in all the apps we made, and a lot of engineers didn't fully understand. So we maybe pbm-security-library. Over time, we migrated to the Auth Code + PKCE flow, so the spring app server had more work to do on validating, requesting the token, using certificates instead of secrets, etc. So it evolved over time. The library is owned and maintained by the EPA Core team and I can give you those contacts. We have gotten approval from CVS Security to use the library. As a semi-important side note... A Microsoft Engineer reviewed the code and the flow a few months ago and recognized that we technically aren't using Auth Code + PKCE flow to its original definition. We kind of created a unique flow that is some combination of Auth Code + PKCE and something else... Microsoft engineer "unofficially" said that the flow looks secure, potentially even more secure than the official Auth Code + PKCE flow. But, because our custom flow hasn't been peer-reviewed and approved by the community/industry, he advised us to refactor the code/flow to follow the official Auth Code + PKCE flow. Also the fact that we are storing tokens in the browser is an item the EPA Core team had on their list to resolve. So, EPA Core team is definitely open to feedback and making updates. Not sure where it would lie on their priorities

 Soooooo... I'm not opposed if you guys want to align with the OSS libraries to bring us in line with official Auth Code + PKCE flow. But also, we could refactor pbm-security-library to use the official flow too. Or we could somehow try to "prove" that our new custom flow is at least "as secure as" the official Auth Code + PKCE flow (after changing how tokens are managed in the browser - outside of pbm-security-library)

 ## Step by Step Implementation

 ## Notes

 Some tutorials on Spring Security will reference a deprecated class:

 ```java
 org.springframework.security.oauth2.server.authorization.config.ProviderSettings
 ```

 This has been replaced by:

 ```java
 org.springframework.security.oauth2.server.authorization.settings.AuthorizationServerSettings
 ```

## References
- [A Guide to CSRF Protection in Spring Security](https://www.baeldung.com/spring-security-csrf)
- [PKCE Support for Secret Clients with Spring Security](https://www.baeldung.com/spring-security-pkce-secret-clients)
- [Spring Security: Upgrading the Deprecated WebSecurityConfigurerAdapter](https://www.baeldung.com/spring-deprecated-websecurityconfigureradapter)
- [Original Source Code in CVS GitLab](https://gitw.cvshealth.com/PBM/XP_Labs/pbm-security-library.git)