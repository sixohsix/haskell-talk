
# Haskell
## Correct Code that Actually Runs (at the Cost of your Sanity)

> Mike Verdone

---

```haskell
    main = print "Hello, world!"
```

---

```haskell
    qsort :: Ord a => [a] -> [a]
    qsort [] = []
    qsort (x:xs) =
      let pivot = x
          left  = [v | v <- xs, v <= pivot]
          right = [v | v <- xs, v > pivot]
      in (qsort left) ++ [pivot] ++ (qsort right)
```

---
= data-x='1000'
## What makes Haskell different?

Basically everything.

- strictly lazy
- purely functional
- strongly typed

---
= data-x='2000'
## lazy

- Haskell doesn't evaluate something if can find a way to avoid it

```haskell
    -- in this example, 'a' is never computed
    let a = 5 + 5
        b = 8 + 8
        c = b + 9
     in print c
```

---
= data-x='2000' data-y='100'

- This is totally fine:


```haskell
    -- an infinite list counting up from 1.
    let a = [1..]

    -- the first 100 elements from the infinite list.
    let b = take 100 [1..]
```

---
= data-x='2000' data-y='200'

- Even this is cool:


```haskell
    -- the length of the infinite list
    let c = length [1..]
```

- This, however, causes your program to lock up:


```haskell
    -- print the length of the infinite list
    print (length [1..])
```

---
= data-x='3000' data-y='0'
## pure functional

- no "variables"
- no mutable data structures
- "no" side effects
- this means "hello world" is impossible


---
= data-x='4000' data-y='0'
## Purely Static Strong Types

```haskell
    -- All methods have type signatures that look
    -- like this:
    addNumbers :: Int -> Int -> Int
    addNumbers a b = a + b

    -- You can leave them out and the compiler will
    -- try and derive them.
    subtractNumbers a b = a - b
```

---
= data-x='4000' data-y='100'

```haskell
    -- You can use classes to make functions 
    -- over abstract types.
    multiply :: Num a => a -> a -> a
    multiply a b = a * b
```

---
= data-x='4000' data-y='200'

## Everything is cool as long as the type can be infered at compile time, BUT:

- There is no `cast` operation
- *None*
- *Zero*
- *Zip*
- *Nada*

---
= data-x='5000' data-y='0'

## At this point you're asking: why would I ever use this?

- challenge
- many classes of bugs are now *impossible to write*

---
= data-x='6000' data-y='0'

## A *lot* of open source libraries

 - 4911 packages in *hackage*
 - web frameworks, graphics, sound, robotics...

```bash
    $ cabal install *anything*
```

---
= data-x='6000' data-y='200'

## Good integration with C

```haskell
        initCurses = do
          void {# call initscr #}
          void {# call cbreak #}
          void $ {# call mousemask #} allEvents nullPtr
          hasColor <- {# call has_colors #}
          when (hasColor == 1) $ do
                   void {# call start_color #}
                   void {# call use_default_colors #}
          stdscr <- peek c_stdscr
          void $ {# call keypad #} (Window stdscr) 1
          void $ {# call meta #} (Window stdscr) 1
          {# call wtimeout #} (Window stdscr) (- 1)
```

---

## It's actually fast

![Programming language speed](images/prog-lang-speed.png)


---

## Resources for learning this thing

- All the books are terrible

---

## Most Haskell books go like this:

- Introduction
- How to run the interpreter
- How to write simple functions
- Recursion
- What is a Monad?! (10 chapters)
- High order *endomorphic functors* and the Applicative typeclass
- Lens composition of *algebraic data types*
- ...
- ...
- **How to read a file**

---

## "Learn You A Haskell For Great Good!" is kinda annoying

![Learn You A Haskell home page](images/learn-you.png)

---

## Real World Haskell is pretty good

![Real World Haskell](images/real-world-haskell.png)

---

## My advice

- **learn by doing**

---

## An example: IO

- Look in the library reference, you find `print` and `getLine`.
- you write this program and it fails:

```haskell
    let umm = print "What's your first name?"
        firstName = getLine
        uhh = print "What's your last name?"
        lastName = getLine
    in print ("Hello " ++ firstName ++ " " ++ lastName)
```

---

## It fails like this

```python
bad.hs:6:30:
    Couldn't match expected type `[Char]' 
      with actual type `IO String'
    In the first argument of `(++)', namely `firstName'
    In the second argument of `(++)', namely
      `firstName ++ " " ++ lastName'
    In the first argument of `print', namely
      `("Hello " ++ firstName ++ " " ++ lastName)'
```

---

## IO Jail

- All of the IO functions are of a type like `a -> IO b`
- The `b` is the output but it's locked inside the IO jail
- This "jail" is called a Monad
- What's a Monad??

---

# Do not ask what a Monad is

---

## But why?

- understanding a Monad is really annoying
- understanding a Monad is unnecessary to use it

---

## Let's build a working model

- IO is a side effect
- But side effects are not allowed
- Because they are bad they are put in IO jail and can't come out
- All IO operations have to be done in IO jail
- (There are other types of jails (monads) but we don't care)

---
```haskell
    main = do
      print "What's your first name?"
      firstName <- getLine
      print "What's your last name?"
      lastName <- getLine
      print ("Hello " ++ firstName ++ " " ++ lastName)
```

