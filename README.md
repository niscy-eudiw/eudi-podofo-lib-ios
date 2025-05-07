# PoDoFo

An iOS Swift Package providing the PoDoFo PDF library as an XCFramework, specifically focused on digital signature capabilities for EUDI Wallet applications.

## Requirements

- iOS 16.0+
- Xcode 12.0+

## Installation

### Swift Package Manager

The [Swift Package Manager](https://swift.org/package-manager/) is the recommended way to install PoDoFo.

#### Using Xcode

1. Open your project in Xcode
2. Go to File → Swift Packages → Add Package Dependency
3. Enter the repository URL: `https://github.com/niscy-eudiw/eudi-podofo-lib-ios.git`
4. Select "Up to Next Major Version" with "1.0.0" as the minimum version
5. Click Next and then Finish

#### Using Package.swift

Add PoDoFo as a dependency in your `Package.swift` file:

```swift
dependencies: [
    .package(url: "https://github.com/niscy-eudiw/eudi-podofo-lib-ios.git", from: "1.0.0")
]
```

And then include it in your target:

```swift
targets: [
    .target(
        name: "YourTarget",
        dependencies: ["PoDoFo"])
]
```

## Usage

The PoDoFo framework primarily provides PDF digital signing functionality through the `PodofoWrapper` class, designed for digital signature workflows in EUDI Wallet applications.

### Importing the Framework

```swift
import PoDoFo
```

### Digital Signing Example

```swift
// Initialize the wrapper with signing parameters
let podofoWrapper = PodofoWrapper(
    conformanceLevel: "Ades_B_B",
    hashAlgorithm: "2.16.840.1.101.3.4.2.1", // SHA-256 OID
    inputPath: "/path/to/input.pdf",
    outputPath: "/path/to/output.pdf",
    certificate: certificateString,
    chainCertificates: [intermediateCertString, rootCertString]
)

// Check if the library is properly loaded
if podofoWrapper.isLoaded() {
    // Optional: Debug information
    podofoWrapper.printState()
    
    // Step 1: Calculate the hash to be signed
    if let hash = podofoWrapper.calculateHash() {
        // Step 2: Sign the hash using your signature service
        let signedHash = yourSigningService.sign(hash)
        
        // Step 3: Finalize the signing process
        podofoWrapper.finalizeSigningWithSignedHash(signedHash)
        
        print("PDF signed successfully!")
    }
}
```

## Features

This implementation focuses on:

- Remote document signing (hash-sign-finalize workflow)
- Support for different signature conformance levels (including AdES profiles)
- Certificate chain handling
- Various hash algorithms
- Integration with EUDI Wallet applications

## API Reference

### PodofoWrapper

```swift
class PodofoWrapper: NSObject {
    
    // Initializes a signing session
    func initWithConformanceLevel(_ conformanceLevel: String,
                                  hashAlgorithm: String,
                                  inputPath: String,
                                  outputPath: String,
                                  certificate: String,
                                  chainCertificates: [String]) -> PodofoWrapper
                                  
    // Checks if the library is properly loaded
    func isLoaded() -> Bool
    
    // Prints debug information about the signing session
    func printState()
    
    // Calculates document hash for remote signing (first step)
    func calculateHash() -> String?
    
    // Finalizes signing with the signed hash (final step)
    func finalizeSigningWithSignedHash(_ signedHash: String)
}
```

## Conformance Levels

The wrapper supports various conformance levels for digital signatures, including:

- `Ades_B_B`: Basic AdES profile
- Other AdES profiles as supported by the PoDoFo library

## Hash Algorithms

You can specify hash algorithms using their OIDs:

- `2.16.840.1.101.3.4.2.1`: SHA-256
- `2.16.840.1.101.3.4.2.2`: SHA-384
- `2.16.840.1.101.3.4.2.3`: SHA-512
