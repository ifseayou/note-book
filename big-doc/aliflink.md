# aliflink

## hologres 结果表

流式语义，根据Hologres Sink的配置和Hologres表的属性，流式语义分为以下两种：

- **Exactly-once**（仅一次）：即使在发生各种故障的情况下，系统只处理一次数据或事件。
- **At-least-once**（至少一次）：如果在系统完全处理之前丢失了数据或事件，则从源头重新传输，因此可以多次处理数据或事件。如果第一次重试成功，则不必进行后续重试。

在Hologres结果表中使用流式语义，需要注意以下几点：

> Flink定义的结果表中的数据列数不一定要和Hologres物理表的列数一致,需要保证缺失的列没有非空约束，即列值可以为Null，否则会报错。

- 如果Hologres物理表未设置主键，则Hologres Sink使用**At-least-once**语义。

- 如果Hologres物理表已设置主键，则Hologres Sink通过主键确保，Exactly-once语义。当同主键数据出现多次时，需要设置**mutatetype**参数确定更新结果表的方式，mutatetype 取值如下：

  - **insertorignore**（默认值）：保留首次出现的数据，忽略后续所有数据。

  - **insertorreplace**：整行替换已有数据。

  - **insertorupdate**：替换部分已有数据。例如一张表有a、b、c和d四个字段，a 是PK（Primary Key），写入Hologres时只写入a和b两个字段，在PK重复的情况下，系统只会更新 b 字段，c 和d保持不变。

    > 在 mutatetype = insertorupdate 的情况下，
    >
    > * ignoreNullWhenUpdate = false (默认) ： 将null值 写入到 Hologres 结果表 
    > * ignoreNullWhenUpdate = ture ： 忽略更新写入数据中的null值 。（假设hologres结果表有[a,b,c,d] 4个字段，a字段为主键，hologres 结果表 有 {a1,b1,null,d1} 记录, 新记录为 {a1,b1,c1,null}，最终 hologres 表记录为{a1,b1,c1,d1}  ）

