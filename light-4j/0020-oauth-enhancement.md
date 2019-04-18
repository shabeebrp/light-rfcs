### Summary

In order to meet the various demands of users, a more generic method to get JWT may needs to be provided, and users should be able to choose multiple caching ways.

### Motivation


### Guide-level explanation
A generic mode for getting jwt consists of two parts. One is the caching strategy, and the other is what information it needs to include in the token request. So a refactoring method can be provided in the TokenManager.

E.g:

getJwt(Jwt.Key key, TokenRequest tokenRequest);

The first parameter is the caching key. It provides the following methods:

1. Control the caching mechanism to be enabled or disabled

2. Set values to identify the caching key. On the other words, using what values to do caching. Currently, the following values is supported to be a caching key
  
  * custom claims
  
  * refresh token
  
  * authorization code
  
  * scope
  
  * customer id

  * saml assertion

The other parameter is tokenRequest, which contains the information that needs to be included in the token request. This information is then automatically integrated into the request header or body.

Through such a design. When users need different caching strategies or different request components, they can make flexible choices.

An example of usage:

1. enable caching and use the scopes as caching key. Sent a client_credential token request

```
// build a jwt key
Jwt.Key key = new Jwt.Key();
key.setScopes(Collections.singleton(scope));
key.setCachable(true);
// build the reqeust
TokenRequest tokenRequest = new TokenRequest();
tokenRequest.setGrantType("client_credential");
tokenRequest.setServerUrl("http://localhost:7777");
tokenRequest.setUri("/oauth2/token");
tokenRequest.setClientId("test_client");
tokenRequest.setClientSecret("test_secret");
tokenRequest.setScope(Collections.singletonList(scope));
tokenRequest.setEnableHttp2(true);
// get token
Result<Jwt> result = TokenManager.getInstance().getJwt(key, tokenRequest);
```

2. disable caching

```
// build a jwt key
Jwt.Key key = new Jwt.Key();
key.setCachable(false);
// build the reqeust
TokenRequest tokenRequest = new TokenRequest();
tokenRequest.setGrantType("client_credential");
tokenRequest.setServerUrl("http://localhost:7777");
tokenRequest.setUri("/oauth2/token");
tokenRequest.setClientId("test_client");
tokenRequest.setClientSecret("test_secret");
tokenRequest.setScope(Collections.singletonList(scope));
tokenRequest.setEnableHttp2(true);
// get token
Result<Jwt> result = TokenManager.getInstance().getJwt(key, tokenRequest);
```

In addition, the client module should also support the custom claim, and the user can add an encoded custom claim to their request header by using Base64Encoder. This header will be verified by the client -- whether it can be successfully decoded. Then it is encoded again and added to the request body sent to the oauth server. At the same time, the custom claim will also be part of the cache key.

### Reference-level explanation


### Drawbacks


### Rationale and Alternatives


### Unresolved questions

