# kjson-annotations

[![Build Status](https://travis-ci.com/pwall567/kjson-annotations.svg?branch=main)](https://app.travis-ci.com/github/pwall567/kjson-annotations)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Kotlin](https://img.shields.io/static/v1?label=Kotlin&message=v1.6.10&color=7f52ff&logo=kotlin&logoColor=7f52ff)](https://github.com/JetBrains/kotlin/releases/tag/v1.6.10)
[![Maven Central](https://img.shields.io/maven-central/v/io.kjson/kjson-annotations?label=Maven%20Central)](https://search.maven.org/search?q=g:%22io.kjson%22%20AND%20a:%kjson-annotations%22)

Annotations for Kotlin JSON serialization and deserialization

These classes provide a means of customizing the operation of the [`kjson`](https://github.com/pwall567/kjson) library.

## Annotations

### `@JSONAllowExtra`

This annotation is applied to classes to indicate that extra properties in an object are allowed (and are to be ignored)
on deserialization.
It has no effect on serialization.

Example:
```kotlin
@JSONAllowExtra
data class Person(val name: String, val age: Int)
```
Deserializing the JSON string `{"name":"Fred Jones","age":25,"role":"manager"}` into this class will silently ignore the
`role` property.

### `@JSONDiscriminator`

This annotation supplies a "discriminator" property name to be used when serializing and deserializing sealed classes.
An additional property is added on serialization, and expected on deserialization, identifying the specific sub-class.

This annotation is applied to the base sealed class, and if it is not used, the `sealedClassDiscriminator` property of
the `JSONConfig` will be used, with the default value being "`class`".

Example:
```kotlin
@JSONDiscriminator("type")
sealed class Party
```
Classes derived from `Party` will have an extra property named `type` indicating the specific sub-class.

### `@JSONIdentifier`

This annotation supplies the value to be used in the discriminator property to determine the sub-class.

Example:
```kotlin
@JSONIdentifier("PERSON")
data class Person(val firstName: String, val lastName: String) : Party()
```
`Person` objects will have the discriminator property set to "`PERSON`" to indicate the sub-class.

### `@JSONIgnore`

This annotation is applied to properties (including `val` or `var` constructor parameters), and it indicates that the
relevant property is to be ignored during serialization and deserialization.
Note that a constructor parameter annotated with `@JSONIgnore` will need to have a default defined if the constructor is
to be used in the deserialization of an object with the property missing.

Example:
```kotlin
class Player(val name: String, @JSONIgnore val note: String? = null) {
    @JSONIgnore
    var score: Int = 0
}
```
Both the `note` and `score` properties will be ignored on serialization, and neither will be required (and will be
ignored if present) on deserialization.

### `@JSONIncludeAllProperties`

This annotation is applied to classes to indicate that all properties in the class are to be included in
auto-serialization even if `null`.
It has no effect on deserialization.

Example:
```kotlin
@JSONIncludeAllProperties
data class Project(val name: String, val description: String?)
```
The `description` property will be serialized as `"description":null` if the property is `null` instead of being
omitted.

### `@JSONIncludeIfNull`

This annotation is applied to individual properties (including `val` or `var` constructor parameters) to indicate that
the relevant property is to be included in auto-serialization even if `null`.

Example:
```kotlin
data class Project(val name: String, @JSONIncludeIfNull val description: String?)
```
The `description` property will be serialized as `"description":null` if the property is `null` instead of being
omitted.
This is similar to the example above but in this case the annotation applies only to the property `description`.

### `@JSONName`

This annotation is applied to properties and constructor parameters, and it supplies the name to be used in place of the
property or parameter name for auto-serialization and deserialization.

It takes a single parameter `name` &ndash; the name to be used.

Example:
```kotlin
data class Person(@JSONName("surname") val lastName: String, val firstName: String)
```
The property `lastName` will be serialized and deserialized using the property name `surname`.

## Alternative Annotations

In many cases, existing code will have annotations for the purposes above already in the code.
The `kjson` library can be configured to use these annotations from other libraries &ndash; see the `JSONConfig` class.

## Dependency Specification

The latest version of the library is 1.2, and it may be obtained from the Maven Central repository.

### Maven
```xml
    <dependency>
      <groupId>io.kjson</groupId>
      <artifactId>kjson-annotations</artifactId>
      <version>1.2</version>
    </dependency>
```
### Gradle
```groovy
    implementation "io.kjson:kjson-annotations:1.2"
```
### Gradle (kts)
```kotlin
    implementation("io.kjson:kjson-annotations:1.2")
```

Peter Wall

2022-01-22
