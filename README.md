![](logo.png)

PigPen is map-reduce for Clojure, or distributed Clojure. It compiles to [Apache Pig](http://pig.apache.org/), but you don't need to know much about Pig to use it.

# Getting Started, Tutorials & Documentation

Getting started with Clojure and PigPen is really easy.

  * The [wiki](https://github.com/Netflix/PigPen/wiki) explains what PigPen does and why we made it
  * The [tutorial](https://github.com/Netflix/PigPen/wiki/Tutorial) is the best way to get Clojure and PigPen installed and start writing queries
  * The [full API](http://netflix.github.io/PigPen/pigpen.core.html) lists all of the operators with example usage
  * [PigPen for Clojure users](https://github.com/Netflix/PigPen/wiki/Getting_Started_for_Clojure_Users) is great for Clojure users new to map-reduce
  * [PigPen for Pig users](https://github.com/Netflix/PigPen/wiki/Getting_Started_for_Pig_Users) is great for Pig users new to Clojure

_Note: If you are not familiar at all with [Clojure](http://clojure.org/), I strongly recommend that you try a tutorial [here](http://tryclj.com/), [here](http://java.ociweb.com/mark/clojure/article.html), or [here](http://learn-clojure.com/) to understand some of the [basics](http://clojure.org/cheatsheet)._

_Note: PigPen is **not** a Clojure wrapper for writing Pig scripts you can hand edit. While entirely possible, the resulting scripts are not intended for human consumption._

# Questions & Complaints

  * pigpen-support@googlegroups.com

# Artifacts

`pigpen` is available from Maven:

With Leiningen:

``` clj
[com.netflix.pigpen/pigpen "0.2.6"]
```

With Gradle:

``` groovy
compile "com.netflix.pigpen:pigpen:0.2.6"
```

With Maven:

``` xml
<dependency>
  <groupId>com.netflix.pigpen</groupId>
  <artifactId>pigpen</artifactId>
  <version>0.2.6</version>
</dependency>
```

_Note: PigPen requires Clojure 1.5.1 or greater_

# Release Notes

  * 0.2.6
    * Minor optimization for local mode. The creation of a UDF was occurring for every value processed, causing it to run out of perm-gen space when processing large collections locally.
    * Fix `(pig/return [])`
    * Fix `(pig/dump (pig/reduce + (pig/return [])))`
    * Fix `Long`s in scripts that are larger than an Integer
    * Memoize local UDF instances per use of `pig/dump`
    * The jar location in the generated script is now configurable. Use the `:pigpen-jar-location` option with `pig/generate-script` or `pig/write-script`.
  * 0.2.5
    * Remove `dump&show` and `dump&show+` in favor of `pigpen.oven/bake`. Call `bake` once and pass to as many outputs as you want. This is a breaking change, but I didn't increment the version because `dump&show` was just a tool to be used in the REPL. No scripts should break because of this change.
    * Remove `dymp-async`. It appeared to be broken and was a bad idea from the start.
    * Fix self-joins. This was a rare issue as a self join (with the same key) just duplicates data in a very expensive way.
    * Clean up functional tests
    * Fix `pigpen.oven/clean`. When it was pruning the graph, it was also removing REGISTER commands.
  * 0.2.4
    * Fix arity checking bug (affected varargs fns)
    * Fix cases where an Algebraic fold function was falling back to the Accumulator interface, which was not supported. This affected using `cogroup` with `fold` over multiple relations. 
    * Fix debug mode (broken in 0.1.5)
    * Change UDF initialization to not rely on memoization (caused stale data in REPL)
    * Enable AOT. Improves cluster perf
    * Add `:partition-by` option to `distinct`
  * 0.2.3
    * Added `load-json`, `store-json`, `load-string`, `store-string`
    * Added `filter-by`, and `remove-by`
  * 0.2.2
    * Fixed bug in `pigpen.fold/vec`. This would also cause `fold/map` and `fold/filter` to not work when run in the cluster.
  * 0.2.1
    * Fixed bug when using `for` to generate scripts
    * Fixed local mode bug with `map` followed by `reduce` or `fold`
  * 0.2.0
    * Added pigpen.fold - Note: this includes a breaking change in the join and cogroup syntax as follows:
    
    ``` clj
    ; before
    (pig/join (foo on :f)
              (bar on :b optional)
              (fn [f b] ...))
    
    ; after
    (pig/join [(foo :on :f)
               (bar :on :b :type :optional)]
              (fn [f b] ...))
    ```
    
    Each of the select clauses must now be wrapped in a vector - there is no longer a varargs overload to either of these forms. Within each of the select clauses, :on is now a keyword instead of a symbol, but a symbol will still work if used. If `optional` or `required` were used, they must be updated to `:type :optional` and `:type :required`, respectively.
    
  * 0.1.5
    * Performance improvements
      * Implemented Pig's Accumulator interface
      * Tuned nippy
      * Reduced number of times data is serialized
  * 0.1.4
    * Fix sort bug in local mode
  * 0.1.3
    * Change Pig & Hadoop to be transitive dependencies
    * Add support for consuming user code via closure
  * 0.1.2
    * Upgrade instaparse to 1.2.14
  * 0.1.1
    * Initial Release
