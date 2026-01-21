# Flowable Keycloak integration library

> **Fork Notice**: This is a fork of [premium-minds/flowable-keycloak](https://github.com/premium-minds/flowable-keycloak) with modifications for **Keycloak 26.x compatibility**.

## Why This Fork?

The original library was designed for older Keycloak versions (pre-17.x) which used a different URL structure (`/auth/realms/...`) and token format. Keycloak 17+ introduced breaking changes:

1. **URL Structure Change**: Keycloak 17+ removed the `/auth` prefix from URLs. The OIDC discovery endpoint moved from `/auth/realms/{realm}/.well-known/openid-configuration` to `/realms/{realm}/.well-known/openid-configuration`.

2. **Token Format Changes**: Keycloak 26.x uses updated JWT token claims and structure, requiring changes to token extraction and validation logic.

3. **Spring Security Updates**: Newer Spring Boot versions use different OAuth2 configuration patterns, requiring updates to property binding and security filter chains.

## Changes Made

**Modified by [Claude Code](https://claude.ai/claude-code):**

| File | Change | Reason |
|------|--------|--------|
| `KeycloakProperties.java` | Updated `@ConfigurationProperties` binding | Spring Boot 2.7+ requires relaxed binding updates |
| `AuthenticationHandler.java` | Fixed OAuth2 token handling | Keycloak 26.x returns tokens with different claim structure |
| `KeycloakAccessTokenExtractor.java` | Updated token parsing logic | New JWT format in Keycloak 26.x (`realm_access` vs `resource_access` claims) |
| `OIDCClient.java` | Fixed OIDC client configuration | Updated for new discovery endpoint URL structure |
| `OIDCMetadataHolder.java` | Updated metadata fetching | Keycloak 26.x OIDC metadata has additional required fields |

## Branch

See the [keycloak-26-compatibility](https://github.com/fefehun/flowable-keycloak/tree/keycloak-26-compatibility) branch for all changes.

## Related Projects

- **[flowable-keycloak-env](https://github.com/fefehun/flowable-keycloak-env)** - Docker image using this fork
- **[flowable-keycloak-example](https://github.com/fefehun/flowable-keycloak-example/tree/keycloak-26-compatibility)** - Flowable UI modules using this library

---

This library allows to replace Flowable IDM with Keycloak integration (through OpenID Connect) to the
Flowable Apps (https://flowable.com/open-source/docs/bpmn/ch14-Applications/).

## Maven project
![Maven Central](https://img.shields.io/maven-central/v/com.premiumminds.flowable/flowable-keycloak)

Add the following maven dependency to your project `pom.xml`:

```xml
<dependency>
   <groupId>com.premiumminds.flowable</groupId>
   <artifactId>flowable-keycloak</artifactId>
   <version>1.6</version>
</dependency>
```
Check out [sonatype repository](https://oss.sonatype.org/index.html#nexus-search;quick~flowable-keycloak) for latest snapshots and releases.

## Example usage

This example works for the flowable-ui-* projects in the flowable repository:

* [Flowable UI Admin](https://github.com/flowable/flowable-engine/tree/master/modules/flowable-ui-admin)
* [Flowable UI Modeler](https://github.com/flowable/flowable-engine/tree/master/modules/flowable-ui-modeler)
* [Flowable UI Task](https://github.com/flowable/flowable-engine/tree/master/modules/flowable-ui-task)

### Changes in the project

Add the `flowable-keycloak` library to the POM of the `flowable-ui-*-conf` project.

Changes in the `SecurityConfiguration` class in the `flowable-ui-*-conf` project:

* Replace the class `FlowableCookieFilterRegistrationBean` with `KeycloakCookieFilterRegistrationBean`

### Configurations

This library reads the following configurations:

```
keycloak.url = <keycloak url>
keycloak.realm = <realm>
keycloak.issuer-url = <realm url, usually something like: http://example.com/auth/realms/mycompany>
keycloak.client.client-id = <client id>
keycloak.client.client-secret = <client secret>
keycloak.client.scope = openid roles
keycloak.client.redirect-uri = ${flowable.common.app.redirect-on-auth-success}/callback 
keycloak.connect-timeout = 10000
keycloak.read-timeout = 10000
```

For testing, you can add this configurations to the file 
`src/main/resources/flowable-default.properties` in the "flowable-ui-*-app" project.

### Keycloak roles

The library will add the client roles as flowable priviledges.

Flowable uses the following priviledges:

* `access-modeler`
* `access-rest-api`
* `access-admin`
* `access-task`

So you should have this as client roles in keycloak instance.

## Continuous Integration

[![Build Status](https://travis-ci.com/premium-minds/flowable-keycloak.png?branch=master)](https://travis-ci.com/github/premium-minds/flowable-keycloak)

CI is hosted by [travis-ci.com](https://travis-ci.com/)

## Licence

Copyright (C) 2020 [Premium Minds](https://www.premium-minds.com/)

Licensed under the [GNU Lesser General Public Licence](https://www.gnu.org/licenses/lgpl.html)
