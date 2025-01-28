# ✦ Tiny Object Notation ✦
A smaller, more efficient JSON alternative

[[JP](https://github.com/371tti/TinyObjectNotation-TON-/blob/master/README.md)]

## **Overview**
**Tiny Object Notation (TON)** is a binary enhancement of JSON that maintains flexibility and simplicity.

It adds:
- Type information + type indicators
- Length information
- Fast key matching
- ~20% reduction in size
- Support for data sizes up to 2^64 bytes

This is useful for databases, networks, or other areas where efficient and high-performance data exchange is necessary.

Compression should be handled by an upper layer that may include TON data.

### **Comparison with Other Formats**

| **Feature**               | **JSON**                           | **BSON**                        | **Custom Format (using key-hash)**   |
|---------------------------|-------------------------------------|---------------------------------|---------------------------------------|
| **Data Size**             | Large (due to string redundancy)    | Relatively small                 | **Smallest (optimized keys)**         |
| **Key Lookup Speed**      | Slow (fully string-based)           | Moderate (NULL-terminated keys)  | **Fast (hash match + string check)**  |
| **Type Information**      | Implicit                            | Explicit (1 byte)               | **Lightweight (6-bit management)**    |
| **Flexibility**           | High                                | Medium (MongoDB-oriented)        | **High (general design)**             |
| **Use Cases**             | Human-readable data storage/transfer| Database usage                   | High-speed, lightweight data handling |

### **Example: Sample Data Format**
```json
{
  "name": "Alice",
  "age": 25,
  "isStudent": false
}
```
```
0b01010001 0x00 0x22
0b01010100 0x04 "name" 0b00111001 0x05 "Alice"
0b00110000 0x03 "age" 0b00001000 0x19 0x00 0x00 0x00
0b01100001 0x00 0x09 "isStudent" 0b00000100 0x00
```
**Total Size**:
- JSON: 48 bytes  
- BSON: 42 bytes  
- ✦TON: **41 bytes** 

---

# **TinyObjectNotation (TON) Specification**

## Table of Contents
1. [Basic Structure of the Format](#1-basic-structure-of-the-format)  
   1.1 [Field Descriptions](#11-field-descriptions)  
2. [Basic Types](#2-basic-types)  
3. [Collection Types](#3-collection-types)  
   3.1 [Object](#31-object)  
   3.2 [Array](#32-array)  
   3.3 [WrappedJSON](#33-wrappedjson)  
   3.4 [Meta](#34-meta)  
4. [Notes](#4-notes)  
5. [Parser Policy](#5-parser-policy)  
6. [Serializer Policy](#6-serializer-policy)  
7. [About Type Meta](#7-about-type-meta)

---

## **1. Basic Structure of the Format**

TON uses the following unified format for data encoding:

```
<6bit: identifier> <2bit: length-size> <8-64bit: data-length> <nbyte: data-body>
```

### **1.1 Field Descriptions**
1. **Identifier (6 bits)**  
   A 6-bit value indicating the data type or role.  
   - **Value**: Identifies the data type (e.g., `Int`, `String`, `Array`, etc.).  
   - **Key**: Used only within an `Object`, storing the **lower 6 bits of the final byte** (`hash[31]`) **of a SHA-256 hash** of the key string.  
     - Example: `hash = SHA-256("name")` → from the 32-byte hash, take the last byte `hash[31]` and apply `0x3F` (to extract the lower 6 bits).  
     - This makes it easy to extract 6 bits via a simple mask operation (`hash[31] & 0x3F`), improving implementation speed.

2. **Length Size (2 bits)**  
   Indicates the size of the **data-length** field (below).
   - `00`: 1 byte (up to 255 bytes)
   - `01`: 2 bytes (up to 65,535 bytes)
   - `10`: 4 bytes (up to 4,294,967,295 bytes)
   - `11`: 8 bytes (up to 18,446,744,073,709,551,615 bytes)

3. **Data Length (8-64 bits, little-endian)**  
   The length (in bytes) of the data body.

4. **Data Body (variable length)**  
   The byte array for the actual content, read according to the above data-length field.

---

## **2. Basic Types**

For **Value** fields in TON, we use the following 6-bit identifiers.  
(Note: The 6-bit value for **Object** keys is separate and may overlap these identifiers.)

| Type            | Identifier (6 bits) | Description                                                    |
|:----------------|:-------------------:|:---------------------------------------------------------------|
| **Null**        | `0b000000`          | Represents no value                                            |
| **Boolean**     | `0b000001`          | True/False value                                               |
| **Int<n>**      | `0b000010`          | Variable-length signed integer (1–128 bytes, includes JS BigInt) |
| **UInt<n>**     | `0b000011`          | Variable-length unsigned integer (1–128 bytes)                 |
| **Float<n>**    | `0b000100`          | Variable-length floating-point (2–16 bytes)                    |
| **BCD**         | `0b000101`          | Binary Coded Decimal (4 bits per digit)                        |
| **String**      | `0b000110`          | UTF-8 encoded string                                           |
| **Binary**      | `0b000111`          | Arbitrary binary data                                          |
| **Hash**        | `0b001000`          | Variable-length hash value                                     |
| **UUID**        | `0b001001`          | 128-bit UUID (16 bytes)                                        |
| **DateTime**    | `0b001010`          | Date-time (millisecond precision, 8 bytes)                     |
| **Timestamp**   | `0b001011`          | POSIX time (seconds precision, 8 bytes)                        |
| **Duration**    | `0b001100`          | Duration (nanosecond precision, 8 bytes)                       |
| **Array**       | `0b001101`          | Array                                                          |
| **Object**      | `0b001110`          | Key-value pairs                                                |
| **WrappedJSON** | `0b001111`          | Encapsulated raw JSON string                                   |
| **Meta**        | `0b010000`          | Internal metadata for TON (not user-editable)                  |

---

## **3. Collection Types**

### **3.1 Object**

| Type     | Identifier (6 bits) |
|:---------|:-------------------:|
| **Object** | `0b001110`        |

`Object` stores **key-value pairs**.

#### **Structure**
```
<Object Header>
  <6bit: 0b001110> <2bit: length-size> <8-64bit: data-length (overall)>
  <Key-Value Pair 1> <Key-Value Pair 2> ... <Key-Value Pair n>
```
- **data-length**: Total byte size of the entire Object (all key-value pairs).

#### **Key-Value Pair**
```
<Key Header> <Key Data> <Value Header> <Value Data>
```
1. **Key**  
   - **Key Header**:
     ```
     <6bit: key-identifier> <2bit: length-size> <8-64bit: data-length>
     ```
     - **Key Identifier (6 bits)**:  
       Derived from the final byte (`hash[31]`) of the SHA-256 hash of the key string, taking the lower 6 bits.  
         ```plaintext
         # (pseudo-code)
         hash = SHA256("key_string")  # 32 bytes
         key_id_6bit = hash[31] & 0x3F
         ```
     - **length-size (2 bits)**: Field size for the key string length.  
     - **data-length (8-64 bits)**: Byte length of the key string.  
   - **Key Data**: The UTF-8 key string itself.

2. **Value**  
   - **Value Header**:
     ```
     <6bit: value-identifier> <2bit: length-size> <8-64bit: data-length>
     ```
     - This 6-bit identifier is one of the **basic or collection** identifiers listed above.  
   - **Value Data**: The actual value bytes.

#### **Example**
```json
{
  "name": "Alice",
  "age": 25
}
```

In TON (conceptual):
```
<Object Header>
0b001110 0b01 0x13 0x00
  # Object(0b001110), length = 19 bytes (2 bytes used for length-size)

<Key-Value Pair 1>
  <Key Header>
    # "name" → sha256("name") → hash[31] & 0x3F → e.g. 0b100111 (example)
    0b100111 0b01 0x04 0x00
  <Key Data>
    "name"

  <Value Header>
    # "Alice" (String: 0b000110)
    0b000110 0b01 0x05 0x00
  <Value Data>
    "Alice"

<Key-Value Pair 2>
  <Key Header>
    # "age" → sha256("age") → hash[31] & 0x3F → e.g. 0b011011 (example)
    0b011011 0b01 0x03 0x00
  <Key Data>
    "age"

  <Value Header>
    # 25 (Int<n>: 0b000010)
    0b000010 0b01 0x01 0x00
  <Value Data>
    0x19  (25)
```

---

### **3.2 Array**

| Type    | Identifier (6 bits) |
|:--------|:-------------------:|
| **Array** | `0b001101`        |

`Array` stores multiple values in sequence.

#### **Structure**
```
<Array Header>
  <6bit: 0b001101> <2bit: length-size> <8-64bit: data-length>
  <Value 1> <Value 2> ... <Value n>
```
- **data-length**: Total byte size of the array.

---

### **3.3 WrappedJSON**

| Type            | Identifier (6 bits) |
|:----------------|:-------------------:|
| **WrappedJSON** | `0b001111`         |

`WrappedJSON` is a special type that stores a raw JSON string as-is.

#### **Structure**
```
<WrappedJSON Header>
  <6bit: 0b001111> <2bit: length-size> <8-64bit: data-length>
  <nbyte: JSON string>
```

---

### **3.4 Meta**

| Type   | Identifier (6 bits) |
|:-------|:-------------------:|
| **Meta** | `0b010000`        |

`Meta` is a special internal type for TON’s internal operations.  
**It is not intended for direct user manipulation.**

#### **Structure**
```
<Meta Header>
  <6bit: 0b010000> <2bit: length-size> <8-64bit: data-length>
  <nbyte: meta-information>
```

---

## **4. Notes**

1. **Collision Risk for Key Identifiers**  
   - Since an `Object` key uses the lower 6 bits from `hash[31]` of the SHA-256 hash, collisions are possible if different key strings yield the same 6-bit value.  
   - Implementations may need to verify the actual **key string** (in `Key Data`) to handle collisions.

2. **Key Order**  
   - Keys in an `Object` retain their encoded order, but some parsers may not preserve it when decoding.

3. **Duplicate Keys**  
   - Encoding the same key multiple times is not recommended. It may cause errors or behave as “last one wins,” depending on the implementation.

4. **Meta Information (`Meta`)**  
   - `Meta` is exclusively for internal TON use and should not be manipulated by users.

---

## **5. Parser Policy**

1. **Handling Key Identifiers (6 bits)**  
   - Within an `Object`, treat those 6 bits as the lower 6 bits of the final byte in a SHA-256 hash.  
   - Even if collisions occur, using the `Key Data` string to distinguish keys is recommended.

2. **Consistency Check of Data Length**  
   - If the length-size (2 bits) and the actual byte count don’t match, treat as an error.  
   - Validate `Value Header` data lengths according to the declared type identifier.

3. **Meta Information Handling**  
   - If user-generated data contains `Meta` (0b010000), return an error or warning, unless it’s used internally.

---

## **6. Serializer Policy**

1. **Determining the Key Identifier**  
   - For an `Object` key, compute SHA-256 of the key string and extract the lower 6 bits of `hash[31]`.  
   - Store the original key string (UTF-8) in `<Key Data>`.

2. **Optimal Length-Size**  
   - If `data-length` ≤ 255, use `00`, and so on.

3. **Accurate Type Identifier**  
   - If the value is `Boolean`, `Int<n>`, `String`, etc., use the correct 6-bit identifier.

4. **Avoid Duplicate Keys**  
   - Ensure the same key string isn’t emitted multiple times, or clarify if the last occurrence overrides previous ones.

5. **Non-User Use of Meta**  
   - Do not generate `Meta` (0b010000) from user input.  
   - Only use it if the internal implementation requires it.

---

## **7. About Type Meta**
The specification of Meta is currently undefined.
