# Queries de apoio ao vídeo

``` https://www.youtube.com/watch?v=xBzNxPHyDmQ
dim_paises e dim_paises_all

```pq
let
    Source = Sql.Database(
        server, 
        database,
        [ Query = "select distinct(Country) from dbo.fact_sales" ]
    )
in
    Source
```

dim_calendario  

```pq
let
    // start_date = #date(2024, 1, 1),
    // end_date = #date(2024, 12, 31),
    dates = List.Generate(
        ()=> [Date = start_date],
        each [Date] <= end_date,
        each [Date = Date.AddDays([Date], 1)],
        each [Date]
    ),
    calendar = #table(
        type table[
            Date = date, 
            MonthNumber = Int64.Type,
            MonthName = text,
            Year = Int64.Type
        ],
        List.Transform(
            dates,
            each {
                _,
                Date.Month(_),
                Date.MonthName(_, "en-US"),
                Date.Year(_)
            }
        )
    )

in
    calendar

```


fact_sales_direct

```pq
let

    where_range_dates = " where OrderDate >= convert(date, '"  & Date.ToText(start_date, [Format="yyyy-MM-dd"]) &   "') and OrderDate <= convert(date, '"& Date.ToText(end_date, [Format="yyyy-MM-dd"]) &"')",

    where_country = " and Country = '" & country_filter &  "' ",

    Source = Sql.Database(server, database,[
        Query = "select * from dbo.fact_sales" &  where_range_dates & where_country
    ]
)

in
    Source
```


fact_sales_direct_all

```pq
let
    // Verifica se o parâmetro é uma lista ou um único valor
    selected_countries = if Type.Is(Value.Type(country_filter_all), List.Type) then
        Text.Combine(List.Transform(country_filter_all, each "'" & _ & "'"), ", ")
    else
        "'" & country_filter_all & "'",

    
    // Verificar se todos os países estão selecionados
    select_all = if Type.Is(Value.Type(country_filter_all), List.Type) then
        List.Contains(country_filter_all, "__SelectAll__")
    else
        country_filter_all = "__SelectAll__", // Boolean

    // Construção da consulta SQL
    where_country = if select_all then
        "" // Sem filtro se todos os países forem selecionados
    else
        " and Country in (" & selected_countries & ")",

    where_range_dates = " where OrderDate >= convert(date, '"  & Date.ToText(start_date, [Format="yyyy-MM-dd"]) &   "') and OrderDate <= convert(date, '"& Date.ToText(end_date, [Format="yyyy-MM-dd"]) &"')",

    Source = Sql.Database(server, database,[
        Query = "select * from dbo.fact_sales" &  where_range_dates & where_country
    ]
)

in
    Source
```

