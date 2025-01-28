# Tiny Object Notation  
~ Making JSON Smaller and More Efficient  

## **Overview**  
**Tiny Object Notation (TON)** is a lightweight and efficient binary upgrade to JSON.  

### **Key Features**:
- Type information and type codes.
- Length information.
- Fast key matching.
- Approximately 20% size reduction.
- Support for data sizes up to 2^64 bytes.  

TON is designed for use in scenarios where lightweight and high-performance data sharing is required, such as databases and networks.  

Compression should be implemented as a higher-layer abstraction over this format.  

---

### **Format Structure**
1. **Value**  
   ```
   <6bit: type> <2bit: data-len-size> <8-64bit: data-len> <nbyte: data>
   ```
   - Encodes data type, length, and the actual value.

2. **Key**  
   ```
   <6bit: key-hash (SipHash[:6])> <2bit: key-len-size> <8-64bit: key-len> <nbyte: key-string>
   ```
   - **`key-hash`**: Stores the hash value of the key string (for faster lookups).
   - **`key-len`**: The length of the key string.
   - **`key-string`**: The actual key name.

3. **Array**  
   ```
   <6bit: type> <2bit: data-len-size> <8-64bit: data-len> <nbyte: elements>
   ```
   - Stores array data.

4. **Object**  
   ```
   <6bit: type> <2bit: data-len-size> <8-64bit: data-len> <nbyte: key-value-pairs>
   ```
   - Stores key-value pairs.

---

## **Advantages**
1. **Faster Key Lookups**:
   - By using `key-hash`, string comparisons are minimized, improving lookup speed.

2. **Versatility**:
   - Supports a wide range of types [TypeList](https://github.com/371tti/TinyObjectNotation-TON-/blob/master/Type.md).  

---

## **Use Cases**
- **Databases and Cache Systems**:  
  - Ideal for systems where frequent key lookups are required.  
- **Low-Resource Environments**:  
  - Suitable for embedded devices and memory-constrained systems.  
- **Network Communication**:  
  - Enables lightweight and efficient data transfer.

---

## **Comparison with Other Formats**

| **Feature**             | **JSON**                          | **BSON**                      | **Custom Format (with key-hash)**         |
|-------------------------|------------------------------------|--------------------------------|------------------------------------------|
| **Data Size**            | Large (string-heavy)              | Relatively small               | **Smallest (optimized with key-hash)**   |
| **Key Lookup Speed**     | Slow (full string comparison)     | Moderate (null-terminated keys)| **Fast (hash-based + string verification)** |
| **Type Information**     | Implicit                         | Explicit (1 byte)              | **Lightweight (6-bit management)**       |
| **Flexibility**          | High                              | Moderate (MongoDB-centric)     | **High (general-purpose design)**        |
| **Usage**                | Human-readable storage/communication | Database-specific usage        | High-speed, lightweight data processing  |

---

## **Example: Formatted Sample Data**
```json
{
  "name": "Alice",
  "age": 25,
  "isStudent": false
}
```

```
[Object Header]
0b01010001 0x00 0x22

[Key-Value Pair 1: "name"]
0b01010100 0x04 "name" 0b00111001 0x05 "Alice"

[Key-Value Pair 2: "age"]
0b00110000 0x03 "age" 0b00001000 0x19 0x00 0x00 0x00

[Key-Value Pair 3: "isStudent"]
0b01100001 0x00 0x09 "isStudent" 0b00000100 0x00
```

---

## **Total Size**
- JSON: 48 bytes  
- BSON: 42 bytes  
- Custom Format: **41 bytes**  