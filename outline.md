# Java JWT: JSON Web Token for Java and Android
## Features
### Currently Unsupported Features

## Overview

## Installation

### JDK

#### Maven

#### Gradle

### Android

#### Gradle

#### Proguard

### Understanding JJWT Dependencies

## Quickstart

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




### Enhancements Beyond the Specification:

* **Body compression.** If the JWT body is large, you can use a `CompressionCodec` to compress it. Best of all, the 
JJWT library will automtically decompress and parse the JWT without additional coding.

    ```java
    String compactJws =  Jwts.builder()
        .setSubject("Joe")
        .compressWith(CompressionCodecs.DEFLATE)
        .signWith(SignatureAlgorithm.HS512, key)
        .compact();
    ```

    If you examine the header section of the `compactJws`, it decodes to this:
    
    ```
    {
      "alg": "HS512",
      "zip": "DEF"
    }
    ```
    
    JJWT automatically detects that compression was used by examining the header and will automatically decompress 
    when parsing. No extra coding is needed on your part for decompression.

* **Require Claims.** When parsing, you can specify that certain claims *must* be present and set to a certain value.

    ```java
    try {
        Jws<Claims> claims = Jwts.parser()
            .requireSubject("Joe")
            .require("hasMotorcycle", true)
            .setSigningKey(key)
            .parseClaimsJws(compactJws);
    } catch (MissingClaimException e) {
    
        // we get here if the required claim is not present
    
    } catch (IncorrectClaimException e) {
    
        // we get here if the required claim has the wrong value
    
    }
    ```