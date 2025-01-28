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
| `0b000010`     | `Int<n>`      | Variable-length signed integer (1-128 bytes).                                | Variable (1-128 bytes)     | General-purpose, equivalent to BigInt. |

- **Examples**:
  - `Int8` / `Int16` / `Int32` / `Int64` / `Int128`.

### 2.2 Unsigned Integer (`UInt<n>`)
| **Bit Value**  | **Type Name** | **Description**                                                               | **Size (Bytes)**           | **Standard/Usage**    |
|----------------|---------------|-------------------------------------------------------------------------------|----------------------------|-----------------------|
| `0b000011`     | `UInt<n>`     | Variable-length unsigned integer (1-128 bytes).                              | Variable (1-128 bytes)     | General-purpose.      |

- **Examples**:
  - `UInt8` / `UInt16` / `UInt32` / `UInt64` / `UInt128`.

### 2.3 Floating-Point (`Float<n>`)
| **Bit Value**  | **Type Name** | **Description**                                                               | **Size (Bytes)**           | **Standard/Usage**        |
|----------------|---------------|-------------------------------------------------------------------------------|----------------------------|---------------------------|
| `0b000100`     | `Float<n>`    | Variable-length floating-point (2-16 bytes).                                 | Variable (2-16 bytes)      | Scientific, financial.    |

- **Examples**:
  - `Float16`, `Float32`, `Float64`, `Float80`, `Float128`.

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

- **Examples**:
  - SHA-256 (32 bytes), SHA-512 (64 bytes).

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
