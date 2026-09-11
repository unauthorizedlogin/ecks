# 🔐 Save Security

### Purpose

`SaveSecurity` provides the serialization, encryption, authentication, decryption, and deserialization layer for `SaveData`.

It deliberately excludes filesystem persistence, leaving storage and save-slot management to `SaveManager`.

### Architecture Role

```text
SaveManager
    ↓
SaveSecurity
├── Serialize SaveData
├── Encrypt
├── HMAC-SHA256
├── Secure Container
├── Verify
├── Decrypt
└── Deserialize
    ↓
SaveData
```

`SaveSecurity` is a `RefCounted` service configured with a `SaveKeyProvider`.

### Responsibilities

* Serialize `SaveData` with object support.
* Encrypt serialized save data.
* Authenticate encrypted data using HMAC-SHA256.
* Validate the security container format.
* Verify authentication before decryption.
* Decrypt authenticated payloads.
* Deserialize and validate `SaveData`.
* Manage temporary encryption/decryption files.
* Maintain security format versioning.

### Security Flow

#### Encode

```text
SaveData
   ↓
var_to_bytes_with_objects()
   ↓
Encryption Key
   ↓
Encrypted Payload
   ↓
MAGIC + Version + Payload
   ↓
HMAC-SHA256
   ↓
Secure Container
```

The HMAC covers the security header and encrypted payload:

```text
MAGIC
FORMAT VERSION
ENCRYPTED DATA
```

This protects both the payload and security format metadata from modification.

#### Decode

```text
Secure Container
   ↓
Parse & Validate Header
   ↓
Calculate HMAC
   ↓
Constant-Time HMAC Comparison
   ↓
Decrypt
   ↓
bytes_to_var_with_objects()
   ↓
Validate SaveData
```

Authentication is verified **before decryption or deserialization**.

### Secure Container

The serialized container format is:

```text
[MAGIC]
[FORMAT VERSION — uint32]
[HMAC-SHA256 — 32 bytes]
[ENCRYPTED PAYLOAD]
```

`SECURITY_FORMAT_VERSION` provides explicit format versioning for future security-container changes.

### Key Management

`SaveSecurity` does not generate or persist keys itself.

`SaveKeyProvider` supplies:

* Encryption key
* Authentication key

`configure()` initializes the provider, while `is_ready()` validates that both keys have the required sizes before security operations proceed.

### Encryption

Encryption uses Godot's `FileAccess.open_encrypted()` with the encryption key.

Because the API operates through file access, `SaveSecurity` uses temporary files for the encryption/decryption operations and removes them after use.

Temporary paths:

* `.save_security_encrypt.tmp`
* `.save_security_decrypt.tmp`

### Authentication

Authentication uses HMAC-SHA256 with the dedicated authentication key.

Decoded containers use `constant_time_compare()` to prevent accepting modified or unauthenticated save data.

### Validation

Decode rejects:

* Unconfigured security state.
* Truncated containers.
* Invalid magic values.
* Unsupported security versions.
* Missing encrypted payloads.
* Invalid HMAC authentication.
* Failed decryption.
* Decrypted values that are not `SaveData`.

### File(s) Affected

* `save_security.gd`
* `SaveData`
* `SaveKeyProvider`
* `SaveManager`

### Notes

`SaveSecurity` is the **cryptographic boundary** of the save system.

`SaveManager` owns persistence, save slots, filenames, and filesystem operations. `SaveSecurity` owns the transformation between trusted `SaveData` and authenticated encrypted save containers.
