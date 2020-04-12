---
layout: post
title: "How to write not so messy code"
comments: true
published: false

---

1. Write code carefully!

Try to catch possible problems (wrong condition, possible memory leaks, ...) early on, and test every line of code at least once before committing.

The sooner a bug is spotted, the less time, money, and bad user experience it costs.

Compare these two cases:

Best Case: Developer spots a bug early on. Involved persons and time: developer.

Worst Case: User spots a bug: Involved persons: developer, QA, customer, support.



2. Whenever an error or problem occurs, make sure it doesn't happen again.

By doing so, the number of bugs in your bug tracker decreases over time. Therefore, more time to implement new features and maintain the code base.




3. Keep things as simple as possible (but not simpler!) and avoid *unnecessary* complexity that has no positive impact. A simple software design helps you to spot errors and deficiencies, while a complex software design makes the spotting of errors no longer obvious.



4. Establish a list of checklists for routine-tasks.

Checklists help you to achieve consistency across your team members.

Example: Checklist for contributing code:
- Does the commit message follow the pattern xyz?
- Is everything integrated/merged?
- Do unit tests exist?



5. Write code in small chunks and make sure that it is easy to read.

Small chunks can be tested easily and readable code makes the visual inspection for correctness way easier.



6. First understand, then commit.

Whenever you stumble across some code in your company's project which you don't understand... and you are in the process of making assumptions... please stop and read the documentation for the affected API or just ask a colleague.

Assumptions are a typical source of errors. How often does it happen that a bug appears and a colleague says: "Ahhh, I didn't know that ..."



7. Write extendable code.

Don't overengineer your software upfront. Just make sure that you can extend your code whenever you need.

Software is hardly ever done. There is always something to add, change or remove. Make your own life more comfortable and double-check that your code provides an *obvious*, *easy* and *fast* way to extend.



8. Make use of code formatters.

A formatter helps you to reduce complexity by simplifying reading. A more readable code makes the visual inspection for correctness way easier and also increases the speed of development. It's critical that all team members use the same code formatter to prevent merge conflicts based on different code formattings.



9. Pay attention to the complexity of your code and make sure that the complexity of your code matches the complexity of the problem you want to solve. (A simple problem should get solved with simple code)

There are different ways to measure complexity:
- number of lines
- number of parameters
- number of layers
- number of nested loops/conditions (please avoid nested loops)
- cyclomatic complexity

The higher the complexity, the lower the understandability. The lower the understandability, the higher the error-proneness and more time it takes to debug errors. 



10. Observe the quality of your tests.

Good unit tests catch bugs over time. Whenever you encounter a bug, try to understand why the (unit) test didn't catch the bug and the fix the reason.



11. Do code reviews!

Every successful author has at least one editor who helps him to improve his writing. If you want to write successful software, you need a second, unbiased view.



12. Whenever you are changing a function, make sure that you leave the code in a better condition as you found it. Over time, your code base gets automatically better and better.



13. Keep the scope of variables as small as reasonable.

The smaller the scope of variables, functions, and classes, the easier to understand and debug.

Consider the following example:

<pre><code class="language-kotlin">
fun doImportantWork() {
  var x = 1
}
</code></pre>

and

<pre><code class="language-kotlin">
fun doImportantWork() {
  x = 1
}
</code></pre>

The first function is easier to debug! If you want to understand the variable x, all you need to do is to read and understand doImportantWork. 

If you want to understand the second function, then you have to search for all mutators. This approach costs definitely more time to understand and is more error-prone.



14. Don't write code before it's needed, but make sure that you can add it later easily.

Don't add code only because...
- you feel that it would make something "complete"
- it is possibly required at some point in the future

Every line of code can cause bugs, increases the complexity and makes refactorings more time-consuming. Therefore, don't write useless code.


15. Clean up code as soon as possible.

Code is fragile. It seems that entropy is especially hard working on code. 
Therefore, the longer you wait, the harder it gets. Fix problems early on and don't wait until your problems get unmanageable.











