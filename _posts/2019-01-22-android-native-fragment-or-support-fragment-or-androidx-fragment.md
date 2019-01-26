---
layout: post
title: "Native Fragment vs. Support Fragment vs. Androidx Fragment"
comments: true
language: "EN"


---

or...

## Fragment vs. Fragment vs. Fragment

Someone who is new in the field of Android development might wonder... 

<i>What is the difference between all these different Fragment implementations???</i>

First of all, what kind of `Fragment` implementations do exist?

<table>
<tr>
	<td>Library</td><td>Package</td>
</tr>
<tr>
	<td>Support Library</td><td>android.support.v4.app.Fragment</td>
</tr>
<tr>
	<td>AndroidX Library</td><td>androidx.fragment.app.Fragment</td>
</tr>
<tr>
	<td>Native</td><td>android.app.Fragment</td>
</tr>

### Support Library Fragment

A little history... Google introduced the concept of Fragments in Android 3 (Honeycomb - API Level 11) to improve the ability to write proper apps by making it possible to make parts of a UI reusable.

Back then, most of the devices were still running on pre-Android 3 devices...

Let's say you want to write an app that makes use of Fragments which is targeted for Android 3, but should also run on older devices. How do you handle that?

To solve this issue, Google kinda forked the implementation of `Fragment` and has provided the fork in a dedicated library called "Support Library". The library allows you to use newer features of Android on older versions of Android.

### Native Fragment

Every Android 3+ system provides a *native* implementation of `Fragment`. Theoretically, if you don't need to support pre-Android 3 devices, then you would not need the Support Library to use `Fragment` ... <u><b>but...</b></u>

### Native Fragment vs. Support Library Fragment

The problem with the native `Fragment` is, that it has been very buggy since the beginning and it doesn't get the "latest update" of `Fragment` because it is part of the Android OS. Therefore an Android 4 Fragment is more buggy than an Android 5 Fragment... and an Android 5 is more buggy than an Android 7 Fragment.

So, *the native Fragment behavior/implementation has subtle differences between versions!*

However, the support library works to make sure that support fragments always work the same! It doesn't matter if your app is running on an Android 6 or Android 8 device. The support library Fragment gets shipped with the app and contains recent bug fixes.

To make it clear: *There is no reason to use the native Fragment implementation!*

A few months ago, Google deprecated the native Fragment class in API level 28

Ok, but what about the Androix Fragment class?

## Androidx Fragment

AndroidX is a significant improvement to the original Android Support Library.  It *contains the existing support library(!)* and also includes Jetpack components.  In case you are already using AndroidX in your project, use androidx.fragment.app.Fragment. Otherwise, you can either use the Support Lib (android.support.v4.app.Fragment) or you can migrate the entire project to AndroidX.


## Summary... in three bullets

- Never use the native Fragment class (android.app.Fragment)
- If you are already using AndroidX, use androidx.fragment.app.Fragment (otherwise consider a migration to AndroidX)
- If you don't want to use AndroidX yet, simply use the Support Lib (android.support.v4.app.Fragment)


## Thank you...

... for reading this article about different types of Fragment in Android. If you have any question, spotted an error or want to know more about that topic, please leave a comment. I'm happy to hear from you :blush:

If you want to stay up to date with news from Android and software development in general, please follow me on <a href="https://twitter.com/andreasschrade" target="_blank">Twitter</a> a or sign up for my newsletter!