# Query API


* [getAPIResults](QueryAPI.md#function-getapiresults)
* [getSummaryTimers](QueryAPI.md#function-getsummarytimers)
* [getPageGroupTimers](QueryAPI.md#function-getpagegrouptimers)
* [getBrowserTimers](QueryAPI.md#function-getbrowsertimers)
* [getABTestTimers](QueryAPI.md#function-getabtesttimers)
* [getGeoTimers](QueryAPI.md#function-getgeotimers)
* [getMetricsByDimension](QueryAPI.md#function-getmetricsbydimension)
* [getTimersMetrics](QueryAPI.md#function-gettimersmetrics)
* [getHistogram](QueryAPI.md#function-gethistogram)
* [getSessionsOverPageLoadTime](QueryAPI.md#function-getsessionsoverpageloadtime)
* [getMetricOverPageLoadTime](QueryAPI.md#function-getmetricoverpageloadtime)
* [getTimerByMinute](QueryAPI.md#function-gettimerbyminute)
* [mergeMetrics](QueryAPI.md#function-mergemetrics)
## Namespaced Functions
!!! note
    The following methods are not exported by default. You may use them by explicitly
    importing them or by prefixing them with the `mPulseAPI.` namespace.


### function `getAPIResults`
[QueryAPI.jl#67-177](https://github.com/akamai/mPulseAPI.jl/tree/master/src/QueryAPI.jl#L67-L177){: .source-link}

Get API results from the mPulse [Query API](http://docs.soasta.com/query-api/)

This method is a generic catch-all that queries the mPulse API and returns results as a Julia data structure matching the JSON structure of the specified API call

#### Arguments
`token::AbstractString`
:    The Repository authentication token fetched by calling [`mPulseAPI.getRepositoryToken`](RepositoryAPI.md#function-getrepositorytoken){: .x-ref}

`appKey::AbstractString`
:    The App Key (formerly known as API key) for the app to query.  If you don't know the App Key, use
     [`mPulseAPI.getRepositoryDomain`](RepositoryAPI.md#function-getrepositorydomain){: .x-ref} to fetch a domain and then inspect `domain["attributes"]["appKey"]`

`query_type::AbstractString`
:    The specific API query to make.  Must be one of the following:

    * summary
    * histogram
    * sessions-per-page-load-time
    * metric-per-page-load-time
    * by-minute
    * geography
    * page-groups
    * browsers
    * bandwidth
    * ab-tests
    * timers-metrics
    * metrics-by-dimension


#### Optional Arguments
`filters::Dict`
:    A dict of filters to pass to the mPulse Query API. For example `Dict("page-group" => "foo-bar")`
     will filter results to the `foo-bar` `page-group`.  The resulting filters will be a merge of
     what is passed in and the default values with whatever is passed in taking precedence.

     The default filters are:

         Dict(
             "date-comparator" => "Last24Hours",
         )

     The `date-start` and `date-end` filters accept a `DateTime` object while the `date` filter
     accepts a `Date` object.

     If you'd like to use a `ZonedDateTime`, pass in its `utc_datetime` field:

         filters = Dict(
             "date-comparator" => "Between",
             "date-start"      => ZonedDateTime(2016, 10, 19, 4, 30, TimeZone("America/New_York")).utc_datetime,
             "date-end"        => DateTime(2016, 10, 19, 16, 30)
         )

     To pass multiple values for a single filter, use an array:

         filters = Dict(
             "beacon-type" => ["page view", "xhr", "spa", "spa_hard"],
             "page-group"  => ["product", "search"]
         )

#### Throws
`ArgumentError`
:   If the `query_type` is not recognized

[`mPulseAPIAuthException`](exceptions.md#datatype-mpulseapiauthexception)
:   If the `token` is invalid or has expired.

[`mPulseAPIException`](exceptions.md#datatype-mpulseapiexception)
:   If the API returned a non-200 status.  Inspect `mPulseAPIException.response` for details about
    the problem

[`mPulseAPIRequestException`](exceptions.md#datatype-mpulseapirequestexception)
:   If the API was unhappy with a request parameter

#### Returns
`{Any}` A Julia representation of the JSON returned by the API call. Convenience wrappers in this library may return more appropriate data structures.

---

### function `getSummaryTimers`
[QueryAPI.jl#226-228](https://github.com/akamai/mPulseAPI.jl/tree/master/src/QueryAPI.jl#L226-L228){: .source-link}

Calls the `summary` endpoint of the mPulse REST API with the passed in filters

#### Arguments
`token::AbstractString`
:    The Repository authentication token fetched by calling [`mPulseAPI.getRepositoryToken`](RepositoryAPI.md#function-getrepositorytoken){: .x-ref}

`appKey::AbstractString`
:    The App Key (formerly known as API key) for the app to query.  If you don't know the App Key, use
     [`mPulseAPI.getRepositoryDomain`](RepositoryAPI.md#function-getrepositorydomain){: .x-ref} to fetch a domain and then inspect `domain["attributes"]["appKey"]`

#### Optional Arguments
`filters::Dict`
:    A dict of filters to pass to the mPulse Query API. For example `Dict("page-group" => "foo-bar")`
     will filter results to the `foo-bar` `page-group`.  The resulting filters will be a merge of
     what is passed in and the default values with whatever is passed in taking precedence.

     The default filters are:

         Dict(
             "date-comparator" => "Last24Hours",
         )

     The `date-start` and `date-end` filters accept a `DateTime` object while the `date` filter
     accepts a `Date` object.

     If you'd like to use a `ZonedDateTime`, pass in its `utc_datetime` field:

         filters = Dict(
             "date-comparator" => "Between",
             "date-start"      => ZonedDateTime(2016, 10, 19, 4, 30, TimeZone("America/New_York")).utc_datetime,
             "date-end"        => DateTime(2016, 10, 19, 16, 30)
         )

     To pass multiple values for a single filter, use an array:

         filters = Dict(
             "beacon-type" => ["page view", "xhr", "spa", "spa_hard"],
             "page-group"  => ["product", "search"]
         )

#### Throws
[`mPulseAPIAuthException`](exceptions.md#datatype-mpulseapiauthexception)
:   If the `token` is invalid or has expired.

[`mPulseAPIException`](exceptions.md#datatype-mpulseapiexception)
:   If the API returned a non-200 status.  Inspect `mPulseAPIException.response` for details about
    the problem

[`mPulseAPIRequestException`](exceptions.md#datatype-mpulseapirequestexception)
:   If the API was unhappy with a request parameter

#### Returns
`{Dict}` A Julia `Dict` with the following string keys:

`n::Int`
:   The number of beacons with data about the requested timer

`median::Int`
:   The median of the requested timer in milliseconds

`p95::Int`
:   The 95th percentile value of the requested timer in milliseconds

`p98::Int`
:   The 98th percentile value of the requested timer in milliseconds

`moe::Float`
:   The 95% confidence interval margin of error on the arithmetic mean of the requested timer in milliseconds


#### Examples

```julia
julia> summary = mPulseAPI.getSummaryTimers(token, appKey)

Dict{Any,Any} with 5 entries:
  "n"      => 356317
  "median" => 3094
  "p95"    => 19700
  "p98"    => 40678
  "moe"    => 13.93
```

---

### function `getPageGroupTimers`
[QueryAPI.jl#252-266](https://github.com/akamai/mPulseAPI.jl/tree/master/src/QueryAPI.jl#L252-L266){: .source-link}

Calls the `page-groups` endpoint of the mPulse REST API with the passed in filters

#### Arguments
`token::AbstractString`
:    The Repository authentication token fetched by calling [`mPulseAPI.getRepositoryToken`](RepositoryAPI.md#function-getrepositorytoken){: .x-ref}

`appKey::AbstractString`
:    The App Key (formerly known as API key) for the app to query.  If you don't know the App Key, use
     [`mPulseAPI.getRepositoryDomain`](RepositoryAPI.md#function-getrepositorydomain){: .x-ref} to fetch a domain and then inspect `domain["attributes"]["appKey"]`

#### Optional Arguments
`filters::Dict`
:    A dict of filters to pass to the mPulse Query API. For example `Dict("page-group" => "foo-bar")`
     will filter results to the `foo-bar` `page-group`.  The resulting filters will be a merge of
     what is passed in and the default values with whatever is passed in taking precedence.

     The default filters are:

         Dict(
             "date-comparator" => "Last24Hours",
         )

     The `date-start` and `date-end` filters accept a `DateTime` object while the `date` filter
     accepts a `Date` object.

     If you'd like to use a `ZonedDateTime`, pass in its `utc_datetime` field:

         filters = Dict(
             "date-comparator" => "Between",
             "date-start"      => ZonedDateTime(2016, 10, 19, 4, 30, TimeZone("America/New_York")).utc_datetime,
             "date-end"        => DateTime(2016, 10, 19, 16, 30)
         )

     To pass multiple values for a single filter, use an array:

         filters = Dict(
             "beacon-type" => ["page view", "xhr", "spa", "spa_hard"],
             "page-group"  => ["product", "search"]
         )

`friendly_names::Bool=false`
:    Specify whether column names in the `DataFrame` should be human friendly or developer friendly (default)

     Human friendly column names are:

     `Page Group`, `Median Time (ms)`, `MoE (ms)`, `Measurements`, `% of total`

     Developer friendly column names are:

     `:page_group`, `:t_done_median`, `:t_done_moe`, `:t_done_count`, `:t_done_total_pc`



#### Throws
[`mPulseAPIAuthException`](exceptions.md#datatype-mpulseapiauthexception)
:   If the `token` is invalid or has expired.

[`mPulseAPIException`](exceptions.md#datatype-mpulseapiexception)
:   If the API returned a non-200 status.  Inspect `mPulseAPIException.response` for details about
    the problem

[`mPulseAPIRequestException`](exceptions.md#datatype-mpulseapirequestexception)
:   If the API was unhappy with a request parameter

[`mPulseAPIResultFormatException`](exceptions.md#datatype-mpulseapiresultformatexception)
:    If the API response had  data in an unexpected data type

#### Returns
`{DataFrame}` A Julia `DataFrame` with the following columns:

`page_group`, `t_done_median`, `t_done_moe`, `t_done_count`, and `t_done_total_pc`

```julia
julia> pgroups = mPulseAPI.getPageGroupTimers(token, appKey)
69x5 DataFrames.DataFrame
| Row | page_group                  | t_done_median    | t_done_moe | t_done_count | t_done_total_pc |
|-----|-----------------------------|------------------|------------|--------------|-----------------|
| 1   | www                         | 3090             | 40.6601    | 49904        | 46.3069         |
| 2   | blog                        | 2557             | 51.7651    | 17779        | 16.4975         |
| 3   | Search                      | 4587             | 88.988     | 7248         | 6.72556         |
| 4   | SKU                         | 3463             | 120.895    | 6885         | 6.38872         |
| 5   | PLU                         | 3276             | 116.507    | 6688         | 6.20592         |
| 6   | (No Page Group)             | 3292             | 165.514    | 2949         | 2.73643         |
| 7   | Checkout                    | 2875             | 169.091    | 2386         | 2.21402         |

```

---

### function `getBrowserTimers`
[QueryAPI.jl#290-304](https://github.com/akamai/mPulseAPI.jl/tree/master/src/QueryAPI.jl#L290-L304){: .source-link}

Calls the `browsers` endpoint of the mPulse REST API with the passed in filters

#### Arguments
`token::AbstractString`
:    The Repository authentication token fetched by calling [`mPulseAPI.getRepositoryToken`](RepositoryAPI.md#function-getrepositorytoken){: .x-ref}

`appKey::AbstractString`
:    The App Key (formerly known as API key) for the app to query.  If you don't know the App Key, use
     [`mPulseAPI.getRepositoryDomain`](RepositoryAPI.md#function-getrepositorydomain){: .x-ref} to fetch a domain and then inspect `domain["attributes"]["appKey"]`

#### Optional Arguments
`filters::Dict`
:    A dict of filters to pass to the mPulse Query API. For example `Dict("page-group" => "foo-bar")`
     will filter results to the `foo-bar` `page-group`.  The resulting filters will be a merge of
     what is passed in and the default values with whatever is passed in taking precedence.

     The default filters are:

         Dict(
             "date-comparator" => "Last24Hours",
         )

     The `date-start` and `date-end` filters accept a `DateTime` object while the `date` filter
     accepts a `Date` object.

     If you'd like to use a `ZonedDateTime`, pass in its `utc_datetime` field:

         filters = Dict(
             "date-comparator" => "Between",
             "date-start"      => ZonedDateTime(2016, 10, 19, 4, 30, TimeZone("America/New_York")).utc_datetime,
             "date-end"        => DateTime(2016, 10, 19, 16, 30)
         )

     To pass multiple values for a single filter, use an array:

         filters = Dict(
             "beacon-type" => ["page view", "xhr", "spa", "spa_hard"],
             "page-group"  => ["product", "search"]
         )

`friendly_names::Bool=false`
:    Specify whether column names in the `DataFrame` should be human friendly or developer friendly (default)

     Human friendly column names are:

     `User Agent`, `Median Time (ms)`, `MoE (ms)`, `Measurements`, `% of total`

     Developer friendly column names are:

     `:user_agent`, `:t_done_median`, `:t_done_moe`, `:t_done_count`, `:t_done_total_pc`



#### Throws
[`mPulseAPIAuthException`](exceptions.md#datatype-mpulseapiauthexception)
:   If the `token` is invalid or has expired.

[`mPulseAPIException`](exceptions.md#datatype-mpulseapiexception)
:   If the API returned a non-200 status.  Inspect `mPulseAPIException.response` for details about
    the problem

[`mPulseAPIRequestException`](exceptions.md#datatype-mpulseapirequestexception)
:   If the API was unhappy with a request parameter

[`mPulseAPIResultFormatException`](exceptions.md#datatype-mpulseapiresultformatexception)
:    If the API response had  data in an unexpected data type

#### Returns
`{DataFrame}` A Julia `DataFrame` with the following columns:

`user_agent`, `t_done_median`, `t_done_moe`, `t_done_count`, and `t_done_total_pc`

```julia
julia> pgroups = mPulseAPI.getBrowserTimers(token, appKey)
69x5 DataFrames.DataFrame
| Row | user_agent                  | t_done_median    | t_done_moe | t_done_count | t_done_total_pc |
|-----|-----------------------------|------------------|------------|--------------|-----------------|
| 1   | Chrome/50                   | 3090             | 40.6601    | 49904        | 46.3069         |
| 2   | Safari/9                    | 2557             | 51.7651    | 17779        | 16.4975         |
| 3   | Mobile Safari/9             | 4587             | 88.988     | 7248         | 6.72556         |
| 4   | Firefox/46                  | 3463             | 120.895    | 6885         | 6.38872         |
| 5   | Chrome/49                   | 3276             | 116.507    | 6688         | 6.20592         |
| 6   | IE/11                       | 3292             | 165.514    | 2949         | 2.73643         |
| 7   | Edge/13                     | 2875             | 169.091    | 2386         | 2.21402         |

```

---

### function `getABTestTimers`
[QueryAPI.jl#328-342](https://github.com/akamai/mPulseAPI.jl/tree/master/src/QueryAPI.jl#L328-L342){: .source-link}

Calls the `ab-tests` endpoint of the mPulse REST API with the passed in filters

#### Arguments
`token::AbstractString`
:    The Repository authentication token fetched by calling [`mPulseAPI.getRepositoryToken`](RepositoryAPI.md#function-getrepositorytoken){: .x-ref}

`appKey::AbstractString`
:    The App Key (formerly known as API key) for the app to query.  If you don't know the App Key, use
     [`mPulseAPI.getRepositoryDomain`](RepositoryAPI.md#function-getrepositorydomain){: .x-ref} to fetch a domain and then inspect `domain["attributes"]["appKey"]`

#### Optional Arguments
`filters::Dict`
:    A dict of filters to pass to the mPulse Query API. For example `Dict("page-group" => "foo-bar")`
     will filter results to the `foo-bar` `page-group`.  The resulting filters will be a merge of
     what is passed in and the default values with whatever is passed in taking precedence.

     The default filters are:

         Dict(
             "date-comparator" => "Last24Hours",
         )

     The `date-start` and `date-end` filters accept a `DateTime` object while the `date` filter
     accepts a `Date` object.

     If you'd like to use a `ZonedDateTime`, pass in its `utc_datetime` field:

         filters = Dict(
             "date-comparator" => "Between",
             "date-start"      => ZonedDateTime(2016, 10, 19, 4, 30, TimeZone("America/New_York")).utc_datetime,
             "date-end"        => DateTime(2016, 10, 19, 16, 30)
         )

     To pass multiple values for a single filter, use an array:

         filters = Dict(
             "beacon-type" => ["page view", "xhr", "spa", "spa_hard"],
             "page-group"  => ["product", "search"]
         )

`friendly_names::Bool=false`
:    Specify whether column names in the `DataFrame` should be human friendly or developer friendly (default)

     Human friendly column names are:

     `Test Name`, `Median Time (ms)`, `MoE (ms)`, `Measurements`, `% of total`

     Developer friendly column names are:

     `:test_name`, `:t_done_median`, `:t_done_moe`, `:t_done_count`, `:t_done_total_pc`



#### Throws
[`mPulseAPIAuthException`](exceptions.md#datatype-mpulseapiauthexception)
:   If the `token` is invalid or has expired.

[`mPulseAPIException`](exceptions.md#datatype-mpulseapiexception)
:   If the API returned a non-200 status.  Inspect `mPulseAPIException.response` for details about
    the problem

[`mPulseAPIRequestException`](exceptions.md#datatype-mpulseapirequestexception)
:   If the API was unhappy with a request parameter

[`mPulseAPIResultFormatException`](exceptions.md#datatype-mpulseapiresultformatexception)
:    If the API response had  data in an unexpected data type

#### Returns
`{DataFrame}` A Julia `DataFrame` with the following columns:

`test_name`, `t_done_median`, `t_done_moe`, `t_done_count`, and `t_done_total_pc`

```julia
julia> pgroups = mPulseAPI.getABTestTimers(token, appKey)
69x5 DataFrames.DataFrame
| Row | test_name                   | t_done_median    | t_done_moe | t_done_count | t_done_total_pc |
|-----|-----------------------------|------------------|------------|--------------|-----------------|
| 1   | (No Value)                  | 3090             | 40.6601    | 49904        | 46.3069         |
| 2   | Test-A                      | 2557             | 51.7651    | 17779        | 16.4975         |
| 3   | Test-B                      | 4587             | 88.988     | 7248         | 6.72556         |
| 4   | BlueHead                    | 3463             | 120.895    | 6885         | 6.38872         |
| 5   | Campaign-XXX                | 3276             | 116.507    | 6688         | 6.20592         |
| 6   | Old-Site                    | 3292             | 165.514    | 2949         | 2.73643         |
| 7   | Slow-SRP                    | 2875             | 169.091    | 2386         | 2.21402         |

```

---

### function `getGeoTimers`
[QueryAPI.jl#366-384](https://github.com/akamai/mPulseAPI.jl/tree/master/src/QueryAPI.jl#L366-L384){: .source-link}

Calls the `geography` endpoint of the mPulse REST API with the passed in filters

#### Arguments
`token::AbstractString`
:    The Repository authentication token fetched by calling [`mPulseAPI.getRepositoryToken`](RepositoryAPI.md#function-getrepositorytoken){: .x-ref}

`appKey::AbstractString`
:    The App Key (formerly known as API key) for the app to query.  If you don't know the App Key, use
     [`mPulseAPI.getRepositoryDomain`](RepositoryAPI.md#function-getrepositorydomain){: .x-ref} to fetch a domain and then inspect `domain["attributes"]["appKey"]`

#### Optional Arguments
`filters::Dict`
:    A dict of filters to pass to the mPulse Query API. For example `Dict("page-group" => "foo-bar")`
     will filter results to the `foo-bar` `page-group`.  The resulting filters will be a merge of
     what is passed in and the default values with whatever is passed in taking precedence.

     The default filters are:

         Dict(
             "date-comparator" => "Last24Hours",
         )

     The `date-start` and `date-end` filters accept a `DateTime` object while the `date` filter
     accepts a `Date` object.

     If you'd like to use a `ZonedDateTime`, pass in its `utc_datetime` field:

         filters = Dict(
             "date-comparator" => "Between",
             "date-start"      => ZonedDateTime(2016, 10, 19, 4, 30, TimeZone("America/New_York")).utc_datetime,
             "date-end"        => DateTime(2016, 10, 19, 16, 30)
         )

     To pass multiple values for a single filter, use an array:

         filters = Dict(
             "beacon-type" => ["page view", "xhr", "spa", "spa_hard"],
             "page-group"  => ["product", "search"]
         )

`friendly_names::Bool=false`
:    Specify whether column names in the `DataFrame` should be human friendly or developer friendly (default)

     Human friendly column names are:

     `Country`, `Median Time (ms)`, `MoE (ms)`, `Measurements`, `% of total`

     Developer friendly column names are:

     `:country`, `:t_done_median`, `:t_done_moe`, `:t_done_count`, `:t_done_total_pc`



#### Throws
[`mPulseAPIAuthException`](exceptions.md#datatype-mpulseapiauthexception)
:   If the `token` is invalid or has expired.

[`mPulseAPIException`](exceptions.md#datatype-mpulseapiexception)
:   If the API returned a non-200 status.  Inspect `mPulseAPIException.response` for details about
    the problem

[`mPulseAPIRequestException`](exceptions.md#datatype-mpulseapirequestexception)
:   If the API was unhappy with a request parameter

[`mPulseAPIResultFormatException`](exceptions.md#datatype-mpulseapiresultformatexception)
:    If the API response had  data in an unexpected data type

#### Returns
`{DataFrame}` A Julia `DataFrame` with the following columns:

`country`, `t_done_median`, `t_done_moe`, `t_done_count`, and `t_done_total_pc`

```julia
julia> pgroups = mPulseAPI.getCountryTimers(token, appKey)
69x5 DataFrames.DataFrame
| Row | country                     | t_done_median    | t_done_moe | t_done_count | t_done_total_pc |
|-----|-----------------------------|------------------|------------|--------------|-----------------|
| 1   | US                          | 3090             | 40.6601    | 49904        | 46.3069         |
| 2   | CA                          | 2557             | 51.7651    | 17779        | 16.4975         |
| 3   | MX                          | 4587             | 88.988     | 7248         | 6.72556         |
| 4   | PH                          | 3463             | 120.895    | 6885         | 6.38872         |
| 5   | AU                          | 3276             | 116.507    | 6688         | 6.20592         |
| 6   | KR                          | 3292             | 165.514    | 2949         | 2.73643         |
| 7   | PE                          | 2875             | 169.091    | 2386         | 2.21402         |

```

---

### function `getMetricsByDimension`
[QueryAPI.jl#431-463](https://github.com/akamai/mPulseAPI.jl/tree/master/src/QueryAPI.jl#L431-L463){: .source-link}

Calls the `metrics-by-dimension` endpoint of the mPulse REST API with the passed in dimension name and filters

#### Arguments
`token::AbstractString`
:    The Repository authentication token fetched by calling [`mPulseAPI.getRepositoryToken`](RepositoryAPI.md#function-getrepositorytoken){: .x-ref}

`appKey::AbstractString`
:    The App Key (formerly known as API key) for the app to query.  If you don't know the App Key, use
     [`mPulseAPI.getRepositoryDomain`](RepositoryAPI.md#function-getrepositorydomain){: .x-ref} to fetch a domain and then inspect `domain["attributes"]["appKey"]`

`dimension::AbstractString`
:    The dimension to split metrics by.  The response contains one row for each value of this dimension.  The following dimensions are supported:

     * page_group
     * browser
     * country
     * bw_block
     * ab_test

     See [http://docs.soasta.com/query-api/#metrics-by-dimension-parameters](http://docs.soasta.com/query-api/#metrics-by-dimension-parameters) for
     an up-to-date list.

#### Optional Arguments
`filters::Dict`
:    A dict of filters to pass to the mPulse Query API. For example `Dict("page-group" => "foo-bar")`
     will filter results to the `foo-bar` `page-group`.  The resulting filters will be a merge of
     what is passed in and the default values with whatever is passed in taking precedence.

     The default filters are:

         Dict(
             "date-comparator" => "Last24Hours",
         )

     The `date-start` and `date-end` filters accept a `DateTime` object while the `date` filter
     accepts a `Date` object.

     If you'd like to use a `ZonedDateTime`, pass in its `utc_datetime` field:

         filters = Dict(
             "date-comparator" => "Between",
             "date-start"      => ZonedDateTime(2016, 10, 19, 4, 30, TimeZone("America/New_York")).utc_datetime,
             "date-end"        => DateTime(2016, 10, 19, 16, 30)
         )

     To pass multiple values for a single filter, use an array:

         filters = Dict(
             "beacon-type" => ["page view", "xhr", "spa", "spa_hard"],
             "page-group"  => ["product", "search"]
         )

#### Throws
[`mPulseAPIAuthException`](exceptions.md#datatype-mpulseapiauthexception)
:   If the `token` is invalid or has expired.

[`mPulseAPIException`](exceptions.md#datatype-mpulseapiexception)
:   If the API returned a non-200 status.  Inspect `mPulseAPIException.response` for details about
    the problem

[`mPulseAPIRequestException`](exceptions.md#datatype-mpulseapirequestexception)
:   If the API was unhappy with a request parameter

[`mPulseAPIResultFormatException`](exceptions.md#datatype-mpulseapiresultformatexception)
:    If the API response had  data in an unexpected data type

#### Returns
`{DataFrame}` A Julia `DataFrame` with the following columns: `:<dimension>`, `:<CustomMetric Name>`...

```julia
julia> mPulseAPI.getMetricsByDimension(token, appKey, "browser")
243x4 DataFrames.DataFrame
| Row | browser                          | Conversion | OrderTotal | ServerDown  |
|-----|----------------------------------|------------|------------|-------------|
| 1   | "Mobile Safari/9"                | 1.381      | 1.62854e7  | 0.000994956 |
| 2   | "Chrome/50"                      | 1.98411    | 3.13401e7  | 0.0050615   |
| 3   | "Safari/9"                       | 3.08288    | 2.10698e7  | 0.00561545  |
| 4   | "Firefox/46"                     | 1.90974    | 8569362    | 0.00462406  |
| 5   | "Mobile Safari/8"                | 2.38545    | 2295848    | 0.0         |
| 6   | "Chrome/49"                      | 2.22828    | 4394331    | 0.0         |
```

---

### function `getTimersMetrics`
[QueryAPI.jl#509-586](https://github.com/akamai/mPulseAPI.jl/tree/master/src/QueryAPI.jl#L509-L586){: .source-link}

Calls the `timers-metrics` endpoint of the mPulse REST API with the passed in filters

#### Arguments
`token::AbstractString`
:    The Repository authentication token fetched by calling [`mPulseAPI.getRepositoryToken`](RepositoryAPI.md#function-getrepositorytoken){: .x-ref}

`appKey::AbstractString`
:    The App Key (formerly known as API key) for the app to query.  If you don't know the App Key, use
     [`mPulseAPI.getRepositoryDomain`](RepositoryAPI.md#function-getrepositorydomain){: .x-ref} to fetch a domain and then inspect `domain["attributes"]["appKey"]`

#### Optional Arguments
`filters::Dict`
:    A dict of filters to pass to the mPulse Query API. For example `Dict("page-group" => "foo-bar")`
     will filter results to the `foo-bar` `page-group`.  The resulting filters will be a merge of
     what is passed in and the default values with whatever is passed in taking precedence.

     The default filters are:

         Dict(
             "date-comparator" => "Last24Hours",
         )

     The `date-start` and `date-end` filters accept a `DateTime` object while the `date` filter
     accepts a `Date` object.

     If you'd like to use a `ZonedDateTime`, pass in its `utc_datetime` field:

         filters = Dict(
             "date-comparator" => "Between",
             "date-start"      => ZonedDateTime(2016, 10, 19, 4, 30, TimeZone("America/New_York")).utc_datetime,
             "date-end"        => DateTime(2016, 10, 19, 16, 30)
         )

     To pass multiple values for a single filter, use an array:

         filters = Dict(
             "beacon-type" => ["page view", "xhr", "spa", "spa_hard"],
             "page-group"  => ["product", "search"]
         )

#### Throws
[`mPulseAPIAuthException`](exceptions.md#datatype-mpulseapiauthexception)
:   If the `token` is invalid or has expired.

[`mPulseAPIException`](exceptions.md#datatype-mpulseapiexception)
:   If the API returned a non-200 status.  Inspect `mPulseAPIException.response` for details about
    the problem

[`mPulseAPIRequestException`](exceptions.md#datatype-mpulseapirequestexception)
:   If the API was unhappy with a request parameter

`Exception`
:    If there was an unexpected type error parsing response values

#### Returns
`{DataFrame}` A `DataFrame` with one column for each timer and metric.  Known columns include:

* `:Beacons` and `:PageLoad` will always be present.
* `:Sessions` and `:BounceRate` will be present if session tracking is enabled (on by default)
* `:DNS`, `:TCP`, `:SSL`, `:FirstByte`, `:DomLoad`, `:DomReady` and `:FirstLastByte` will be present if NavigationTiming is available (almost always available)
* Custom Timers & Custom Metrics are included if defined and if they have data for the selected time period

The last row in the DataFrame is the latest value of the timer or metric.  All preceding rows are historic values over the time period broken down by a predefined time unit.
For example, for Last24Hours, there will be 1440 entries representing each minute in the 24 hour period.

```julia
julia> mPulseAPI.getTimersMetrics(token, appKey)
1441x16 DataFrames.DataFrame
| Row  | PageLoad | DNS | DomLoad | DomReady | FirstByte | SSL | FirstLastByte | TCP | DOM Interactive | Sessions | BounceRate | Conversion | OrderTotal | Beacons |
|------|----------|-----|---------|----------|-----------|-----|---------------|-----|-----------------|----------|------------|------------|------------|---------|
| 1    | 3442     │ 46  │ 805     │ 3345     │ 732       │ 59  │ 2468          │ 36  │ 1696            │ 353      │ 34         │ 0.566572   │ 58652.0    │ 1808    │
| 2    | 3308     | 45  | 758     | 3173     | 705       | 56  | 2351          | 32  | 1620            | 331      | 30         | 2.1148     | 266219.0   | 1767    |
| 3    | 3412     | 38  | 794     | 3287     | 726       | 69  | 2360          | 31  | 1707            | 346      | 29         | 1.44509    | 209205.0   | 1806    |
| 4    | 3368     | 40  | 775     | 3250     | 701       | 51  | 2500          | 34  | 1670            | 354      | 32         | 2.25989    | 47354.0    | 1850    |
| 5    | 3346     | 37  | 754     | 3222     | 691       | 61  | 2516          | 31  | 1624            | 326      | 30         | 2.76074    | 132915.0   | 1742    |
| 6    | 3162     | 36  | 729     | 3040     | 665       | 86  | 2283          | 30  | 1611            | 382      | 28         | 1.57068    | 117284.0   | 1803    |
| 7    | 3453     | 39  | 862     | 3320     | 787       | 64  | 2471          | 32  | 1772            | 356      | 33         | 2.52809    | 108045.0   | 1727    |
| 8    | 3593     | 46  | 1028    | 3491     | 889       | 92  | 2495          | 33  | 1952            | 314      | 33         | 1.27389    | 150020.0   | 1715    |
```

---

### function `getHistogram`
[QueryAPI.jl#652-666](https://github.com/akamai/mPulseAPI.jl/tree/master/src/QueryAPI.jl#L652-L666){: .source-link}

Calls the `histogram` endpoint of the mPulse REST API with the passed in filters

#### Arguments
`token::AbstractString`
:    The Repository authentication token fetched by calling [`mPulseAPI.getRepositoryToken`](RepositoryAPI.md#function-getrepositorytoken){: .x-ref}

`appKey::AbstractString`
:    The App Key (formerly known as API key) for the app to query.  If you don't know the App Key, use
     [`mPulseAPI.getRepositoryDomain`](RepositoryAPI.md#function-getrepositorydomain){: .x-ref} to fetch a domain and then inspect `domain["attributes"]["appKey"]`

#### Optional Arguments
`filters::Dict`
:    A dict of filters to pass to the mPulse Query API. For example `Dict("page-group" => "foo-bar")`
     will filter results to the `foo-bar` `page-group`.  The resulting filters will be a merge of
     what is passed in and the default values with whatever is passed in taking precedence.

     The default filters are:

         Dict(
             "date-comparator" => "Last24Hours",
         )

     The `date-start` and `date-end` filters accept a `DateTime` object while the `date` filter
     accepts a `Date` object.

     If you'd like to use a `ZonedDateTime`, pass in its `utc_datetime` field:

         filters = Dict(
             "date-comparator" => "Between",
             "date-start"      => ZonedDateTime(2016, 10, 19, 4, 30, TimeZone("America/New_York")).utc_datetime,
             "date-end"        => DateTime(2016, 10, 19, 16, 30)
         )

     To pass multiple values for a single filter, use an array:

         filters = Dict(
             "beacon-type" => ["page view", "xhr", "spa", "spa_hard"],
             "page-group"  => ["product", "search"]
         )

#### Throws
[`mPulseAPIAuthException`](exceptions.md#datatype-mpulseapiauthexception)
:   If the `token` is invalid or has expired.

[`mPulseAPIException`](exceptions.md#datatype-mpulseapiexception)
:   If the API returned a non-200 status.  Inspect `mPulseAPIException.response` for details about
    the problem

[`mPulseAPIRequestException`](exceptions.md#datatype-mpulseapirequestexception)
:   If the API was unhappy with a request parameter

[`mPulseAPIResultFormatException`](exceptions.md#datatype-mpulseapiresultformatexception)
:    If the API response had a missing `series` element or data in an unexpected data type

#### Returns
`{Dict}` A Julia `Dict` with the following string keys:

`median::Int`
:   The median value for values in the histogram in milliseconds

`p95::Int`
:   The 95th percentile value for values in the histogram in milliseconds

`p98::Int`
:   The 98th percentile value for values in the histogram in milliseconds

`buckets::DataFrame`
:   Buckets for the histogram.  These buckets are variable width. See below for a description.


```julia
julia> histo = mPulseAPI.getHistogram(token, appKey)
Dict{AbstractString,Any} with 4 entries:
  "median"  => 3439
  "p95"     => 12843
  "p98"     => 22816
  "buckets" => 117x3 DataFrames.DataFrame…
```

The buckets `DataFrame` has the following columns: `:bucket_start`, `:bucket_end`, `:element_count`

```julia
julia> histo["buckets"]
117x3 DataFrames.DataFrame
| Row | bucket_start | bucket_end | element_count |
|-----|--------------|------------|---------------|
| 1   | 1            | 2          | 1             |
| 2   | 3            | 4          | 3             |
| 3   | 4            | 5          | 8             |
| 4   | 5            | 6          | 7             |
| 5   | 6            | 7          | 9             |
| 6   | 7            | 8          | 8             |
| 7   | 8            | 9          | 14            |
| 8   | 9            | 10         | 13            |
| 9   | 10           | 11         | 17            |
| 10  | 11           | 12         | 15            |
| 11  | 12           | 13         | 19            |
| 12  | 13           | 14         | 18            |
| 13  | 14           | 15         | 19            |
```

---

### function `getSessionsOverPageLoadTime`
[QueryAPI.jl#686-688](https://github.com/akamai/mPulseAPI.jl/tree/master/src/QueryAPI.jl#L686-L688){: .source-link}

Calls the `sessions-per-page-load-time` endpoint of the mPulse REST API with the passed in filters

#### Arguments
`token::AbstractString`
:    The Repository authentication token fetched by calling [`mPulseAPI.getRepositoryToken`](RepositoryAPI.md#function-getrepositorytoken){: .x-ref}

`appKey::AbstractString`
:    The App Key (formerly known as API key) for the app to query.  If you don't know the App Key, use
     [`mPulseAPI.getRepositoryDomain`](RepositoryAPI.md#function-getrepositorydomain){: .x-ref} to fetch a domain and then inspect `domain["attributes"]["appKey"]`

#### Optional Arguments
`filters::Dict`
:    A dict of filters to pass to the mPulse Query API. For example `Dict("page-group" => "foo-bar")`
     will filter results to the `foo-bar` `page-group`.  The resulting filters will be a merge of
     what is passed in and the default values with whatever is passed in taking precedence.

     The default filters are:

         Dict(
             "date-comparator" => "Last24Hours",
         )

     The `date-start` and `date-end` filters accept a `DateTime` object while the `date` filter
     accepts a `Date` object.

     If you'd like to use a `ZonedDateTime`, pass in its `utc_datetime` field:

         filters = Dict(
             "date-comparator" => "Between",
             "date-start"      => ZonedDateTime(2016, 10, 19, 4, 30, TimeZone("America/New_York")).utc_datetime,
             "date-end"        => DateTime(2016, 10, 19, 16, 30)
         )

     To pass multiple values for a single filter, use an array:

         filters = Dict(
             "beacon-type" => ["page view", "xhr", "spa", "spa_hard"],
             "page-group"  => ["product", "search"]
         )

#### Throws
[`mPulseAPIAuthException`](exceptions.md#datatype-mpulseapiauthexception)
:   If the `token` is invalid or has expired.

[`mPulseAPIException`](exceptions.md#datatype-mpulseapiexception)
:   If the API returned a non-200 status.  Inspect `mPulseAPIException.response` for details about
    the problem

[`mPulseAPIRequestException`](exceptions.md#datatype-mpulseapirequestexception)
:   If the API was unhappy with a request parameter

#### Returns
`{DataFrame}` A julia `DataFrame` mapping the Sessions to a load time bucket:

```julia
julia> mPulseAPI.getSessionsOverPageLoadTime(token, appKey)
60x2 DataFrames.DataFrame
| Row | t_done | Sessions   |
|-----|--------|------------|
| 1   | 210    | 72         |
| 2   | 300    | 36         |
| 3   | 400    | 72         |
| 4   | 500    | 30         |
| 5   | 550    | 66         |
| 6   | 600    | 464        |
| 7   | 700    | 30         |
| 8   | 800    | 749        |
| 9   | 900    | 709        |
| 10  | 1050   | 1246       |
```

---

### function `getMetricOverPageLoadTime`
[QueryAPI.jl#713-737](https://github.com/akamai/mPulseAPI.jl/tree/master/src/QueryAPI.jl#L713-L737){: .source-link}

Calls the `metric-per-page-load-time` endpoint of the mPulse REST API with the passed in filters

#### Arguments
`token::AbstractString`
:    The Repository authentication token fetched by calling [`mPulseAPI.getRepositoryToken`](RepositoryAPI.md#function-getrepositorytoken){: .x-ref}

`appKey::AbstractString`
:    The App Key (formerly known as API key) for the app to query.  If you don't know the App Key, use
     [`mPulseAPI.getRepositoryDomain`](RepositoryAPI.md#function-getrepositorydomain){: .x-ref} to fetch a domain and then inspect `domain["attributes"]["appKey"]`

#### Optional Arguments
`metric::AbstractString`
:    The name of the metric that we want data for.  If not specified, defaults to `BounceRate`

`filters::Dict`
:    A dict of filters to pass to the mPulse Query API. For example `Dict("page-group" => "foo-bar")`
     will filter results to the `foo-bar` `page-group`.  The resulting filters will be a merge of
     what is passed in and the default values with whatever is passed in taking precedence.

     The default filters are:

         Dict(
             "date-comparator" => "Last24Hours",
         )

     The `date-start` and `date-end` filters accept a `DateTime` object while the `date` filter
     accepts a `Date` object.

     If you'd like to use a `ZonedDateTime`, pass in its `utc_datetime` field:

         filters = Dict(
             "date-comparator" => "Between",
             "date-start"      => ZonedDateTime(2016, 10, 19, 4, 30, TimeZone("America/New_York")).utc_datetime,
             "date-end"        => DateTime(2016, 10, 19, 16, 30)
         )

     To pass multiple values for a single filter, use an array:

         filters = Dict(
             "beacon-type" => ["page view", "xhr", "spa", "spa_hard"],
             "page-group"  => ["product", "search"]
         )

#### Throws
[`mPulseAPIAuthException`](exceptions.md#datatype-mpulseapiauthexception)
:   If the `token` is invalid or has expired.

[`mPulseAPIException`](exceptions.md#datatype-mpulseapiexception)
:   If the API returned a non-200 status.  Inspect `mPulseAPIException.response` for details about
    the problem

[`mPulseAPIRequestException`](exceptions.md#datatype-mpulseapirequestexception)
:   If the API was unhappy with a metric

[`mPulseAPIResultFormatException`](exceptions.md#datatype-mpulseapiresultformatexception)
:    If the API response had a missing `series` element or data in an unexpected data type

#### Returns
`{DataFrame}` A julia `DataFrame` mapping the Metric to a load time bucket:

```julia
julia> mPulseAPI.getMetricOverPageLoadTime(token, appKey)
60x2 DataFrames.DataFrame
| Row | t_done | BounceRate |
|-----|--------|------------|
| 1   | 210    | NA         |
| 2   | 300    | 100.0      |
| 3   | 400    | NA         |
| 4   | 500    | 68.57      |
| 5   | 550    | 12.65      |
| 6   | 600    | 71.08      |
| 7   | 700    | 68.57      |
| 8   | 800    | 14.51      |
| 9   | 900    | 20.83      |
| 10  | 1050   | 24.58      |
```

---

### function `getTimerByMinute`
[QueryAPI.jl#792-826](https://github.com/akamai/mPulseAPI.jl/tree/master/src/QueryAPI.jl#L792-L826){: .source-link}

Calls the `by-minute` endpoint of the mPulse REST API with the passed in filters

#### Arguments
`token::AbstractString`
:    The Repository authentication token fetched by calling [`mPulseAPI.getRepositoryToken`](RepositoryAPI.md#function-getrepositorytoken){: .x-ref}

`appKey::AbstractString`
:    The App Key (formerly known as API key) for the app to query.  If you don't know the App Key, use
     [`mPulseAPI.getRepositoryDomain`](RepositoryAPI.md#function-getrepositorydomain){: .x-ref} to fetch a domain and then inspect `domain["attributes"]["appKey"]`

#### Optional Arguments
`timer::AbstractString`
:    The name of the timer whose data we want.  If not specified, defaults to `PageLoad`.  Other possible
     values are:

     * PageLoad
     * DNS
     * TCP
     * SSL
     * FirstByte
     * DomLoad
     * DomReady
     * FirstLastByte
     * &lt;all custom timers&gt;

`filters::Dict`
:    A dict of filters to pass to the mPulse Query API. For example `Dict("page-group" => "foo-bar")`
     will filter results to the `foo-bar` `page-group`.  The resulting filters will be a merge of
     what is passed in and the default values with whatever is passed in taking precedence.

     The default filters are:

         Dict(
             "date-comparator" => "Last24Hours",
         )

     The `date-start` and `date-end` filters accept a `DateTime` object while the `date` filter
     accepts a `Date` object.

     If you'd like to use a `ZonedDateTime`, pass in its `utc_datetime` field:

         filters = Dict(
             "date-comparator" => "Between",
             "date-start"      => ZonedDateTime(2016, 10, 19, 4, 30, TimeZone("America/New_York")).utc_datetime,
             "date-end"        => DateTime(2016, 10, 19, 16, 30)
         )

     To pass multiple values for a single filter, use an array:

         filters = Dict(
             "beacon-type" => ["page view", "xhr", "spa", "spa_hard"],
             "page-group"  => ["product", "search"]
         )

#### Throws
[`mPulseAPIAuthException`](exceptions.md#datatype-mpulseapiauthexception)
:   If the `token` is invalid or has expired.

[`mPulseAPIException`](exceptions.md#datatype-mpulseapiexception)
:   If the API returned a non-200 status.  Inspect `mPulseAPIException.response` for details about
    the problem

[`mPulseAPIRequestException`](exceptions.md#datatype-mpulseapirequestexception)
:   If the API was unhappy with a timer

[`mPulseAPIResultFormatException`](exceptions.md#datatype-mpulseapiresultformatexception)
:    If the API response had a missing `series` element or data in an unexpected data type

#### Returns
`{DataFrame}` A julia `DataFrame` containing timeseries data for the median value of the timer and its margin of error.
The fields are: `:timestamp` in milliseconds since the UNIX epoch, `:<TimerName>` in milliseconds and `:moe` in milliseconds.

```julia
julia> data = mPulseAPI.getTimerByMinute(token, appKey, timer="PageLoad")
1440x3 DataFrames.DataFrame
| Row  | timestamp     | PageLoad | moe  |
|------|---------------|----------|------|
| 1    | 1463452800000 | 3679     | 135  |
| 2    | 1463452860000 | 3731     | 202  |
| 3    | 1463452920000 | 3706     | 116  |
| 4    | 1463452980000 | 3911     | 171  |
| 5    | 1463453040000 | 3757     | 181  |
| 6    | 1463453100000 | 3729     | 174  |
| 7    | 1463453160000 | 3779     | 174  |
| 8    | 1463453220000 | 3916     | 182  |

```

---

### function `mergeMetrics`
[QueryAPI.jl#891-903](https://github.com/akamai/mPulseAPI.jl/tree/master/src/QueryAPI.jl#L891-L903){: .source-link}

Merge multiple similar `DataFrames` into a single `DataFrame`

Use this method to merge the results from multiple calls to `getMetricOverPageLoadTime()` and `getSessionsOverPageLoadTime()`.
All passed in `DataFrame`s MUST contain a `:t_done` column.

### Arguments
`df1::DataFrame`
:    The first `DataFrame` in the collection.  This method requires at least one `DataFrame` to be passed in.

`df2::DataFrame...`
:    One or more `DataFrame` to be merged together with the first one

### Optional Arguments
`keyField::Symbol=:t_done`
:    The column name to join on.  Defaults to `:t_done`

`joinType::Symbol=:outer`
:    The type of join to perform.  See the `kind` parameter in `?join` for a list of supported join types

### Throws
`KeyError`
:    if the `keyField` column does not exist in all passed in `DataFrame`s

### Returns
* If only one `DataFrame` is passed in, it is returned as-is.  This is not a copy of the first DataFrame.
* If multiple `DataFrame`s are passed in, they are merged using an `outer` join on the `keyField` column, and the resulting `DataFrame` is returned.
  Since we perform an outer join, rows in any of the DataFrames that do not have a matching `keyField` value found in other DataFrames will be filled with `NA`

```julia
julia> sessions   = mPulseAPI.getSessionsOverPageLoadTime(token, appKey);
julia> bouncerate = mPulseAPI.getMetricOverPageLoadTime(token, appKey);
julia> conversion = mPulseAPI.getMetricOverPageLoadTime(token, appKey, metric="Conversion");

julia> mPulseAPI.mergeMetrics(sessions, bouncerate, conversion)
65x4 DataFrames.DataFrame
| Row | t_done | Sessions | BounceRate | Conversion |
|-----|--------|----------|------------|------------|
| 1   | 6      | 1        | NA         | NA         |
| 2   | 10     | 2        | 50.0       | NA         |
| 3   | 12     | 2        | 100.0      | NA         |
| 4   | 17     | 1        | 100.0      | NA         |
| 5   | 30     | 1        | 100.0      | NA         |
| 6   | 34     | 1        | NA         | NA         |
| 7   | 40     | 1        | 100.0      | NA         |
| 8   | 60     | 2        | 100.0      | NA         |
| 9   | 70     | 1        | 100.0      | NA         |
| 10  | 120    | 2        | 100.0      | NA         |
| 11  | 140    | 1        | NA         | NA         |
| 12  | 170    | 1        | NA         | NA         |
| 13  | 190    | 1        | 100.0      | NA         |
| 14  | 230    | 1        | NA         | NA         |
...
| 44  | 3750   | 8332     | 29.5915    | 2.25043    |
| 45  | 3950   | 7957     | 31.7591    | 2.08962    |
| 46  | 4200   | 7342     | 34.9953    | 2.02302    |
| 47  | 4500   | 6783     | 37.0922    | 1.59947    |
| 48  | 4800   | 6140     | 40.2336    | 1.67129    |
| 49  | 5100   | 5530     | 42.2393    | 1.32839    |
```

---

