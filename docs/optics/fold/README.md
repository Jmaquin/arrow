---
layout: docs
title: Fold
permalink: /docs/optics/fold/
---

## Fold

A `Fold` is an optic that can see into a structure and get 0 to N foci.
It is a generalisation of an instance of [`Foldable`](/docs/typeclasses/foldable).

Creating a `Fold` can be done by manually defining `foldMap`.

```kotlin
import arrow.*
import arrow.typeclasses.*
import arrow.optics.*

fun <T> nullableFold(): Fold<T?, T> = object : Fold<T?, T> {
    override fun <R> foldMap(M: Monoid<R>, s: T?, f: (T) -> R): R =
        s?.let(f) ?: M.empty()
}
//kotlin.Unit
```

Or you can get a `Fold` from any existing `Foldable`.

```kotlin
import arrow.data.*

val nonEmptyIntFold: Fold<NonEmptyListKind<Int>, Int> = Fold.fromFoldable(NonEmptyList.foldable())
//kotlin.Unit
```

`Fold` has a similar API as `Foldable` but because it's defined in terms of `foldMap` there are no associative fold functions available.

```kotlin
nullableFold<Int>().isEmpty(null)
//kotlin.Unit
```
```kotlin
nonEmptyIntFold.combineAll(NonEmptyList.of(1, 2, 3))
//kotlin.Unit
```
```kotlin
nullableFold<Int>().headOption(null)
//kotlin.Unit
```
```kotlin
nonEmptyIntFold.headOption(NonEmptyList.of(1, 2, 3, 4))
//kotlin.Unit
```

## Composition

Composing `Fold` can be used for accessing foci in nested structures.

```kotlin
val nestedNelFold: Fold<NonEmptyListKind<NonEmptyListKind<Int>>, NonEmptyListKind<Int>> = Fold.fromFoldable()

val nestedNel = NonEmptyList.of(1, 2, 3, 4).map {
    NonEmptyList.of(it, it)
}

(nestedNelFold compose nonEmptyIntFold).getAll(nestedNel)
//kotlin.Unit
```

`Fold` can be composed with all optics but `Setter` and results in the following optics.

|   | Iso | Lens | Prism |Optional | Getter | Setter | Fold | Traversal |
| --- | --- | --- | --- |--- | --- | --- | --- | --- |
| Fold | Fold | Fold | Fold | Fold | Fold | X | Fold | Fold |