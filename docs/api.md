# API

## Endpoints

### `GET /metrics/`

Lists the IDs, name, and descriptions of all metrics.

Example output:

```
{
  "metrics": [
    {
      "id": 1,
      "name": "CPU Architecture",
      "description": "The architecture of the client machine"
      "tooltip": "{y}% of all {pop} records have {x} architecture",
      "type": "categorical"
    },
    {
      "id": 2,
      "name": "CPU Count",
      "description": "The number of CPUs available"
      "tooltip": "{p}% of all {pop} records have {x} or more CPUs",
      "type": "numerical"
    },
    ...
  ]
}
```


### `GET /metric/[metric-id]/`

Returns data for a specific metric, grouped by the population, if provided.

Query parameters:

* `exp` (optional): This string will be used to find the experiment. If none
  provided this will default to the most recently added experiment.
* `pop` (optional): A comma separated list of populations. If none provided,
  this will default to "All".

Example output:

```
{
  "id": 1,
  "dataSet": "Experiment A",
  "type": "categorical",
  "name": "CPU Architecture",
  "populations": [
    {
      "name": "All",
      "numObs": 5321560,
      "points": [
        {
          "p": 0.932504,
          "refRank": 1,
          "b": "x86",
          "c": 0.932504
        },
        {
          "p": 0.0672972,
          "refRank": 2,
          "b": "x86-64",
          "c": 0.999801
        },
        {
          "p": 0.00015503,
          "refRank": 3,
          "b": "missing",
          "c": 0.999956
        },
        {
          "p": 0.0000362676,
          "refRank": 4,
          "b": "arm",
          "c": 0.999992
        },
        {
          "p": 0.00000357038,
          "refRank": 5,
          "b": "sparc",
          "c": 0.999996
        },
        {
          "p": 0.00000432204,
          "refRank": 6,
          "b": "ppc",
          "c": 1
        }
      ]
    }
  ]
}
```

#### Field descriptions

<dl>
  <dt>dataSet</dt>
  <dd>
    All metrics are connected to a <code>DataSet</code> which is the experiment
    from which the data was created and imported.
  </dd>

  <dt>populations.name</dt>
  <dd>
    The population this data set applies to. Besides "All", some examples
    include: "control" or "group-a".
  </dd>

  <dt>type</dt>
  <dd>
    One of <code>categorical</code> or <code>numerical</code>. When the type is
    <code>categorical</code>, <code>points.b</code> will be a string containing
    a word like <i>arm</i> or <i>sparc</i>. When the type is
    <code>numerical</code>, <code>points.b</code> will be a string containing a
    floating-point number.
  </dd>

  <dt>points</dt>
  <dd>
    <dt>b</dt>
    <dd>
      The bin to which a given amount of weight is assigned
    </dd>

    <dt>p</dt>
    <dd>
      The proportion of weight assigned to that bin (all <code>p</code>s should
      sum to 1)
    </dd>

    <dt>c</dt>
    <dd>
      The cumulative amount of weight if the bins are sorted in
      <code>refRank</code> order
    </dd>

    <dt>refRank</dt>
    <dd>
      The sort order of the bin from the reference population ordered from most
      weight to least weight. This is needed because if you display more than one
      population, the proportion of records bins will be be different, so you need
      a stable ranking to display them side-by-side.
    </dd>
  </dd>
</dl>

#### Other notes

* Data points are guaranteed to be in order, such that the `c` and the `b` (when
  the data is numerical) of an object are always higher than those of the object
  before it.
