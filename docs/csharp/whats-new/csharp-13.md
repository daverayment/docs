---
title: What's new in C# 13
description: Get an overview of the new features in C# 13. Follow the release of new preview features as .NET 9 and C# 13 previews are released.
ms.date: 05/17/2024
ms.topic: whats-new
---
# What's new in C# 13

C# 13 includes the following new features. You can try these features using the latest [Visual Studio 2022](https://visualstudio.microsoft.com/vs/preview/) version or the [.NET 9 Preview SDK](https://dotnet.microsoft.com/download/dotnet).

- [`params` collections](#params-collections)
- [New `lock` type and semantics](#new-lock-object).
- [New escape sequence - `\e`](#new-escape-sequence).
- [Method group natural type improvements](#method-group-natural-type)
- [Implicit indexer access in object initializers](#implicit-index-access)
- [Enable `ref` locals and `unsafe` contexts in iterators and async methods](#ref-and-unsafe-in-iterators-and-async-methods)

C# 13 is supported on **.NET 9**. For more information, see [C# language versioning](../language-reference/configure-language-version.md).

You can download the latest .NET 9 preview SDK from the [.NET downloads page](https://dotnet.microsoft.com/download). You can also download [Visual Studio 2022 - preview](https://visualstudio.microsoft.com/vs/), which includes the .NET 9 Preview SDK.

New features are added to the "What's new in C#" page when they're available in public preview releases. The [working set](https://github.com/dotnet/roslyn/blob/main/docs/Language%20Feature%20Status.md#working-set) section of the [roslyn feature status page](https://github.com/dotnet/roslyn/blob/main/docs/Language%20Feature%20Status.md) tracks when upcoming features are merged into the main branch.

You can find any breaking changes introduced in C# 13 in our article on [breaking changes](~/_roslyn/docs/compilers/CSharp/Compiler%20Breaking%20Changes%20-%20DotNet%209.md).

[!INCLUDE [released-version-feedback](./includes/released-feedback.md)]

## `params` collections

The `params` modifier isn't limited to array types. You can now use `params` with any recognized collection type, including <xref:System.Span%601?displayProperty=nameWithType>, <xref:System.ReadOnlySpan%601?displayProperty=nameWithType>, and types that implement <xref:System.Collections.Generic.IEnumerable%601?displayProperty=nameWithType> and have an `Add` method. In addition to concrete types, the interfaces <xref:System.Collections.Generic.IEnumerable%601?displayProperty=nameWithType>, <xref:System.Collections.Generic.IReadOnlyCollection%601?displayProperty=nameWithType>, <xref:System.Collections.Generic.IReadOnlyList%601?displayProperty=nameWithType>, <xref:System.Collections.Generic.ICollection%601?displayProperty=nameWithType>, and <xref:System.Collections.Generic.IList%601?displayProperty=nameWithType> can also be used.

When an interface type is used, the compiler synthesizes the storage for the arguments supplied. You can learn more in the feature specification for [`params` collections](~/_csharplang/proposals/params-collections.md).

## New lock object

The .NET 9 runtime includes a new type for thread synchronization, the <xref:System.Threading.Lock?displayProperty=fullName> type. This type provides better thread synchronization through its API. The <xref:System.Threading.Lock.EnterScope?displayProperty=nameWithType> method enters an exclusive scope. The `ref struct` returned from that supports the `Dispose()` pattern to exit the exclusive scope.

The C# [`lock`](../language-reference/statements/lock.md) statement recognizes if the target of the lock is a `Lock` object. If so, it uses the updated API, rather than the traditional API using <xref:System.Threading.Monitor?displayProperty=nameWithType>. The compiler also recognizes if you convert a `Lock` object to another type and the `Monitor` based code would be generated. You can read more in the feature specification for the [new lock object](~/_csharplang/proposals/lock-object.md).

## New escape sequence

You can use `\e` as a [character literal](~/_csharpstandard/standard/lexical-structure.md#6455-character-literals) escape sequence for the `ESCAPE` character, Unicode `U+001B`. Previously, you used `\u001b` or `\x1b`. Using `\x1b` wasn't recommended because if the next characters following `1b` were valid hexadecimal digits, those characters became part of the escape sequence.

## Method group natural type

This feature makes small optimizations to overload resolution involving method groups. The previous behavior was for the compiler to construct the full set of candidate methods for a method group. If a natural type was needed, the natural type was determined from the full set of candidate methods.

The new behavior is to prune the set of candidate methods at each scope, removing those candidate methods that aren't applicable. Typically, the removed methods are generic methods with the wrong arity, or constraints that aren't satisfied. The process continues to the next outer scope only if no candidate methods are found. This process more closely follows the general algorithm for overload resolution. If all candidate methods found at a given scope don't match, the method group doesn't have a natural type.

You can read the details of the changes in the [proposal specification](~/_csharplang/proposals/method-group-natural-type-improvements.md).

## Implicit index access

The implicit "from the end" index operator, `^`, is now allowed in an object initializer expression. For example, you can now initialize an array in an object initializer as shown in the following code:

```csharp
var countdown = new TimerRemaining()
{
    buffer =
    {
        [^1] = 0,
        [^2] = 1,
        [^3] = 2,
        [^4] = 3,
        [^5] = 4,
        [^6] = 5,
        [^7] = 6,
        [^8] = 7,
        [^9] = 8,
        [^10] = 9
    }
};
```

The preceding example creates an array that counts down from 9 to 0. In versions before C# 13, the `^` operator can't be used in an object initializer. You need to index the elements from the front.

## `ref` and `unsafe` in iterators and `async` methods

Before C# 13, iterator methods (methods that use `yield return`) and `async` methods couldn't declare local `ref` variables, nor could they have an `unsafe` context.

In C# 13, `async` methods can declare `ref` local variables, or local variables of a `ref struct` type. However, those variables can't be accessed across an `await` boundary. Neither can they be accessed across a `yield return` boundary.

This relaxed restriction enables the compiler to allow verifiably safe use of `ref` local variables and `ref struct` types in more places. You can safely use types like <xref:System.ReadOnlySpan%601?displayProperty=nameWithType> in these methods. The compiler tells you if you violate safety rules.

In the same fashion, C# 13 allows `unsafe` contexts in iterator methods, provided no unsafe code appears in the same context as the `yield return` statements.

## See also

- [What's new in .NET 9](../../core/whats-new/dotnet-9/overview.md)
