# Android Secure Persist Library
## Leverage your Persistence and Encryption in Android
*by Ioannis Anifantakis*

## Introduction

In modern mobile apps, safeguarding user data is crucial. Whether it’s user credentials, API tokens, or any sensitive information, SecurePersist allows developers to implement robust encryption and persistence with minimal effort.

> Imagine needing to securely store users’ authentication tokens in a finance app — this library will help you do that efficiently while keeping the process simple.

**Android Secure Persist Library** is a pure Kotlin library designed to provide secure and efficient storage of preferences, complex data types, and files in Android applications. By leveraging the Android KeyStore and modern encryption techniques, SecurePersist ensures that sensitive data — including complex objects and files — is stored safely, protecting it from unauthorized access.

With the latest addition of **State Persistence**, SecurePersist now also offers seamless integration with *Jetpack Compose*, enabling developers to persist and restore UI state effortlessly. This ensures that your Compose-based interfaces maintain consistent and secure state across recompositions and app restarts without additional boilerplate.

## What this library does

This library allows out of the box with zero-configuration encrypting and decrypting preferences using `SharedPreferences` and `DataStore`, supports serialization of complex data types, and provides robust raw data and file encryption capabilities.

So this library makes it easy for developers to

1. implement comprehensive encrypted storage solutions
2. encrypt raw data and files with ease


## Features

This library offers a wide range of features for securely persisting data, while also providing encryption services for raw data and files when needed.

### Feature Set 1 - Encrypted Persistence

* **Secure Preferences Management**: Easily encrypts and decrypts preferences utilizing `SharedPreferences` and `DataStore`.

* **Support for Complex Data Types**: Automatically **serializes** and securely stores complex objects, including custom classes and collections.

* **File Encryption and Decryption**: Securely encrypts and decrypts files, ensuring sensitive data remains protected even when stored externally.

* **Property Delegation**: Uses Kotlin property delegation for seamless integration of encrypted preferences.

* **Asynchronous Operations**: Efficiently handles preferences with non-blocking operations using `DataStore`.

* **Jetpack Compose State Persistence**: Seamlessly integrates with Jetpack Compose by providing `MutableState` delegates that automatically persist and restore UI state. This ensures your Compose components maintain consistent and secure state across recompositions and app restarts without additional boilerplate.

### Feature Set 2 - Raw Encryption

* **Raw Data Encryption**: Directly encrypts and decrypts raw **data and files** using `EncryptionManager` for additional flexibility.

* **External Key Management**: Allows for custom external keys for scenarios requiring cross-device data decryption or storing the key on a remote server.

## Why Use SecurePersist?

* **Security**: Protects sensitive data with robust encryption techniques, including complex objects and files.

* **Ease of Use**: Simplifies the process of managing encrypted preferences and data with a user-friendly API.

* **State Persistence**: Seamlessly integrates with Jetpack Compose by providing `MutableState` delegates that automatically persist and restore UI state. This ensures your Compose components maintain consistent and secure state across recompositions and app restarts without additional boilerplate.

* **Versatility**: Supports a variety of data types, including primitives, complex objects, and files, integrating seamlessly with existing Android components.

* **Performance**: Ensures non-blocking operations for a smooth user experience.

* **Flexibility**: Allows for external key management, enabling secure data storage and retrieval across devices or from remote servers.

---

## Installation

There are two ways to install this library to your project

### Option 1: By adding the Module Directly to Your Project
The `secure-persist` directory contains the core module of this library, while `secure-persist-compose` provides additional functionality for Jetpack Compose state persistence. You can choose to include either or both based on your project's needs.

1. **Copy the Modules**
    * Copy the `secure-persist` directory from this repository into the root folder of your project.
    * If you plan to use Jetpack Compose state persistence, also copy the `secure-persist-compose` directory.
2. **Include the Modules** in `settings.gradle` so android recognize these folders as modules
```kotlin
rootProject.name = "My App Name"
include(":app")
include(":secure-persist") // Core library for secure data persistence
include(":secure-persist-compose") // Additional module for Jetpack Compose state persistence
```
3. At your App-Module's `build.gradle` file dependencies, add the module that your project contains
```kotlin
implementation(project(":secure-persist"))
implementation(project(":secure-persist-compose")) 
```
> **Note:** If your project does not utilize Jetpack Compose, you can omit the `secure-persist-compose` dependency.

### Option 2: Through JitPack Repository

[![](https://jitpack.io/v/ioannisa/SecuredAndroidPersistence.svg)](https://jitpack.io/#ioannisa/secured-android-persist)

1. Add this to your dependencies
```kotlin
implementation("com.github.ioannisa.secured-android-persist:secure-persist:2.4.0")
implementation("com.github.ioannisa.secured-android-persist:secure-persist-compose:2.4.0")
```



2. Add **Jitpack** as a dependencies repository in your `settings.gradle` (or at Project's `build.gradle` for older Android projects) for gradle to know how to fetch dependencies served by that repository:
```kotlin
repositories {
    google()
    mavenCentral()
    maven(url = "https://jitpack.io") // <-- add this line
}
```
> **Important:** Similar to Option 1, if your project does not use Jetpack Compose, you can omit the `secure-persist-compose` dependency.



### Why Two Implementation Steps?
SecurePersist is modularized to provide flexibility based on your project's requirements:
* **`secure-persist` (Core Library):**

    * **Purpose:** Provides secure data persistence capabilities using `SharedPreferences` and `DataStore`.
    * **Usage:** Ideal for projects that need to securely store preferences, complex data types, and files
    
* **`secure-persist-compose` (Jetpack Compose Integration):**
    * **Purpose:** Extends the core library by adding Jetpack Compose state persistence functionality.
    * **Usage:** Specifically for projects utilizing Jetpack Compose, allowing developers to persist and restore UI state effortlessly using property delegation.

**Reason for Separation:** Not all Android projects use Jetpack Compose. By keeping this functionality in a separate module, developers can include it only when needed, reducing unnecessary dependencies and potential overhead in projects that do not use Compose.

> **Summary:**
    - **Include Both Modules:** If your project uses Jetpack Compose and you want to leverage state persistence alongside secure data storage.
    - **Include Only `secure-persist`:** If your project does not use Jetpack Compose or you do not require state persistence.

---

## PersistManager

The `PersistManager` is the core component of `SecurePersist`, responsible for managing encrypted preferences using both `SharedPreferences` and `DataStore` **with zero-configuration**.

It utilizes the `EncryptionManager`'s cryptographic algorithms to securely handle data. Additionally, it supports encryption of complex data types, including custom objects and collections, with automatic serialization powered by the Gson library in the background.

## Initialization

When initializing `PersistManager`, an instance of `EncryptionManager` is automatically created to manage the encryption and decryption of persisted data. If your encryption needs are limited to preferences, creating an additional `EncryptionManager` instance is unnecessary.

*However, if you need to apply encryption to raw data or files outside of preference management, you can directly utilize the `EncryptionManager` included in the library for custom encryption tasks unrelated to persistence. This flexibility allows you to securely handle both persistent and non-persistent data within a single solution.*

During `PersistManager` initialization, you can optionally specify a `KeyStore` alias. If none is provided, a default alias ("keyAlias") will be generated for you. This alias serves as an identifier for the pool of keys stored in the KeyStore, so even if you don’t define a custom alias, the default will function without any issues.

```Kotlin
// Create a PersistManager instance with a custom KeyStore alias
val persistManager = PersistManager(context, "your_key_alias")
    
// Create a PersistManager instance with the default KeyStore alias ("keyAlias")
val persistManager = PersistManager(context)
```

---

## Preference Management

`PersistManager` provides a unified and flexible way to handle encrypted preferences using both `SharedPreferences` and `DataStore`.

The `PersistManager` class currently provides multiple ways to manage `SharedPreferences` and `DataStore` preferences, offering flexibility based on your needs:

via the `sharedPrefs` instance and the `dataStorePrefs` instance exposed by the `PersistManager` class, you can access the **EncryptedSharedPreferences** and **DataStore Preferences - With Encryption Support** directly and via delegation:

 — **`sharedPrefs` instance variable** that leads to the `SharedPreferencesManager` class, responsible for managing encrypted shared preferences.
 — **`dataStorePrefs` instance variable** that leads to the `DataStoreManager` class, responsible for managing DataStore Preferences with encryption.

## Preference Management using Property Delegation

As mentioned, `PersistManager` simplifies the usage of encrypted `SharedPreferences` or `DataStore` preferences by supporting **property delegation** *(using the `by` keyword)*. This makes handling advanced persistence as straightforward as working with regular Kotlin properties, while also managing encryption and serialization behind the scenes.


Via the **`preference`** of the `sharedPrefs` and the `dataStorePrefs` you can use delegation.

Both methods offer a seamless and secure way to manage preferences with minimal effort.

> **Important Note:**
**When handling DataStore Preferences via the property delegation approaches**, it's crucial to note that the system manages coroutines internally. This means that `put` operations are non-blocking, ensuring efficient data storage. However, retrieving preferences with `get` is handled in a blocking manner, which may impact performance during data access.

**1. `preference`** function utilizing the **`EncryptedSharedPreferences`**
```kotlin
// assuming key to be the variable name (myKey) 
var myKey by persistManager.sharedPrefs.preference( "default value")

// declaring the key to be "myKey"
var myPref by persistManager.sharedPrefs.preference("default value", "myKey")
```

**2. `preference`** fuction utilizing the **`DataStore`**
```kotlin
// declaring the key to be "myKey"
// declaring Storage to Encrypted DataStore Preferences
var myPref by persistManager.dataStorePrefs.preference(
    defaultValue = "default value",
    key = "myKey",
)

// declaring the key to be "myKey"
// declaring Storage to Unencrypted DataStore Preferences
var myPref by persistManager.dataStorePrefs.preference(
    defaultValue = "default value",
    key = "myKey",
    encrypted = false
)
```

**Notes:**
* If `key` is `null` or empty, the property name will be used as the key.
* When using `DataStore`, you can specify whether the data should be encrypted by choosing the appropriate Storage.


### Reading and Writing persisted data with property delegation
In the example below, we declare an encrypted shared preference managed by `PersistManager`, which handles an instance of a data class `AuthInfo`.

When we declare it, `PersistManager` automatically uses the variable's name as the `key` for the encrypted shared preference.
Since we don't specify a `Storage` type, `PersistManager` defaults to `SHARED_PREFERENCES`. It serializes the object into JSON using the `Gson` library and stores it encrypted.

Whenever we access this object, the `get` method of the property delegate is triggered. This method decrypts and deserializes the stored data, reconstructing an instance of `AuthInfo`.

To delete a preference, we need to use the `delete` function provided by `PersistManager`.

```kotlin
data class AuthInfo(
    val accessToken: String = "",
    val refreshToken: String = "",
    val expiresIn: Long = 0L
)

// EncryptedSharedPreferendes, and key="authInfo"
var authInfo by persistManager.sharedPrefs.preference(AuthInfo())

// Update authInfo as if it was a normal variable
authInfo = AuthInfo(
    accessToken = "token123",
    refreshToken = "refresh123",
    expiresIn = 3600L
)

// Access as if it was a normal variable
// It retrieves the encrypted shared preference
println(authInfo)

// Deleting data
// if you try to access the delegate again it will return default value
persistManager.delete("authInfo")
```

## PersistManager — Secure Preferences without delegation

The `PersistManager` class supports all the mentioned functionalities, while also offering traditional coding methods alongside the two property delegate approaches previously discussed.

For that, it exposes two public instance variables, each tailored for a specific use case:

* **`sharedPrefs`** specialized for encrypted preferences via EncryptedSharedPreferences

* **`dataStorePrefs`** specialized for preferences via DataStore with encryption via the `EncryptionManager` class which is contained in this library.

These variables allow you to choose the appropriate preference management method based on your needs, ensuring flexibility and security across different storage mechanisms.

## Handling SharedPreferences using the `sharedPrefs` instance variable
This will introduce you to the `PersistManager`’s `sharedPrefs` instance variable to handle encrypted shared preferences.

```kotlin
// Encrypt and save a preference
persistManager.sharedPrefs.put("key1", "secureValue")

// Decrypt and retrieve a preference
val value: String = persistManager.sharedPrefs.get("key1", "defaultValue")

// Delete a preference
persistManager.sharedPrefs.delete("key1")
```

To handle `Double` and complex data types, the library uses serialization via the `gson` library, while standard types are supported natively by the `SharedPreferences` avoid serialization.

| Data Type | Supported Directly | Handled via gson Serialization |
|-----------|:------------------:|:------------------------------:|
| Boolean   | Yes                | No                             |
| Int       | Yes                | No                             |
| Float     | Yes                | No                             |
| Long      | Yes                | No                             |
| Double    | No                 | Yes                            |
| String    | Yes                | No                            |
| Custom Objects (e.g., Data Classes) | No | Yes                  |

## Handling DataStore Preferences using the dataStorePrefs instance variable

**`DataStore`** Preferences is a modern, non-blocking, and highly efficient solution for managing application preferences, built around Kotlin’s **coroutine** architecture. Unlike `SharedPreferences`, which operates synchronously and can block the main thread, `DataStore` performs operations asynchronously, ensuring a smoother and more responsive user experience.

However, a key limitation of `DataStore` is the lack of built-in encryption, unlike `EncryptedSharedPreferences`. This makes secure implementations more challenging, leading some developers to revert to `SharedPreferences` despite `DataStore`'s superior performance and flexibility.

1. To address this, our library’s **`EncryptionManager`** provides the missing encryption layer for `DataStore`, allowing developers to securely handle preferences without compromising on performance or having to revert to `SharedPreferences`.

1. Additionally, the library allows for automatic serialization of complex data

1. And finally, our library can provide an extra way of accessing the DataStore preferences by internally handling coroutines, enabling direct access to `DataStore` preferences with non-blocking functions for storing and deleting preferences. For retrieval operations, blocking access is provided, making usage as simple as with `EncryptedSharedPreferences`, but more performant for storing and deleting data.

> **Note**: For DataStore preferences handled via PersistManager, encryption is enabled by default unless specified otherwise.

### Option 1 — Accessing `DataStore` with `Coroutines`

Exposing coroutines directly to the developer allows for non-blocking and performant way of storing, deleting and retrieving data, just as designed by DataStore, but with the extra ability to have encryption and without having to make any initialization.

By exposing coroutines directly to the developer, our library enables a non-blocking, efficient way to `put`, `delete`, and `get` data — just as DataStore was designed to do.

This approach maintains the performance benefits of `DataStore`, while adding the critical capability of encryption, all without requiring any additional setup or initialization from the developer. This seamless integration ensures secure, asynchronous data management with minimal effort.

**Related Functions:**
* `put`
* `get`
* `delete`

SecurePersist extends encryption capabilities to `DataStore`, supporting both primitive and complex data types. Since `DataStore` operations are suspend functions, you need to call them within a coroutine or another suspend function.

```kotlin
import kotlinx.coroutines.CoroutineScope
import kotlinx.coroutines.Dispatchers
import kotlinx.coroutines.launch

// Encrypt and save a preference
CoroutineScope(Dispatchers.IO).launch {
    persistManager.dataStorePrefs.put("key1", "secureValue")
}

// Decrypt and retrieve a preference
CoroutineScope(Dispatchers.IO).launch {
    val value = persistManager.dataStorePrefs.get("key1", "defaultValue")
    println("Retrieved value: $value")
}

// Delete a preference
CoroutineScope(Dispatchers.IO).launch {
    persistManager.dataStorePrefs.delete("key1")
}
```

### Option 2 — Accessing `DataStore` Without Coroutines
If you prefer ease of use without dealing with coroutines directly, `PersistManager` has you covered! The second approach for accessing `DataStore` preferences in `PersistManager` **handles coroutines behind the scenes**, delivering the same performance benefits while simplifying usage.

This method still offers superior performance over `EncryptedSharedPreferences`, particularly for `put` and `delete` operations, as they are executed in a non-blocking manner. The only blocking operation is retrieving data with get.

> *To access DataStore preferences without coroutines, use the `Direct` versions of the functions.*

**Related Functions:**
* `putDirect` (non-blocking)
* `getDirect` (blocking)
* `deleteDirect` (non-blocking)

```kotlin
// assuming encryption - Encrypt and store in a non-blocking way to DataStore
persistManager.dataStorePrefs.putDirect("key1", "secureValue")

// assuming encryption - Decrypt and get in a blocking way the value from DataStore
val value: String = persistManager.dataStorePrefs.getDirect("key1", "defaultValue")

// no encryption - store unencrypted in a non-blocking way to DataStore
persistManager.dataStorePrefs.putDirect("key1", "secureValue", encrypted = false)

// no encryption - get unencrypted in a blocking way the value from DataStore
val value: String = persistManager.dataStorePrefs.getDirect("key1", "defaultValue", encrypted = false)

// Delete the DataStore preference without using coroutines
persistManager.dataStorePrefs.deleteDirect("key1")
```

##### How PersistManager handles DataStore's encrypted and unencrypted data:

When storing data in `DataStore` with encryption enabled, all data types are `serialized`, as `DataStore` does not natively support encryption. Here's how the library handles different scenarios:

**-- Without Encryption:** If you use DataStore without encryption, only complex types (e.g., custom objects, collections) and Double values are serialized, as these require transformation for storage.

| Data Type | Supported Directly | Handled via gson Serialization |
|-----------|:------------------:|:------------------------------:|
| Boolean   | Yes                | No                             |
| Int       | Yes                | No                             |
| Float     | Yes                | No                             |
| Long      | Yes                | No                             |
| Double    | No                 | Yes                            |
| String    | Yes                | No                            |
| Custom Objects (e.g., Data Classes) | No | Yes                  |

**--With Encryption:** If encryption is enabled, everything - regardless of type - is serialized and stored as an encrypted string to ensure data security.

| Data Type | Supported Directly | Handled via gson Serialization |
|-----------|:------------------:|:------------------------------:|
| Boolean   | No                 | Yes                            |
| Int       | No                 | Yes                            |
| Float     | No                 | Yes                            |
| Long      | No                 | Yes                            |
| Double    | No                 | Yes                            |
| String    | No                 | Yes                           |
| Custom Objects (e.g., Data Classes) | No | Yes                  |

This approach ensures that encrypted data remains secure, while unencrypted data is only serialized when necessary, optimizing performance and storage.

### Deleting Preferences with PersistManager
You can delete preferences using the `delete` function from inside the PersistManager or utilize the delete functions of the equivalent storage types like that:

```kotlin
// Delete a preference from both SharedPreferences and DataStore
persistManager.delete("key1")

// Delete a preference from SharedPreferences
persistManager.sharedPrefs.delete("key1")

// Delete a preference from DataStore Preferences using coroutines
CoroutineScope(Dispatchers.IO).launch {
    persistManager.dataStorePrefs.delete("key1")
}

// Delete a preference from DataStore Preferences directly without coroutines
persistManager.dataStorePrefs.deleteDirect("key1")
```

## Jetpack Compose State Persistence
If you included the `secure-persist-compose` add-on module to the `secure-persist` module in your `implementation`s then you are ready to utilize another **zero-configuration secure persistence with state management**.

SecurePersist seamlessly integrates with Jetpack Compose, enabling developers to persist and restore UI state effortlessly. By leveraging on more time Kotlin's property delegation, you can bind your Compose state variables to secure storage mechanisms, ensuring that your UI remains consistent across recompositions and app restarts.

**Usage Example**
Below is a code snippet demonstrating how to implement state persistence in a `ViewModel` using SecurePersist:

```Kotlin
import androidx.lifecycle.ViewModel
import eu.anifantakis.lib.securepersist.PersistManager
import eu.anifantakis.lib.securepersist.compose.mutableStateOf

class LibCounterViewModel(
    persistManager: PersistManager
) : ViewModel() {

    // If key is unspecified, property name becomes the key

    // Defaults to EncryptedSharedPreferences and uses the property name as the key
    var count1 by persistManager.mutableStateOf(1000)
        private set

    // Sets a custom key and uses DataStorePreferences with encryption
    var count2 by persistManager.mutableStateOf(
        defaultValue = 2000, 
        key = "counter2Key",
        storage = PersistManager.Storage.DATA_STORE_ENCRYPTED
    )
        private set

    // Uses the property name as the key and sets storage to Unencrypted DataStorePreferences
    var count3 by persistManager.mutableStateOf(3000,
            storage = PersistManager.Storage.DATA_STORE
    )
        private set

    fun increment() {
        count1++
        count2++
        count3++
    }
}
```

#### Explanation of Each Configuration

##### `count1` - Default Configuration:
* **Usage Scenario:** When you want to store a sensitive value using the default secure storage mechanism.
* **Behavior:** Automatically uses the property name as the key and stores the value in `EncryptedSharedPreferences`, ensuring that count1 is securely persisted without additional configuration.

##### `count2` - Custom Key with Encrypted DataStore:
* **Usage Scenario:** When you need to use a custom key and prefer DataStore with encryption for storing sensitive data.
* **Behavior:** Allows specifying a custom key (`counter2Key`) and sets `Storage` to `DATA_STORE_ENCRYPTED` to specify DataStore with encryption usage. This is useful when you want to manage keys explicitly or need the benefits of DataStore over SharedPreferences.
 

##### `count3` - Default Key with Unencrypted DataStore:
* **Usage Scenario:** When storing non-sensitive data and prefer using DataStore without encryption for better performance or simplicity.
* **Behavior:** Uses the property name (`count3`) as the key and sets `Storage` to `DATA_STORE` to specify DataStore without encryption. This is ideal for scenarios where data security is not a primary concern, and you want to leverage DataStore's advantages like type safety and better asynchronous handling.


---

# EncryptionManager
The `EncryptionManager` can be used independently of `PersistManager`, offering robust functionality for encrypting and decrypting raw data, files, and complex objects.

It provides two flexible key management options:

1. **KeyStore Integration**: Leverages Android’s **`KeyStore`** for hardware-backed encryption, where keys are securely managed within the device's hardware chip.

1. **External Keys**: Allows you to generate and manage your own **`SecretKey`** outside of KeyStore. This is useful if you need to store keys remotely, for instance, when encrypting data that will also be stored on a remote server.
### Initialization

#### You can initialize using the `KeyStore`
```kotlin
val encryptionManager = EncryptionManager(context, "your_key_alias")
```

#### You can initialize using the `External Key` to generate a `SecretKey`

```kotlin
// First, generate an external key:
val externalKey = EncryptionManager.generateExternalKey()

// Then, use the generated key to create an instance of EncryptionManager:
val encryptionManager = EncryptionManager(context, externalKey)
```

#### Encryption Details
* **Algorithm:** AES (Advanced Encryption Standard)
* **Mode:** GCM (Galois/Counter Mode)
* **Padding:** No Padding (GCM handles it internally)
* **Key Management:** Managed by Android KeyStore
* **Key Strength:** 256-bit keys for strong encryption

AES in GCM mode is an authenticated encryption algorithm that provides both data confidentiality and integrity. Using the Android KeyStore for key management adds an extra layer of security by storing keys in a secure, hardware-backed environment.

### Encrypting and Decrypting Raw Data via KeyStore

```kotlin
val encryptionManager = EncryptionManager(context, "your_key_alias")

// Encrypt data
val encryptedData = encryptionManager.encryptData("plainText")

// Decrypt data
val decryptedData = encryptionManager.decryptData(encryptedData)
val plainText = String(decryptedData, Charsets.UTF_8)

// Encrypt a value and encode it to a Base64 string
val encryptedValue = encryptionManager.encryptValue("valueToEncrypt")

// Decrypt a Base64 encoded string and return the original value
val decryptedValue: String = encryptionManager.decryptValue(encryptedValue, "defaultValue")

```

### Encrypting and Decrypting Raw Data with `SecretKey` via an Externa Key
One important feature is the ability generate an external key, which you can then pass to the library.

By doing so, you can safe-keep that key on a server to be able to decrypt data when needed in the future.

```kotlin
// Generate an external key
val externalKey = EncryptionManager.generateExternalKey()

// Create an EncryptionManager instance with the external key
val encryptionManager = EncryptionManager(externalKey)
```

Also you can supply that key at runtime  only for a specific entryption/decryption in Static context, leaving the keystore key for everything else

```kotlin
// Create an EncryptionManager instance
val encryptionManager = EncryptionManager(context, "myKeyAlias")

// Generate an external key
val externalKey = EncryptionManager.generateExternalKey()

// we will now use that key only for the specified encryptions/decryptions

// Encrypt a value and encode it to a Base64 string with custom key
val encryptedValue1 = EncryptionManager.encryptValue("valueToEncrypt", secretKey = externalKey)
// Encrypt a value and encode it to a Base64 string with default key
val encryptedValue2 = encryptionManager.encryptValue("valueToEncrypt")

// Decrypt a Base64 encoded string and return the original value with custom key
val decryptedValue1 = EncryptionManager.decryptValue(encryptedValue, "defaultValue", secretKey = externalKey)
// Decrypt a Base64 encoded string and return the original value with default key
val decryptedValue2 = encryptionManager.decryptValue(encryptedValue, "defaultValue")
```

### Storing the externalKey (SecretKey) to some other medium
If you have generated a key and want to securely transmit it to some API or retrieve it, then this library provides two convenience static methods for `encoding` and `decoding` that key to a string so you can easily transfer it.

##### Exporting custom key to save it to some server
```kotlin
// generate a key
val originalKey = EncryptionManager.generateExternalKey()

// encrypt your data with that external key
val encryptedData = EncryptionManager.encryptData("Hello, Secure World!", originalKey)

// create a string that contains the encoded key (then send it to some server)
val encodedKey: String = EncryptionManager.encodeSecretKey(originalKey)

// make network call and save the encoded key string to some server
```

##### Retrieving the custom key from the server to use it in the app
```kotlin
val encodedKey = ... // fetch the string with encoded key from a remote server

// construct a SecretKey from that encodedKey retrieved from the server
val decodedKey: SecretKey = EncryptionManager.decodeSecretKey(encodedKey)

// as you can see, you can decode the encrypted data using the key that was reconstructed from the encoded string
val decryptedText = EncryptionManager.decryptData(encryptedData, decodedKey)
```

### FILES Encryption/Decription ##

Additionally, `EncryptionManager` offers **`encryptFile`** and **`decryptFile`** functions, enabling you to securely encrypt data and store it in a file, as well as read and decrypt the data from the file when needed.

These functions provide a seamless way to handle file-level encryption and decryption, ensuring that sensitive data remains secure during both storage and retrieval.

```kotlin
// Create an EncryptionManager instance
val encryptionManager = EncryptionManager(context, "your_key_alias")

// Specify the input file and the name for the encrypted file
val testFile = File(context.filesDir, "plain.txt")
val encryptedFileName = "encrypted.dat"

// Encrypt the file
encryptionManager.encryptFile(testFile, encryptedFileName)

// Decrypt the file
val decryptedContent: ByteArray = encryptionManager.decryptFile(encryptedFileName)
val decryptedText = String(decryptedContent)
```

---

# Testing
You can find extensive tests inside the `androidTest` folder for both the `PersistManager` and the `EncryptionManager`, providing more examples and ensuring reliability. 

---

## Contributing
Contributions are welcome! Please open an issue or submit a pull request on GitHub.
