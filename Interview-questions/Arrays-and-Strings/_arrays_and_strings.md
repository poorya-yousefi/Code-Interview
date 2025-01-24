# Arrays and Strings

## Index

- [Hash Table](/Interview-questions/Arrays-and-Strings/hash_table.md)
- [Array List](/Interview-questions/Arrays-and-Strings/arraylist.md)
- [String Builder](/Interview-questions/Arrays-and-Strings/string_builder.md)

## Interview Questions

### 1.1 Is Unique

**Is Unique**: Implement an algorithm to determine if a string has all unique characters. What if you
cannot use additional data structures?

#### Solution

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

#### Other Solutions

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

### 1.2 Check Permutation

**Check Permutation**: Given two strings, write a method to decide if one is a permutation of the other.

#### Solution

```csharp
bool CheckPermutation(string str1, string str2)
{
    if (str1.Length != str2.Length) return false;

    short[] letters = new short[128];
    foreach (var ch in str1)
    {
        letters[ch]++;
    }

    foreach (var ch in str2)
    {
        letters[ch]--;
        if (letters[ch] < 0) return false;
    }

    return true;
}
```

#### Other Solutions

```csharp
// using LinQ
bool CheckPermutation(string str1, string str2)
{
    return str1.OrderBy(c => c).Equals(str2.OrderBy(c => c));
}
```

### 1.3 URLify

**URLify**: Write a method to replace all spaces in a string with '%20'. You may assume that the string has sufficient space at the end to hold the additional characters, and that you are given the "true" length of the string. (Note: If implementing in Java, please use a character array so that you can perform this operation in place.)

#### Solution

```csharp
string Urlify(char[] str, int trueLength)
{
    int spaceCount = 0;
    for (int i = 0; i < trueLength; i++)
    {
        if (str[i] == ' ')
        {
            spaceCount++;
        }
    }

    int index = trueLength + spaceCount * 2;
    if (trueLength < str.Length) str[trueLength] = '\0';
    for (int i = trueLength - 1; i >= 0; i--)
    {
        if (str[i] == ' ')
        {
            str[index - 1] = '0';
            str[index - 2] = '2';
            str[index - 3] = '%';
            index = index - 3;
        }
        else
        {
            str[--index] = str[i];
        }
    }
    return new string(str);
}
```

#### Other Solutions

```csharp
// using LinQ
string Urlify(string str, int trueLength)
{
    return new string(str.Take(trueLength).Select(c => c == ' ' ? "%20" : c.ToString()).SelectMany(c => c).ToArray());
}
```

### 1.4 Palindrome Permutation
Palindrome Permutation: Given a string, write a function to check if it is a permutation of a palindrome. A palindrome is a word or phrase that is the same forwards and backwards. A permutation is a rearrangement of letters. The palindrome does not need to be limited to just dictionary words.

>EXAMPLE
>Input: Tact Coa
>Output: True (permutations: "taco cat'; "atco eta·; etc.)

#### Palindrome

> A `palindrome` is a string that is the same forwards and backwards. We need to have an even number of almost all characters, so that half can be on one side and half can be on the other side. At most one character (the middle character) can have an odd count.

#### Solution

```csharp
bool isPal(string phrase)
{
    phrase = phrase.ToLower().Trim().Replace(" ", "");

    int a_index = 'a';
    int z_index = 'z';

    int[] table = new int[z_index - a_index + 1];

    foreach (char item in phrase)
    {
        if (item >= a_index && item <= z_index)
        {
            table[item - a_index]++;
        }
    }
    bool oddFound = false;
    for (int i = 0; i < table.Length; i++)
    {
        if (table[i] % 2 == 1)
        {
            if (oddFound) return false;
            oddFound = true;
        }
    }
    return true;
```

#### Other Solutions

```csharp
// using LinQ
bool isPal(string phrase)
{
    phrase = phrase.ToLower().Trim().Replace(" ", "");
    return phrase.GroupBy(c => c).Count(g => g.Count() % 2 == 1) <= 1;
}
```
