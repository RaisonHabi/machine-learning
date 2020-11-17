## shift()
### 函数原型：
DataFrame.shift(periods=1, freq=None, axis=0)
### 参数
```
periods：类型为int，表示移动的幅度，可以是正数，也可以是负数，默认值是1,
1就表示移动一次，注意这里移动的都是数据，而索引是不移动的，移动之后没有对应值的，就赋值为NaN。

freq： DateOffset, timedelta, or time rule string，可选参数，默认值为None，
只适用于时间序列，如果这个参数存在，那么会按照参数值移动时间索引，而数据值没有发生变化。例如现在有df1如下：

axis：{0, 1, ‘index’, ‘columns’}，表示移动的方向，如果是0或者’index’表示上下移动，如果是1或者’columns’，则会左右移动。
```
### 示例
<p>shift函数是对数据进行移动的操作，假如现在有一个DataFrame数据df，如下所示：</p>
<table>
<thead>
<tr><th>index</th><th>value1</th></tr>
</thead>
<tbody>
<tr>
<td>A</td>
<td>0</td>
</tr>
<tr>
<td>B</td>
<td>1</td>
</tr>
<tr>
<td>C</td>
<td>2</td>
</tr>
<tr>
<td>D</td>
<td>3</td>
</tr>
</tbody>
</table>
<p>那么如果执行以下代码：</p>
<pre><code class="hljs language-python">df.shift()</code></pre>
<p>就会变成如下：</p>
<table>
<thead>
<tr><th>index</th><th>value1</th></tr>
</thead>
<tbody>
<tr>
<td>A</td>
<td>NaN</td>
</tr>
<tr>
<td>B</td>
<td>0</td>
</tr>
<tr>
<td>C</td>
<td>1</td>
</tr>
<tr>
<td>D</td>
<td>2</td>
</tr>
</tbody>
</table>

&nbsp;
## reference
[pandas常用函数之shift](https://www.cnblogs.com/iamxyq/p/6283334.html)
