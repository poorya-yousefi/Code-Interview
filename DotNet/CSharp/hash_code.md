# HashCode

- `GetHashCode` method is used to get the hash code of an object.
- The hash code is used to quickly identify the object in the collection.
- The hash code is not unique for each object. Two different objects can have the same hash code.

In C#, the default implementation of `GetHashCode` in the System.Object class returns a hash code that is based on the object's reference. This means that the hash code is derived from the **memory address of the object**. However, the exact details of how this hash code is computed are not specified and can *vary* between different versions of the .NET runtime.

## String.GetHashCode()

In most programming languages, if two strings are exactly the same, their hash codes will also be the same. This is because the hash code is typically computed based on the content of the string.

```csharp
public int hashCode() {
    int h = 0;
    int length = this.length();
    // 31 is prime number
    for (int i = 0; i < length; i++) {
        h = 31 * h + this.charAt(i);
    }
    return h;
}
```

> This algorithm ensures that the hash code is influenced by the order and value of the characters in the string, providing a good distribution of hash codes for different strings.

## Example

```csharp
using System;

public class MyClass
{
    public int Id { get; set; }
    public string Name { get; set; }

    public override int GetHashCode()
    {
        // Combine the hash codes of the properties to generate a unique hash code for the object
        int hash = 17;
        hash = hash * 31 + Id.GetHashCode();
        hash = hash * 31 + (Name != null ? Name.GetHashCode() : 0);
        return hash;
    }

    public override bool Equals(object obj)
    {
        if (obj == null || GetType() != obj.GetType())
        {
            return false;
        }

        if (ReferenceEquals(this, obj))
        {
            return true;
        }

        MyClass other = (MyClass)obj;
        return Id == other.Id && Name == other.Name;
    }
}
```