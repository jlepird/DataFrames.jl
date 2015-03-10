



# Reshaping




## stack

Stacks a DataFrame; convert from a wide to long format


```julia
stack(df::AbstractDataFrame, measure_vars, id_vars)
stack(df::AbstractDataFrame, measure_vars)
stack(df::AbstractDataFrame)
melt(df::AbstractDataFrame, id_vars, measure_vars)
melt(df::AbstractDataFrame, id_vars)
```

### Arguments

* `df` : the AbstractDataFrame to be stacked

* `measure_vars` : the columns to be stacked (the measurement
  variables), a normal column indexing type, like a Symbol,
  Vector{Symbol}, Int, etc.; for `melt`, defaults to all
  variables that are not `id_vars`

* `id_vars` : the identifier columns that are repeated during
  stacking, a normal column indexing type; for `stack` defaults to all
  variables that are not `measure_vars`

If neither `measure_vars` or `id_vars` are given, `measure_vars`
defaults to all floating point columns.

### Result

* `::DataFrame` : the long-format dataframe with column `:value`
  holding the values of the stacked columns (`measure_vars`), with
  column `:variable` a Vector of Symbols with the `measure_vars` name,
  and with columns for each of the `id_vars`.

See also `stackdf` and `meltdf` for stacking methods that return a
view into the original DataFrame. See `unstack` for converting from
long to wide format.


### Examples

```julia
d1 = DataFrame(a = repeat([1:3;], inner = [4]),
               b = repeat([1:4;], inner = [3]),
               c = randn(12),
               d = randn(12),
               e = map(string, 'a':'l'))

d1s = stack(d1, [:c, :d])
d1s2 = stack(d1, [:c, :d], [:a])
d1m = melt(d1, [:a, :b, :e])
```


[DataFrames/src/abstractdataframe/reshape.jl:75](https://github.com/JuliaStats/DataFrames.jl/tree/3320b2b7d160be66bb6a4bd3f95f78b8be6e0d23/src/abstractdataframe/reshape.jl#L75)



## melt

Stacks a DataFrame; convert from a wide to long format; see
`stack`.

[DataFrames/src/abstractdataframe/reshape.jl:109](https://github.com/JuliaStats/DataFrames.jl/tree/3320b2b7d160be66bb6a4bd3f95f78b8be6e0d23/src/abstractdataframe/reshape.jl#L109)



## unstack

Unstacks a DataFrame; convert from a long to wide format

```julia
unstack(df::AbstractDataFrame, rowkey, colkey, value)
unstack(df::AbstractDataFrame, colkey, value)
unstack(df::AbstractDataFrame)
```

### Arguments

* `df` : the AbstractDataFrame to be unstacked

* `rowkey` : the column with a unique key for each row, if not given,
  find a key by grouping on anything not a `colkey` or `value`

* `colkey` : the column holding the column names in wide format,
  defaults to `:variable`

* `value` : the value column, defaults to `:value`

### Result

* `::DataFrame` : the wide-format dataframe


### Examples

```julia
wide = DataFrame(id = 1:12,
                 a  = repeat([1:3;], inner = [4]),
                 b  = repeat([1:4;], inner = [3]),
                 c  = randn(12),
                 d  = randn(12))

long = stack(wide)
wide0 = unstack(long)
wide1 = unstack(long, :variable, :value)
wide2 = unstack(long, :id, :variable, :value)
```
Note that there are some differences between the widened results above.


[DataFrames/src/abstractdataframe/reshape.jl:166](https://github.com/JuliaStats/DataFrames.jl/tree/3320b2b7d160be66bb6a4bd3f95f78b8be6e0d23/src/abstractdataframe/reshape.jl#L166)



## DataFrames.StackedVector

An AbstractVector{Any} that is a linear, concatenated view into
another set of AbstractVectors

NOTE: Not exported.

### Constructor

```julia
RepeatedVector(d::AbstractVector...)
```

### Arguments

* `d...` : one or more AbstractVectors

### Examples

```julia
StackedVector(Any[[1,2], [9,10], [11,12]])  # [1,2,9,10,11,12]
```


[DataFrames/src/abstractdataframe/reshape.jl:279](https://github.com/JuliaStats/DataFrames.jl/tree/3320b2b7d160be66bb6a4bd3f95f78b8be6e0d23/src/abstractdataframe/reshape.jl#L279)



## DataFrames.RepeatedVector{T}

An AbstractVector that is a view into another AbstractVector with
repeated elements

NOTE: Not exported.

### Constructor

```julia
RepeatedVector(parent::AbstractVector, inner::Int, outer::Int)
```

### Arguments

* `parent` : the AbstractVector that's repeated
* `inner` : the numer of times each element is repeated
* `outer` : the numer of times the whole vector is repeated after
  expanded by `inner`

`inner` and `outer` have the same meaning as similarly named arguments
to `repeat`.

### Examples

```julia
RepeatedVector([1,2], 3, 1)   # [1,1,1,2,2,2]
RepeatedVector([1,2], 1, 3)   # [1,2,1,2,1,2]
RepeatedVector([1,2], 2, 2)   # [1,2,1,2,1,2,1,2]
```


[DataFrames/src/abstractdataframe/reshape.jl:345](https://github.com/JuliaStats/DataFrames.jl/tree/3320b2b7d160be66bb6a4bd3f95f78b8be6e0d23/src/abstractdataframe/reshape.jl#L345)



## stackdf

A stacked view of a DataFrame (long format)

Like `stack` and `melt`, but a view is returned rather than data
copies.

```julia
stackdf(df::AbstractDataFrame, measure_vars, id_vars)
stackdf(df::AbstractDataFrame, measure_vars)
meltdf(df::AbstractDataFrame, id_vars, measure_vars)
meltdf(df::AbstractDataFrame, id_vars)
```

### Arguments

* `df` : the wide AbstractDataFrame

* `measure_vars` : the columns to be stacked (the measurement
  variables), a normal column indexing type, like a Symbol,
  Vector{Symbol}, Int, etc.; for `melt`, defaults to all
  variables that are not `id_vars`

* `id_vars` : the identifier columns that are repeated during
  stacking, a normal column indexing type; for `stack` defaults to all
  variables that are not `measure_vars`

### Result

* `::DataFrame` : the long-format dataframe with column `:value`
  holding the values of the stacked columns (`measure_vars`), with
  column `:variable` a Vector of Symbols with the `measure_vars` name,
  and with columns for each of the `id_vars`.

The result is a view because the columns are special AbstractVectors
that return indexed views into the original DataFrame.

### Examples

```julia
d1 = DataFrame(a = repeat([1:3;], inner = [4]),
               b = repeat([1:4;], inner = [3]),
               c = randn(12),
               d = randn(12),
               e = map(string, 'a':'l'))

d1s = stackdf(d1, [:c, :d])
d1s2 = stackdf(d1, [:c, :d], [:a])
d1m = meltdf(d1, [:a, :b, :e])
```


[DataFrames/src/abstractdataframe/reshape.jl:436](https://github.com/JuliaStats/DataFrames.jl/tree/3320b2b7d160be66bb6a4bd3f95f78b8be6e0d23/src/abstractdataframe/reshape.jl#L436)

