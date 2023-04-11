# 1 创建

## 创建顶点

```groovy
// properties():属性列表
// useAutomaticId():Automatic 自增主键策略
// useCustomizeStringId():Customize_String 自定义字符策略
// useCustomizeNumberId():Customize_Number 自定义数字策略
// usePrimaryKeyId():PrimaryKey 自定义属性策略
schema.vertexLabel("person").properties("name", "age").useAutomaticId().ifNotExist().create();
schema.vertexLabel("person").properties("name", "age").useCustomizeStringId().ifNotExist().create();
schema.vertexLabel("person").properties("name", "age").useCustomizeNumberId().ifNotExist().create();
schema.vertexLabel("person").properties("name", "age").usePrimaryKeyId().primaryKeys("name").ifNotExist().create();
```

## 创建边

```groovy
// link():双向
// sourceLabel():单向起始顶点
// targetLabel():单向目标顶点
// multiTimes():允许多次连接
// properties():属性列表
// nullableKeys():非空属性

schema.edgeLabel("knows").link("person", "person").properties("date").ifNotExist().create();
schema.edgeLabel("created")
.multiTimes()
.sourceLabel("person")
.targetLabel("person")
.properties("date")
.nullableKeys("date")
.ifNotExist().create();
```

## 删除

```groovy
// 删除
schema.vertexLabel("person").remove();
schema.edgeLabel("knows").remove();
```

## 属性

```groovy
// 获取property key属性
schema.getVertexLabel("person").idStrategy()
schema.getVertexLabel("person").primaryKeys()
schema.getVertexLabel("person").name()
schema.getVertexLabel("person").properties()
schema.getVertexLabel("person").nullableKeys()
schema.getVertexLabel("person").userdata()
// 获取property key属性
schema.getEdgeLabel("knows").frequency()
schema.getEdgeLabel("knows").sourceLabel()
schema.getEdgeLabel("knows").targetLabel()
schema.getEdgeLabel("knows").sortKeys()
schema.getEdgeLabel("knows").name()
schema.getEdgeLabel("knows").properties()
schema.getEdgeLabel("knows").nullableKeys()
schema.getEdgeLabel("knows").userdata()
```

# 2 图的基本操作

##  图查询

```groovy
// 查询图中所有的顶点
g.V()
// 根据主键id查询顶点
g.V('123456','420822199011233409','420822198403093446','420822199505073256')

// 查询图中所有的边
g.E()
// 根据主键id查询边
g.E('420822199011233409>18>>S9:鄂A3T398')
```

## 属性查询

```groovy
// 查询所有顶点的主键id
g.V().id()
// 查询所有边的主键id
g.E().id()

// 查询所有顶点的label
g.V().label()
// 查询所有边的label
g.E().label()

// valueMap()与 properties()不同的地方是：它们返回的结构不一样
// properties()：将所有的属性扁平化到一个大列表里面，一个元素代表一个属性；
// valueMap()：保持一个顶点或一条边的属性作为一组，每一组由若干属性的键值对组成。

// 查询所有顶点的属性
g.V().properties()
g.V().valueMap()
// 查询所有边的属性
g.E().properties()
g.E().valueMap()

// 查询所有顶点的“name”属性,如果无“name”属性的顶点将跳过
g.V().properties('name')
// 查询所有顶点的属性名称
g.V().properties().key()
// 查询所有顶点的属性值
g.V().properties().value()

// 查询所有顶点的属性值
// 效果等同于： g.V().properties().value()
g.V().values()
// 查询所有边的属性值
// 效果等同于： g.E().properties().value()
g.E().values()

// 查询所有顶点的“name”属性
// 效果等同于：g.V().properties('name').value()
g.V().values('name')
```

# 3 查询

![image-20220210163011737](C:\Users\quanchao\AppData\Roaming\Typora\typora-user-images\image-20220210163011737.png)

```dylan
以顶点为基准:
out(label):查询顶点的OUT方向邻接点
in(label):查询顶点的IN方向邻接点
both(label):查询顶点的双向邻接点
outE(label):查询顶点的OUT方向邻接边
inE(label):查询顶点的IN方向邻接边
bothE(label):查询顶点的双向邻接边

以边为基准:
outV(): 访问边的出顶点，出顶点是指边的起始顶点
inV(): 访问边的入顶点，入顶点是指边的目标顶点，也就是箭头指向的顶点
bothV(): 访问边的双向顶点
otherV(): 访问边的伙伴顶点，即相对于基准顶点而言的另一端的顶点
```

## 基于顶点查顶点

```groovy
// 先查询图中所有的顶点，然后查询顶点的OUT方向邻接点
g.V().out()
// 查询顶点的OUT方向邻接点
g.V('7:123456').out()

g.V().in()
g.V('7:420822199505073256').in()

g.V().both()
// 查询顶点的双向邻接点
g.V('7:420822199505073256').both()
// label类型筛选
g.V('7:123456').both("relative")
```

## 基于顶点查边

```groovy
g.V().outE()
g.V('7:123456').outE()
// label类型筛选
g.V('7:123456').outE('relative')

g.V().inE()
g.V('7:420822199505073256').inE()
g.V('7:420822199505073256').inE("same_train")

g.V().bothE("imsi_owner")
g.V('8:420822199505073256').bothE()
g.V('8:420822199505073256').bothE("imsi_owner")
```

## 基于边查顶点

```groovy
// 先查询某个顶点的IN邻接边，然后查询边的出顶点
g.V('7:420822199505073256').inE().outV()
// 一般情况下：inE().outV()等价于in()

// 先查询顶点的OUT邻接边，然后查询边的入顶点
g.V('7:420822199505073256').outE().inV()
// 一般情况下：outE().inV()等价于out()

// 先查询顶点的OUT邻接边，然后查询边的双向顶点
g.V('8:420822199505073256').outE().bothV()
// bothV()会把源顶点也一起返回，因此只要源顶点有多少条出边，结果集中就会出现多少次源顶点

// 先查询顶点的OUT邻接边，然后查询边的伙伴顶点
g.V('8:420822199505073256').outE().otherV()
// 一般情况下：
// outE().otherV()等价于out()，
// inE().otherV()等价于in()
// bothE().otherV()等价于both()
```

# 4 索引

在HugeGraph中，按property的值查询之前，应该对property建立索引，否则将无法查到结果并引发异常。

有两个例外，Vertex的PrimaryKeys和Edge的SortKeys，具体参见[HugeGraph官网](https://hugegraph.github.io/hugegraph-doc/)

## 创建索引

```groovy
// 创建索引
graph.schema().indexLabel(索引名).onV(顶点类型).by(属性名).secondary().ifNotExist().create()
graph.schema().indexLabel(索引名).onE(边类型).by(属性名).search().ifNotExist().create()

// secondary():二级索引，可用于单个属性，也允许联合索引(前缀匹配)
// range():范围索引，仅适用于numeric或者date属性
// search():全文索引，仅适用于Text属性,可以用

/// 查询字符相等
graph.schema().indexLabel("personBySex").onV("人").by("sex").secondary().ifNotExist().create()
g.V().has('sex','man')

// 查询数字或者日期范围
graph.schema().indexLabel('personByAge').onV('人').by('age').range().ifNotExist().create()
g.V().has('age',26)
g.V().has('age',P.gt(26)) 

// 模糊查询
graph.schema().indexLabel("imsiByLocation").onV("IMSI").by("location").search().ifNotExist().create()
g.V().has('location', Text.contains('武汉'))
```

## 查询索引

```groovy
graph.schema().indexLabel('personByAge')
```

## 删除索引

```groovy
graph.schema().indexLabel('personByAge').remove()
```

# 5 has条件过滤

```dylan
hasLabel(labels…): object的label与labels列表中任何一个匹配就可以通过
hasId(ids…): object的id满足ids列表中的任何一个就可以通过
has(key): 包含键为key的属性的object通过，作用于顶点或者边
hasNot(key): 不包含键为key的属性的object通过，作用于顶点或者边
has(key, value): 包含属性“key=value”的object通过，作用于顶点或者边
has(label, key, value): 包含属性“key=value”且label值匹配的object通过，作用于顶点或者边
has(key, predicate): 包含键为key且对应的value满足predicate的object通过，作用于顶点或者边
hasKey(keys…): object的属性键包含所有的keys列表成员才能通过，作用于顶点属性
hasValue(values…): object的属性值包含所有的values列表成员才能通过，作用于顶点属性
```

##  根据"顶点类型"查询

```groovy
// 查询label为"person"的顶点
g.V().hasLabel('imsi')
g.V().hasLabel('imsi','zjhm')
```

##  根据"ID"查询

```groovy
// 查询id为"420822199610246423"的顶点
g.V().hasId('420822199610246423')
g.V().hasId('420822199610246423','420028198202056955')

g.V(id1).both().hasId(id2).hasNext()
```

##  根据"属性"查询

```groovy
g.V().has('age')
g.V().has('name')
g.V().has('vehicle_no')

g.V().hasNot('age')
g.V().hasNot('name')

g.V().properties().hasKey('sex')
g.V().properties().hasKey('age')
g.V().properties().hasKey('location')
```

## 根据"属性值"查询

```groovy
g.V().properties().hasValue(26)
g.V().properties().hasValue('武汉')
g.V().properties().hasValue(26,'man','武汉')
```

## 根据"属性、属性值"查询

```groovy
// 查询sex=man
g.V().has('sex','man')
```

##  根据"类型、属性、属性值"查询

```groovy
// 查询sex=man的'人'
g.V().has('person','sex','man')
g.V().has('person','age',P.gt(26)) 
g.V().has('zjhm','name', Text.contains('小意'))
```

## 根据"属性、表达式"查询

```groovy
g.V().has('age',P.gt(26))
g.V().has('location', Text.contains('武汉'))
```

# 6 查询结果约束

## 排序

```groovy
// 升序，默认排序方式；
order().by('',incr)
// 降序
order().by('',decr)
// 随机序
order().by('',shuffle)
```

## 计数

```groovy
g.V().count()
// 查询含有sex属性的顶点数量
g.V().has('sex').count()
// 查询张三的out邻接点数量
g.V('123456').out().count()
```

## 范围查询

下界以“0”作为第一个元素，上界为“-1”时表示取到最后的元素。

```groovy
g.V().range(8,12)
// 查询顶点'123456'的所有out邻接点
g.V('123456').out().range(0,-1)
// 查询顶点'7:123456'的out邻接点的第2个到第6个
g.V('123456').out().range(2,6)
// 查询顶点'7:123456'的out邻接点的第4个到最后
g.V('123456').out().range(4,-1)
```

## 查询前“n”个元素

```groovy
// 查询前3个，等价于g.V().range(0,3)
g.V().limit(3) 
// 查询顶点'7:123456'的前4个out邻接点
g.V('123456').out().limit(4)
```

## 查询最后’n‘个元素，与limit()相反

```groovy
// 查询后3个，等价于g.V().range(count-n,-1)
g.V().tail(3) 
g.V().range(16,-1) // count=19
```

## 跳过前“n”个元素，获取剩余的全部元素

```groovy
// skip(n)等价于range(n, -1)
g.V().skip(16)
```

# 7 路径查询

## 获取当前遍历过的所有路径，返回是路径

```groovy
// 查询张三的OUT邻接点中location='武汉'的路径
g.V('123456').out().has('location','武汉').path()
// 查询张三的OUT邻接点中sex='man'的路径
g.V('123456').out().has('sex','man').path()
```

## 查询不含有环路的对象，返回是对象

```groovy
// 查询张三的OUT邻接点中location='武汉'
g.V('123456').out().has('location','武汉').simplePath()
// 查询跟张三间隔3层OUT的顶点
g.V('123456').out().out().out().simplePath()
```

## 查询含有环路的对象，返回是对象

```groovy
g.V('123456').out().cyclicPath()
```

# 8 循环查询

```dylan
循环操作是指多次执行某一部分语句，用于语句需要重复运行的场景，比如“查找朋友的朋友的朋友”，可以直接使用循环操作来完成即“查找3层朋友”
repeat(): 指定要重复执行的语句，如repeat(out('friend'))
times(): 指定要重复执行的次数，如执行3次repeat(out('friend')).times(3)
until(): 指定循环终止的条件，如一直找到某个名字的朋友为止repeat(out('friend')).until(has('name','xiaofang'))
emit(): 指定循环语句的执行过程中收集数据的条件，每一步的结果只要符合条件则被收集，不指定条件时收集所有结果
loops(): 当前循环的次数，可用于控制最大循环次数等，如最多执行3次repeat(out('friend')).until(loops().is(3))
```

## 按照指定的次数重复执行语句

```groovy
g.V().repeat(out()).times(2)

// 先查询顶点'123456'的out邻接点（第一层）
// 再查询上一步结果顶点的out邻接点（第二层）
// 等价于 g.V('7:123456').out().out()
g.V('123456').repeat(out()).times(2)
g.V('420026198608052539').repeat(out()).times(108).path()
```

## 根据条件来重复执行语句

```groovy
// 查询顶点'7:123456'到顶点'李四'之间的路径
// 循环的终止条件是遇到'name'是'李四'的顶点
g.V('123456').repeat(out()).until(has('name','李四')).path()
// until()与 times()是互斥的，两个语句无法同时存在于同一个循环中。
// until()放在repeat()之前或之后的顺序是会影响逻辑的，放前面表示先判断再执行，放后面表示先执行后判断。
g.V('123456').until(has('name','李四')).repeat(out()).path()
```

## 收集执行过程中的数据

```groovy
// 查询顶点'123456'的所有OUT可达点的路径
g.V('123456').repeat(out()).emit().path()

// 查询顶点'123456'的所有OUT可达点的路径
// 且必须满足是'person'类型的点
g.V('123456').repeat(out()).emit(has('name', '王五')).path()

// 查询顶点'123456'到顶点'李四'之间的路径
// 此外还收集过程中的'person'类型的顶点
// emit()与until()搭配使用时，是“或”的关系而不是“与”的关系，满足两者间任意一个即可。
g.V('123456').repeat(out()).until(has('name', '王五')).emit(has('name', '赵六')).path()
```

## 根据最大次数限制来重复执行语句

```groovy
// 查询顶点'张三'的3层OUT可达点路径
g.V('123456').repeat(out()).until(loops().is(3)).path()

// 查询顶点'张三'到顶点'王五'之间的路径且之间只相差2跳的距离
g.V('123456').repeat(out()).until(has('vehicle_no', '鄂A5R4T4').and().loops().is(3)).path()
g.V('123456').repeat(out()).until(has('vehicle_no', '鄂A5R4T4').and().loops().is(2)).path()
```

# 9 分组和去重

```dylan
group(): 对结果集进行分组，可通过by(property)来指定根据什么维度进行分组，可称维度为分组键；如果不指定维度则以元素id作为分组键，相当于重复的元素被分为一组。每一组由分组键+组内元素列表构成。如果有需要也可对每一组的元素列表进行reduce操作，依然使用by()语句，如by(count())对组内元素计数。
groupCount(): 对结果集进行分组，并统计每一组的元素个数。每一组由分组键+组内元素数量构成。
dedup(): 去除结果集中相同的元素，可通过by(property)来指定根据什么维度进行去重。
by(): 语义上一般指“根据什么维度”，与上述语句配合使用，如group().by()、dedup().by()等
```

## 分组

```groovy
// 直接分组
g.V().hasLabel('person').group()
// 将person类型的顶点按sex进行分组
g.V().hasLabel('person').group().by('sex')

```

## 分组计数

```groovy
// 直接分组计数
g.V().hasLabel('person').groupCount()

// 将person类型的顶点按sex进行分组计数，即各个年龄有多少人
// 等同于 g.V().hasLabel('person').group().by('sex').by(count())
g.V().hasLabel('person').groupCount().by('sex')
```

## 去重

```groovy
// 对person类型的顶点去重
g.V().both().hasLabel('person').dedup()
// 对person类型的顶点的sex去重
g.V().hasLabel('person').values('sex').dedup()
```

## 组合使用

```groovy
// 根据地域分组，并得到各个组的年龄
g.V().hasLabel('person').group().by('address').by(values('age'))
// 根据地域分组，并得到各个组的平均年龄
g.V().hasLabel('person').group().by('address').by(values('age').mean())

// 拥有相同数量边的顶点作为一组
// 并获取每一组的顶点数量
// 结果相当于：拥有m条边的顶点有n个
g.V().groupCount().by(bothE().count())
```

# 10 条件过滤

## predicate条件表达式

| Predicate         | Description           |
| ----------------- | --------------------- |
| eq(object)        | 等于                  |
| neq(object)       | 不等于                |
| lt(number)        | 小于                  |
| lte(number)       | 小于或者等于          |
| gt(number)        | 大于                  |
| gte(number)       | 大于或者等于          |
| inside(low,high)  | 大于low且小于high     |
| outside(low,high) | 小于low或者大于high   |
| between(low,high) | 大于等于low且小于high |
| within(objects…)  | 包含                  |
| without(objects…) | 不包含                |

```groovy
// predicate使用test()进行测试
eq(2).test(3)  							//true
within('a','b','c').test('d') 			//false

// and()连接的predicate
within(1,2,3).and(not(eq(2))).test(3)	//true

// or()连接的predicate
inside(1,4).or(eq(5)).test(3)			//true

// not()作用于neq()，等价于eq()
not(neq(2)).test(2)					    //true
```

## where()

`where()`常与`select()`或者`match()`配合使用，也可以单独使用。

`where()`有以下使用方式：

- where(predicate)方式
- where(String, predicate)方式
- where(Traversal)方式
- select().where()方式
- match().where()方式

```groovy
//where(predicate)方式
// 找123456的兄弟姐妹
g.V('123456').as('a').out('relative').in('relative').where(neq('a')).dedup()

//where(String, predicate)方式 
// 找123456的兄弟姐妹
g.V('123456').as('a').out('relative').in('relative').as('b').where('a',neq('b')).dedup()

//where(Traversal)方式
// 
g.V('7123456').out('relative').in('relative').where(out('relative').count().is(gt(1))).dedup()

// select().where()方式
// 查看“张三”的同辈，并将“张三”及同辈的名字以map输出
g.V('7:123456').as('a').out('relative').in('relative').as('b').select('a','b').by('name').where('a',neq('b'))

// match().where()方式
// 查看“张三”的同辈，并将“张三”及同辈的名字以map输出
g.V('7:123456').match(__.as('a').out('relative').as('b'),__.as('b').in('relative').as('c')).where('a', neq('c')).select('a','c').by('name')
```

## filter()

`filter()`有三种用法：

- lambda方式，filter{it.get()…}
- Traversal方式，filter(Traversal)
- 特定filter step方式

```groovy
// 1、lambda方式，filter{it.get()…}
g.V().filter{it.get().label() == 'person'}

// 2、Traversal方式，filter(Traversal)
g.V().filter(label().is('person'))

// 3、特定filter step方式
g.V().hasLabel('person')
```

#   11 统计

```groovy
sum()：将 traversal 中的所有的数字求和；
max()：对 traversal 中的所有的数字求最大值；
min()：对 traversal 中的所有的数字求最小值；
mean()：将 traversal 中的所有的数字求均值；
count()：统计 traversal 中 item 总数。
```

# 12 分支

```groovy
// 查找所有的“person”类型的顶点
// 如果“age”属性等于0，输出名字
// 如果“age”属性等于28，输出年龄
// 如果“age”属性等于29，输出他开发的软件的名字
// choose(predicate).option().option()...
g.V().hasLabel('person')
.choose(values('age'))
.option(0, values('name'))
.option(28, values('age'))
.option(29, __.out('created').values('name'))
.option(none, values('name'))
```

