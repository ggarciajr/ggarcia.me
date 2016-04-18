---
layout: post
title: "Flora Time Tracker - Token Generator"
excerpt: "How to use Haskell'l random library to creating a token generator"
date: 2016-04-18
tags: [Haskell, Random, UUID]
author: ggarciajr
comments: true
---

An essencial part of the **free** session page is the unique token that is used to generate a url that allow us to identify the project and the related activities of a given session.

That way, the user can enter its unique url in the browser and access the session with all previously created activities.

In order to generate the token we need a random number generator, which is provided by Haskell's *random* library.

The token generation consists in creating a function that takes a generator and an int as argument and then returns a random token. Such function is pretty simple and doesn't need further explanation.

```haskell
import ClassyPrelude
import Data.Char
import Safe (atNote)
import System.Random

generateToken :: RandomGen g => g -> Int -> Text
generateToken randomGen numOfChars =
  let chars = filter isAlphaNum ['0'..'z'] -- generates a list of eligible chars for the token
      -- given a list of 'random' numbers, returns the list of number resulting
      -- by the application of: generated number mod number of eligible chars.
      -- this ensures that we won't use an invalid index when trying to get
      -- a char from the list of eligible chars using the generated numbers.
      ints = (\i -> i `mod` length chars) <$> take numOfChars (randoms randomGen :: [Int])
      token = pack $ map (atNote "invalid index of chars array" chars) ints
  in token
```

<div class="spacer">
  <div class="mask"></div>
</div>

A more detailed discussion about random numbers in Haskell can be found in <a href="https://www.schoolofhaskell.com/school/starting-with-haskell/libraries-and-frameworks/randoms" target="_blank">here</a> and you can find the entire changes on <a href="https://github.com/ggarciajr/flora/commit/35e0d7f505db9590a2f32528d29a660e3bb45c5e" target="_blank">github</a>.
