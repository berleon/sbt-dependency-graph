sbt-dependency-graph
====================

Visualize your project's dependencies.

How To Use
----------

For sbt 0.11/0.12, add sbt-dependency-graph as a dependency in `project/plugins.sbt`:

```scala
addSbtPlugin("net.virtual-void" % "sbt-dependency-graph" % "0.7.0")
```

Then, add the following to your `<project-root>/build.sbt` (that's not `project/build.sbt`!) as a standalone line:

```scala
net.virtualvoid.sbt.graph.Plugin.graphSettings
```

OR, alternatively, if you use the full configuration, i.e. you define your build definition in `project/build.scala`, for example,
to define a multi-module project, you should add

```scala
.settings(net.virtualvoid.sbt.graph.Plugin.graphSettings: _*)
```

to each of the project definitions for which you want to use the plugin. The definition of your project should then
look approximately this way:

```scala
object MyBuild extends Build {
  val proj =
    Project("my-project", file("base"))
      .settings(net.virtualvoid.sbt.graph.Plugin.graphSettings: _*)
}
```

Check out the [example project] for a skeleton build setup.

Tasks & Settings
----------------

 * `dependency-graph`: Shows an ASCII graph of the project's dependencies on the sbt console
 * `dependency-graph-ml`: Generates a .graphml file with the project's dependencies to `target/dependencies-<config>.graphml`.
   Use e.g. [yEd](http://www.yworks.com/en/products_yed_about.html) to format the graph to your needs.
 * `dependency-tree`: Shows an ASCII tree representation of the project's dependencies
 * `what-depends-on <organization> <module> <revision>`: Find out what depends on an artifact. Shows a reverse dependency
   tree for the selected module.
 * `dependency-license-info`: show dependencies grouped by declared license
 * `filter-scala-library`: Defines if the scala library should be excluded from the output of the dependency-* functions.
   If `true`, instead of showing the dependency `"[S]"` is appended to the artifact name. Set to `false` if
   you want the scala-library dependency to appear in the output. (default: true)
 * `dependency-graph-ml-file`: a setting which allows configuring the output path of `dependency-graph-ml`.
 * `ivy-report`: let's ivy generate the resolution report for you project. Use
   `show ivy-report` for the filename of the generated report

All tasks can be scoped to a configuration to get the report for a specific configuration. `test:dependency-graph`,
for example, prints the dependencies in the `test` configuration. If you don't specify any configuration, `compile` is
assumed as usual.

Standalone usage
----------------

You can use the project without sbt as well by either depending on the library and calling
`IvyGraphMLDependencies.saveAsGraphML(IvyGraphMLDependencies.graph(reportFile), outputFile)` or by just getting the binary
and calling it like `scala sbt-dependency-graph-0.7.0.jar <ivy-report-xml-path> <target-path>`.

Inner Workings
--------------

sbt/Ivy's `update` task create ivy-report xml-files inside `.ivy2/cache` (in sbt 0.12.1:
`<project-dir>/target/resolution-cache/reports/<project-id>`). You can
just open them with your browser to look at the dependency report for your project.
This project takes the report xml of your project and creates a graphml file out of it. (BTW,
ivy can create graphml files itself, but since I didn't want to spend to much time getting
sbt to call into Ivy to create graphs, I went with the easy way here)

Known issues
------------

 * #19: There's an unfixed bug with graph generation for particular layouts. Workaround:
   Use `dependency-tree` instead of `dependency-graph`.
 * #12: Excluded dependencies will be shown in the graph in sbt < 0.12, works with later versions

Credits
-------

 * Matt Russell (@mdr) for contributing the ASCII graph layout.

License
-------

Copyright (c) 2011, 2012 Johannes Rudolph

Published under the [Apache License 2.0](http://en.wikipedia.org/wiki/Apache_license).

[example project]: https://gist.github.com/3106492