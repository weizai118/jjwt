[![Build Status](https://travis-ci.org/jwtk/jjwt.svg?branch=master)](https://travis-ci.org/jwtk/jjwt)
[![Coverage Status](https://coveralls.io/repos/github/jwtk/jjwt/badge.svg?branch=master)](https://coveralls.io/github/jwtk/jjwt?branch=master)

## Java JWT: JSON Web Token for Java and Android

JJWT aims to be the easiest to use and understand library for creating and verifying JSON Web Tokens (JWTs) on the JVM.

JJWT is a pure Java implementation based exclusively on the [JWT](https://tools.ietf.org/html/rfc7519), 
[JWS](https://tools.ietf.org/html/rfc7515), [JWE](https://tools.ietf.org/html/rfc7516), 
[JWK](https://tools.ietf.org/html/rfc7517) and [JWA](https://tools.ietf.org/html/rfc7518) RFC specifications.

The library was created by [Okta's](http://www.okta.com) Senior Architect, [Les Hazlewood](https://github.com/lhazlewood)
and is supported and maintained by a [community](https://github.com/jwtk/jjwt/graphs/contributors) of contributors.

[Okta](https://developer.okta.com/) is a complete authentication and user management API for developers.

We've also added some convenience extensions that are not part of the specification, such as JWT compression and claim 
enforcement.

## Features

 * Fully functional on all standard JDKs and Android
 * Automatic security best practices and assertions
 * Stable implementation with 100% test code coverage
 * Easy to learn and read API
 * Convenient and readable [fluent](http://en.wikipedia.org/wiki/Fluent_interface) interfaces, great for IDE auto-completion to write code quickly
 * Fully RFC specification compliant on all implemented functionality, tested against RFC-specified test vectors
 * Creating, parsing and verifying digitally signed compact JWTs (aka JWSs) with all standard JWS algorithms:
    * HS256: HMAC using SHA-256
    * HS384: HMAC using SHA-384
    * HS512: HMAC using SHA-512
    * RS256: RSASSA-PKCS-v1_5 using SHA-256
    * RS384: RSASSA-PKCS-v1_5 using SHA-384
    * RS512: RSASSA-PKCS-v1_5 using SHA-512
    * PS256: RSASSA-PSS using SHA-256 and MGF1 with SHA-256<sup>1</sup>
    * PS384: RSASSA-PSS using SHA-384 and MGF1 with SHA-384<sup>1</sup>
    * PS512: RSASSA-PSS using SHA-512 and MGF1 with SHA-512<sup>1</sup>
    * ES256: ECDSA using P-256 and SHA-256<sup>1</sup>
    * ES384: ECDSA using P-384 and SHA-384<sup>1</sup>
    * ES512: ECDSA using P-521 and SHA-512<sup>1</sup>
    
     <sup>1. Requires a compatible JCA Provider (like BouncyCastle) in the runtime classpath.</sup>
 * Convenience enhancements beyond the specification such as
    * Body compression for any large JWT, not just JWEs
    * Claims assertions (requiring specific values)
    * Claim POJO marshaling and unmarshaling when using a compatible JSON parser (e.g. Jackson) 
    * Secure Key generation based on desired JWA algorithms
    * and more...
    
### Currently Unsupported Features

* [Non-compact](https://tools.ietf.org/html/rfc7515#section-7.2) serialization and parsing.
* JWE (Encryption for JWT)

These features will be implemented in a future release.  Community contributions are welcome!

## What is a JSON Web Token?

Don't know what a JSON Web Token is? Read on. Otherwise, jump on down to the [Installation](#Installation) section.

JWT is a means of transmitting information between two parties in a compact, verifiable form.

The bits of information encoded in the body of a JWT are called `claims`. The expanded form of the JWT is in a JSON format, so each `claim` is a key in the JSON object.
 
JWTs can be cryptographically signed (making it a [JWS](https://tools.ietf.org/html/rfc7515)) or encrypted (making it a [JWE](https://tools.ietf.org/html/rfc7516)).

This adds a powerful layer of verifiability to the user of JWTs. The receiver has a high degree of confidence that the JWT has not been tampered with by verifying the signature, for instance.

The compact representation of a signed JWT is a string that has three parts, each separated by a `.`:

```
eyJhbGciOiJIUzI1NiJ9.eyJzdWIiOiJKb2UifQ.ipevRNuRP6HflG8cFKnmUPtypruRC4fb1DWtoLL62SY
```

Each part is [Base64URL](https://en.wikipedia.org/wiki/Base64)-encoded. The first part is the header, which at a 
minimum needs to specify the algorithm used to sign the JWT. The second part is the body. This part has all 
the claims of this JWT encoded in it. The final part is the signature. It's computed by passing a combination of 
the header and body through the algorithm specified in the header.
 
If you pass the first two parts through a base 64 url decoder, you'll get the following (formatting added for 
clarity):

`header`
```
{
  "alg": "HS256"
}
```

`body`
```
{
  "sub": "Joe"
}
```

In this case, the information we have is that the HMAC using SHA-256 algorithm was used to sign the JWT. And, the 
body has a single claim, `sub` with value `Joe`.

There are a number of standard claims, called [Registered Claims](https://tools.ietf.org/html/rfc7519#section-4.1), 
in the specification and `sub` (for subject) is one of them.

To compute the signature, you need a secret key to sign it. We'll cover keys and algorithms later.

<a name="install"></a>
## Installation

Use your favorite Maven-compatible build tool to pull the dependencies from Maven Central.

The dependencies could differ slightly if you are working with a standard [JDK project](#install-jdk) or an 
[Android project](#install-android).

<a name="install-jdk"></a>
### JDK

If you're building a standard (non-Android) JDK project, you will want to define the following dependencies:

#### Maven

```xml
<dependency>
    <groupId>io.jsonwebtoken</groupId>
    <artifactId>jjwt-api</artifactId>
    <version>0.10.0</version>
</dependency>
<dependency>
    <groupId>io.jsonwebtoken</groupId>
    <artifactId>jjwt-impl</artifactId>
    <version>0.10.0</version>
    <scope>runtime</scope>
</dependency>
<dependency>
    <groupId>io.jsonwebtoken</groupId>
    <artifactId>jjwt-jackson</artifactId>
    <version>0.10.0</version>
    <scope>runtime</scope>
</dependency>

```

Note: The above `jjwt-jackson` dependency assumes you're using Jackson 2.x.  If you're using Jackson 1.x, 
please [read this](#olderJackson)

#### Gradle

```groovy
dependencies {
    compile 'io.jsonwebtoken:jjwt-api:0.10.0'
    runtime 'io.jsonwebtoken:jjwt-impl:0.10.0',
            // Uncomment the next line if you want to use Elliptic Curve crypto:
            //'org.bouncycastle:bcprov-jdk15on:1.60',
            'io.jsonwebtoken:jjwt-jackson:0.10.0'
}
```

<a name="install-android"></a>
### Android

Android projects will want to define the following dependencies and Proguard exclusions:

#### Gradle

Add the dependencies to your project:

```groovy
dependencies {
    compile 'io.jsonwebtoken:jjwt-api:0.10.0'
    runtime 'io.jsonwebtoken:jjwt-impl:0.10.0' 
    runtime('io.jsonwebtoken:jjwt-orgjson:0.10.0') {
        exclude group: 'org.json', module: 'json' //provided by Android natively
    }
    // Uncomment the next line if you want to use Elliptic Curve crypto:
    //runtime 'org.bouncycastle:bcprov-jdk15on:1.60'
}
```

#### Proguard

You can use the following [Proguard Exclusions](https://github.com/jwtk/jjwt/wiki/ProGaurd-exclusions) rules: 

```
-keepattributes InnerClasses

-keep class io.jsonwebtoken.** { *; }
-keepnames class io.jsonwebtoken.* { *; }
-keepnames interface io.jsonwebtoken.* { *; }

-keep class org.bouncycastle.** { *; }
-keepnames class org.bouncycastle.** { *; }
-dontwarn org.bouncycastle.**
```

### Understanding JJWT Dependencies

Notice the above dependency declarations all have only one compile-time dependency and the rest are declared as 
_runtime_ dependencies.

This is because JJWT is designed so you only depend on the APIs that are explicitly designed for you to use in
your applications and all other internal implementation details - that can change without warning - are relegated to
runtime-only dependencies.  This is an extremely important point if you want to ensure stable JJWT usage and
upgrades over time:

**JJWT guarantees semantic versioning compatibility for all of its artifacts _except_ the `jjwt-impl` .jar.  No such 
guarantee is made for the `jjwt-impl` .jar and internal changes in that .jar can happen at any time.  Never add the 
`jjwt-impl` .jar to your project with `compile` scope - always declare it with `runtime` scope.**

This is done to benefit you: great care goes into curating the `jjwt-api` .jar and ensuring it contains what you need
and remains backwards compatible as much as is possible so you can depend on that safely with compile scope.  The 
runtime `jjwt-impl` .jar strategy affords the JJWT developers the flexibility to change the internal packages and 
implementations whenever and however necessary.  This helps us implement features, fix bugs, and ship new releases to 
you more quickly and efficiently.

## Quickstart

Most complexity is hidden behind a convenient and readable builder-based [fluent interface](http://en.wikipedia.org/wiki/Fluent_interface), great for relying on IDE auto-completion to write code quickly.  Here's an example:

```java
import io.jsonwebtoken.Jwts;
import io.jsonwebtoken.SignatureAlgorithm;
import io.jsonwebtoken.security.Keys;
import java.security.Key;

// We need a signing key, so we'll create one just for this example. Usually
// the key would be read from your application configuration instead.
Key key = Keys.secretKeyFor(SignatureAlgorithm.HS256);

String jws = Jwts.builder().setSubject("Joe").signWith(key).compact();
```

How easy was that!?

In this case, we are:
 
 1. *building* a JWT that will have the 
[registered claim](https://tools.ietf.org/html/rfc7519#section-4.1) `sub` (subject) set to `Joe`. We are then
 2. *signing* the JWT using a key suitable for the HMAC-SHA-512 algorithm.  Finally, we are:
 3. *compacting* it into its final `String` form.  A signed JWT is called a 'JWS'.

The resultant `jws` String looks like this:

```
eyJhbGciOiJIUzI1NiJ9.eyJzdWIiOiJKb2UifQ.1KP0SsvENi7Uz1oQc07aXTL7kpQG5jBNIybqr60AlD4
```

Now let's verify the JWT (you should always discard JWTs that don't match an expected signature):

```java
assert Jwts.parser().setSigningKey(key).parseClaimsJws(jws).getBody().getSubject().equals("Joe");
```

**NOTE: Ensure you call the `parseClaimsJws` method** (since there are many similar methods available). You will get an `UnsupportedJwtException` if you parse your JWT with wrong method.

There are two things going on here. The `key` from before is being used to validate the signature of the JWT. If it 
fails to verify the JWT, a `SignatureException` (which extends from `JwtException`) is thrown. Assuming the JWT is 
validated, we parse out the claims and assert that that subject is set to `Joe`.

You have to love code one-liners that pack a punch!

But what if parsing or signature validation failed?  You can catch `JwtException` and react accordingly:

```java
try {

    Jwts.parser().setSigningKey(key).parseClaimsJws(compactJws);

    //OK, we can trust this JWT

} catch (JwtException e) {

    //don't trust the JWT!
}
```

## Signed JWTs (aka JWSs)

### Algorithms and Keys

#### Keys utility class

### Creating a JWS

#### Header Parameters

#### Claims

#### Body Compression

#### JSON Writer

#### Base64 URL Encoding

### Parsing a JWS

#### Claim Assertions

#### Other Exceptions

#### Body Compression

#### JSON Parser

#### Base64 URL Decoding

#### Security

<!-- TODO: ## Encrypted JWTs -->

## JSON Support

## Base64 Support


## Learn More

- [JSON Web Token for Java and Android](https://stormpath.com/blog/jjwt-how-it-works-why/)
- [How to Create and Verify JWTs in Java](https://stormpath.com/blog/jwt-java-create-verify/)
- [Where to Store Your JWTs - Cookies vs HTML5 Web Storage](https://stormpath.com/blog/where-to-store-your-jwts-cookies-vs-html5-web-storage/)
- [Use JWT the Right Way!](https://stormpath.com/blog/jwt-the-right-way/)
- [Token Authentication for Java Applications](https://stormpath.com/blog/token-auth-for-java/)
- [JJWT Changelog](CHANGELOG.md)

<a name="olderJackson"></a>
#### Already using an older Jackson dependency?

JJWT depends on Jackson 2.x (or later).  If you are already using a Jackson version in your own application less than 
2.x, for example 1.9.x, you will likely see [runtime errors](https://github.com/jwtk/jjwt/issues/1).  To avoid this, 
you should change your project build configuration to explicitly point to a 2.x version of Jackson.  For example:

```xml
<dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-databind</artifactId>
    <version>2.9.6</version>
</dependency>
```

## Author

Maintained by Les Hazlewood &amp; [Okta](https://okta.com/)

## License

This project is open-source via the [Apache 2.0 License](http://www.apache.org/licenses/LICENSE-2.0).
