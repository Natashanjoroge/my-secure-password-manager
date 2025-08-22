# My Secure Password Manager 


This library provides a secure, password-based key-value storage solution for managing sensitive data like credentials. The `Keychain` class uses AES-GCM encryption to protect stored data, allowing for safe storage, retrieval, and serialization of encrypted information.

![Node.js](https://img.shields.io/badge/Node.js-339933?style=flat&logo=node.js&logoColor=white)
![MIT](https://img.shields.io/badge/License-MIT-green)
![Mocha](https://img.shields.io/badge/Mocha-5.2.0-8d8d8d)
![Built by Natasha](https://img.shields.io/badge/Built%20By-Natasha-blue)


## Brief Introduction 

This project is a simple password manager I built using AES-GCM encryption. It allows users to safely store and retrieve credentials like website passwords and API keys, while ensuring that only those with the correct master password can unlock the data.

## Key Features 

- AES-GCM encryption to protect passwords

- Secure key derivation using PBKDF2

- Backup and restore with integrity checks

- Detects tampering with SHA-256 checksum

## How It Works ⚙️

### Key Derivation 

To securely derive an encryption key from the user-provided password, the library uses PBKDF2 (Password-Based Key Derivation Function 2). This key derivation process includes:

- **Password**: User-supplied text to generate the encryption key.
- **Salt**: A random byte array to protect against dictionary attacks.
- **Iterations**: A count that determines how many times the key derivation function is applied, increasing security.

This process results in a master key that is used to encrypt and decrypt data within the keychain.

### Data Encryption and Decryption 

The `Keychain` class encrypts and decrypts the key-value store (`kvs`) using AES-GCM, a modern encryption algorithm suitable for secure data storage. The encryption process includes:

1. **Encrypting Data**: Converting the key-value store into an encrypted format with AES-GCM.
2. **Initialization Vector (IV)**: A unique, randomly generated IV used for each encryption session.
3. **Decryption**: Decoding the encrypted key-value pairs with the correct master key and IV.

These steps ensure that data remains secure and can only be accessed by users with the correct password.

### Data Dump and Load 

To support backup and restoration, the library offers methods to export (`dump`) and import (`load`) encrypted key-value pairs. Each dump includes:

- **Encrypted Data**: The encrypted key-value store.
- **Salt**: Used in key derivation for data recovery.
- **Checksum**: A SHA-256 hash of the serialized data, ensuring integrity during backup and restoration.

Loading data requires the correct password to derive the master key, allowing for secure decryption of the key-value pairs.

## Installation 

To use this library, clone the repository and install the required dependencies:

```bash
git clone https://github.com/Natashanjoroge/my-secure-password-manager.git
cd my-secure-password-manager
npm install


```

## Usage 

Example usage of the Keychain class:
```javascript

const { Keychain } = require('./lib.js');

// Initialize a new keychain
const keychain = await Keychain.init('your-password');

// Add credentials
await keychain.set('https://example.com', 'password123');

// Retrieve credentials
const password = await keychain.get('https://example.com');

// Dump keychain for backup
const [contents, checksum] = await keychain.dump();

// Load an existing keychain
const loadedKeychain = await Keychain.load('your-password', contents, checksum);

```

## Testing 

To test the project, I used Mocha. If you don’t already have it:

```bash
npm install -g mocha

```
To test:
```bash
mocha
```

## Running the Server
To run the server, you need to have Node.js installed. Then, you can run the server.
```bash
node server.js
```
This will start the server on port 3000.

## Website🛜
You can also open index.html in your browser to test the frontend.
<br>
<br>
<br>

---

# <u> SHORT QUESTIONS ANSWERS </u>
## 1. Preventing the Adversary from Learning Password Lengths

## Approach:
To stop attackers from guessing the length of stored passwords, each password is padded to a fixed maximum size (e.g., 64 characters) before encryption. This way, every encrypted entry looks the same length, so nothing about the real password size is revealed.

## Advantages:

Protects privacy by hiding clues about whether a password is short or long.

Makes all encrypted records uniform, leaving no length-based patterns.

## Disadvantages:

Uses more storage space since short passwords still take up maximum length.

Adds a small processing cost during padding and encryption.

## 2. Preventing Swap Attacks

## Approach:
Each domain–password pair is linked to an HMAC created from the domain name. If an attacker swaps records, the mismatch will be detected when verifying the HMAC. On top of that, a SHA-256 checksum of the whole database is calculated and checked whenever it’s loaded.

## Advantages:

Detects tampering since every entry is tied to its own HMAC.

The SHA-256 checksum ensures the integrity of the whole database.

## Disadvantages:

Requires extra computation to calculate HMACs and checksums.

If verification fails, figuring out exactly which entry was altered may be tricky.

## 3. Trusted Location for SHA-256 Hash (Rollback Attacks)

## Approach:
Storing the database hash in a trusted location makes rollback attacks harder since attackers can’t just replace data with an old version. If no trusted location exists, an alternative is to use version numbers or timestamps to detect rollbacks. While this works, it’s more complex than having a secure place to keep the hash.

## Advantages:

Strong rollback protection if the hash or version info is kept safe.

Easy and reliable integrity checks.

## Disadvantages:

Relies on having a trusted storage location.

If not available, versioning adds complexity and must be carefully managed.

## 4. Randomized MACs and Domain Lookups

Using a randomized MAC for domain lookups complicates things because the output changes every time. With HMAC, the same input always gives the same tag, making it easy to look up a password. With randomized MACs, you’d need to store every tag along with its domain, which increases both storage and lookup complexity.

##  Possible Solution:
Maintain an index that maps each domain to all the tags generated for it. This allows lookups but at the cost of more storage and management overhead.

## 5. Reducing Information About Number of Records

## Approach:
Use hash-based partitions with padding. Records are grouped into partitions, each with a maximum size (e.g., 8). Empty slots are filled with dummy records so the exact number of real records isn’t visible.

## Advantages:

Only leaks an approximate range (log2(k)) instead of the exact record count.

Small changes in the number of records don’t reveal new information.

Can scale easily as more partitions are added.

## Disadvantages:

Requires extra storage for dummy records.

Adds more logic for managing partitions and padding.

## 6. Multi-User Support Without Losing Security

 Approach:
Use Attribute-Based Encryption (ABE) so specific users get access only to the sites they need. Each user is given attributes (like “nytimes-access”) and passwords are encrypted under these conditions. Only users with the matching attribute can decrypt.

## Advantages: 

Fine-grained access control (each user only sees what they’re allowed).

Other users’ passwords stay private.

Scales easily by adding new attributes or revoking them.


## Author 
Developed by Natasha Njoroge  

