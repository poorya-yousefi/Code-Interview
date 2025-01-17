# StringBuilder

StringBuilder simply creates a resizable array of all
the strings, copying them back to a string only when necessary.

```csharp
public class StringBuilder {
    private char[] value;
    private int count;

    public StringBuilder() {
        value = new char[16];
        count = 0;
    }

    public StringBuilder(int capacity) {
        value = new char[capacity];
        count = 0;
    }

    public StringBuilder append(String str) {
        if (str == null) {
            str = "null";
        }
        int len = str.length();
        ensureCapacityInternal(count + len);
        str.getChars(0, len, value, count);
        count += len;
        return this;
    }

    public StringBuilder append(char c) {
        ensureCapacityInternal(count + 1);
        value[count++] = c;
        return this;
    }

    public String toString() {
        return new String(value, 0, count);
    }

}
```
