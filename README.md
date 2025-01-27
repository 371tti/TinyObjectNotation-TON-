# Tiny Object Notation
～ jsonをちっちゃく効率的に

### **フォーマット構造**
1. **Value**
   ```
   <6bit: type> <2bit: data-len-size> <8-64bit: data-len> <nbyte: data>
   ```
   - データ型、長さ、値を格納。

2. **Key**
   ```
   <6bit: key-hash> <2bit: key-len-size> <8-64bit: key-len> <nbyte: key-string>
   ```
   - **`key-hash`**: キー文字列のハッシュ値を格納（検索高速化用）。
   - **`key-len`**: キー文字列の長さ。
   - **`key-string`**: 実際のキー名。

3. **Array**
   ```
   <6bit: type> <2bit: data-len-size> <8-64bit: data-len> <nbyte: elements>
   ```
   - 配列型データ。

4. **Object**
   ```
   <6bit: type> <2bit: data-len-size> <8-64bit: data-len> <nbyte: key-value-pairs>
   ```
   - キーと値のペアを格納。

---

## **利点**
1. **キー検索の高速化**:
   - `key-hash` を用いることで、文字列比較を減らし検索性能を向上。

2. **小型化**:
   - キーが長い場合でも、ハッシュ値（6bit）を使うことで検索高速化。

3. **汎用性**:
   - ハッシュ衝突を許容しつつ、完全一致は文字列で確認可能。
   - ネスト構造（`Array`、`Object`）にも対応。

4. **拡張性**:
   - 6bit型フィールドで64種類のデータ型を表現可能。
   - 2bitで可変長フィールドを管理し、効率的に長さを格納。

---

## **用途**
- **データベースやキャッシュシステム**:
  - 頻繁にキー検索を行うシステムで有効。
- **低リソース環境**:
  - 組み込みデバイスやメモリ制限のあるシステム。
- **ネットワーク通信**:
  - 軽量で効率的なデータ転送が必要な場合。

---

## **他フォーマットとの比較**

| **特徴**               | **JSON**                          | **BSON**                      | **カスタムフォーマット（key-hash採用）** |
|------------------------|------------------------------------|--------------------------------|------------------------------------------|
| **データサイズ**        | 大きい（文字列冗長）              | 比較的小さい                   | **最小（キー効率化）**                   |
| **キー検索速度**        | 低速（完全文字列比較）            | 中速（NULL終端で効率化）       | **高速（ハッシュ比較 + 文字列検証）**    |
| **型情報の管理**        | 暗黙的                           | 明示的（1バイト）              | **軽量（6bit管理）**                     |
| **柔軟性**             | 高い                              | 中程度（MongoDB特化）          | **高い（汎用設計）**                     |
| **用途**               | 人間可読データの保存・通信        | データベース用途               | 高速・軽量データ処理                    |

---

## **例: サンプルデータのフォーマット**
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


**合計サイズ**:
- JSON: 48バイト  
- BSON: 42バイト  
- カスタムフォーマット: **41バイト** 
