# Arrays and Strings

## Index

- [Hash Table](/Interview-questions/Arrays-and-Strings/hash_table.md)
- [Array List](/Interview-questions/Arrays-and-Strings/arraylist.md)
- [String Builder](/Interview-questions/Arrays-and-Strings/string_builder.md)

## Interview Questions

### 1.1 Is Unique

**Is Unique**: Implement an algorithm to determine if a string has all unique characters. What if you
cannot use additional data structures?

### Solution

```csharp
bool IsUnique(string str)
{
    if (str.Length > 128) return false;

    bool[] char_set = new bool[128];
    for (int i = 0; i < str.Length; i++)
    {
        int val = str[i];
        if (char_set[val]) return false;
        char_set[val] = true;
    }
    return true;
}
```

> The time complexity for this code is O(n), where n is the length of the string. The space complexity is O(1).
If we can't use additional data structures, we can do the following:

### Other Solutions

```csharp
// using LinQ
bool IsUnique(string str)
{
    return str.Distinct().Count() == str.Length;
}

// using LinQ
bool IsUnique(string str)
{
    return str.GroupBy(c => c).All(g => g.Count() == 1);
}

//using HashSet data structure
bool IsUnique(string str)
{
    HashSet<char> set = new HashSet<char>(str.Length);
    foreach (var ch in str)
    {
        if (set.Contains(ch))
        {
            return false;
        }
        set.Add(ch);
    }
    return true;
}

// O(n^2) time complexity
// Compare every character of the string to every other character of the string. This will take 0(n2) time and 0(1) space.
bool IsUnique(string str)
{
    for (int i = 0; i < str.Length; i++)
    {
        var ch1 = str[i];
        for (int j = i + 1; j < str.Length; j++)
        {
            if (j == str.Length){
                break;
            }
            var ch2 = str[j];
            if (ch1 == ch2)
            {
                return false;
            }
        }
    }
    return true;
}

// If we are allowed to modify the input string, we could sort the string in O(nlog(n)) time and then
// linearly check the string for neighboring characters that are identical. Careful, though: many sorting
// algorithms take up extra space.
bool IsUnique(string str)
{
    var sorted = str.OrderBy(c => c).ToArray();
    for (int i = 0; i < sorted.Length - 1; i++)
    {
        if (sorted[i] == sorted[i + 1])
        {
            return false;
        }
    }
    return true;
}

```
