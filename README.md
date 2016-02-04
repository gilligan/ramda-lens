# Ramda Lens

![build status](https://travis-ci.org/ramda/ramda-lens.svg?branch=master)

This library is an implementation of profunctor optics using JS, heavily
influenced by the [PureScript Profunctor Lenses][1] and [Kmett's Lens][2]
libraries.

The project is still very young and as such, documentation is currently
a work-in-progress. In the mean time, please feel free to drop by the
[Ramda room][3] on Gitter for any questions or further discussion.

[1]: https://github.com/purescript-contrib/purescript-profunctor-lenses
[2]: https://github.com/ekmett/lens
[3]: https://gitter.im/ramda/ramda

## Notes on Types and Classes

## API Documentation

### Lens

A lens represents the combination of a getter and setter which can be used to focus a certain subpart of
a data type in order to get, set or modify this subpart by applying a function on it.


#### lens
_Construct a new lens from a getter and a setter function_
```
lens :: (s -> a) -> (s -> b -> t) -> Lens s t a b

```
 * `(s -> a)`: The getter function: Given an object `s` return the focused subpart `a`
 * `(s -> b -> t)`: The setter function: Given object `s` set the focused subpart to `b` and return object `t` containing the update.

##### Example
```js
const xLens = lens(R.prop('x'), R.assoc('x'));
```

#### _lens
```
_lens :: (s -> Tuple a (b -> t)) -> Lens s t a b
```

#### view
_Return the focused subpart of an object by applying the given lens_
```
view :: Getter s t a b -> s -> a
```
 * `Getter s t a b`: ???
 * `s`: an object
 * `a`: the focused subpart

##### Example
```js
const xLens = lens(R.prop('x'), R.assoc('x'));
view(xLens, {x: 42}); // => 42
```


#### set
_Set the subpart focused by the given lens to the supplied value returning the updated object_
```
set :: Setter s t a b -> b -> s -> t
```
 * `Setter s t a b`: ???
 * `b`: value to set
 * `s`: an object
 * `t`: object containing the update

##### Example
```js
const xLens = lens(R.prop('x'), R.assoc('x'));
set(xLens, 1337, {x: 42}); // => {x: 1337}
```

#### over
_Apply the given function to the subpart focused by the provided lens_
```
over :: Setter s t a b -> (a -> b) -> s -> t
```
 * `Setter s t a b`: ???
 * `(a -> b)`: function to apply
 * `s`: an object
 * `t`: object containing the update

##### Example
```js
const xLens = lens(R.prop('x'), R.assoc('x'));
over(xLens, R.inc, {'x': 1}); // => {'x': 2}
```

#### mapped
```
mapped :: Functor f => Setter (f a) (f b) a b
```

#### _1
```
_1 :: Lens (Tuple a c) (Tuple b c) a b
```

##### Example
```js
L.view(L._1, RF.Tuple(3,2)) // => 3
```

#### _2
```
_2 :: Lens (Tuple c a) (Tuple c b) a b
```

##### Example
```js
L.view(L._2, RF.Tuple(3,2)) // => 2
```

#### atObject
```
atObject :: String -> LensP (Object a) (Maybe a)
```

##### Example
```js
L.view(L.atObject('foo'), {'foo':1}) // =>  _Just { value: 1 }
L.view(L.atObject('foo'), {'bar':1}) // => _Nothing {}
```

### Prism

#### prism
```
prism :: (b -> t) -> (s -> Either t a) -> Prism s t a b
```
##### Example
```js
const parseIntP = Prism.prism(x => x.toString(), s => {
  const i = parseInt(s, 10);
  return isNaN(i) ? RF.Either.Left(s) : RF.Either.Right(i);
});
```

#### prism_
```
prism_ :: (a -> s) -> (s -> Maybe a) -> PrismP s a
```

#### review
```
review :: Review s t a b -> b -> t
```

#### nearly
```
nearly :: a -> (a -> Boolean) -> PrismP a Unit
```

#### only
```
only :: Eq a => a -> PrismP a Unit
```

#### _Left
```
_Left :: Prism (Either a c) (Either b c) a b
```

#### _Right
```
_Right :: Prism (Either c a) (Either c b) a b
```

#### _Nothing
```
_Nothing :: Prism (Maybe a) (Maybe b) Unit Unit
```

#### _Just
```
_Just :: Prism (Maybe a) (Maybe b) a b
```

### Fold

#### foldMapOf
```
foldMapOf :: Monoid m => Type m -> Fold m s t a b -> (a -> m) -> s -> m
```

#### foldOf
```
foldOf :: Monoid m => Type m -> Fold m s t m b -> s -> m
```

#### preview
```
preview :: Fold (First a) s t a b -> s -> Maybe a
```

#### foldrOf
```
Fold (Endo r) s t a b -> (a -> r -> r) -> r -> s -> r
```

#### foldlOf
```
foldlOf :: Fold (Dual (Endo r)) s t a b -> (r -> a -> r) -> r -> s -> r
```

#### andOf
```
andOf :: Fold (Conj Boolean) s t Boolean b -> s -> Boolean
```
#### orOf
```
orOf :: Fold (Disj Boolean) s t Boolean b -> s -> Boolean
```
#### elemOf
```
elemOf :: Eq a => Fold (Disj Boolean) s t a b -> a -> s -> Boolean
```

#### notElemOf
```
notElemOf :: Eq a => Fold (Conj Boolean) s t a b -> a -> s -> Boolean
```
#### sumOf
```
sumOf :: Fold (Additive Number) s t Number b -> s -> Number
```

#### productOf
```
productOf :: Fold (Multiplicative Number) s t Number b -> s -> Number
```

#### lengthOf
```
lengthOf :: Fold (Additive Int) s t a b -> s -> Int
```
#### firstOf
```
firstOf :: Fold (First a) s t a b -> s -> Maybe a
```
#### lastOf
```
lastOf :: Fold (Last a) s t a b -> s -> Maybe a
```
#### maximumOf
```
maximumOf :: Ord a => Fold (Endo (Maybe a)) s t a b -> s -> Maybe a
```
#### minimumOf
```
minimumOf :: Ord a => Fold (Endo (Maybe a)) s t a b -> s -> Maybe a
```
#### findOf
```
findOf :: Fold (Endo (Maybe a)) s t a b -> (a -> Boolean) -> s -> Maybe a
```
#### sequenceOf_
```
sequenceOf_ :: Applicative f => Type f -> Fold (Endo (f Unit)) s t (f a) b -> s -> f Unit
```
#### toListOf
```
toListOf :: Fold (Endo [a]) s t a b -> s -> [a]
```
#### has
```
has :: Fold (Disj Boolean) s t a b -> s -> Boolean
```
#### hasnt
```
hasnt :: Fold (Conj Boolean) s t a b -> s -> Boolean
```
#### filtered
```
filtered :: Choice p => (a -> Boolean) -> OpticP p a a
```

### Iso
#### iso
```
iso :: (s -> a) -> (b -> t) -> Iso s t a b
```
#### re
```
re :: Optic (Re p a b) s t a b -> Optic p b a t s
```
#### withIso
```
withIso :: Iso s t a b -> ((s -> a) -> (b -> t) -> r) -> r
```
#### under
```
under :: Iso s t a b -> (t -> s) -> b -> a
```
#### non_
```
non_ :: PrismP a Unit -> IsoP (Maybe a) a
```
#### non
```
non_ :: PrismP a Unit -> IsoP (Maybe a) a
```

### Traversal
#### traversed
```
traversed :: Traversable t => Traversal (t a) (t b) a b
```

#### traverseOf
```
traverseOf :: Applicative f => Type f -> Optic (Star f) s t a b -> (a -> f b) -> s -> f t
```
#### sequenceOf
```
sequenceOf :: Applicative f => Type f -> Optic (Star f) s t (f a) a -> s -> f t
```
#### ixArray
```
ixArray :: Int -> TraversalP [a] a
```
#### ixObject
```
ixObject :: String -> TraversalP (Object a) a
```

### Cons

####  _Cons
```
 _Cons :: Prism [a] [b] (a, [a]) (b, [b])
```
####  _Snoc
```
_Snoc :: Prism [a] [b] ([a], a) ([b], b)
```
####  cons
```
cons :: a -> [a] -> [a]
```
####  uncons
```
uncons :: [a] -> Maybe (Tuple a [a])
```
####  snoc
```
snoc :: [a] -> a -> [a]
```
####  unsnoc
```
unsnoc :: [a] -> Maybe (Tuple [a] a)
```
####  _head
```
head :: TraversalP s a
```
####  _tail
```
_tail :: TraversalP s s
```
####  _init
```
_init :: TraversalP s a
```
####  _last
```
_last :: TraversalP s s
```
