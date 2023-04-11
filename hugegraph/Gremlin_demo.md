```groovy
g.V().where(bothE().count().is(0)).limit(2000).drop()
g.V().where(bothE().count().is(0)).count()
```

```groovy
// 利用sideEffect计算3种度中心性
g.V('12345')
  .group('both').by().by(bothE().count())
     .group('out').by().by(outE().count())
     .group('in').by().by(inE().count())
     .cap('both', 'out', 'in')
```

