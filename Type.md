# TinyObjectNotation (TON) Type List - Improved Version

## 1. Basic Types

### 1.1 Null
| **Bit Value**  | **Type Name** | **Description**                               | **Size (Bytes)** | **Standard/Usage**             |
|----------------|---------------|-----------------------------------------------|------------------|---------------------------------|
| `0b000000`     | `Null`        | Represents an empty or non-existent value.    | 0                | General-purpose.               |

### 1.2 Boolean
| **Bit Value**  | **Type Name** | **Description**                               | **Size (Bytes)** | **Standard/Usage**             |
|----------------|---------------|-----------------------------------------------|------------------|---------------------------------|
| `0b000001`     | `Boolean`     | Represents a true/false logical value.        | 1 (implementation-dependent) | Flags, conditions.            |

---

## 2. Numeric Types

### 2.1 Signed Integer (`Int<n>`)
| **Bit Value**  | **Type Name** | **Description**                                                               | **Size (Bytes)**           | **Standard/Usage**                  |
|----------------|---------------|-------------------------------------------------------------------------------|----------------------------|-------------------------------------|
| `0b000010`     | `Int<n>`      | Variable-length signed integer (1-128 bytes). Includes values equivalent to JavaScript's `BigInt`. | Variable (1-128 bytes)     | General-purpose, large number operations. |

- **Implementation**:
  - `Int8` (1 byte): Supports -128 to 127.
  - `Int16` (2 bytes): Supports -32,768 to 32,767.
  - `Int32` (4 bytes): Supports -2,147,483,648 to 2,147,483,647.
  - `Int64` (8 bytes): Supports -9,223,372,036,854,775,808 to 9,223,372,036,854,775,807.
  - `Int128` (16 bytes): Large signed integers for extended precision.
  - `Int<n>`: Supports arbitrary-length signed integers for custom use cases.

### 2.2 Unsigned Integer (`UInt<n>`)
| **Bit Value**  | **Type Name** | **Description**                                                               | **Size (Bytes)**           | **Standard/Usage**    |
|----------------|---------------|-------------------------------------------------------------------------------|----------------------------|-----------------------|
| `0b000011`     | `UInt<n>`     | Variable-length unsigned integer (1-128 bytes).                              | Variable (1-128 bytes)     | General-purpose.      |

- **Implementation**:
  - `UInt8` (1 byte): Supports 0 to 255.
  - `UInt16` (2 bytes): Supports 0 to 65,535.
  - `UInt32` (4 bytes): Supports 0 to 4,294,967,295.
  - `UInt64` (8 bytes): Supports 0 to 18,446,744,073,709,551,615.
  - `UInt128` (16 bytes): Very large unsigned integers.
  - `UInt<n>`: Supports arbitrary-length unsigned integers for custom use cases.

### 2.3 Floating-Point (`Float<n>`)
| **Bit Value**  | **Type Name** | **Description**                                                               | **Size (Bytes)**           | **Standard/Usage**        |
|----------------|---------------|-------------------------------------------------------------------------------|----------------------------|---------------------------|
| `0b000100`     | `Float<n>`    | Variable-length floating-point (2-16 bytes).                                 | Variable (2-16 bytes)      | Scientific, financial.    |

- **Implementation**:
  - `Float16`: Half-precision floating-point (2 bytes).
  - `Float32`: Single-precision floating-point (4 bytes).
  - `Float64`: Double-precision floating-point (8 bytes).
  - `Float80`: Extended precision floating-point (10 bytes).
  - `Float128`: Quadruple-precision floating-point (16 bytes).

### 2.4 BCD
| **Bit Value**  | **Type Name** | **Description**                                                               | **Size (Bytes)**           | **Standard/Usage**                   |
|----------------|---------------|-------------------------------------------------------------------------------|----------------------------|--------------------------------------|
| `0b000101`     | `BCD`         | Binary Coded Decimal (4 bits per digit).                                      | Variable (digits/2 bytes)  | Financial, quantity management.      |

---

## 3. String, Binary, and Identifiers

### 3.1 String
| **Bit Value**  | **Type Name** | **Description**                               | **Size (Bytes)**         | **Standard/Usage** |
|----------------|---------------|-----------------------------------------------|--------------------------|---------------------|
| `0b000110`     | `String`      | UTF-8 encoded string.                         | `n` (depends on length)  | General text.       |

### 3.2 Binary
| **Bit Value**  | **Type Name** | **Description**                               | **Size (Bytes)**         | **Standard/Usage** |
|----------------|---------------|-----------------------------------------------|--------------------------|---------------------|
| `0b000111`     | `Binary`      | Arbitrary binary data.                        | `n` (depends on length)  | General-purpose.    |

### 3.3 Hash
| **Bit Value**  | **Type Name** | **Description**                               | **Size (Bytes)**         | **Standard/Usage**         |
|----------------|---------------|-----------------------------------------------|--------------------------|----------------------------|
| `0b001000`     | `Hash`        | Arbitrary-length hash value.                  | Variable                 | Cryptography, checksums.   |

- **Implementation**:
  - SHA-256 (32 bytes).
  - SHA-512 (64 bytes).
  - Custom hash lengths as required.

### 3.4 UUID
| **Bit Value**  | **Type Name** | **Description**                                                        | **Size (Bytes)** | **Standard/Usage**                          |
|----------------|---------------|------------------------------------------------------------------------|------------------|---------------------------------------------|
| `0b001001`     | `UUID`        | 128-bit (16-byte) Universally Unique Identifier.                       | 16               | Object IDs, tracking IDs, etc.              |

---

## 4. Date and Time Types

### 4.1 DateTime
| **Bit Value**  | **Type Name** | **Description**                               | **Size (Bytes)** | **Standard/Usage**     |
|----------------|---------------|-----------------------------------------------|------------------|------------------------|
| `0b001010`     | `DateTime`    | Millisecond-precision date and time.          | 8                | ISO 8601 equivalent.   |

### 4.2 Timestamp
| **Bit Value**  | **Type Name** | **Description**                               | **Size (Bytes)** | **Standard/Usage** |
|----------------|---------------|-----------------------------------------------|------------------|---------------------|
| `0b001011`     | `Timestamp`   | Second-precision timestamp.                   | 8                | POSIX time.         |

### 4.3 Duration
| **Bit Value**  | **Type Name** | **Description**                               | **Size (Bytes)** | **Standard/Usage**      |
|----------------|---------------|-----------------------------------------------|------------------|-------------------------|
| `0b001100`     | `Duration`    | Duration in nanoseconds.                      | 8                | General-purpose timing. |

---

## 5. Collections

### 5.1 Array
| **Bit Value**  | **Type Name** | **Description**                            | **Size (Bytes)**         | **Standard/Usage** |
|----------------|---------------|--------------------------------------------|--------------------------|---------------------|
| `0b001101`     | `Array`       | Collection of multiple values.             | `n` (depends on elements)| General-purpose.    |

### 5.2 Object
| **Bit Value**  | **Type Name** | **Description**                            | **Size (Bytes)**        | **Standard/Usage** |
|----------------|---------------|--------------------------------------------|-------------------------|---------------------|
| `0b001110`     | `Object`      | Key-value pair collection.                 | `n` (depends on elements)| General-purpose.    |

### 5.3 WrappedJSON
| **Bit Value**  | **Type Name**    | **Description**                                      | **Size (Bytes)**              | **Standard/Usage**          |
|----------------|------------------|-----------------------------------------------------|-------------------------------|-----------------------------|
| `0b001111`     | `WrappedJSON`    | Encapsulates raw JSON string directly.              | `n` (depends on JSON length)  | JSON compatibility.         |

### 5.4 Meta
| **Bit Value**  | **Type Name** | **Description**                                                     | **Size (Bytes)** | **Standard/Usage**                |
|----------------|---------------|--------------------------------------------------------------------|------------------|-----------------------------------|
| `0b010000`     | `Meta`        | Metadata used internally for TON operations. Not user-modifiable.  | Variable         | Internal optimizations.           |

---

## 6. Reserved

### 6.1 Undefined
| **Bit Value**  | **Type Name**   | **Description**                                     | **Size (Bytes)** | **Standard/Usage**                     |
|----------------|-----------------|-----------------------------------------------------|------------------|----------------------------------------|
| `0b111111`     | `Undefined`     | Represents undefined or uninitialized values.       | 0                | Error handling or unset data.          |