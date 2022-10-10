---
date: 2022-09-24

title: Biking is awesome, so is making tables.
summary: | 
  Let's use the `{gt}` package to see which of largest cities in France are the 
  most bike friendly. Because tables deserve love as much as plots.

format: hugo

draft: true

freeze: auto
tags:
- R
---

# France & biking

Last week, French prime minister Elisabeth Borne argued that France should
become a "nation vélo"[^1]. Whatever this means, the government is planning
on putting 250M€ on the table to help people developing bike habits.

{{< figure src="img/tiffany-nutt-0ClfreiNppM-unsplash.jpg" caption="Photo by Tiffany Nutt." >}}

But, what is exactly the situation in France with biking? Well, thanks to the
**Fédération des Utilisateurs de Bicyclettes** (or FUB for short), we can have
a look. Every now and then since 2017, the FUB conducts national surveys where
cyclists are invited to report their opinion on the biking infrastructure of
their cities.

This will help us see what cyclists think about their city.

# The code

For the sake of reading length, I will skip the data wrangling part of the
data set[^2] and jump right into the formatting part of the table.

Before going into details, it is worth remembering that a table is more than a
mere tabular representation of data. It can have captions, a title, or even
label spanning over several columns.

{{< figure src="img/mourizal-zativa-OSvN1fBcXYE-unsplash.jpg" caption="Building a table with {gt} feels like building blocks. Photo by Mourizal Zativa" >}}

To format the table, we will use the [`{gt}` package](https://gt.rstudio.com/).
This package offers a grammar of table--this is what `{gt}` stands for--the same
way `{ggplot2}` offers a grammar of graphics.

``` r
library(gt)
library(gtExtras)
```

Currently, in our environment, we have a tibble with the data we want to put
on a table.

``` r
head(gt_dat)
```

    # A tibble: 6 × 6
      commune        population  note note_2017 note_2019 evolution
      <fct>               <int> <dbl>     <dbl>     <dbl> <list>   
    1 Paris (75)        2190327     4      4.33      3.24 <dbl [2]>
    2 Marseille (13)     862211     7      2.18      1.96 <dbl [2]>
    3 Lyon (69)          515695     4      3.81      3.19 <dbl [2]>
    4 Toulouse (31)      475438     5      3.00      2.88 <dbl [2]>
    5 Nice (06)          342637     6      2.53      2.37 <dbl [2]>
    6 Nantes (44)        306694     3      3.97      3.55 <dbl [2]>

The same way we need to map the data to a plot before defining its
characteristics, we will need to map our data to the table.

``` r
gt_table <- gt(gt_dat) 
```

Then, we can use a wide range of functions to define our tables. First,
let's figure out the header part--note that this step could come at the end. We
will first define a title, and a label spanning over two columns.

``` r
gt_table <-
  gt_table |>
  tab_header(
    title = "What does it look like to bike in France's biggest cities?",
    subtitle = 
      "Every few year, the FUB conducts a national survey on bike usage. 
      People are invited to report what do they thing about bike infrastructure 
      in their city. The perfect bike-friendly city would score an A, the worst
      one an F."
  ) |>
  tab_spanner(
    label = "Time",
    columns = c(note_2017, note_2019)
  )
```

Then we can rename some of the columns for the sake of clarity.

``` r
gt_table <-
  gt_table |> 
  cols_label(
    population = "population (k)",
    note_2017 = "2017",
    note_2019 = "2019"
  )
```

We can also decide to format the cells, and even replace a list-column with a
spark line chart!

``` r
gt_table <-
  gt_table |> 
  
  # Let's format how some numbers will be shown
  fmt_number(
    columns = 2,
    scale_by = 1e-3
  ) |>
  fmt_number(
    columns = c(4, 5),
    decimals = 2,
  ) |>
  
  # A small color box around cities grading
  gtExtras::gt_color_box(
    note,
    domain = c(1, 7), palette = c("green", "orange", "red")) |>
  text_transform(
    locations  = cells_body(columns = note),
    fn = function(x) { str_replace_all(x, number_to_grade) }
  ) |>

  # Let's transform our list-column in a sparkline
  gtExtras::gt_plt_sparkline(evolution)
```

Let's give credit where it is due.

``` r
gt_table <- gt_table |> 
  tab_source_note(source_note = md("Source: _FUB_"))
```

And let's add a theme so that our table looks nice out of the box. 🪄

``` r
gt_table <- gt_table |> gtExtras::gt_theme_nytimes() 
```

Once everything done, all we have to do is print the object so that we can show
this pretty table to our friends.

``` r
gt_table
```

<div id="hlzobjktzo" style="overflow-x:auto;overflow-y:auto;width:auto;height:auto;">
<style>@import url("https://fonts.googleapis.com/css2?family=Source+Sans+Pro:ital,wght@0,100;0,200;0,300;0,400;0,500;0,600;0,700;0,800;0,900;1,100;1,200;1,300;1,400;1,500;1,600;1,700;1,800;1,900&display=swap");
@import url("https://fonts.googleapis.com/css2?family=Libre+Franklin:ital,wght@0,100;0,200;0,300;0,400;0,500;0,600;0,700;0,800;0,900;1,100;1,200;1,300;1,400;1,500;1,600;1,700;1,800;1,900&display=swap");
@import url("https://fonts.googleapis.com/css2?family=Source+Sans+Pro:ital,wght@0,100;0,200;0,300;0,400;0,500;0,600;0,700;0,800;0,900;1,100;1,200;1,300;1,400;1,500;1,600;1,700;1,800;1,900&display=swap");
html {
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, 'Helvetica Neue', 'Fira Sans', 'Droid Sans', Arial, sans-serif;
}

#hlzobjktzo .gt_table {
  display: table;
  border-collapse: collapse;
  margin-left: auto;
  margin-right: auto;
  color: #333333;
  font-size: 16px;
  font-weight: normal;
  font-style: normal;
  background-color: #FFFFFF;
  width: auto;
  border-top-style: none;
  border-top-width: 2px;
  border-top-color: #A8A8A8;
  border-right-style: none;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #A8A8A8;
  border-left-style: none;
  border-left-width: 2px;
  border-left-color: #D3D3D3;
}

#hlzobjktzo .gt_heading {
  background-color: #FFFFFF;
  text-align: left;
  border-bottom-color: #FFFFFF;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
}

#hlzobjktzo .gt_title {
  color: #333333;
  font-size: 125%;
  font-weight: initial;
  padding-top: 4px;
  padding-bottom: 4px;
  padding-left: 5px;
  padding-right: 5px;
  border-bottom-color: #FFFFFF;
  border-bottom-width: 0;
}

#hlzobjktzo .gt_subtitle {
  color: #333333;
  font-size: 85%;
  font-weight: initial;
  padding-top: 0;
  padding-bottom: 6px;
  padding-left: 5px;
  padding-right: 5px;
  border-top-color: #FFFFFF;
  border-top-width: 0;
}

#hlzobjktzo .gt_bottom_border {
  border-bottom-style: none;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}

#hlzobjktzo .gt_col_headings {
  border-top-style: none;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: none;
  border-bottom-width: 1px;
  border-bottom-color: #334422;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
}

#hlzobjktzo .gt_col_heading {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 12px;
  font-weight: normal;
  text-transform: inherit;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
  vertical-align: bottom;
  padding-top: 5px;
  padding-bottom: 6px;
  padding-left: 5px;
  padding-right: 5px;
  overflow-x: hidden;
}

#hlzobjktzo .gt_column_spanner_outer {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 12px;
  font-weight: normal;
  text-transform: inherit;
  padding-top: 0;
  padding-bottom: 0;
  padding-left: 4px;
  padding-right: 4px;
}

#hlzobjktzo .gt_column_spanner_outer:first-child {
  padding-left: 0;
}

#hlzobjktzo .gt_column_spanner_outer:last-child {
  padding-right: 0;
}

#hlzobjktzo .gt_column_spanner {
  border-bottom-style: none;
  border-bottom-width: 1px;
  border-bottom-color: #334422;
  vertical-align: bottom;
  padding-top: 5px;
  padding-bottom: 5px;
  overflow-x: hidden;
  display: inline-block;
  width: 100%;
}

#hlzobjktzo .gt_group_heading {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  text-transform: inherit;
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
  vertical-align: middle;
}

#hlzobjktzo .gt_empty_group_heading {
  padding: 0.5px;
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  vertical-align: middle;
}

#hlzobjktzo .gt_from_md > :first-child {
  margin-top: 0;
}

#hlzobjktzo .gt_from_md > :last-child {
  margin-bottom: 0;
}

#hlzobjktzo .gt_row {
  padding-top: 7px;
  padding-bottom: 7px;
  padding-left: 5px;
  padding-right: 5px;
  margin: 10px;
  border-top-style: solid;
  border-top-width: 1px;
  border-top-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
  vertical-align: middle;
  overflow-x: hidden;
}

#hlzobjktzo .gt_stub {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  text-transform: inherit;
  border-right-style: solid;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
  padding-left: 5px;
  padding-right: 5px;
}

#hlzobjktzo .gt_stub_row_group {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  text-transform: inherit;
  border-right-style: solid;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
  padding-left: 5px;
  padding-right: 5px;
  vertical-align: top;
}

#hlzobjktzo .gt_row_group_first td {
  border-top-width: 2px;
}

#hlzobjktzo .gt_summary_row {
  color: #333333;
  background-color: #FFFFFF;
  text-transform: inherit;
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
}

#hlzobjktzo .gt_first_summary_row {
  border-top-style: solid;
  border-top-color: #D3D3D3;
}

#hlzobjktzo .gt_first_summary_row.thick {
  border-top-width: 2px;
}

#hlzobjktzo .gt_last_summary_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}

#hlzobjktzo .gt_grand_summary_row {
  color: #333333;
  background-color: #FFFFFF;
  text-transform: inherit;
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
}

#hlzobjktzo .gt_first_grand_summary_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  border-top-style: double;
  border-top-width: 6px;
  border-top-color: #D3D3D3;
}

#hlzobjktzo .gt_striped {
  background-color: rgba(128, 128, 128, 0.05);
}

#hlzobjktzo .gt_table_body {
  border-top-style: none;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #FFFFFF;
}

#hlzobjktzo .gt_footnotes {
  color: #333333;
  background-color: #FFFFFF;
  border-bottom-style: none;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 2px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
}

#hlzobjktzo .gt_footnote {
  margin: 0px;
  font-size: 90%;
  padding-left: 4px;
  padding-right: 4px;
  padding-left: 5px;
  padding-right: 5px;
}

#hlzobjktzo .gt_sourcenotes {
  color: #333333;
  background-color: #FFFFFF;
  border-bottom-style: none;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 2px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
}

#hlzobjktzo .gt_sourcenote {
  font-size: 90%;
  padding-top: 4px;
  padding-bottom: 4px;
  padding-left: 5px;
  padding-right: 5px;
}

#hlzobjktzo .gt_left {
  text-align: left;
}

#hlzobjktzo .gt_center {
  text-align: center;
}

#hlzobjktzo .gt_right {
  text-align: right;
  font-variant-numeric: tabular-nums;
}

#hlzobjktzo .gt_font_normal {
  font-weight: normal;
}

#hlzobjktzo .gt_font_bold {
  font-weight: bold;
}

#hlzobjktzo .gt_font_italic {
  font-style: italic;
}

#hlzobjktzo .gt_super {
  font-size: 65%;
}

#hlzobjktzo .gt_footnote_marks {
  font-style: italic;
  font-weight: normal;
  font-size: 75%;
  vertical-align: 0.4em;
}

#hlzobjktzo .gt_asterisk {
  font-size: 100%;
  vertical-align: 0;
}

#hlzobjktzo .gt_indent_1 {
  text-indent: 5px;
}

#hlzobjktzo .gt_indent_2 {
  text-indent: 10px;
}

#hlzobjktzo .gt_indent_3 {
  text-indent: 15px;
}

#hlzobjktzo .gt_indent_4 {
  text-indent: 20px;
}

#hlzobjktzo .gt_indent_5 {
  text-indent: 25px;
}
</style>
<table class="gt_table">
  <thead class="gt_header">
    <tr>
      <td colspan="6" class="gt_heading gt_title gt_font_normal" style="font-family: &#39;Libre Franklin&#39;; font-weight: 800;">What does it look like to bike in France's biggest cities?</td>
    </tr>
    <tr>
      <td colspan="6" class="gt_heading gt_subtitle gt_font_normal gt_bottom_border" style>Every few year, the FUB conducts a national survey on bike usage. 
      People are invited to report what do they thing about bike infrastructure 
      in their city. The perfect bike-friendly city would score an A, the worst
      one an F.</td>
    </tr>
  </thead>
  <thead class="gt_col_headings">
    <tr>
      <th class="gt_col_heading gt_columns_bottom_border gt_center" rowspan="2" colspan="1" style="color: #A9A9A9; font-family: &#39;Source Sans Pro&#39;; text-transform: uppercase;" scope="col">commune</th>
      <th class="gt_col_heading gt_columns_bottom_border gt_right" rowspan="2" colspan="1" style="color: #A9A9A9; font-family: &#39;Source Sans Pro&#39;; text-transform: uppercase;" scope="col">population (k)</th>
      <th class="gt_col_heading gt_columns_bottom_border gt_right" rowspan="2" colspan="1" style="color: #A9A9A9; font-family: &#39;Source Sans Pro&#39;; text-transform: uppercase;" scope="col">note</th>
      <th class="gt_center gt_columns_top_border gt_column_spanner_outer" rowspan="1" colspan="2" scope="colgroup">
        <span class="gt_column_spanner">Time</span>
      </th>
      <th class="gt_col_heading gt_columns_bottom_border gt_center" rowspan="2" colspan="1" style="color: #A9A9A9; font-family: &#39;Source Sans Pro&#39;; text-transform: uppercase;" scope="col">evolution</th>
    </tr>
    <tr>
      <th class="gt_col_heading gt_columns_bottom_border gt_right" rowspan="1" colspan="1" style="color: #A9A9A9; font-family: &#39;Source Sans Pro&#39;; text-transform: uppercase;" scope="col">2017</th>
      <th class="gt_col_heading gt_columns_bottom_border gt_right" rowspan="1" colspan="1" style="color: #A9A9A9; font-family: &#39;Source Sans Pro&#39;; text-transform: uppercase;" scope="col">2019</th>
    </tr>
  </thead>
  <tbody class="gt_table_body">
    <tr><td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">Paris (75)</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">2,190.33</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><div>
  <div style="height: 20px;width:70px; background-color: #FFA50033;border-radius:5px;)">
    <div style="height: 13px;width: 13px;background-color: #FFA500;display: inline-block;border-radius:4px;float:left;position:relative;top:17%;left:6%;"></div>
    <div style="display: inline-block;float:right;line-height:20px; font-weight: bold;padding: 0px 2.5px;">D</div>
  </div>
</div></td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">4.33</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">3.24</td>
<td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><?xml version='1.0' encoding='UTF-8' ?><svg xmlns='http://www.w3.org/2000/svg' xmlns:xlink='http://www.w3.org/1999/xlink' class='svglite' width='85.04pt' height='14.17pt' viewBox='0 0 85.04 14.17'><defs>  <style type='text/css'><![CDATA[    .svglite line, .svglite polyline, .svglite polygon, .svglite path, .svglite rect, .svglite circle {      fill: none;      stroke: #000000;      stroke-linecap: round;      stroke-linejoin: round;      stroke-miterlimit: 10.00;    }    .svglite text {      white-space: pre;    }  ]]></style></defs><rect width='100%' height='100%' style='stroke: none; fill: none;'/><defs>  <clipPath id='cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3'>    <rect x='0.00' y='0.00' width='85.04' height='14.17' />  </clipPath></defs><g clip-path='url(#cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3)'><text x='67.43' y='9.11' style='font-size: 5.69px; font-family: "Arial";' textLength='10.24px' lengthAdjust='spacingAndGlyphs'>3.2</text><polyline points='29.63,4.52 63.99,7.49 ' style='stroke-width: 1.07; stroke-linecap: butt;' /><circle cx='63.99' cy='7.49' r='0.89' style='stroke-width: 0.71; fill: #000000;' /><circle cx='63.99' cy='7.49' r='0.89' style='stroke-width: 0.71; stroke: #A020F0; fill: #A020F0;' /><circle cx='29.63' cy='4.52' r='0.89' style='stroke-width: 0.71; stroke: #00FF00; fill: #00FF00;' /></g></svg></td></tr>
    <tr><td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">Marseille (13)</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">862.21</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><div>
  <div style="height: 20px;width:70px; background-color: #FF000033;border-radius:5px;)">
    <div style="height: 13px;width: 13px;background-color: #FF0000;display: inline-block;border-radius:4px;float:left;position:relative;top:17%;left:6%;"></div>
    <div style="display: inline-block;float:right;line-height:20px; font-weight: bold;padding: 0px 2.5px;">G</div>
  </div>
</div></td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">2.18</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">1.96</td>
<td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><?xml version='1.0' encoding='UTF-8' ?><svg xmlns='http://www.w3.org/2000/svg' xmlns:xlink='http://www.w3.org/1999/xlink' class='svglite' width='85.04pt' height='14.17pt' viewBox='0 0 85.04 14.17'><defs>  <style type='text/css'><![CDATA[    .svglite line, .svglite polyline, .svglite polygon, .svglite path, .svglite rect, .svglite circle {      fill: none;      stroke: #000000;      stroke-linecap: round;      stroke-linejoin: round;      stroke-miterlimit: 10.00;    }    .svglite text {      white-space: pre;    }  ]]></style></defs><rect width='100%' height='100%' style='stroke: none; fill: none;'/><defs>  <clipPath id='cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3'>    <rect x='0.00' y='0.00' width='85.04' height='14.17' />  </clipPath></defs><g clip-path='url(#cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3)'><text x='67.43' y='12.57' style='font-size: 5.69px; font-family: "Arial";' textLength='10.24px' lengthAdjust='spacingAndGlyphs'>2.0</text><polyline points='29.63,10.36 63.99,10.95 ' style='stroke-width: 1.07; stroke-linecap: butt;' /><circle cx='63.99' cy='10.95' r='0.89' style='stroke-width: 0.71; fill: #000000;' /><circle cx='63.99' cy='10.95' r='0.89' style='stroke-width: 0.71; stroke: #A020F0; fill: #A020F0;' /><circle cx='29.63' cy='10.36' r='0.89' style='stroke-width: 0.71; stroke: #00FF00; fill: #00FF00;' /></g></svg></td></tr>
    <tr><td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">Lyon (69)</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">515.70</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><div>
  <div style="height: 20px;width:70px; background-color: #FFA50033;border-radius:5px;)">
    <div style="height: 13px;width: 13px;background-color: #FFA500;display: inline-block;border-radius:4px;float:left;position:relative;top:17%;left:6%;"></div>
    <div style="display: inline-block;float:right;line-height:20px; font-weight: bold;padding: 0px 2.5px;">D</div>
  </div>
</div></td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">3.81</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">3.19</td>
<td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><?xml version='1.0' encoding='UTF-8' ?><svg xmlns='http://www.w3.org/2000/svg' xmlns:xlink='http://www.w3.org/1999/xlink' class='svglite' width='85.04pt' height='14.17pt' viewBox='0 0 85.04 14.17'><defs>  <style type='text/css'><![CDATA[    .svglite line, .svglite polyline, .svglite polygon, .svglite path, .svglite rect, .svglite circle {      fill: none;      stroke: #000000;      stroke-linecap: round;      stroke-linejoin: round;      stroke-miterlimit: 10.00;    }    .svglite text {      white-space: pre;    }  ]]></style></defs><rect width='100%' height='100%' style='stroke: none; fill: none;'/><defs>  <clipPath id='cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3'>    <rect x='0.00' y='0.00' width='85.04' height='14.17' />  </clipPath></defs><g clip-path='url(#cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3)'><text x='67.43' y='9.24' style='font-size: 5.69px; font-family: "Arial";' textLength='10.24px' lengthAdjust='spacingAndGlyphs'>3.2</text><polyline points='29.63,5.94 63.99,7.61 ' style='stroke-width: 1.07; stroke-linecap: butt;' /><circle cx='63.99' cy='7.61' r='0.89' style='stroke-width: 0.71; fill: #000000;' /><circle cx='63.99' cy='7.61' r='0.89' style='stroke-width: 0.71; stroke: #A020F0; fill: #A020F0;' /><circle cx='29.63' cy='5.94' r='0.89' style='stroke-width: 0.71; stroke: #00FF00; fill: #00FF00;' /></g></svg></td></tr>
    <tr><td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">Toulouse (31)</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">475.44</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><div>
  <div style="height: 20px;width:70px; background-color: #FF820033;border-radius:5px;)">
    <div style="height: 13px;width: 13px;background-color: #FF8200;display: inline-block;border-radius:4px;float:left;position:relative;top:17%;left:6%;"></div>
    <div style="display: inline-block;float:right;line-height:20px; font-weight: bold;padding: 0px 2.5px;">E</div>
  </div>
</div></td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">3.00</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">2.88</td>
<td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><?xml version='1.0' encoding='UTF-8' ?><svg xmlns='http://www.w3.org/2000/svg' xmlns:xlink='http://www.w3.org/1999/xlink' class='svglite' width='85.04pt' height='14.17pt' viewBox='0 0 85.04 14.17'><defs>  <style type='text/css'><![CDATA[    .svglite line, .svglite polyline, .svglite polygon, .svglite path, .svglite rect, .svglite circle {      fill: none;      stroke: #000000;      stroke-linecap: round;      stroke-linejoin: round;      stroke-miterlimit: 10.00;    }    .svglite text {      white-space: pre;    }  ]]></style></defs><rect width='100%' height='100%' style='stroke: none; fill: none;'/><defs>  <clipPath id='cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3'>    <rect x='0.00' y='0.00' width='85.04' height='14.17' />  </clipPath></defs><g clip-path='url(#cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3)'><text x='67.43' y='10.07' style='font-size: 5.69px; font-family: "Arial";' textLength='10.24px' lengthAdjust='spacingAndGlyphs'>2.9</text><polyline points='29.63,8.14 63.99,8.45 ' style='stroke-width: 1.07; stroke-linecap: butt;' /><circle cx='63.99' cy='8.45' r='0.89' style='stroke-width: 0.71; fill: #000000;' /><circle cx='63.99' cy='8.45' r='0.89' style='stroke-width: 0.71; stroke: #A020F0; fill: #A020F0;' /><circle cx='29.63' cy='8.14' r='0.89' style='stroke-width: 0.71; stroke: #00FF00; fill: #00FF00;' /></g></svg></td></tr>
    <tr><td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">Nice (06)</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">342.64</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><div>
  <div style="height: 20px;width:70px; background-color: #FF580033;border-radius:5px;)">
    <div style="height: 13px;width: 13px;background-color: #FF5800;display: inline-block;border-radius:4px;float:left;position:relative;top:17%;left:6%;"></div>
    <div style="display: inline-block;float:right;line-height:20px; font-weight: bold;padding: 0px 2.5px;">F</div>
  </div>
</div></td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">2.53</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">2.37</td>
<td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><?xml version='1.0' encoding='UTF-8' ?><svg xmlns='http://www.w3.org/2000/svg' xmlns:xlink='http://www.w3.org/1999/xlink' class='svglite' width='85.04pt' height='14.17pt' viewBox='0 0 85.04 14.17'><defs>  <style type='text/css'><![CDATA[    .svglite line, .svglite polyline, .svglite polygon, .svglite path, .svglite rect, .svglite circle {      fill: none;      stroke: #000000;      stroke-linecap: round;      stroke-linejoin: round;      stroke-miterlimit: 10.00;    }    .svglite text {      white-space: pre;    }  ]]></style></defs><rect width='100%' height='100%' style='stroke: none; fill: none;'/><defs>  <clipPath id='cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3'>    <rect x='0.00' y='0.00' width='85.04' height='14.17' />  </clipPath></defs><g clip-path='url(#cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3)'><text x='67.43' y='11.46' style='font-size: 5.69px; font-family: "Arial";' textLength='10.24px' lengthAdjust='spacingAndGlyphs'>2.4</text><polyline points='29.63,9.41 63.99,9.84 ' style='stroke-width: 1.07; stroke-linecap: butt;' /><circle cx='63.99' cy='9.84' r='0.89' style='stroke-width: 0.71; fill: #000000;' /><circle cx='63.99' cy='9.84' r='0.89' style='stroke-width: 0.71; stroke: #A020F0; fill: #A020F0;' /><circle cx='29.63' cy='9.41' r='0.89' style='stroke-width: 0.71; stroke: #00FF00; fill: #00FF00;' /></g></svg></td></tr>
    <tr><td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">Nantes (44)</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">306.69</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><div>
  <div style="height: 20px;width:70px; background-color: #D4C80033;border-radius:5px;)">
    <div style="height: 13px;width: 13px;background-color: #D4C800;display: inline-block;border-radius:4px;float:left;position:relative;top:17%;left:6%;"></div>
    <div style="display: inline-block;float:right;line-height:20px; font-weight: bold;padding: 0px 2.5px;">C</div>
  </div>
</div></td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">3.97</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">3.55</td>
<td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><?xml version='1.0' encoding='UTF-8' ?><svg xmlns='http://www.w3.org/2000/svg' xmlns:xlink='http://www.w3.org/1999/xlink' class='svglite' width='85.04pt' height='14.17pt' viewBox='0 0 85.04 14.17'><defs>  <style type='text/css'><![CDATA[    .svglite line, .svglite polyline, .svglite polygon, .svglite path, .svglite rect, .svglite circle {      fill: none;      stroke: #000000;      stroke-linecap: round;      stroke-linejoin: round;      stroke-miterlimit: 10.00;    }    .svglite text {      white-space: pre;    }  ]]></style></defs><rect width='100%' height='100%' style='stroke: none; fill: none;'/><defs>  <clipPath id='cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3'>    <rect x='0.00' y='0.00' width='85.04' height='14.17' />  </clipPath></defs><g clip-path='url(#cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3)'><text x='67.43' y='8.27' style='font-size: 5.69px; font-family: "Arial";' textLength='10.24px' lengthAdjust='spacingAndGlyphs'>3.5</text><polyline points='29.63,5.50 63.99,6.65 ' style='stroke-width: 1.07; stroke-linecap: butt;' /><circle cx='63.99' cy='6.65' r='0.89' style='stroke-width: 0.71; fill: #000000;' /><circle cx='63.99' cy='6.65' r='0.89' style='stroke-width: 0.71; stroke: #A020F0; fill: #A020F0;' /><circle cx='29.63' cy='5.50' r='0.89' style='stroke-width: 0.71; stroke: #00FF00; fill: #00FF00;' /></g></svg></td></tr>
    <tr><td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">Montpellier (34)</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">281.61</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><div>
  <div style="height: 20px;width:70px; background-color: #FF580033;border-radius:5px;)">
    <div style="height: 13px;width: 13px;background-color: #FF5800;display: inline-block;border-radius:4px;float:left;position:relative;top:17%;left:6%;"></div>
    <div style="display: inline-block;float:right;line-height:20px; font-weight: bold;padding: 0px 2.5px;">F</div>
  </div>
</div></td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">2.39</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">2.48</td>
<td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><?xml version='1.0' encoding='UTF-8' ?><svg xmlns='http://www.w3.org/2000/svg' xmlns:xlink='http://www.w3.org/1999/xlink' class='svglite' width='85.04pt' height='14.17pt' viewBox='0 0 85.04 14.17'><defs>  <style type='text/css'><![CDATA[    .svglite line, .svglite polyline, .svglite polygon, .svglite path, .svglite rect, .svglite circle {      fill: none;      stroke: #000000;      stroke-linecap: round;      stroke-linejoin: round;      stroke-miterlimit: 10.00;    }    .svglite text {      white-space: pre;    }  ]]></style></defs><rect width='100%' height='100%' style='stroke: none; fill: none;'/><defs>  <clipPath id='cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3'>    <rect x='0.00' y='0.00' width='85.04' height='14.17' />  </clipPath></defs><g clip-path='url(#cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3)'><text x='67.43' y='11.16' style='font-size: 5.69px; font-family: "Arial";' textLength='10.24px' lengthAdjust='spacingAndGlyphs'>2.5</text><polyline points='29.63,9.77 63.99,9.54 ' style='stroke-width: 1.07; stroke-linecap: butt;' /><circle cx='63.99' cy='9.54' r='0.89' style='stroke-width: 0.71; fill: #000000;' /><circle cx='29.63' cy='9.77' r='0.89' style='stroke-width: 0.71; stroke: #A020F0; fill: #A020F0;' /><circle cx='63.99' cy='9.54' r='0.89' style='stroke-width: 0.71; stroke: #00FF00; fill: #00FF00;' /></g></svg></td></tr>
    <tr><td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">Strasbourg (67)</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">279.28</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><div>
  <div style="height: 20px;width:70px; background-color: #9BE50033;border-radius:5px;)">
    <div style="height: 13px;width: 13px;background-color: #9BE500;display: inline-block;border-radius:4px;float:left;position:relative;top:17%;left:6%;"></div>
    <div style="display: inline-block;float:right;line-height:20px; font-weight: bold;padding: 0px 2.5px;">B</div>
  </div>
</div></td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">4.43</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">4.02</td>
<td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><?xml version='1.0' encoding='UTF-8' ?><svg xmlns='http://www.w3.org/2000/svg' xmlns:xlink='http://www.w3.org/1999/xlink' class='svglite' width='85.04pt' height='14.17pt' viewBox='0 0 85.04 14.17'><defs>  <style type='text/css'><![CDATA[    .svglite line, .svglite polyline, .svglite polygon, .svglite path, .svglite rect, .svglite circle {      fill: none;      stroke: #000000;      stroke-linecap: round;      stroke-linejoin: round;      stroke-miterlimit: 10.00;    }    .svglite text {      white-space: pre;    }  ]]></style></defs><rect width='100%' height='100%' style='stroke: none; fill: none;'/><defs>  <clipPath id='cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3'>    <rect x='0.00' y='0.00' width='85.04' height='14.17' />  </clipPath></defs><g clip-path='url(#cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3)'><text x='67.43' y='6.99' style='font-size: 5.69px; font-family: "Arial";' textLength='10.24px' lengthAdjust='spacingAndGlyphs'>4.0</text><polyline points='29.63,4.27 63.99,5.36 ' style='stroke-width: 1.07; stroke-linecap: butt;' /><circle cx='63.99' cy='5.36' r='0.89' style='stroke-width: 0.71; fill: #000000;' /><circle cx='63.99' cy='5.36' r='0.89' style='stroke-width: 0.71; stroke: #A020F0; fill: #A020F0;' /><circle cx='29.63' cy='4.27' r='0.89' style='stroke-width: 0.71; stroke: #00FF00; fill: #00FF00;' /></g></svg></td></tr>
    <tr><td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">Bordeaux (33)</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">252.04</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><div>
  <div style="height: 20px;width:70px; background-color: #FFA50033;border-radius:5px;)">
    <div style="height: 13px;width: 13px;background-color: #FFA500;display: inline-block;border-radius:4px;float:left;position:relative;top:17%;left:6%;"></div>
    <div style="display: inline-block;float:right;line-height:20px; font-weight: bold;padding: 0px 2.5px;">D</div>
  </div>
</div></td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">3.34</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">3.24</td>
<td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><?xml version='1.0' encoding='UTF-8' ?><svg xmlns='http://www.w3.org/2000/svg' xmlns:xlink='http://www.w3.org/1999/xlink' class='svglite' width='85.04pt' height='14.17pt' viewBox='0 0 85.04 14.17'><defs>  <style type='text/css'><![CDATA[    .svglite line, .svglite polyline, .svglite polygon, .svglite path, .svglite rect, .svglite circle {      fill: none;      stroke: #000000;      stroke-linecap: round;      stroke-linejoin: round;      stroke-miterlimit: 10.00;    }    .svglite text {      white-space: pre;    }  ]]></style></defs><rect width='100%' height='100%' style='stroke: none; fill: none;'/><defs>  <clipPath id='cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3'>    <rect x='0.00' y='0.00' width='85.04' height='14.17' />  </clipPath></defs><g clip-path='url(#cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3)'><text x='67.43' y='9.11' style='font-size: 5.69px; font-family: "Arial";' textLength='10.24px' lengthAdjust='spacingAndGlyphs'>3.2</text><polyline points='29.63,7.22 63.99,7.49 ' style='stroke-width: 1.07; stroke-linecap: butt;' /><circle cx='63.99' cy='7.49' r='0.89' style='stroke-width: 0.71; fill: #000000;' /><circle cx='63.99' cy='7.49' r='0.89' style='stroke-width: 0.71; stroke: #A020F0; fill: #A020F0;' /><circle cx='29.63' cy='7.22' r='0.89' style='stroke-width: 0.71; stroke: #00FF00; fill: #00FF00;' /></g></svg></td></tr>
    <tr><td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">Lille (59)</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">232.44</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><div>
  <div style="height: 20px;width:70px; background-color: #FF820033;border-radius:5px;)">
    <div style="height: 13px;width: 13px;background-color: #FF8200;display: inline-block;border-radius:4px;float:left;position:relative;top:17%;left:6%;"></div>
    <div style="display: inline-block;float:right;line-height:20px; font-weight: bold;padding: 0px 2.5px;">E</div>
  </div>
</div></td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">3.40</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">3.03</td>
<td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><?xml version='1.0' encoding='UTF-8' ?><svg xmlns='http://www.w3.org/2000/svg' xmlns:xlink='http://www.w3.org/1999/xlink' class='svglite' width='85.04pt' height='14.17pt' viewBox='0 0 85.04 14.17'><defs>  <style type='text/css'><![CDATA[    .svglite line, .svglite polyline, .svglite polygon, .svglite path, .svglite rect, .svglite circle {      fill: none;      stroke: #000000;      stroke-linecap: round;      stroke-linejoin: round;      stroke-miterlimit: 10.00;    }    .svglite text {      white-space: pre;    }  ]]></style></defs><rect width='100%' height='100%' style='stroke: none; fill: none;'/><defs>  <clipPath id='cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3'>    <rect x='0.00' y='0.00' width='85.04' height='14.17' />  </clipPath></defs><g clip-path='url(#cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3)'><text x='67.43' y='9.67' style='font-size: 5.69px; font-family: "Arial";' textLength='10.24px' lengthAdjust='spacingAndGlyphs'>3.0</text><polyline points='29.63,7.04 63.99,8.05 ' style='stroke-width: 1.07; stroke-linecap: butt;' /><circle cx='63.99' cy='8.05' r='0.89' style='stroke-width: 0.71; fill: #000000;' /><circle cx='63.99' cy='8.05' r='0.89' style='stroke-width: 0.71; stroke: #A020F0; fill: #A020F0;' /><circle cx='29.63' cy='7.04' r='0.89' style='stroke-width: 0.71; stroke: #00FF00; fill: #00FF00;' /></g></svg></td></tr>
    <tr><td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">Rennes (35)</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">216.27</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><div>
  <div style="height: 20px;width:70px; background-color: #FFA50033;border-radius:5px;)">
    <div style="height: 13px;width: 13px;background-color: #FFA500;display: inline-block;border-radius:4px;float:left;position:relative;top:17%;left:6%;"></div>
    <div style="display: inline-block;float:right;line-height:20px; font-weight: bold;padding: 0px 2.5px;">D</div>
  </div>
</div></td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">4.29</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">3.46</td>
<td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><?xml version='1.0' encoding='UTF-8' ?><svg xmlns='http://www.w3.org/2000/svg' xmlns:xlink='http://www.w3.org/1999/xlink' class='svglite' width='85.04pt' height='14.17pt' viewBox='0 0 85.04 14.17'><defs>  <style type='text/css'><![CDATA[    .svglite line, .svglite polyline, .svglite polygon, .svglite path, .svglite rect, .svglite circle {      fill: none;      stroke: #000000;      stroke-linecap: round;      stroke-linejoin: round;      stroke-miterlimit: 10.00;    }    .svglite text {      white-space: pre;    }  ]]></style></defs><rect width='100%' height='100%' style='stroke: none; fill: none;'/><defs>  <clipPath id='cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3'>    <rect x='0.00' y='0.00' width='85.04' height='14.17' />  </clipPath></defs><g clip-path='url(#cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3)'><text x='67.43' y='8.50' style='font-size: 5.69px; font-family: "Arial";' textLength='10.24px' lengthAdjust='spacingAndGlyphs'>3.5</text><polyline points='29.63,4.64 63.99,6.87 ' style='stroke-width: 1.07; stroke-linecap: butt;' /><circle cx='63.99' cy='6.87' r='0.89' style='stroke-width: 0.71; fill: #000000;' /><circle cx='63.99' cy='6.87' r='0.89' style='stroke-width: 0.71; stroke: #A020F0; fill: #A020F0;' /><circle cx='29.63' cy='4.64' r='0.89' style='stroke-width: 0.71; stroke: #00FF00; fill: #00FF00;' /></g></svg></td></tr>
    <tr><td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">Reims (51)</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">183.11</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><div>
  <div style="height: 20px;width:70px; background-color: #FF580033;border-radius:5px;)">
    <div style="height: 13px;width: 13px;background-color: #FF5800;display: inline-block;border-radius:4px;float:left;position:relative;top:17%;left:6%;"></div>
    <div style="display: inline-block;float:right;line-height:20px; font-weight: bold;padding: 0px 2.5px;">F</div>
  </div>
</div></td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">2.95</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">2.55</td>
<td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><?xml version='1.0' encoding='UTF-8' ?><svg xmlns='http://www.w3.org/2000/svg' xmlns:xlink='http://www.w3.org/1999/xlink' class='svglite' width='85.04pt' height='14.17pt' viewBox='0 0 85.04 14.17'><defs>  <style type='text/css'><![CDATA[    .svglite line, .svglite polyline, .svglite polygon, .svglite path, .svglite rect, .svglite circle {      fill: none;      stroke: #000000;      stroke-linecap: round;      stroke-linejoin: round;      stroke-miterlimit: 10.00;    }    .svglite text {      white-space: pre;    }  ]]></style></defs><rect width='100%' height='100%' style='stroke: none; fill: none;'/><defs>  <clipPath id='cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3'>    <rect x='0.00' y='0.00' width='85.04' height='14.17' />  </clipPath></defs><g clip-path='url(#cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3)'><text x='67.43' y='10.98' style='font-size: 5.69px; font-family: "Arial";' textLength='10.24px' lengthAdjust='spacingAndGlyphs'>2.5</text><polyline points='29.63,8.27 63.99,9.36 ' style='stroke-width: 1.07; stroke-linecap: butt;' /><circle cx='63.99' cy='9.36' r='0.89' style='stroke-width: 0.71; fill: #000000;' /><circle cx='63.99' cy='9.36' r='0.89' style='stroke-width: 0.71; stroke: #A020F0; fill: #A020F0;' /><circle cx='29.63' cy='8.27' r='0.89' style='stroke-width: 0.71; stroke: #00FF00; fill: #00FF00;' /></g></svg></td></tr>
    <tr><td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">Saint-Étienne (42)</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">171.92</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><div>
  <div style="height: 20px;width:70px; background-color: #FF580033;border-radius:5px;)">
    <div style="height: 13px;width: 13px;background-color: #FF5800;display: inline-block;border-radius:4px;float:left;position:relative;top:17%;left:6%;"></div>
    <div style="display: inline-block;float:right;line-height:20px; font-weight: bold;padding: 0px 2.5px;">F</div>
  </div>
</div></td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">2.81</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">2.53</td>
<td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><?xml version='1.0' encoding='UTF-8' ?><svg xmlns='http://www.w3.org/2000/svg' xmlns:xlink='http://www.w3.org/1999/xlink' class='svglite' width='85.04pt' height='14.17pt' viewBox='0 0 85.04 14.17'><defs>  <style type='text/css'><![CDATA[    .svglite line, .svglite polyline, .svglite polygon, .svglite path, .svglite rect, .svglite circle {      fill: none;      stroke: #000000;      stroke-linecap: round;      stroke-linejoin: round;      stroke-miterlimit: 10.00;    }    .svglite text {      white-space: pre;    }  ]]></style></defs><rect width='100%' height='100%' style='stroke: none; fill: none;'/><defs>  <clipPath id='cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3'>    <rect x='0.00' y='0.00' width='85.04' height='14.17' />  </clipPath></defs><g clip-path='url(#cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3)'><text x='67.43' y='11.02' style='font-size: 5.69px; font-family: "Arial";' textLength='10.24px' lengthAdjust='spacingAndGlyphs'>2.5</text><polyline points='29.63,8.66 63.99,9.40 ' style='stroke-width: 1.07; stroke-linecap: butt;' /><circle cx='63.99' cy='9.40' r='0.89' style='stroke-width: 0.71; fill: #000000;' /><circle cx='63.99' cy='9.40' r='0.89' style='stroke-width: 0.71; stroke: #A020F0; fill: #A020F0;' /><circle cx='29.63' cy='8.66' r='0.89' style='stroke-width: 0.71; stroke: #00FF00; fill: #00FF00;' /></g></svg></td></tr>
    <tr><td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">Le Havre (76)</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">170.35</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><div>
  <div style="height: 20px;width:70px; background-color: #FFA50033;border-radius:5px;)">
    <div style="height: 13px;width: 13px;background-color: #FFA500;display: inline-block;border-radius:4px;float:left;position:relative;top:17%;left:6%;"></div>
    <div style="display: inline-block;float:right;line-height:20px; font-weight: bold;padding: 0px 2.5px;">D</div>
  </div>
</div></td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">3.60</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">3.13</td>
<td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><?xml version='1.0' encoding='UTF-8' ?><svg xmlns='http://www.w3.org/2000/svg' xmlns:xlink='http://www.w3.org/1999/xlink' class='svglite' width='85.04pt' height='14.17pt' viewBox='0 0 85.04 14.17'><defs>  <style type='text/css'><![CDATA[    .svglite line, .svglite polyline, .svglite polygon, .svglite path, .svglite rect, .svglite circle {      fill: none;      stroke: #000000;      stroke-linecap: round;      stroke-linejoin: round;      stroke-miterlimit: 10.00;    }    .svglite text {      white-space: pre;    }  ]]></style></defs><rect width='100%' height='100%' style='stroke: none; fill: none;'/><defs>  <clipPath id='cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3'>    <rect x='0.00' y='0.00' width='85.04' height='14.17' />  </clipPath></defs><g clip-path='url(#cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3)'><text x='67.43' y='9.41' style='font-size: 5.69px; font-family: "Arial";' textLength='10.24px' lengthAdjust='spacingAndGlyphs'>3.1</text><polyline points='29.63,6.52 63.99,7.78 ' style='stroke-width: 1.07; stroke-linecap: butt;' /><circle cx='63.99' cy='7.78' r='0.89' style='stroke-width: 0.71; fill: #000000;' /><circle cx='63.99' cy='7.78' r='0.89' style='stroke-width: 0.71; stroke: #A020F0; fill: #A020F0;' /><circle cx='29.63' cy='6.52' r='0.89' style='stroke-width: 0.71; stroke: #00FF00; fill: #00FF00;' /></g></svg></td></tr>
    <tr><td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">Toulon (83)</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">169.63</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><div>
  <div style="height: 20px;width:70px; background-color: #FF580033;border-radius:5px;)">
    <div style="height: 13px;width: 13px;background-color: #FF5800;display: inline-block;border-radius:4px;float:left;position:relative;top:17%;left:6%;"></div>
    <div style="display: inline-block;float:right;line-height:20px; font-weight: bold;padding: 0px 2.5px;">F</div>
  </div>
</div></td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">2.32</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">2.31</td>
<td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><?xml version='1.0' encoding='UTF-8' ?><svg xmlns='http://www.w3.org/2000/svg' xmlns:xlink='http://www.w3.org/1999/xlink' class='svglite' width='85.04pt' height='14.17pt' viewBox='0 0 85.04 14.17'><defs>  <style type='text/css'><![CDATA[    .svglite line, .svglite polyline, .svglite polygon, .svglite path, .svglite rect, .svglite circle {      fill: none;      stroke: #000000;      stroke-linecap: round;      stroke-linejoin: round;      stroke-miterlimit: 10.00;    }    .svglite text {      white-space: pre;    }  ]]></style></defs><rect width='100%' height='100%' style='stroke: none; fill: none;'/><defs>  <clipPath id='cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3'>    <rect x='0.00' y='0.00' width='85.04' height='14.17' />  </clipPath></defs><g clip-path='url(#cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3)'><text x='67.43' y='11.62' style='font-size: 5.69px; font-family: "Arial";' textLength='10.24px' lengthAdjust='spacingAndGlyphs'>2.3</text><polyline points='29.63,9.98 63.99,10.00 ' style='stroke-width: 1.07; stroke-linecap: butt;' /><circle cx='63.99' cy='10.00' r='0.89' style='stroke-width: 0.71; fill: #000000;' /><circle cx='63.99' cy='10.00' r='0.89' style='stroke-width: 0.71; stroke: #A020F0; fill: #A020F0;' /><circle cx='29.63' cy='9.98' r='0.89' style='stroke-width: 0.71; stroke: #00FF00; fill: #00FF00;' /></g></svg></td></tr>
    <tr><td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">Grenoble (38)</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">158.18</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><div>
  <div style="height: 20px;width:70px; background-color: #9BE50033;border-radius:5px;)">
    <div style="height: 13px;width: 13px;background-color: #9BE500;display: inline-block;border-radius:4px;float:left;position:relative;top:17%;left:6%;"></div>
    <div style="display: inline-block;float:right;line-height:20px; font-weight: bold;padding: 0px 2.5px;">B</div>
  </div>
</div></td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">5.32</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">4.12</td>
<td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><?xml version='1.0' encoding='UTF-8' ?><svg xmlns='http://www.w3.org/2000/svg' xmlns:xlink='http://www.w3.org/1999/xlink' class='svglite' width='85.04pt' height='14.17pt' viewBox='0 0 85.04 14.17'><defs>  <style type='text/css'><![CDATA[    .svglite line, .svglite polyline, .svglite polygon, .svglite path, .svglite rect, .svglite circle {      fill: none;      stroke: #000000;      stroke-linecap: round;      stroke-linejoin: round;      stroke-miterlimit: 10.00;    }    .svglite text {      white-space: pre;    }  ]]></style></defs><rect width='100%' height='100%' style='stroke: none; fill: none;'/><defs>  <clipPath id='cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3'>    <rect x='0.00' y='0.00' width='85.04' height='14.17' />  </clipPath></defs><g clip-path='url(#cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3)'><text x='67.43' y='6.71' style='font-size: 5.69px; font-family: "Arial";' textLength='10.24px' lengthAdjust='spacingAndGlyphs'>4.1</text><polyline points='29.63,1.84 63.99,5.09 ' style='stroke-width: 1.07; stroke-linecap: butt;' /><circle cx='63.99' cy='5.09' r='0.89' style='stroke-width: 0.71; fill: #000000;' /><circle cx='63.99' cy='5.09' r='0.89' style='stroke-width: 0.71; stroke: #A020F0; fill: #A020F0;' /><circle cx='29.63' cy='1.84' r='0.89' style='stroke-width: 0.71; stroke: #00FF00; fill: #00FF00;' /></g></svg></td></tr>
    <tr><td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">Dijon (21)</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">155.09</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><div>
  <div style="height: 20px;width:70px; background-color: #FFA50033;border-radius:5px;)">
    <div style="height: 13px;width: 13px;background-color: #FFA500;display: inline-block;border-radius:4px;float:left;position:relative;top:17%;left:6%;"></div>
    <div style="display: inline-block;float:right;line-height:20px; font-weight: bold;padding: 0px 2.5px;">D</div>
  </div>
</div></td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">3.39</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">3.20</td>
<td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><?xml version='1.0' encoding='UTF-8' ?><svg xmlns='http://www.w3.org/2000/svg' xmlns:xlink='http://www.w3.org/1999/xlink' class='svglite' width='85.04pt' height='14.17pt' viewBox='0 0 85.04 14.17'><defs>  <style type='text/css'><![CDATA[    .svglite line, .svglite polyline, .svglite polygon, .svglite path, .svglite rect, .svglite circle {      fill: none;      stroke: #000000;      stroke-linecap: round;      stroke-linejoin: round;      stroke-miterlimit: 10.00;    }    .svglite text {      white-space: pre;    }  ]]></style></defs><rect width='100%' height='100%' style='stroke: none; fill: none;'/><defs>  <clipPath id='cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3'>    <rect x='0.00' y='0.00' width='85.04' height='14.17' />  </clipPath></defs><g clip-path='url(#cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3)'><text x='67.43' y='9.21' style='font-size: 5.69px; font-family: "Arial";' textLength='10.24px' lengthAdjust='spacingAndGlyphs'>3.2</text><polyline points='29.63,7.08 63.99,7.59 ' style='stroke-width: 1.07; stroke-linecap: butt;' /><circle cx='63.99' cy='7.59' r='0.89' style='stroke-width: 0.71; fill: #000000;' /><circle cx='63.99' cy='7.59' r='0.89' style='stroke-width: 0.71; stroke: #A020F0; fill: #A020F0;' /><circle cx='29.63' cy='7.08' r='0.89' style='stroke-width: 0.71; stroke: #00FF00; fill: #00FF00;' /></g></svg></td></tr>
    <tr><td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">Angers (49)</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">151.23</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><div>
  <div style="height: 20px;width:70px; background-color: #FFA50033;border-radius:5px;)">
    <div style="height: 13px;width: 13px;background-color: #FFA500;display: inline-block;border-radius:4px;float:left;position:relative;top:17%;left:6%;"></div>
    <div style="display: inline-block;float:right;line-height:20px; font-weight: bold;padding: 0px 2.5px;">D</div>
  </div>
</div></td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">3.58</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">3.38</td>
<td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><?xml version='1.0' encoding='UTF-8' ?><svg xmlns='http://www.w3.org/2000/svg' xmlns:xlink='http://www.w3.org/1999/xlink' class='svglite' width='85.04pt' height='14.17pt' viewBox='0 0 85.04 14.17'><defs>  <style type='text/css'><![CDATA[    .svglite line, .svglite polyline, .svglite polygon, .svglite path, .svglite rect, .svglite circle {      fill: none;      stroke: #000000;      stroke-linecap: round;      stroke-linejoin: round;      stroke-miterlimit: 10.00;    }    .svglite text {      white-space: pre;    }  ]]></style></defs><rect width='100%' height='100%' style='stroke: none; fill: none;'/><defs>  <clipPath id='cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3'>    <rect x='0.00' y='0.00' width='85.04' height='14.17' />  </clipPath></defs><g clip-path='url(#cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3)'><text x='67.43' y='8.73' style='font-size: 5.69px; font-family: "Arial";' textLength='10.24px' lengthAdjust='spacingAndGlyphs'>3.4</text><polyline points='29.63,6.57 63.99,7.11 ' style='stroke-width: 1.07; stroke-linecap: butt;' /><circle cx='63.99' cy='7.11' r='0.89' style='stroke-width: 0.71; fill: #000000;' /><circle cx='63.99' cy='7.11' r='0.89' style='stroke-width: 0.71; stroke: #A020F0; fill: #A020F0;' /><circle cx='29.63' cy='6.57' r='0.89' style='stroke-width: 0.71; stroke: #00FF00; fill: #00FF00;' /></g></svg></td></tr>
    <tr><td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">Nîmes (30)</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">151.00</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><div>
  <div style="height: 20px;width:70px; background-color: #FF000033;border-radius:5px;)">
    <div style="height: 13px;width: 13px;background-color: #FF0000;display: inline-block;border-radius:4px;float:left;position:relative;top:17%;left:6%;"></div>
    <div style="display: inline-block;float:right;line-height:20px; font-weight: bold;padding: 0px 2.5px;">G</div>
  </div>
</div></td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">2.43</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">2.27</td>
<td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><?xml version='1.0' encoding='UTF-8' ?><svg xmlns='http://www.w3.org/2000/svg' xmlns:xlink='http://www.w3.org/1999/xlink' class='svglite' width='85.04pt' height='14.17pt' viewBox='0 0 85.04 14.17'><defs>  <style type='text/css'><![CDATA[    .svglite line, .svglite polyline, .svglite polygon, .svglite path, .svglite rect, .svglite circle {      fill: none;      stroke: #000000;      stroke-linecap: round;      stroke-linejoin: round;      stroke-miterlimit: 10.00;    }    .svglite text {      white-space: pre;    }  ]]></style></defs><rect width='100%' height='100%' style='stroke: none; fill: none;'/><defs>  <clipPath id='cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3'>    <rect x='0.00' y='0.00' width='85.04' height='14.17' />  </clipPath></defs><g clip-path='url(#cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3)'><text x='67.43' y='11.74' style='font-size: 5.69px; font-family: "Arial";' textLength='10.24px' lengthAdjust='spacingAndGlyphs'>2.3</text><polyline points='29.63,9.67 63.99,10.12 ' style='stroke-width: 1.07; stroke-linecap: butt;' /><circle cx='63.99' cy='10.12' r='0.89' style='stroke-width: 0.71; fill: #000000;' /><circle cx='63.99' cy='10.12' r='0.89' style='stroke-width: 0.71; stroke: #A020F0; fill: #A020F0;' /><circle cx='29.63' cy='9.67' r='0.89' style='stroke-width: 0.71; stroke: #00FF00; fill: #00FF00;' /></g></svg></td></tr>
    <tr><td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">Villeurbanne (69)</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">149.02</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><div>
  <div style="height: 20px;width:70px; background-color: #FF820033;border-radius:5px;)">
    <div style="height: 13px;width: 13px;background-color: #FF8200;display: inline-block;border-radius:4px;float:left;position:relative;top:17%;left:6%;"></div>
    <div style="display: inline-block;float:right;line-height:20px; font-weight: bold;padding: 0px 2.5px;">E</div>
  </div>
</div></td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">3.41</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">3.03</td>
<td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><?xml version='1.0' encoding='UTF-8' ?><svg xmlns='http://www.w3.org/2000/svg' xmlns:xlink='http://www.w3.org/1999/xlink' class='svglite' width='85.04pt' height='14.17pt' viewBox='0 0 85.04 14.17'><defs>  <style type='text/css'><![CDATA[    .svglite line, .svglite polyline, .svglite polygon, .svglite path, .svglite rect, .svglite circle {      fill: none;      stroke: #000000;      stroke-linecap: round;      stroke-linejoin: round;      stroke-miterlimit: 10.00;    }    .svglite text {      white-space: pre;    }  ]]></style></defs><rect width='100%' height='100%' style='stroke: none; fill: none;'/><defs>  <clipPath id='cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3'>    <rect x='0.00' y='0.00' width='85.04' height='14.17' />  </clipPath></defs><g clip-path='url(#cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3)'><text x='67.43' y='9.68' style='font-size: 5.69px; font-family: "Arial";' textLength='10.24px' lengthAdjust='spacingAndGlyphs'>3.0</text><polyline points='29.63,7.03 63.99,8.06 ' style='stroke-width: 1.07; stroke-linecap: butt;' /><circle cx='63.99' cy='8.06' r='0.89' style='stroke-width: 0.71; fill: #000000;' /><circle cx='63.99' cy='8.06' r='0.89' style='stroke-width: 0.71; stroke: #A020F0; fill: #A020F0;' /><circle cx='29.63' cy='7.03' r='0.89' style='stroke-width: 0.71; stroke: #00FF00; fill: #00FF00;' /></g></svg></td></tr>
    <tr><td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">Saint-Denis (974)</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">147.92</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><div>
  <div style="height: 20px;width:70px; background-color: #FF000033;border-radius:5px;)">
    <div style="height: 13px;width: 13px;background-color: #FF0000;display: inline-block;border-radius:4px;float:left;position:relative;top:17%;left:6%;"></div>
    <div style="display: inline-block;float:right;line-height:20px; font-weight: bold;padding: 0px 2.5px;">G</div>
  </div>
</div></td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">1.86</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">2.09</td>
<td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><?xml version='1.0' encoding='UTF-8' ?><svg xmlns='http://www.w3.org/2000/svg' xmlns:xlink='http://www.w3.org/1999/xlink' class='svglite' width='85.04pt' height='14.17pt' viewBox='0 0 85.04 14.17'><defs>  <style type='text/css'><![CDATA[    .svglite line, .svglite polyline, .svglite polygon, .svglite path, .svglite rect, .svglite circle {      fill: none;      stroke: #000000;      stroke-linecap: round;      stroke-linejoin: round;      stroke-miterlimit: 10.00;    }    .svglite text {      white-space: pre;    }  ]]></style></defs><rect width='100%' height='100%' style='stroke: none; fill: none;'/><defs>  <clipPath id='cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3'>    <rect x='0.00' y='0.00' width='85.04' height='14.17' />  </clipPath></defs><g clip-path='url(#cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3)'><text x='67.43' y='12.22' style='font-size: 5.69px; font-family: "Arial";' textLength='10.24px' lengthAdjust='spacingAndGlyphs'>2.1</text><polyline points='29.63,11.21 63.99,10.59 ' style='stroke-width: 1.07; stroke-linecap: butt;' /><circle cx='63.99' cy='10.59' r='0.89' style='stroke-width: 0.71; fill: #000000;' /><circle cx='29.63' cy='11.21' r='0.89' style='stroke-width: 0.71; stroke: #A020F0; fill: #A020F0;' /><circle cx='63.99' cy='10.59' r='0.89' style='stroke-width: 0.71; stroke: #00FF00; fill: #00FF00;' /></g></svg></td></tr>
    <tr><td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">Aix-en-Provence (13)</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">143.01</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><div>
  <div style="height: 20px;width:70px; background-color: #FF580033;border-radius:5px;)">
    <div style="height: 13px;width: 13px;background-color: #FF5800;display: inline-block;border-radius:4px;float:left;position:relative;top:17%;left:6%;"></div>
    <div style="display: inline-block;float:right;line-height:20px; font-weight: bold;padding: 0px 2.5px;">F</div>
  </div>
</div></td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">2.64</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">2.40</td>
<td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><?xml version='1.0' encoding='UTF-8' ?><svg xmlns='http://www.w3.org/2000/svg' xmlns:xlink='http://www.w3.org/1999/xlink' class='svglite' width='85.04pt' height='14.17pt' viewBox='0 0 85.04 14.17'><defs>  <style type='text/css'><![CDATA[    .svglite line, .svglite polyline, .svglite polygon, .svglite path, .svglite rect, .svglite circle {      fill: none;      stroke: #000000;      stroke-linecap: round;      stroke-linejoin: round;      stroke-miterlimit: 10.00;    }    .svglite text {      white-space: pre;    }  ]]></style></defs><rect width='100%' height='100%' style='stroke: none; fill: none;'/><defs>  <clipPath id='cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3'>    <rect x='0.00' y='0.00' width='85.04' height='14.17' />  </clipPath></defs><g clip-path='url(#cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3)'><text x='67.43' y='11.39' style='font-size: 5.69px; font-family: "Arial";' textLength='10.24px' lengthAdjust='spacingAndGlyphs'>2.4</text><polyline points='29.63,9.11 63.99,9.76 ' style='stroke-width: 1.07; stroke-linecap: butt;' /><circle cx='63.99' cy='9.76' r='0.89' style='stroke-width: 0.71; fill: #000000;' /><circle cx='63.99' cy='9.76' r='0.89' style='stroke-width: 0.71; stroke: #A020F0; fill: #A020F0;' /><circle cx='29.63' cy='9.11' r='0.89' style='stroke-width: 0.71; stroke: #00FF00; fill: #00FF00;' /></g></svg></td></tr>
    <tr><td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">Le Mans (72)</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">142.99</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><div>
  <div style="height: 20px;width:70px; background-color: #FF820033;border-radius:5px;)">
    <div style="height: 13px;width: 13px;background-color: #FF8200;display: inline-block;border-radius:4px;float:left;position:relative;top:17%;left:6%;"></div>
    <div style="display: inline-block;float:right;line-height:20px; font-weight: bold;padding: 0px 2.5px;">E</div>
  </div>
</div></td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">3.47</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">2.98</td>
<td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><?xml version='1.0' encoding='UTF-8' ?><svg xmlns='http://www.w3.org/2000/svg' xmlns:xlink='http://www.w3.org/1999/xlink' class='svglite' width='85.04pt' height='14.17pt' viewBox='0 0 85.04 14.17'><defs>  <style type='text/css'><![CDATA[    .svglite line, .svglite polyline, .svglite polygon, .svglite path, .svglite rect, .svglite circle {      fill: none;      stroke: #000000;      stroke-linecap: round;      stroke-linejoin: round;      stroke-miterlimit: 10.00;    }    .svglite text {      white-space: pre;    }  ]]></style></defs><rect width='100%' height='100%' style='stroke: none; fill: none;'/><defs>  <clipPath id='cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3'>    <rect x='0.00' y='0.00' width='85.04' height='14.17' />  </clipPath></defs><g clip-path='url(#cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3)'><text x='67.43' y='9.81' style='font-size: 5.69px; font-family: "Arial";' textLength='10.24px' lengthAdjust='spacingAndGlyphs'>3.0</text><polyline points='29.63,6.86 63.99,8.18 ' style='stroke-width: 1.07; stroke-linecap: butt;' /><circle cx='63.99' cy='8.18' r='0.89' style='stroke-width: 0.71; fill: #000000;' /><circle cx='63.99' cy='8.18' r='0.89' style='stroke-width: 0.71; stroke: #A020F0; fill: #A020F0;' /><circle cx='29.63' cy='6.86' r='0.89' style='stroke-width: 0.71; stroke: #00FF00; fill: #00FF00;' /></g></svg></td></tr>
    <tr><td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">Clermont-Ferrand (63)</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">142.69</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><div>
  <div style="height: 20px;width:70px; background-color: #FF820033;border-radius:5px;)">
    <div style="height: 13px;width: 13px;background-color: #FF8200;display: inline-block;border-radius:4px;float:left;position:relative;top:17%;left:6%;"></div>
    <div style="display: inline-block;float:right;line-height:20px; font-weight: bold;padding: 0px 2.5px;">E</div>
  </div>
</div></td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">2.92</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">2.72</td>
<td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><?xml version='1.0' encoding='UTF-8' ?><svg xmlns='http://www.w3.org/2000/svg' xmlns:xlink='http://www.w3.org/1999/xlink' class='svglite' width='85.04pt' height='14.17pt' viewBox='0 0 85.04 14.17'><defs>  <style type='text/css'><![CDATA[    .svglite line, .svglite polyline, .svglite polygon, .svglite path, .svglite rect, .svglite circle {      fill: none;      stroke: #000000;      stroke-linecap: round;      stroke-linejoin: round;      stroke-miterlimit: 10.00;    }    .svglite text {      white-space: pre;    }  ]]></style></defs><rect width='100%' height='100%' style='stroke: none; fill: none;'/><defs>  <clipPath id='cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3'>    <rect x='0.00' y='0.00' width='85.04' height='14.17' />  </clipPath></defs><g clip-path='url(#cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3)'><text x='67.43' y='10.53' style='font-size: 5.69px; font-family: "Arial";' textLength='10.24px' lengthAdjust='spacingAndGlyphs'>2.7</text><polyline points='29.63,8.34 63.99,8.90 ' style='stroke-width: 1.07; stroke-linecap: butt;' /><circle cx='63.99' cy='8.90' r='0.89' style='stroke-width: 0.71; fill: #000000;' /><circle cx='63.99' cy='8.90' r='0.89' style='stroke-width: 0.71; stroke: #A020F0; fill: #A020F0;' /><circle cx='29.63' cy='8.34' r='0.89' style='stroke-width: 0.71; stroke: #00FF00; fill: #00FF00;' /></g></svg></td></tr>
    <tr><td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">Brest (29)</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">139.34</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><div>
  <div style="height: 20px;width:70px; background-color: #FF820033;border-radius:5px;)">
    <div style="height: 13px;width: 13px;background-color: #FF8200;display: inline-block;border-radius:4px;float:left;position:relative;top:17%;left:6%;"></div>
    <div style="display: inline-block;float:right;line-height:20px; font-weight: bold;padding: 0px 2.5px;">E</div>
  </div>
</div></td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">3.31</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">2.96</td>
<td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><?xml version='1.0' encoding='UTF-8' ?><svg xmlns='http://www.w3.org/2000/svg' xmlns:xlink='http://www.w3.org/1999/xlink' class='svglite' width='85.04pt' height='14.17pt' viewBox='0 0 85.04 14.17'><defs>  <style type='text/css'><![CDATA[    .svglite line, .svglite polyline, .svglite polygon, .svglite path, .svglite rect, .svglite circle {      fill: none;      stroke: #000000;      stroke-linecap: round;      stroke-linejoin: round;      stroke-miterlimit: 10.00;    }    .svglite text {      white-space: pre;    }  ]]></style></defs><rect width='100%' height='100%' style='stroke: none; fill: none;'/><defs>  <clipPath id='cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3'>    <rect x='0.00' y='0.00' width='85.04' height='14.17' />  </clipPath></defs><g clip-path='url(#cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3)'><text x='67.43' y='9.86' style='font-size: 5.69px; font-family: "Arial";' textLength='10.24px' lengthAdjust='spacingAndGlyphs'>3.0</text><polyline points='29.63,7.30 63.99,8.24 ' style='stroke-width: 1.07; stroke-linecap: butt;' /><circle cx='63.99' cy='8.24' r='0.89' style='stroke-width: 0.71; fill: #000000;' /><circle cx='63.99' cy='8.24' r='0.89' style='stroke-width: 0.71; stroke: #A020F0; fill: #A020F0;' /><circle cx='29.63' cy='7.30' r='0.89' style='stroke-width: 0.71; stroke: #00FF00; fill: #00FF00;' /></g></svg></td></tr>
    <tr><td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">Tours (37)</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">136.56</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><div>
  <div style="height: 20px;width:70px; background-color: #FF820033;border-radius:5px;)">
    <div style="height: 13px;width: 13px;background-color: #FF8200;display: inline-block;border-radius:4px;float:left;position:relative;top:17%;left:6%;"></div>
    <div style="display: inline-block;float:right;line-height:20px; font-weight: bold;padding: 0px 2.5px;">E</div>
  </div>
</div></td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">3.06</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">3.03</td>
<td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><?xml version='1.0' encoding='UTF-8' ?><svg xmlns='http://www.w3.org/2000/svg' xmlns:xlink='http://www.w3.org/1999/xlink' class='svglite' width='85.04pt' height='14.17pt' viewBox='0 0 85.04 14.17'><defs>  <style type='text/css'><![CDATA[    .svglite line, .svglite polyline, .svglite polygon, .svglite path, .svglite rect, .svglite circle {      fill: none;      stroke: #000000;      stroke-linecap: round;      stroke-linejoin: round;      stroke-miterlimit: 10.00;    }    .svglite text {      white-space: pre;    }  ]]></style></defs><rect width='100%' height='100%' style='stroke: none; fill: none;'/><defs>  <clipPath id='cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3'>    <rect x='0.00' y='0.00' width='85.04' height='14.17' />  </clipPath></defs><g clip-path='url(#cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3)'><text x='67.43' y='9.67' style='font-size: 5.69px; font-family: "Arial";' textLength='10.24px' lengthAdjust='spacingAndGlyphs'>3.0</text><polyline points='29.63,7.96 63.99,8.04 ' style='stroke-width: 1.07; stroke-linecap: butt;' /><circle cx='63.99' cy='8.04' r='0.89' style='stroke-width: 0.71; fill: #000000;' /><circle cx='63.99' cy='8.04' r='0.89' style='stroke-width: 0.71; stroke: #A020F0; fill: #A020F0;' /><circle cx='29.63' cy='7.96' r='0.89' style='stroke-width: 0.71; stroke: #00FF00; fill: #00FF00;' /></g></svg></td></tr>
    <tr><td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">Amiens (80)</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">133.75</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><div>
  <div style="height: 20px;width:70px; background-color: #FF580033;border-radius:5px;)">
    <div style="height: 13px;width: 13px;background-color: #FF5800;display: inline-block;border-radius:4px;float:left;position:relative;top:17%;left:6%;"></div>
    <div style="display: inline-block;float:right;line-height:20px; font-weight: bold;padding: 0px 2.5px;">F</div>
  </div>
</div></td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">2.20</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">2.50</td>
<td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><?xml version='1.0' encoding='UTF-8' ?><svg xmlns='http://www.w3.org/2000/svg' xmlns:xlink='http://www.w3.org/1999/xlink' class='svglite' width='85.04pt' height='14.17pt' viewBox='0 0 85.04 14.17'><defs>  <style type='text/css'><![CDATA[    .svglite line, .svglite polyline, .svglite polygon, .svglite path, .svglite rect, .svglite circle {      fill: none;      stroke: #000000;      stroke-linecap: round;      stroke-linejoin: round;      stroke-miterlimit: 10.00;    }    .svglite text {      white-space: pre;    }  ]]></style></defs><rect width='100%' height='100%' style='stroke: none; fill: none;'/><defs>  <clipPath id='cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3'>    <rect x='0.00' y='0.00' width='85.04' height='14.17' />  </clipPath></defs><g clip-path='url(#cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3)'><text x='67.43' y='11.12' style='font-size: 5.69px; font-family: "Arial";' textLength='10.24px' lengthAdjust='spacingAndGlyphs'>2.5</text><polyline points='29.63,10.30 63.99,9.50 ' style='stroke-width: 1.07; stroke-linecap: butt;' /><circle cx='63.99' cy='9.50' r='0.89' style='stroke-width: 0.71; fill: #000000;' /><circle cx='29.63' cy='10.30' r='0.89' style='stroke-width: 0.71; stroke: #A020F0; fill: #A020F0;' /><circle cx='63.99' cy='9.50' r='0.89' style='stroke-width: 0.71; stroke: #00FF00; fill: #00FF00;' /></g></svg></td></tr>
    <tr><td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">Limoges (87)</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">132.66</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><div>
  <div style="height: 20px;width:70px; background-color: #FF580033;border-radius:5px;)">
    <div style="height: 13px;width: 13px;background-color: #FF5800;display: inline-block;border-radius:4px;float:left;position:relative;top:17%;left:6%;"></div>
    <div style="display: inline-block;float:right;line-height:20px; font-weight: bold;padding: 0px 2.5px;">F</div>
  </div>
</div></td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">2.81</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">2.56</td>
<td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><?xml version='1.0' encoding='UTF-8' ?><svg xmlns='http://www.w3.org/2000/svg' xmlns:xlink='http://www.w3.org/1999/xlink' class='svglite' width='85.04pt' height='14.17pt' viewBox='0 0 85.04 14.17'><defs>  <style type='text/css'><![CDATA[    .svglite line, .svglite polyline, .svglite polygon, .svglite path, .svglite rect, .svglite circle {      fill: none;      stroke: #000000;      stroke-linecap: round;      stroke-linejoin: round;      stroke-miterlimit: 10.00;    }    .svglite text {      white-space: pre;    }  ]]></style></defs><rect width='100%' height='100%' style='stroke: none; fill: none;'/><defs>  <clipPath id='cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3'>    <rect x='0.00' y='0.00' width='85.04' height='14.17' />  </clipPath></defs><g clip-path='url(#cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3)'><text x='67.43' y='10.96' style='font-size: 5.69px; font-family: "Arial";' textLength='10.24px' lengthAdjust='spacingAndGlyphs'>2.6</text><polyline points='29.63,8.65 63.99,9.33 ' style='stroke-width: 1.07; stroke-linecap: butt;' /><circle cx='63.99' cy='9.33' r='0.89' style='stroke-width: 0.71; fill: #000000;' /><circle cx='63.99' cy='9.33' r='0.89' style='stroke-width: 0.71; stroke: #A020F0; fill: #A020F0;' /><circle cx='29.63' cy='8.65' r='0.89' style='stroke-width: 0.71; stroke: #00FF00; fill: #00FF00;' /></g></svg></td></tr>
    <tr><td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">Annecy (74)</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">126.42</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><div>
  <div style="height: 20px;width:70px; background-color: #FFA50033;border-radius:5px;)">
    <div style="height: 13px;width: 13px;background-color: #FFA500;display: inline-block;border-radius:4px;float:left;position:relative;top:17%;left:6%;"></div>
    <div style="display: inline-block;float:right;line-height:20px; font-weight: bold;padding: 0px 2.5px;">D</div>
  </div>
</div></td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">3.35</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">3.15</td>
<td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><?xml version='1.0' encoding='UTF-8' ?><svg xmlns='http://www.w3.org/2000/svg' xmlns:xlink='http://www.w3.org/1999/xlink' class='svglite' width='85.04pt' height='14.17pt' viewBox='0 0 85.04 14.17'><defs>  <style type='text/css'><![CDATA[    .svglite line, .svglite polyline, .svglite polygon, .svglite path, .svglite rect, .svglite circle {      fill: none;      stroke: #000000;      stroke-linecap: round;      stroke-linejoin: round;      stroke-miterlimit: 10.00;    }    .svglite text {      white-space: pre;    }  ]]></style></defs><rect width='100%' height='100%' style='stroke: none; fill: none;'/><defs>  <clipPath id='cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3'>    <rect x='0.00' y='0.00' width='85.04' height='14.17' />  </clipPath></defs><g clip-path='url(#cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3)'><text x='67.43' y='9.34' style='font-size: 5.69px; font-family: "Arial";' textLength='10.24px' lengthAdjust='spacingAndGlyphs'>3.2</text><polyline points='29.63,7.18 63.99,7.72 ' style='stroke-width: 1.07; stroke-linecap: butt;' /><circle cx='63.99' cy='7.72' r='0.89' style='stroke-width: 0.71; fill: #000000;' /><circle cx='63.99' cy='7.72' r='0.89' style='stroke-width: 0.71; stroke: #A020F0; fill: #A020F0;' /><circle cx='29.63' cy='7.18' r='0.89' style='stroke-width: 0.71; stroke: #00FF00; fill: #00FF00;' /></g></svg></td></tr>
    <tr><td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">Perpignan (66)</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">121.88</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><div>
  <div style="height: 20px;width:70px; background-color: #FF580033;border-radius:5px;)">
    <div style="height: 13px;width: 13px;background-color: #FF5800;display: inline-block;border-radius:4px;float:left;position:relative;top:17%;left:6%;"></div>
    <div style="display: inline-block;float:right;line-height:20px; font-weight: bold;padding: 0px 2.5px;">F</div>
  </div>
</div></td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">2.51</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">2.34</td>
<td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><?xml version='1.0' encoding='UTF-8' ?><svg xmlns='http://www.w3.org/2000/svg' xmlns:xlink='http://www.w3.org/1999/xlink' class='svglite' width='85.04pt' height='14.17pt' viewBox='0 0 85.04 14.17'><defs>  <style type='text/css'><![CDATA[    .svglite line, .svglite polyline, .svglite polygon, .svglite path, .svglite rect, .svglite circle {      fill: none;      stroke: #000000;      stroke-linecap: round;      stroke-linejoin: round;      stroke-miterlimit: 10.00;    }    .svglite text {      white-space: pre;    }  ]]></style></defs><rect width='100%' height='100%' style='stroke: none; fill: none;'/><defs>  <clipPath id='cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3'>    <rect x='0.00' y='0.00' width='85.04' height='14.17' />  </clipPath></defs><g clip-path='url(#cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3)'><text x='67.43' y='11.54' style='font-size: 5.69px; font-family: "Arial";' textLength='10.24px' lengthAdjust='spacingAndGlyphs'>2.3</text><polyline points='29.63,9.45 63.99,9.91 ' style='stroke-width: 1.07; stroke-linecap: butt;' /><circle cx='63.99' cy='9.91' r='0.89' style='stroke-width: 0.71; fill: #000000;' /><circle cx='63.99' cy='9.91' r='0.89' style='stroke-width: 0.71; stroke: #A020F0; fill: #A020F0;' /><circle cx='29.63' cy='9.45' r='0.89' style='stroke-width: 0.71; stroke: #00FF00; fill: #00FF00;' /></g></svg></td></tr>
    <tr><td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">Boulogne-Billancourt (92)</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">119.64</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><div>
  <div style="height: 20px;width:70px; background-color: #FF580033;border-radius:5px;)">
    <div style="height: 13px;width: 13px;background-color: #FF5800;display: inline-block;border-radius:4px;float:left;position:relative;top:17%;left:6%;"></div>
    <div style="display: inline-block;float:right;line-height:20px; font-weight: bold;padding: 0px 2.5px;">F</div>
  </div>
</div></td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">3.03</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">2.57</td>
<td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><?xml version='1.0' encoding='UTF-8' ?><svg xmlns='http://www.w3.org/2000/svg' xmlns:xlink='http://www.w3.org/1999/xlink' class='svglite' width='85.04pt' height='14.17pt' viewBox='0 0 85.04 14.17'><defs>  <style type='text/css'><![CDATA[    .svglite line, .svglite polyline, .svglite polygon, .svglite path, .svglite rect, .svglite circle {      fill: none;      stroke: #000000;      stroke-linecap: round;      stroke-linejoin: round;      stroke-miterlimit: 10.00;    }    .svglite text {      white-space: pre;    }  ]]></style></defs><rect width='100%' height='100%' style='stroke: none; fill: none;'/><defs>  <clipPath id='cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3'>    <rect x='0.00' y='0.00' width='85.04' height='14.17' />  </clipPath></defs><g clip-path='url(#cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3)'><text x='67.43' y='10.92' style='font-size: 5.69px; font-family: "Arial";' textLength='10.24px' lengthAdjust='spacingAndGlyphs'>2.6</text><polyline points='29.63,8.06 63.99,9.30 ' style='stroke-width: 1.07; stroke-linecap: butt;' /><circle cx='63.99' cy='9.30' r='0.89' style='stroke-width: 0.71; fill: #000000;' /><circle cx='63.99' cy='9.30' r='0.89' style='stroke-width: 0.71; stroke: #A020F0; fill: #A020F0;' /><circle cx='29.63' cy='8.06' r='0.89' style='stroke-width: 0.71; stroke: #00FF00; fill: #00FF00;' /></g></svg></td></tr>
    <tr><td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">Metz (57)</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">117.89</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><div>
  <div style="height: 20px;width:70px; background-color: #FFA50033;border-radius:5px;)">
    <div style="height: 13px;width: 13px;background-color: #FFA500;display: inline-block;border-radius:4px;float:left;position:relative;top:17%;left:6%;"></div>
    <div style="display: inline-block;float:right;line-height:20px; font-weight: bold;padding: 0px 2.5px;">D</div>
  </div>
</div></td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">3.51</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">3.11</td>
<td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><?xml version='1.0' encoding='UTF-8' ?><svg xmlns='http://www.w3.org/2000/svg' xmlns:xlink='http://www.w3.org/1999/xlink' class='svglite' width='85.04pt' height='14.17pt' viewBox='0 0 85.04 14.17'><defs>  <style type='text/css'><![CDATA[    .svglite line, .svglite polyline, .svglite polygon, .svglite path, .svglite rect, .svglite circle {      fill: none;      stroke: #000000;      stroke-linecap: round;      stroke-linejoin: round;      stroke-miterlimit: 10.00;    }    .svglite text {      white-space: pre;    }  ]]></style></defs><rect width='100%' height='100%' style='stroke: none; fill: none;'/><defs>  <clipPath id='cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3'>    <rect x='0.00' y='0.00' width='85.04' height='14.17' />  </clipPath></defs><g clip-path='url(#cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3)'><text x='67.43' y='9.45' style='font-size: 5.69px; font-family: "Arial";' textLength='10.24px' lengthAdjust='spacingAndGlyphs'>3.1</text><polyline points='29.63,6.74 63.99,7.83 ' style='stroke-width: 1.07; stroke-linecap: butt;' /><circle cx='63.99' cy='7.83' r='0.89' style='stroke-width: 0.71; fill: #000000;' /><circle cx='63.99' cy='7.83' r='0.89' style='stroke-width: 0.71; stroke: #A020F0; fill: #A020F0;' /><circle cx='29.63' cy='6.74' r='0.89' style='stroke-width: 0.71; stroke: #00FF00; fill: #00FF00;' /></g></svg></td></tr>
    <tr><td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">Besançon (25)</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">116.47</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><div>
  <div style="height: 20px;width:70px; background-color: #FF820033;border-radius:5px;)">
    <div style="height: 13px;width: 13px;background-color: #FF8200;display: inline-block;border-radius:4px;float:left;position:relative;top:17%;left:6%;"></div>
    <div style="display: inline-block;float:right;line-height:20px; font-weight: bold;padding: 0px 2.5px;">E</div>
  </div>
</div></td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">3.29</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">3.01</td>
<td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><?xml version='1.0' encoding='UTF-8' ?><svg xmlns='http://www.w3.org/2000/svg' xmlns:xlink='http://www.w3.org/1999/xlink' class='svglite' width='85.04pt' height='14.17pt' viewBox='0 0 85.04 14.17'><defs>  <style type='text/css'><![CDATA[    .svglite line, .svglite polyline, .svglite polygon, .svglite path, .svglite rect, .svglite circle {      fill: none;      stroke: #000000;      stroke-linecap: round;      stroke-linejoin: round;      stroke-miterlimit: 10.00;    }    .svglite text {      white-space: pre;    }  ]]></style></defs><rect width='100%' height='100%' style='stroke: none; fill: none;'/><defs>  <clipPath id='cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3'>    <rect x='0.00' y='0.00' width='85.04' height='14.17' />  </clipPath></defs><g clip-path='url(#cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3)'><text x='67.43' y='9.74' style='font-size: 5.69px; font-family: "Arial";' textLength='10.24px' lengthAdjust='spacingAndGlyphs'>3.0</text><polyline points='29.63,7.35 63.99,8.11 ' style='stroke-width: 1.07; stroke-linecap: butt;' /><circle cx='63.99' cy='8.11' r='0.89' style='stroke-width: 0.71; fill: #000000;' /><circle cx='63.99' cy='8.11' r='0.89' style='stroke-width: 0.71; stroke: #A020F0; fill: #A020F0;' /><circle cx='29.63' cy='7.35' r='0.89' style='stroke-width: 0.71; stroke: #00FF00; fill: #00FF00;' /></g></svg></td></tr>
    <tr><td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">Orléans (45)</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">114.78</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><div>
  <div style="height: 20px;width:70px; background-color: #FF820033;border-radius:5px;)">
    <div style="height: 13px;width: 13px;background-color: #FF8200;display: inline-block;border-radius:4px;float:left;position:relative;top:17%;left:6%;"></div>
    <div style="display: inline-block;float:right;line-height:20px; font-weight: bold;padding: 0px 2.5px;">E</div>
  </div>
</div></td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">3.21</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">2.97</td>
<td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><?xml version='1.0' encoding='UTF-8' ?><svg xmlns='http://www.w3.org/2000/svg' xmlns:xlink='http://www.w3.org/1999/xlink' class='svglite' width='85.04pt' height='14.17pt' viewBox='0 0 85.04 14.17'><defs>  <style type='text/css'><![CDATA[    .svglite line, .svglite polyline, .svglite polygon, .svglite path, .svglite rect, .svglite circle {      fill: none;      stroke: #000000;      stroke-linecap: round;      stroke-linejoin: round;      stroke-miterlimit: 10.00;    }    .svglite text {      white-space: pre;    }  ]]></style></defs><rect width='100%' height='100%' style='stroke: none; fill: none;'/><defs>  <clipPath id='cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3'>    <rect x='0.00' y='0.00' width='85.04' height='14.17' />  </clipPath></defs><g clip-path='url(#cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3)'><text x='67.43' y='9.84' style='font-size: 5.69px; font-family: "Arial";' textLength='10.24px' lengthAdjust='spacingAndGlyphs'>3.0</text><polyline points='29.63,7.57 63.99,8.22 ' style='stroke-width: 1.07; stroke-linecap: butt;' /><circle cx='63.99' cy='8.22' r='0.89' style='stroke-width: 0.71; fill: #000000;' /><circle cx='63.99' cy='8.22' r='0.89' style='stroke-width: 0.71; stroke: #A020F0; fill: #A020F0;' /><circle cx='29.63' cy='7.57' r='0.89' style='stroke-width: 0.71; stroke: #00FF00; fill: #00FF00;' /></g></svg></td></tr>
    <tr><td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">Saint-Denis (93)</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">111.35</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><div>
  <div style="height: 20px;width:70px; background-color: #FF580033;border-radius:5px;)">
    <div style="height: 13px;width: 13px;background-color: #FF5800;display: inline-block;border-radius:4px;float:left;position:relative;top:17%;left:6%;"></div>
    <div style="display: inline-block;float:right;line-height:20px; font-weight: bold;padding: 0px 2.5px;">F</div>
  </div>
</div></td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">2.40</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">2.35</td>
<td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><?xml version='1.0' encoding='UTF-8' ?><svg xmlns='http://www.w3.org/2000/svg' xmlns:xlink='http://www.w3.org/1999/xlink' class='svglite' width='85.04pt' height='14.17pt' viewBox='0 0 85.04 14.17'><defs>  <style type='text/css'><![CDATA[    .svglite line, .svglite polyline, .svglite polygon, .svglite path, .svglite rect, .svglite circle {      fill: none;      stroke: #000000;      stroke-linecap: round;      stroke-linejoin: round;      stroke-miterlimit: 10.00;    }    .svglite text {      white-space: pre;    }  ]]></style></defs><rect width='100%' height='100%' style='stroke: none; fill: none;'/><defs>  <clipPath id='cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3'>    <rect x='0.00' y='0.00' width='85.04' height='14.17' />  </clipPath></defs><g clip-path='url(#cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3)'><text x='67.43' y='11.53' style='font-size: 5.69px; font-family: "Arial";' textLength='10.24px' lengthAdjust='spacingAndGlyphs'>2.3</text><polyline points='29.63,9.77 63.99,9.90 ' style='stroke-width: 1.07; stroke-linecap: butt;' /><circle cx='63.99' cy='9.90' r='0.89' style='stroke-width: 0.71; fill: #000000;' /><circle cx='63.99' cy='9.90' r='0.89' style='stroke-width: 0.71; stroke: #A020F0; fill: #A020F0;' /><circle cx='29.63' cy='9.77' r='0.89' style='stroke-width: 0.71; stroke: #00FF00; fill: #00FF00;' /></g></svg></td></tr>
    <tr><td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">Argenteuil (95)</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">110.47</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><div>
  <div style="height: 20px;width:70px; background-color: #FF000033;border-radius:5px;)">
    <div style="height: 13px;width: 13px;background-color: #FF0000;display: inline-block;border-radius:4px;float:left;position:relative;top:17%;left:6%;"></div>
    <div style="display: inline-block;float:right;line-height:20px; font-weight: bold;padding: 0px 2.5px;">G</div>
  </div>
</div></td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">1.96</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">2.08</td>
<td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><?xml version='1.0' encoding='UTF-8' ?><svg xmlns='http://www.w3.org/2000/svg' xmlns:xlink='http://www.w3.org/1999/xlink' class='svglite' width='85.04pt' height='14.17pt' viewBox='0 0 85.04 14.17'><defs>  <style type='text/css'><![CDATA[    .svglite line, .svglite polyline, .svglite polygon, .svglite path, .svglite rect, .svglite circle {      fill: none;      stroke: #000000;      stroke-linecap: round;      stroke-linejoin: round;      stroke-miterlimit: 10.00;    }    .svglite text {      white-space: pre;    }  ]]></style></defs><rect width='100%' height='100%' style='stroke: none; fill: none;'/><defs>  <clipPath id='cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3'>    <rect x='0.00' y='0.00' width='85.04' height='14.17' />  </clipPath></defs><g clip-path='url(#cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3)'><text x='67.43' y='12.24' style='font-size: 5.69px; font-family: "Arial";' textLength='10.24px' lengthAdjust='spacingAndGlyphs'>2.1</text><polyline points='29.63,10.95 63.99,10.62 ' style='stroke-width: 1.07; stroke-linecap: butt;' /><circle cx='63.99' cy='10.62' r='0.89' style='stroke-width: 0.71; fill: #000000;' /><circle cx='29.63' cy='10.95' r='0.89' style='stroke-width: 0.71; stroke: #A020F0; fill: #A020F0;' /><circle cx='63.99' cy='10.62' r='0.89' style='stroke-width: 0.71; stroke: #00FF00; fill: #00FF00;' /></g></svg></td></tr>
    <tr><td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">Rouen (76)</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">110.12</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><div>
  <div style="height: 20px;width:70px; background-color: #FF820033;border-radius:5px;)">
    <div style="height: 13px;width: 13px;background-color: #FF8200;display: inline-block;border-radius:4px;float:left;position:relative;top:17%;left:6%;"></div>
    <div style="display: inline-block;float:right;line-height:20px; font-weight: bold;padding: 0px 2.5px;">E</div>
  </div>
</div></td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">3.53</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">2.87</td>
<td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><?xml version='1.0' encoding='UTF-8' ?><svg xmlns='http://www.w3.org/2000/svg' xmlns:xlink='http://www.w3.org/1999/xlink' class='svglite' width='85.04pt' height='14.17pt' viewBox='0 0 85.04 14.17'><defs>  <style type='text/css'><![CDATA[    .svglite line, .svglite polyline, .svglite polygon, .svglite path, .svglite rect, .svglite circle {      fill: none;      stroke: #000000;      stroke-linecap: round;      stroke-linejoin: round;      stroke-miterlimit: 10.00;    }    .svglite text {      white-space: pre;    }  ]]></style></defs><rect width='100%' height='100%' style='stroke: none; fill: none;'/><defs>  <clipPath id='cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3'>    <rect x='0.00' y='0.00' width='85.04' height='14.17' />  </clipPath></defs><g clip-path='url(#cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3)'><text x='67.43' y='10.10' style='font-size: 5.69px; font-family: "Arial";' textLength='10.24px' lengthAdjust='spacingAndGlyphs'>2.9</text><polyline points='29.63,6.69 63.99,8.47 ' style='stroke-width: 1.07; stroke-linecap: butt;' /><circle cx='63.99' cy='8.47' r='0.89' style='stroke-width: 0.71; fill: #000000;' /><circle cx='63.99' cy='8.47' r='0.89' style='stroke-width: 0.71; stroke: #A020F0; fill: #A020F0;' /><circle cx='29.63' cy='6.69' r='0.89' style='stroke-width: 0.71; stroke: #00FF00; fill: #00FF00;' /></g></svg></td></tr>
    <tr><td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">Mulhouse (68)</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">109.00</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><div>
  <div style="height: 20px;width:70px; background-color: #FF820033;border-radius:5px;)">
    <div style="height: 13px;width: 13px;background-color: #FF8200;display: inline-block;border-radius:4px;float:left;position:relative;top:17%;left:6%;"></div>
    <div style="display: inline-block;float:right;line-height:20px; font-weight: bold;padding: 0px 2.5px;">E</div>
  </div>
</div></td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">3.12</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">2.99</td>
<td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><?xml version='1.0' encoding='UTF-8' ?><svg xmlns='http://www.w3.org/2000/svg' xmlns:xlink='http://www.w3.org/1999/xlink' class='svglite' width='85.04pt' height='14.17pt' viewBox='0 0 85.04 14.17'><defs>  <style type='text/css'><![CDATA[    .svglite line, .svglite polyline, .svglite polygon, .svglite path, .svglite rect, .svglite circle {      fill: none;      stroke: #000000;      stroke-linecap: round;      stroke-linejoin: round;      stroke-miterlimit: 10.00;    }    .svglite text {      white-space: pre;    }  ]]></style></defs><rect width='100%' height='100%' style='stroke: none; fill: none;'/><defs>  <clipPath id='cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3'>    <rect x='0.00' y='0.00' width='85.04' height='14.17' />  </clipPath></defs><g clip-path='url(#cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3)'><text x='67.43' y='9.77' style='font-size: 5.69px; font-family: "Arial";' textLength='10.24px' lengthAdjust='spacingAndGlyphs'>3.0</text><polyline points='29.63,7.80 63.99,8.15 ' style='stroke-width: 1.07; stroke-linecap: butt;' /><circle cx='63.99' cy='8.15' r='0.89' style='stroke-width: 0.71; fill: #000000;' /><circle cx='63.99' cy='8.15' r='0.89' style='stroke-width: 0.71; stroke: #A020F0; fill: #A020F0;' /><circle cx='29.63' cy='7.80' r='0.89' style='stroke-width: 0.71; stroke: #00FF00; fill: #00FF00;' /></g></svg></td></tr>
    <tr><td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">Montreuil (93)</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">108.40</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><div>
  <div style="height: 20px;width:70px; background-color: #FFA50033;border-radius:5px;)">
    <div style="height: 13px;width: 13px;background-color: #FFA500;display: inline-block;border-radius:4px;float:left;position:relative;top:17%;left:6%;"></div>
    <div style="display: inline-block;float:right;line-height:20px; font-weight: bold;padding: 0px 2.5px;">D</div>
  </div>
</div></td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">3.95</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">3.15</td>
<td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><?xml version='1.0' encoding='UTF-8' ?><svg xmlns='http://www.w3.org/2000/svg' xmlns:xlink='http://www.w3.org/1999/xlink' class='svglite' width='85.04pt' height='14.17pt' viewBox='0 0 85.04 14.17'><defs>  <style type='text/css'><![CDATA[    .svglite line, .svglite polyline, .svglite polygon, .svglite path, .svglite rect, .svglite circle {      fill: none;      stroke: #000000;      stroke-linecap: round;      stroke-linejoin: round;      stroke-miterlimit: 10.00;    }    .svglite text {      white-space: pre;    }  ]]></style></defs><rect width='100%' height='100%' style='stroke: none; fill: none;'/><defs>  <clipPath id='cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3'>    <rect x='0.00' y='0.00' width='85.04' height='14.17' />  </clipPath></defs><g clip-path='url(#cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3)'><text x='67.43' y='9.36' style='font-size: 5.69px; font-family: "Arial";' textLength='10.24px' lengthAdjust='spacingAndGlyphs'>3.1</text><polyline points='29.63,5.55 63.99,7.73 ' style='stroke-width: 1.07; stroke-linecap: butt;' /><circle cx='63.99' cy='7.73' r='0.89' style='stroke-width: 0.71; fill: #000000;' /><circle cx='63.99' cy='7.73' r='0.89' style='stroke-width: 0.71; stroke: #A020F0; fill: #A020F0;' /><circle cx='29.63' cy='5.55' r='0.89' style='stroke-width: 0.71; stroke: #00FF00; fill: #00FF00;' /></g></svg></td></tr>
    <tr><td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">Saint-Paul (974)</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">105.48</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><div>
  <div style="height: 20px;width:70px; background-color: #FF000033;border-radius:5px;)">
    <div style="height: 13px;width: 13px;background-color: #FF0000;display: inline-block;border-radius:4px;float:left;position:relative;top:17%;left:6%;"></div>
    <div style="display: inline-block;float:right;line-height:20px; font-weight: bold;padding: 0px 2.5px;">G</div>
  </div>
</div></td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">2.38</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">2.25</td>
<td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><?xml version='1.0' encoding='UTF-8' ?><svg xmlns='http://www.w3.org/2000/svg' xmlns:xlink='http://www.w3.org/1999/xlink' class='svglite' width='85.04pt' height='14.17pt' viewBox='0 0 85.04 14.17'><defs>  <style type='text/css'><![CDATA[    .svglite line, .svglite polyline, .svglite polygon, .svglite path, .svglite rect, .svglite circle {      fill: none;      stroke: #000000;      stroke-linecap: round;      stroke-linejoin: round;      stroke-miterlimit: 10.00;    }    .svglite text {      white-space: pre;    }  ]]></style></defs><rect width='100%' height='100%' style='stroke: none; fill: none;'/><defs>  <clipPath id='cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3'>    <rect x='0.00' y='0.00' width='85.04' height='14.17' />  </clipPath></defs><g clip-path='url(#cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3)'><text x='67.43' y='11.80' style='font-size: 5.69px; font-family: "Arial";' textLength='10.24px' lengthAdjust='spacingAndGlyphs'>2.2</text><polyline points='29.63,9.80 63.99,10.17 ' style='stroke-width: 1.07; stroke-linecap: butt;' /><circle cx='63.99' cy='10.17' r='0.89' style='stroke-width: 0.71; fill: #000000;' /><circle cx='63.99' cy='10.17' r='0.89' style='stroke-width: 0.71; stroke: #A020F0; fill: #A020F0;' /><circle cx='29.63' cy='9.80' r='0.89' style='stroke-width: 0.71; stroke: #00FF00; fill: #00FF00;' /></g></svg></td></tr>
    <tr><td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">Caen (14)</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">105.40</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><div>
  <div style="height: 20px;width:70px; background-color: #FFA50033;border-radius:5px;)">
    <div style="height: 13px;width: 13px;background-color: #FFA500;display: inline-block;border-radius:4px;float:left;position:relative;top:17%;left:6%;"></div>
    <div style="display: inline-block;float:right;line-height:20px; font-weight: bold;padding: 0px 2.5px;">D</div>
  </div>
</div></td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">3.75</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">3.25</td>
<td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><?xml version='1.0' encoding='UTF-8' ?><svg xmlns='http://www.w3.org/2000/svg' xmlns:xlink='http://www.w3.org/1999/xlink' class='svglite' width='85.04pt' height='14.17pt' viewBox='0 0 85.04 14.17'><defs>  <style type='text/css'><![CDATA[    .svglite line, .svglite polyline, .svglite polygon, .svglite path, .svglite rect, .svglite circle {      fill: none;      stroke: #000000;      stroke-linecap: round;      stroke-linejoin: round;      stroke-miterlimit: 10.00;    }    .svglite text {      white-space: pre;    }  ]]></style></defs><rect width='100%' height='100%' style='stroke: none; fill: none;'/><defs>  <clipPath id='cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3'>    <rect x='0.00' y='0.00' width='85.04' height='14.17' />  </clipPath></defs><g clip-path='url(#cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3)'><text x='67.43' y='9.07' style='font-size: 5.69px; font-family: "Arial";' textLength='10.24px' lengthAdjust='spacingAndGlyphs'>3.3</text><polyline points='29.63,6.10 63.99,7.45 ' style='stroke-width: 1.07; stroke-linecap: butt;' /><circle cx='63.99' cy='7.45' r='0.89' style='stroke-width: 0.71; fill: #000000;' /><circle cx='63.99' cy='7.45' r='0.89' style='stroke-width: 0.71; stroke: #A020F0; fill: #A020F0;' /><circle cx='29.63' cy='6.10' r='0.89' style='stroke-width: 0.71; stroke: #00FF00; fill: #00FF00;' /></g></svg></td></tr>
    <tr><td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">Nancy (54)</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">104.59</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><div>
  <div style="height: 20px;width:70px; background-color: #FF580033;border-radius:5px;)">
    <div style="height: 13px;width: 13px;background-color: #FF5800;display: inline-block;border-radius:4px;float:left;position:relative;top:17%;left:6%;"></div>
    <div style="display: inline-block;float:right;line-height:20px; font-weight: bold;padding: 0px 2.5px;">F</div>
  </div>
</div></td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">2.74</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">2.65</td>
<td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><?xml version='1.0' encoding='UTF-8' ?><svg xmlns='http://www.w3.org/2000/svg' xmlns:xlink='http://www.w3.org/1999/xlink' class='svglite' width='85.04pt' height='14.17pt' viewBox='0 0 85.04 14.17'><defs>  <style type='text/css'><![CDATA[    .svglite line, .svglite polyline, .svglite polygon, .svglite path, .svglite rect, .svglite circle {      fill: none;      stroke: #000000;      stroke-linecap: round;      stroke-linejoin: round;      stroke-miterlimit: 10.00;    }    .svglite text {      white-space: pre;    }  ]]></style></defs><rect width='100%' height='100%' style='stroke: none; fill: none;'/><defs>  <clipPath id='cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3'>    <rect x='0.00' y='0.00' width='85.04' height='14.17' />  </clipPath></defs><g clip-path='url(#cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3)'><text x='67.43' y='10.71' style='font-size: 5.69px; font-family: "Arial";' textLength='10.24px' lengthAdjust='spacingAndGlyphs'>2.6</text><polyline points='29.63,8.84 63.99,9.08 ' style='stroke-width: 1.07; stroke-linecap: butt;' /><circle cx='63.99' cy='9.08' r='0.89' style='stroke-width: 0.71; fill: #000000;' /><circle cx='63.99' cy='9.08' r='0.89' style='stroke-width: 0.71; stroke: #A020F0; fill: #A020F0;' /><circle cx='29.63' cy='8.84' r='0.89' style='stroke-width: 0.71; stroke: #00FF00; fill: #00FF00;' /></g></svg></td></tr>
    <tr><td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">Nouméa (98)</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">99.93</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><div>
  <div style="height: 20px;width:70px; background-color: #FF580033;border-radius:5px;)">
    <div style="height: 13px;width: 13px;background-color: #FF5800;display: inline-block;border-radius:4px;float:left;position:relative;top:17%;left:6%;"></div>
    <div style="display: inline-block;float:right;line-height:20px; font-weight: bold;padding: 0px 2.5px;">F</div>
  </div>
</div></td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">3.39</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">2.57</td>
<td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><?xml version='1.0' encoding='UTF-8' ?><svg xmlns='http://www.w3.org/2000/svg' xmlns:xlink='http://www.w3.org/1999/xlink' class='svglite' width='85.04pt' height='14.17pt' viewBox='0 0 85.04 14.17'><defs>  <style type='text/css'><![CDATA[    .svglite line, .svglite polyline, .svglite polygon, .svglite path, .svglite rect, .svglite circle {      fill: none;      stroke: #000000;      stroke-linecap: round;      stroke-linejoin: round;      stroke-miterlimit: 10.00;    }    .svglite text {      white-space: pre;    }  ]]></style></defs><rect width='100%' height='100%' style='stroke: none; fill: none;'/><defs>  <clipPath id='cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3'>    <rect x='0.00' y='0.00' width='85.04' height='14.17' />  </clipPath></defs><g clip-path='url(#cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3)'><text x='67.43' y='10.92' style='font-size: 5.69px; font-family: "Arial";' textLength='10.24px' lengthAdjust='spacingAndGlyphs'>2.6</text><polyline points='29.63,7.06 63.99,9.29 ' style='stroke-width: 1.07; stroke-linecap: butt;' /><circle cx='63.99' cy='9.29' r='0.89' style='stroke-width: 0.71; fill: #000000;' /><circle cx='63.99' cy='9.29' r='0.89' style='stroke-width: 0.71; stroke: #A020F0; fill: #A020F0;' /><circle cx='29.63' cy='7.06' r='0.89' style='stroke-width: 0.71; stroke: #00FF00; fill: #00FF00;' /></g></svg></td></tr>
    <tr><td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">Tourcoing (59)</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">97.48</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><div>
  <div style="height: 20px;width:70px; background-color: #FF580033;border-radius:5px;)">
    <div style="height: 13px;width: 13px;background-color: #FF5800;display: inline-block;border-radius:4px;float:left;position:relative;top:17%;left:6%;"></div>
    <div style="display: inline-block;float:right;line-height:20px; font-weight: bold;padding: 0px 2.5px;">F</div>
  </div>
</div></td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">2.46</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">2.53</td>
<td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><?xml version='1.0' encoding='UTF-8' ?><svg xmlns='http://www.w3.org/2000/svg' xmlns:xlink='http://www.w3.org/1999/xlink' class='svglite' width='85.04pt' height='14.17pt' viewBox='0 0 85.04 14.17'><defs>  <style type='text/css'><![CDATA[    .svglite line, .svglite polyline, .svglite polygon, .svglite path, .svglite rect, .svglite circle {      fill: none;      stroke: #000000;      stroke-linecap: round;      stroke-linejoin: round;      stroke-miterlimit: 10.00;    }    .svglite text {      white-space: pre;    }  ]]></style></defs><rect width='100%' height='100%' style='stroke: none; fill: none;'/><defs>  <clipPath id='cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3'>    <rect x='0.00' y='0.00' width='85.04' height='14.17' />  </clipPath></defs><g clip-path='url(#cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3)'><text x='67.43' y='11.03' style='font-size: 5.69px; font-family: "Arial";' textLength='10.24px' lengthAdjust='spacingAndGlyphs'>2.5</text><polyline points='29.63,9.58 63.99,9.41 ' style='stroke-width: 1.07; stroke-linecap: butt;' /><circle cx='63.99' cy='9.41' r='0.89' style='stroke-width: 0.71; fill: #000000;' /><circle cx='29.63' cy='9.58' r='0.89' style='stroke-width: 0.71; stroke: #A020F0; fill: #A020F0;' /><circle cx='63.99' cy='9.41' r='0.89' style='stroke-width: 0.71; stroke: #00FF00; fill: #00FF00;' /></g></svg></td></tr>
    <tr><td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">Roubaix (59)</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">96.41</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><div>
  <div style="height: 20px;width:70px; background-color: #FF580033;border-radius:5px;)">
    <div style="height: 13px;width: 13px;background-color: #FF5800;display: inline-block;border-radius:4px;float:left;position:relative;top:17%;left:6%;"></div>
    <div style="display: inline-block;float:right;line-height:20px; font-weight: bold;padding: 0px 2.5px;">F</div>
  </div>
</div></td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">2.96</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">2.64</td>
<td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><?xml version='1.0' encoding='UTF-8' ?><svg xmlns='http://www.w3.org/2000/svg' xmlns:xlink='http://www.w3.org/1999/xlink' class='svglite' width='85.04pt' height='14.17pt' viewBox='0 0 85.04 14.17'><defs>  <style type='text/css'><![CDATA[    .svglite line, .svglite polyline, .svglite polygon, .svglite path, .svglite rect, .svglite circle {      fill: none;      stroke: #000000;      stroke-linecap: round;      stroke-linejoin: round;      stroke-miterlimit: 10.00;    }    .svglite text {      white-space: pre;    }  ]]></style></defs><rect width='100%' height='100%' style='stroke: none; fill: none;'/><defs>  <clipPath id='cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3'>    <rect x='0.00' y='0.00' width='85.04' height='14.17' />  </clipPath></defs><g clip-path='url(#cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3)'><text x='67.43' y='10.73' style='font-size: 5.69px; font-family: "Arial";' textLength='10.24px' lengthAdjust='spacingAndGlyphs'>2.6</text><polyline points='29.63,8.24 63.99,9.11 ' style='stroke-width: 1.07; stroke-linecap: butt;' /><circle cx='63.99' cy='9.11' r='0.89' style='stroke-width: 0.71; fill: #000000;' /><circle cx='63.99' cy='9.11' r='0.89' style='stroke-width: 0.71; stroke: #A020F0; fill: #A020F0;' /><circle cx='29.63' cy='8.24' r='0.89' style='stroke-width: 0.71; stroke: #00FF00; fill: #00FF00;' /></g></svg></td></tr>
    <tr><td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">Nanterre (92)</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">94.26</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><div>
  <div style="height: 20px;width:70px; background-color: #FF580033;border-radius:5px;)">
    <div style="height: 13px;width: 13px;background-color: #FF5800;display: inline-block;border-radius:4px;float:left;position:relative;top:17%;left:6%;"></div>
    <div style="display: inline-block;float:right;line-height:20px; font-weight: bold;padding: 0px 2.5px;">F</div>
  </div>
</div></td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">2.75</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">2.68</td>
<td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><?xml version='1.0' encoding='UTF-8' ?><svg xmlns='http://www.w3.org/2000/svg' xmlns:xlink='http://www.w3.org/1999/xlink' class='svglite' width='85.04pt' height='14.17pt' viewBox='0 0 85.04 14.17'><defs>  <style type='text/css'><![CDATA[    .svglite line, .svglite polyline, .svglite polygon, .svglite path, .svglite rect, .svglite circle {      fill: none;      stroke: #000000;      stroke-linecap: round;      stroke-linejoin: round;      stroke-miterlimit: 10.00;    }    .svglite text {      white-space: pre;    }  ]]></style></defs><rect width='100%' height='100%' style='stroke: none; fill: none;'/><defs>  <clipPath id='cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3'>    <rect x='0.00' y='0.00' width='85.04' height='14.17' />  </clipPath></defs><g clip-path='url(#cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3)'><text x='67.43' y='10.63' style='font-size: 5.69px; font-family: "Arial";' textLength='10.24px' lengthAdjust='spacingAndGlyphs'>2.7</text><polyline points='29.63,8.81 63.99,9.01 ' style='stroke-width: 1.07; stroke-linecap: butt;' /><circle cx='63.99' cy='9.01' r='0.89' style='stroke-width: 0.71; fill: #000000;' /><circle cx='63.99' cy='9.01' r='0.89' style='stroke-width: 0.71; stroke: #A020F0; fill: #A020F0;' /><circle cx='29.63' cy='8.81' r='0.89' style='stroke-width: 0.71; stroke: #00FF00; fill: #00FF00;' /></g></svg></td></tr>
    <tr><td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">Vitry-sur-Seine (94)</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">92.75</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><div>
  <div style="height: 20px;width:70px; background-color: #FF000033;border-radius:5px;)">
    <div style="height: 13px;width: 13px;background-color: #FF0000;display: inline-block;border-radius:4px;float:left;position:relative;top:17%;left:6%;"></div>
    <div style="display: inline-block;float:right;line-height:20px; font-weight: bold;padding: 0px 2.5px;">G</div>
  </div>
</div></td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">1.67</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">2.06</td>
<td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><?xml version='1.0' encoding='UTF-8' ?><svg xmlns='http://www.w3.org/2000/svg' xmlns:xlink='http://www.w3.org/1999/xlink' class='svglite' width='85.04pt' height='14.17pt' viewBox='0 0 85.04 14.17'><defs>  <style type='text/css'><![CDATA[    .svglite line, .svglite polyline, .svglite polygon, .svglite path, .svglite rect, .svglite circle {      fill: none;      stroke: #000000;      stroke-linecap: round;      stroke-linejoin: round;      stroke-miterlimit: 10.00;    }    .svglite text {      white-space: pre;    }  ]]></style></defs><rect width='100%' height='100%' style='stroke: none; fill: none;'/><defs>  <clipPath id='cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3'>    <rect x='0.00' y='0.00' width='85.04' height='14.17' />  </clipPath></defs><g clip-path='url(#cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3)'><text x='67.43' y='12.29' style='font-size: 5.69px; font-family: "Arial";' textLength='10.24px' lengthAdjust='spacingAndGlyphs'>2.1</text><polyline points='29.63,11.72 63.99,10.67 ' style='stroke-width: 1.07; stroke-linecap: butt;' /><circle cx='63.99' cy='10.67' r='0.89' style='stroke-width: 0.71; fill: #000000;' /><circle cx='29.63' cy='11.72' r='0.89' style='stroke-width: 0.71; stroke: #A020F0; fill: #A020F0;' /><circle cx='63.99' cy='10.67' r='0.89' style='stroke-width: 0.71; stroke: #00FF00; fill: #00FF00;' /></g></svg></td></tr>
    <tr><td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">Avignon (84)</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">92.38</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><div>
  <div style="height: 20px;width:70px; background-color: #FF820033;border-radius:5px;)">
    <div style="height: 13px;width: 13px;background-color: #FF8200;display: inline-block;border-radius:4px;float:left;position:relative;top:17%;left:6%;"></div>
    <div style="display: inline-block;float:right;line-height:20px; font-weight: bold;padding: 0px 2.5px;">E</div>
  </div>
</div></td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">2.93</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">2.83</td>
<td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><?xml version='1.0' encoding='UTF-8' ?><svg xmlns='http://www.w3.org/2000/svg' xmlns:xlink='http://www.w3.org/1999/xlink' class='svglite' width='85.04pt' height='14.17pt' viewBox='0 0 85.04 14.17'><defs>  <style type='text/css'><![CDATA[    .svglite line, .svglite polyline, .svglite polygon, .svglite path, .svglite rect, .svglite circle {      fill: none;      stroke: #000000;      stroke-linecap: round;      stroke-linejoin: round;      stroke-miterlimit: 10.00;    }    .svglite text {      white-space: pre;    }  ]]></style></defs><rect width='100%' height='100%' style='stroke: none; fill: none;'/><defs>  <clipPath id='cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3'>    <rect x='0.00' y='0.00' width='85.04' height='14.17' />  </clipPath></defs><g clip-path='url(#cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3)'><text x='67.43' y='10.21' style='font-size: 5.69px; font-family: "Arial";' textLength='10.24px' lengthAdjust='spacingAndGlyphs'>2.8</text><polyline points='29.63,8.32 63.99,8.58 ' style='stroke-width: 1.07; stroke-linecap: butt;' /><circle cx='63.99' cy='8.58' r='0.89' style='stroke-width: 0.71; fill: #000000;' /><circle cx='63.99' cy='8.58' r='0.89' style='stroke-width: 0.71; stroke: #A020F0; fill: #A020F0;' /><circle cx='29.63' cy='8.32' r='0.89' style='stroke-width: 0.71; stroke: #00FF00; fill: #00FF00;' /></g></svg></td></tr>
    <tr><td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">Créteil (94)</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">89.39</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><div>
  <div style="height: 20px;width:70px; background-color: #FF580033;border-radius:5px;)">
    <div style="height: 13px;width: 13px;background-color: #FF5800;display: inline-block;border-radius:4px;float:left;position:relative;top:17%;left:6%;"></div>
    <div style="display: inline-block;float:right;line-height:20px; font-weight: bold;padding: 0px 2.5px;">F</div>
  </div>
</div></td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">2.31</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">2.45</td>
<td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><?xml version='1.0' encoding='UTF-8' ?><svg xmlns='http://www.w3.org/2000/svg' xmlns:xlink='http://www.w3.org/1999/xlink' class='svglite' width='85.04pt' height='14.17pt' viewBox='0 0 85.04 14.17'><defs>  <style type='text/css'><![CDATA[    .svglite line, .svglite polyline, .svglite polygon, .svglite path, .svglite rect, .svglite circle {      fill: none;      stroke: #000000;      stroke-linecap: round;      stroke-linejoin: round;      stroke-miterlimit: 10.00;    }    .svglite text {      white-space: pre;    }  ]]></style></defs><rect width='100%' height='100%' style='stroke: none; fill: none;'/><defs>  <clipPath id='cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3'>    <rect x='0.00' y='0.00' width='85.04' height='14.17' />  </clipPath></defs><g clip-path='url(#cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3)'><text x='67.43' y='11.23' style='font-size: 5.69px; font-family: "Arial";' textLength='10.24px' lengthAdjust='spacingAndGlyphs'>2.5</text><polyline points='29.63,9.99 63.99,9.61 ' style='stroke-width: 1.07; stroke-linecap: butt;' /><circle cx='63.99' cy='9.61' r='0.89' style='stroke-width: 0.71; fill: #000000;' /><circle cx='29.63' cy='9.99' r='0.89' style='stroke-width: 0.71; stroke: #A020F0; fill: #A020F0;' /><circle cx='63.99' cy='9.61' r='0.89' style='stroke-width: 0.71; stroke: #00FF00; fill: #00FF00;' /></g></svg></td></tr>
    <tr><td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">Dunkerque (59)</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">88.11</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><div>
  <div style="height: 20px;width:70px; background-color: #D4C80033;border-radius:5px;)">
    <div style="height: 13px;width: 13px;background-color: #D4C800;display: inline-block;border-radius:4px;float:left;position:relative;top:17%;left:6%;"></div>
    <div style="display: inline-block;float:right;line-height:20px; font-weight: bold;padding: 0px 2.5px;">C</div>
  </div>
</div></td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">4.65</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">3.51</td>
<td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><?xml version='1.0' encoding='UTF-8' ?><svg xmlns='http://www.w3.org/2000/svg' xmlns:xlink='http://www.w3.org/1999/xlink' class='svglite' width='85.04pt' height='14.17pt' viewBox='0 0 85.04 14.17'><defs>  <style type='text/css'><![CDATA[    .svglite line, .svglite polyline, .svglite polygon, .svglite path, .svglite rect, .svglite circle {      fill: none;      stroke: #000000;      stroke-linecap: round;      stroke-linejoin: round;      stroke-miterlimit: 10.00;    }    .svglite text {      white-space: pre;    }  ]]></style></defs><rect width='100%' height='100%' style='stroke: none; fill: none;'/><defs>  <clipPath id='cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3'>    <rect x='0.00' y='0.00' width='85.04' height='14.17' />  </clipPath></defs><g clip-path='url(#cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3)'><text x='67.43' y='8.38' style='font-size: 5.69px; font-family: "Arial";' textLength='10.24px' lengthAdjust='spacingAndGlyphs'>3.5</text><polyline points='29.63,3.66 63.99,6.75 ' style='stroke-width: 1.07; stroke-linecap: butt;' /><circle cx='63.99' cy='6.75' r='0.89' style='stroke-width: 0.71; fill: #000000;' /><circle cx='63.99' cy='6.75' r='0.89' style='stroke-width: 0.71; stroke: #A020F0; fill: #A020F0;' /><circle cx='29.63' cy='3.66' r='0.89' style='stroke-width: 0.71; stroke: #00FF00; fill: #00FF00;' /></g></svg></td></tr>
    <tr><td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">Poitiers (86)</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">87.96</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><div>
  <div style="height: 20px;width:70px; background-color: #FF820033;border-radius:5px;)">
    <div style="height: 13px;width: 13px;background-color: #FF8200;display: inline-block;border-radius:4px;float:left;position:relative;top:17%;left:6%;"></div>
    <div style="display: inline-block;float:right;line-height:20px; font-weight: bold;padding: 0px 2.5px;">E</div>
  </div>
</div></td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">2.70</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">2.78</td>
<td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><?xml version='1.0' encoding='UTF-8' ?><svg xmlns='http://www.w3.org/2000/svg' xmlns:xlink='http://www.w3.org/1999/xlink' class='svglite' width='85.04pt' height='14.17pt' viewBox='0 0 85.04 14.17'><defs>  <style type='text/css'><![CDATA[    .svglite line, .svglite polyline, .svglite polygon, .svglite path, .svglite rect, .svglite circle {      fill: none;      stroke: #000000;      stroke-linecap: round;      stroke-linejoin: round;      stroke-miterlimit: 10.00;    }    .svglite text {      white-space: pre;    }  ]]></style></defs><rect width='100%' height='100%' style='stroke: none; fill: none;'/><defs>  <clipPath id='cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3'>    <rect x='0.00' y='0.00' width='85.04' height='14.17' />  </clipPath></defs><g clip-path='url(#cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3)'><text x='67.43' y='10.35' style='font-size: 5.69px; font-family: "Arial";' textLength='10.24px' lengthAdjust='spacingAndGlyphs'>2.8</text><polyline points='29.63,8.96 63.99,8.73 ' style='stroke-width: 1.07; stroke-linecap: butt;' /><circle cx='63.99' cy='8.73' r='0.89' style='stroke-width: 0.71; fill: #000000;' /><circle cx='29.63' cy='8.96' r='0.89' style='stroke-width: 0.71; stroke: #A020F0; fill: #A020F0;' /><circle cx='63.99' cy='8.73' r='0.89' style='stroke-width: 0.71; stroke: #00FF00; fill: #00FF00;' /></g></svg></td></tr>
    <tr><td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">Aubervilliers (93)</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">86.06</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><div>
  <div style="height: 20px;width:70px; background-color: #FF000033;border-radius:5px;)">
    <div style="height: 13px;width: 13px;background-color: #FF0000;display: inline-block;border-radius:4px;float:left;position:relative;top:17%;left:6%;"></div>
    <div style="display: inline-block;float:right;line-height:20px; font-weight: bold;padding: 0px 2.5px;">G</div>
  </div>
</div></td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">1.81</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">1.95</td>
<td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><?xml version='1.0' encoding='UTF-8' ?><svg xmlns='http://www.w3.org/2000/svg' xmlns:xlink='http://www.w3.org/1999/xlink' class='svglite' width='85.04pt' height='14.17pt' viewBox='0 0 85.04 14.17'><defs>  <style type='text/css'><![CDATA[    .svglite line, .svglite polyline, .svglite polygon, .svglite path, .svglite rect, .svglite circle {      fill: none;      stroke: #000000;      stroke-linecap: round;      stroke-linejoin: round;      stroke-miterlimit: 10.00;    }    .svglite text {      white-space: pre;    }  ]]></style></defs><rect width='100%' height='100%' style='stroke: none; fill: none;'/><defs>  <clipPath id='cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3'>    <rect x='0.00' y='0.00' width='85.04' height='14.17' />  </clipPath></defs><g clip-path='url(#cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3)'><text x='67.43' y='12.60' style='font-size: 5.69px; font-family: "Arial";' textLength='10.24px' lengthAdjust='spacingAndGlyphs'>1.9</text><polyline points='29.63,11.35 63.99,10.98 ' style='stroke-width: 1.07; stroke-linecap: butt;' /><circle cx='63.99' cy='10.98' r='0.89' style='stroke-width: 0.71; fill: #000000;' /><circle cx='29.63' cy='11.35' r='0.89' style='stroke-width: 0.71; stroke: #A020F0; fill: #A020F0;' /><circle cx='63.99' cy='10.98' r='0.89' style='stroke-width: 0.71; stroke: #00FF00; fill: #00FF00;' /></g></svg></td></tr>
    <tr><td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">Asnières-sur-Seine (92)</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">85.97</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><div>
  <div style="height: 20px;width:70px; background-color: #FF580033;border-radius:5px;)">
    <div style="height: 13px;width: 13px;background-color: #FF5800;display: inline-block;border-radius:4px;float:left;position:relative;top:17%;left:6%;"></div>
    <div style="display: inline-block;float:right;line-height:20px; font-weight: bold;padding: 0px 2.5px;">F</div>
  </div>
</div></td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">2.61</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">2.63</td>
<td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><?xml version='1.0' encoding='UTF-8' ?><svg xmlns='http://www.w3.org/2000/svg' xmlns:xlink='http://www.w3.org/1999/xlink' class='svglite' width='85.04pt' height='14.17pt' viewBox='0 0 85.04 14.17'><defs>  <style type='text/css'><![CDATA[    .svglite line, .svglite polyline, .svglite polygon, .svglite path, .svglite rect, .svglite circle {      fill: none;      stroke: #000000;      stroke-linecap: round;      stroke-linejoin: round;      stroke-miterlimit: 10.00;    }    .svglite text {      white-space: pre;    }  ]]></style></defs><rect width='100%' height='100%' style='stroke: none; fill: none;'/><defs>  <clipPath id='cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3'>    <rect x='0.00' y='0.00' width='85.04' height='14.17' />  </clipPath></defs><g clip-path='url(#cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3)'><text x='67.43' y='10.75' style='font-size: 5.69px; font-family: "Arial";' textLength='10.24px' lengthAdjust='spacingAndGlyphs'>2.6</text><polyline points='29.63,9.18 63.99,9.13 ' style='stroke-width: 1.07; stroke-linecap: butt;' /><circle cx='63.99' cy='9.13' r='0.89' style='stroke-width: 0.71; fill: #000000;' /><circle cx='29.63' cy='9.18' r='0.89' style='stroke-width: 0.71; stroke: #A020F0; fill: #A020F0;' /><circle cx='63.99' cy='9.13' r='0.89' style='stroke-width: 0.71; stroke: #00FF00; fill: #00FF00;' /></g></svg></td></tr>
    <tr><td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">Colombes (92)</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">85.37</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><div>
  <div style="height: 20px;width:70px; background-color: #FF580033;border-radius:5px;)">
    <div style="height: 13px;width: 13px;background-color: #FF5800;display: inline-block;border-radius:4px;float:left;position:relative;top:17%;left:6%;"></div>
    <div style="display: inline-block;float:right;line-height:20px; font-weight: bold;padding: 0px 2.5px;">F</div>
  </div>
</div></td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">2.51</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">2.61</td>
<td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><?xml version='1.0' encoding='UTF-8' ?><svg xmlns='http://www.w3.org/2000/svg' xmlns:xlink='http://www.w3.org/1999/xlink' class='svglite' width='85.04pt' height='14.17pt' viewBox='0 0 85.04 14.17'><defs>  <style type='text/css'><![CDATA[    .svglite line, .svglite polyline, .svglite polygon, .svglite path, .svglite rect, .svglite circle {      fill: none;      stroke: #000000;      stroke-linecap: round;      stroke-linejoin: round;      stroke-miterlimit: 10.00;    }    .svglite text {      white-space: pre;    }  ]]></style></defs><rect width='100%' height='100%' style='stroke: none; fill: none;'/><defs>  <clipPath id='cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3'>    <rect x='0.00' y='0.00' width='85.04' height='14.17' />  </clipPath></defs><g clip-path='url(#cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3)'><text x='67.43' y='10.81' style='font-size: 5.69px; font-family: "Arial";' textLength='10.24px' lengthAdjust='spacingAndGlyphs'>2.6</text><polyline points='29.63,9.45 63.99,9.19 ' style='stroke-width: 1.07; stroke-linecap: butt;' /><circle cx='63.99' cy='9.19' r='0.89' style='stroke-width: 0.71; fill: #000000;' /><circle cx='29.63' cy='9.45' r='0.89' style='stroke-width: 0.71; stroke: #A020F0; fill: #A020F0;' /><circle cx='63.99' cy='9.19' r='0.89' style='stroke-width: 0.71; stroke: #00FF00; fill: #00FF00;' /></g></svg></td></tr>
    <tr><td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">Versailles (78)</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">85.35</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><div>
  <div style="height: 20px;width:70px; background-color: #D4C80033;border-radius:5px;)">
    <div style="height: 13px;width: 13px;background-color: #D4C800;display: inline-block;border-radius:4px;float:left;position:relative;top:17%;left:6%;"></div>
    <div style="display: inline-block;float:right;line-height:20px; font-weight: bold;padding: 0px 2.5px;">C</div>
  </div>
</div></td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">4.20</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">3.64</td>
<td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><?xml version='1.0' encoding='UTF-8' ?><svg xmlns='http://www.w3.org/2000/svg' xmlns:xlink='http://www.w3.org/1999/xlink' class='svglite' width='85.04pt' height='14.17pt' viewBox='0 0 85.04 14.17'><defs>  <style type='text/css'><![CDATA[    .svglite line, .svglite polyline, .svglite polygon, .svglite path, .svglite rect, .svglite circle {      fill: none;      stroke: #000000;      stroke-linecap: round;      stroke-linejoin: round;      stroke-miterlimit: 10.00;    }    .svglite text {      white-space: pre;    }  ]]></style></defs><rect width='100%' height='100%' style='stroke: none; fill: none;'/><defs>  <clipPath id='cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3'>    <rect x='0.00' y='0.00' width='85.04' height='14.17' />  </clipPath></defs><g clip-path='url(#cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3)'><text x='67.43' y='8.02' style='font-size: 5.69px; font-family: "Arial";' textLength='10.24px' lengthAdjust='spacingAndGlyphs'>3.6</text><polyline points='29.63,4.89 63.99,6.39 ' style='stroke-width: 1.07; stroke-linecap: butt;' /><circle cx='63.99' cy='6.39' r='0.89' style='stroke-width: 0.71; fill: #000000;' /><circle cx='63.99' cy='6.39' r='0.89' style='stroke-width: 0.71; stroke: #A020F0; fill: #A020F0;' /><circle cx='29.63' cy='4.89' r='0.89' style='stroke-width: 0.71; stroke: #00FF00; fill: #00FF00;' /></g></svg></td></tr>
    <tr><td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">Aulnay-sous-Bois (93)</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">84.66</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><div>
  <div style="height: 20px;width:70px; background-color: #FF000033;border-radius:5px;)">
    <div style="height: 13px;width: 13px;background-color: #FF0000;display: inline-block;border-radius:4px;float:left;position:relative;top:17%;left:6%;"></div>
    <div style="display: inline-block;float:right;line-height:20px; font-weight: bold;padding: 0px 2.5px;">G</div>
  </div>
</div></td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">1.73</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">2.27</td>
<td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><?xml version='1.0' encoding='UTF-8' ?><svg xmlns='http://www.w3.org/2000/svg' xmlns:xlink='http://www.w3.org/1999/xlink' class='svglite' width='85.04pt' height='14.17pt' viewBox='0 0 85.04 14.17'><defs>  <style type='text/css'><![CDATA[    .svglite line, .svglite polyline, .svglite polygon, .svglite path, .svglite rect, .svglite circle {      fill: none;      stroke: #000000;      stroke-linecap: round;      stroke-linejoin: round;      stroke-miterlimit: 10.00;    }    .svglite text {      white-space: pre;    }  ]]></style></defs><rect width='100%' height='100%' style='stroke: none; fill: none;'/><defs>  <clipPath id='cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3'>    <rect x='0.00' y='0.00' width='85.04' height='14.17' />  </clipPath></defs><g clip-path='url(#cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3)'><text x='67.43' y='11.73' style='font-size: 5.69px; font-family: "Arial";' textLength='10.24px' lengthAdjust='spacingAndGlyphs'>2.3</text><polyline points='29.63,11.58 63.99,10.11 ' style='stroke-width: 1.07; stroke-linecap: butt;' /><circle cx='63.99' cy='10.11' r='0.89' style='stroke-width: 0.71; fill: #000000;' /><circle cx='29.63' cy='11.58' r='0.89' style='stroke-width: 0.71; stroke: #A020F0; fill: #A020F0;' /><circle cx='63.99' cy='10.11' r='0.89' style='stroke-width: 0.71; stroke: #00FF00; fill: #00FF00;' /></g></svg></td></tr>
    <tr><td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">Saint-Pierre (974)</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">84.17</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><div>
  <div style="height: 20px;width:70px; background-color: #FF000033;border-radius:5px;)">
    <div style="height: 13px;width: 13px;background-color: #FF0000;display: inline-block;border-radius:4px;float:left;position:relative;top:17%;left:6%;"></div>
    <div style="display: inline-block;float:right;line-height:20px; font-weight: bold;padding: 0px 2.5px;">G</div>
  </div>
</div></td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">1.62</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">2.14</td>
<td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><?xml version='1.0' encoding='UTF-8' ?><svg xmlns='http://www.w3.org/2000/svg' xmlns:xlink='http://www.w3.org/1999/xlink' class='svglite' width='85.04pt' height='14.17pt' viewBox='0 0 85.04 14.17'><defs>  <style type='text/css'><![CDATA[    .svglite line, .svglite polyline, .svglite polygon, .svglite path, .svglite rect, .svglite circle {      fill: none;      stroke: #000000;      stroke-linecap: round;      stroke-linejoin: round;      stroke-miterlimit: 10.00;    }    .svglite text {      white-space: pre;    }  ]]></style></defs><rect width='100%' height='100%' style='stroke: none; fill: none;'/><defs>  <clipPath id='cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3'>    <rect x='0.00' y='0.00' width='85.04' height='14.17' />  </clipPath></defs><g clip-path='url(#cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3)'><text x='67.43' y='12.09' style='font-size: 5.69px; font-family: "Arial";' textLength='10.24px' lengthAdjust='spacingAndGlyphs'>2.1</text><polyline points='29.63,11.86 63.99,10.46 ' style='stroke-width: 1.07; stroke-linecap: butt;' /><circle cx='63.99' cy='10.46' r='0.89' style='stroke-width: 0.71; fill: #000000;' /><circle cx='29.63' cy='11.86' r='0.89' style='stroke-width: 0.71; stroke: #A020F0; fill: #A020F0;' /><circle cx='63.99' cy='10.46' r='0.89' style='stroke-width: 0.71; stroke: #00FF00; fill: #00FF00;' /></g></svg></td></tr>
    <tr><td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">Courbevoie (92)</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">81.72</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><div>
  <div style="height: 20px;width:70px; background-color: #FF580033;border-radius:5px;)">
    <div style="height: 13px;width: 13px;background-color: #FF5800;display: inline-block;border-radius:4px;float:left;position:relative;top:17%;left:6%;"></div>
    <div style="display: inline-block;float:right;line-height:20px; font-weight: bold;padding: 0px 2.5px;">F</div>
  </div>
</div></td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">3.00</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">2.69</td>
<td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><?xml version='1.0' encoding='UTF-8' ?><svg xmlns='http://www.w3.org/2000/svg' xmlns:xlink='http://www.w3.org/1999/xlink' class='svglite' width='85.04pt' height='14.17pt' viewBox='0 0 85.04 14.17'><defs>  <style type='text/css'><![CDATA[    .svglite line, .svglite polyline, .svglite polygon, .svglite path, .svglite rect, .svglite circle {      fill: none;      stroke: #000000;      stroke-linecap: round;      stroke-linejoin: round;      stroke-miterlimit: 10.00;    }    .svglite text {      white-space: pre;    }  ]]></style></defs><rect width='100%' height='100%' style='stroke: none; fill: none;'/><defs>  <clipPath id='cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3'>    <rect x='0.00' y='0.00' width='85.04' height='14.17' />  </clipPath></defs><g clip-path='url(#cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3)'><text x='67.43' y='10.59' style='font-size: 5.69px; font-family: "Arial";' textLength='10.24px' lengthAdjust='spacingAndGlyphs'>2.7</text><polyline points='29.63,8.13 63.99,8.97 ' style='stroke-width: 1.07; stroke-linecap: butt;' /><circle cx='63.99' cy='8.97' r='0.89' style='stroke-width: 0.71; fill: #000000;' /><circle cx='63.99' cy='8.97' r='0.89' style='stroke-width: 0.71; stroke: #A020F0; fill: #A020F0;' /><circle cx='29.63' cy='8.13' r='0.89' style='stroke-width: 0.71; stroke: #00FF00; fill: #00FF00;' /></g></svg></td></tr>
    <tr><td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">Cherbourg-en-Cotentin (50)</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">80.08</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><div>
  <div style="height: 20px;width:70px; background-color: #FFA50033;border-radius:5px;)">
    <div style="height: 13px;width: 13px;background-color: #FFA500;display: inline-block;border-radius:4px;float:left;position:relative;top:17%;left:6%;"></div>
    <div style="display: inline-block;float:right;line-height:20px; font-weight: bold;padding: 0px 2.5px;">D</div>
  </div>
</div></td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">3.31</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">3.12</td>
<td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><?xml version='1.0' encoding='UTF-8' ?><svg xmlns='http://www.w3.org/2000/svg' xmlns:xlink='http://www.w3.org/1999/xlink' class='svglite' width='85.04pt' height='14.17pt' viewBox='0 0 85.04 14.17'><defs>  <style type='text/css'><![CDATA[    .svglite line, .svglite polyline, .svglite polygon, .svglite path, .svglite rect, .svglite circle {      fill: none;      stroke: #000000;      stroke-linecap: round;      stroke-linejoin: round;      stroke-miterlimit: 10.00;    }    .svglite text {      white-space: pre;    }  ]]></style></defs><rect width='100%' height='100%' style='stroke: none; fill: none;'/><defs>  <clipPath id='cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3'>    <rect x='0.00' y='0.00' width='85.04' height='14.17' />  </clipPath></defs><g clip-path='url(#cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3)'><text x='67.43' y='9.42' style='font-size: 5.69px; font-family: "Arial";' textLength='10.24px' lengthAdjust='spacingAndGlyphs'>3.1</text><polyline points='29.63,7.30 63.99,7.79 ' style='stroke-width: 1.07; stroke-linecap: butt;' /><circle cx='63.99' cy='7.79' r='0.89' style='stroke-width: 0.71; fill: #000000;' /><circle cx='63.99' cy='7.79' r='0.89' style='stroke-width: 0.71; stroke: #A020F0; fill: #A020F0;' /><circle cx='29.63' cy='7.30' r='0.89' style='stroke-width: 0.71; stroke: #00FF00; fill: #00FF00;' /></g></svg></td></tr>
    <tr><td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">Rueil-Malmaison (92)</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">78.20</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><div>
  <div style="height: 20px;width:70px; background-color: #FF820033;border-radius:5px;)">
    <div style="height: 13px;width: 13px;background-color: #FF8200;display: inline-block;border-radius:4px;float:left;position:relative;top:17%;left:6%;"></div>
    <div style="display: inline-block;float:right;line-height:20px; font-weight: bold;padding: 0px 2.5px;">E</div>
  </div>
</div></td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">3.06</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">2.94</td>
<td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><?xml version='1.0' encoding='UTF-8' ?><svg xmlns='http://www.w3.org/2000/svg' xmlns:xlink='http://www.w3.org/1999/xlink' class='svglite' width='85.04pt' height='14.17pt' viewBox='0 0 85.04 14.17'><defs>  <style type='text/css'><![CDATA[    .svglite line, .svglite polyline, .svglite polygon, .svglite path, .svglite rect, .svglite circle {      fill: none;      stroke: #000000;      stroke-linecap: round;      stroke-linejoin: round;      stroke-miterlimit: 10.00;    }    .svglite text {      white-space: pre;    }  ]]></style></defs><rect width='100%' height='100%' style='stroke: none; fill: none;'/><defs>  <clipPath id='cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3'>    <rect x='0.00' y='0.00' width='85.04' height='14.17' />  </clipPath></defs><g clip-path='url(#cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3)'><text x='67.43' y='9.93' style='font-size: 5.69px; font-family: "Arial";' textLength='10.24px' lengthAdjust='spacingAndGlyphs'>2.9</text><polyline points='29.63,7.97 63.99,8.30 ' style='stroke-width: 1.07; stroke-linecap: butt;' /><circle cx='63.99' cy='8.30' r='0.89' style='stroke-width: 0.71; fill: #000000;' /><circle cx='63.99' cy='8.30' r='0.89' style='stroke-width: 0.71; stroke: #A020F0; fill: #A020F0;' /><circle cx='29.63' cy='7.97' r='0.89' style='stroke-width: 0.71; stroke: #00FF00; fill: #00FF00;' /></g></svg></td></tr>
    <tr><td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">Champigny-sur-Marne (94)</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">77.41</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><div>
  <div style="height: 20px;width:70px; background-color: #FF000033;border-radius:5px;)">
    <div style="height: 13px;width: 13px;background-color: #FF0000;display: inline-block;border-radius:4px;float:left;position:relative;top:17%;left:6%;"></div>
    <div style="display: inline-block;float:right;line-height:20px; font-weight: bold;padding: 0px 2.5px;">G</div>
  </div>
</div></td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">2.06</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">2.29</td>
<td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><?xml version='1.0' encoding='UTF-8' ?><svg xmlns='http://www.w3.org/2000/svg' xmlns:xlink='http://www.w3.org/1999/xlink' class='svglite' width='85.04pt' height='14.17pt' viewBox='0 0 85.04 14.17'><defs>  <style type='text/css'><![CDATA[    .svglite line, .svglite polyline, .svglite polygon, .svglite path, .svglite rect, .svglite circle {      fill: none;      stroke: #000000;      stroke-linecap: round;      stroke-linejoin: round;      stroke-miterlimit: 10.00;    }    .svglite text {      white-space: pre;    }  ]]></style></defs><rect width='100%' height='100%' style='stroke: none; fill: none;'/><defs>  <clipPath id='cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3'>    <rect x='0.00' y='0.00' width='85.04' height='14.17' />  </clipPath></defs><g clip-path='url(#cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3)'><text x='67.43' y='11.67' style='font-size: 5.69px; font-family: "Arial";' textLength='10.24px' lengthAdjust='spacingAndGlyphs'>2.3</text><polyline points='29.63,10.69 63.99,10.05 ' style='stroke-width: 1.07; stroke-linecap: butt;' /><circle cx='63.99' cy='10.05' r='0.89' style='stroke-width: 0.71; fill: #000000;' /><circle cx='29.63' cy='10.69' r='0.89' style='stroke-width: 0.71; stroke: #A020F0; fill: #A020F0;' /><circle cx='63.99' cy='10.05' r='0.89' style='stroke-width: 0.71; stroke: #00FF00; fill: #00FF00;' /></g></svg></td></tr>
    <tr><td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">Pau (64)</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">77.25</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><div>
  <div style="height: 20px;width:70px; background-color: #FF820033;border-radius:5px;)">
    <div style="height: 13px;width: 13px;background-color: #FF8200;display: inline-block;border-radius:4px;float:left;position:relative;top:17%;left:6%;"></div>
    <div style="display: inline-block;float:right;line-height:20px; font-weight: bold;padding: 0px 2.5px;">E</div>
  </div>
</div></td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">3.58</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">2.92</td>
<td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><?xml version='1.0' encoding='UTF-8' ?><svg xmlns='http://www.w3.org/2000/svg' xmlns:xlink='http://www.w3.org/1999/xlink' class='svglite' width='85.04pt' height='14.17pt' viewBox='0 0 85.04 14.17'><defs>  <style type='text/css'><![CDATA[    .svglite line, .svglite polyline, .svglite polygon, .svglite path, .svglite rect, .svglite circle {      fill: none;      stroke: #000000;      stroke-linecap: round;      stroke-linejoin: round;      stroke-miterlimit: 10.00;    }    .svglite text {      white-space: pre;    }  ]]></style></defs><rect width='100%' height='100%' style='stroke: none; fill: none;'/><defs>  <clipPath id='cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3'>    <rect x='0.00' y='0.00' width='85.04' height='14.17' />  </clipPath></defs><g clip-path='url(#cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3)'><text x='67.43' y='9.96' style='font-size: 5.69px; font-family: "Arial";' textLength='10.24px' lengthAdjust='spacingAndGlyphs'>2.9</text><polyline points='29.63,6.57 63.99,8.34 ' style='stroke-width: 1.07; stroke-linecap: butt;' /><circle cx='63.99' cy='8.34' r='0.89' style='stroke-width: 0.71; fill: #000000;' /><circle cx='63.99' cy='8.34' r='0.89' style='stroke-width: 0.71; stroke: #A020F0; fill: #A020F0;' /><circle cx='29.63' cy='6.57' r='0.89' style='stroke-width: 0.71; stroke: #00FF00; fill: #00FF00;' /></g></svg></td></tr>
    <tr><td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">Béziers (34)</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">76.49</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><div>
  <div style="height: 20px;width:70px; background-color: #FF000033;border-radius:5px;)">
    <div style="height: 13px;width: 13px;background-color: #FF0000;display: inline-block;border-radius:4px;float:left;position:relative;top:17%;left:6%;"></div>
    <div style="display: inline-block;float:right;line-height:20px; font-weight: bold;padding: 0px 2.5px;">G</div>
  </div>
</div></td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">1.58</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">1.93</td>
<td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><?xml version='1.0' encoding='UTF-8' ?><svg xmlns='http://www.w3.org/2000/svg' xmlns:xlink='http://www.w3.org/1999/xlink' class='svglite' width='85.04pt' height='14.17pt' viewBox='0 0 85.04 14.17'><defs>  <style type='text/css'><![CDATA[    .svglite line, .svglite polyline, .svglite polygon, .svglite path, .svglite rect, .svglite circle {      fill: none;      stroke: #000000;      stroke-linecap: round;      stroke-linejoin: round;      stroke-miterlimit: 10.00;    }    .svglite text {      white-space: pre;    }  ]]></style></defs><rect width='100%' height='100%' style='stroke: none; fill: none;'/><defs>  <clipPath id='cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3'>    <rect x='0.00' y='0.00' width='85.04' height='14.17' />  </clipPath></defs><g clip-path='url(#cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3)'><text x='67.43' y='12.66' style='font-size: 5.69px; font-family: "Arial";' textLength='10.24px' lengthAdjust='spacingAndGlyphs'>1.9</text><polyline points='29.63,11.99 63.99,11.04 ' style='stroke-width: 1.07; stroke-linecap: butt;' /><circle cx='63.99' cy='11.04' r='0.89' style='stroke-width: 0.71; fill: #000000;' /><circle cx='29.63' cy='11.99' r='0.89' style='stroke-width: 0.71; stroke: #A020F0; fill: #A020F0;' /><circle cx='63.99' cy='11.04' r='0.89' style='stroke-width: 0.71; stroke: #00FF00; fill: #00FF00;' /></g></svg></td></tr>
    <tr><td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">La Rochelle (17)</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">75.74</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><div>
  <div style="height: 20px;width:70px; background-color: #9BE50033;border-radius:5px;)">
    <div style="height: 13px;width: 13px;background-color: #9BE500;display: inline-block;border-radius:4px;float:left;position:relative;top:17%;left:6%;"></div>
    <div style="display: inline-block;float:right;line-height:20px; font-weight: bold;padding: 0px 2.5px;">B</div>
  </div>
</div></td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">5.10</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">4.04</td>
<td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><?xml version='1.0' encoding='UTF-8' ?><svg xmlns='http://www.w3.org/2000/svg' xmlns:xlink='http://www.w3.org/1999/xlink' class='svglite' width='85.04pt' height='14.17pt' viewBox='0 0 85.04 14.17'><defs>  <style type='text/css'><![CDATA[    .svglite line, .svglite polyline, .svglite polygon, .svglite path, .svglite rect, .svglite circle {      fill: none;      stroke: #000000;      stroke-linecap: round;      stroke-linejoin: round;      stroke-miterlimit: 10.00;    }    .svglite text {      white-space: pre;    }  ]]></style></defs><rect width='100%' height='100%' style='stroke: none; fill: none;'/><defs>  <clipPath id='cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3'>    <rect x='0.00' y='0.00' width='85.04' height='14.17' />  </clipPath></defs><g clip-path='url(#cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3)'><text x='67.43' y='6.95' style='font-size: 5.69px; font-family: "Arial";' textLength='10.24px' lengthAdjust='spacingAndGlyphs'>4.0</text><polyline points='29.63,2.44 63.99,5.32 ' style='stroke-width: 1.07; stroke-linecap: butt;' /><circle cx='63.99' cy='5.32' r='0.89' style='stroke-width: 0.71; fill: #000000;' /><circle cx='63.99' cy='5.32' r='0.89' style='stroke-width: 0.71; stroke: #A020F0; fill: #A020F0;' /><circle cx='29.63' cy='2.44' r='0.89' style='stroke-width: 0.71; stroke: #00FF00; fill: #00FF00;' /></g></svg></td></tr>
    <tr><td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">Calais (62)</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">74.98</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><div>
  <div style="height: 20px;width:70px; background-color: #FF580033;border-radius:5px;)">
    <div style="height: 13px;width: 13px;background-color: #FF5800;display: inline-block;border-radius:4px;float:left;position:relative;top:17%;left:6%;"></div>
    <div style="display: inline-block;float:right;line-height:20px; font-weight: bold;padding: 0px 2.5px;">F</div>
  </div>
</div></td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">2.30</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">2.46</td>
<td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><?xml version='1.0' encoding='UTF-8' ?><svg xmlns='http://www.w3.org/2000/svg' xmlns:xlink='http://www.w3.org/1999/xlink' class='svglite' width='85.04pt' height='14.17pt' viewBox='0 0 85.04 14.17'><defs>  <style type='text/css'><![CDATA[    .svglite line, .svglite polyline, .svglite polygon, .svglite path, .svglite rect, .svglite circle {      fill: none;      stroke: #000000;      stroke-linecap: round;      stroke-linejoin: round;      stroke-miterlimit: 10.00;    }    .svglite text {      white-space: pre;    }  ]]></style></defs><rect width='100%' height='100%' style='stroke: none; fill: none;'/><defs>  <clipPath id='cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3'>    <rect x='0.00' y='0.00' width='85.04' height='14.17' />  </clipPath></defs><g clip-path='url(#cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3)'><text x='67.43' y='11.21' style='font-size: 5.69px; font-family: "Arial";' textLength='10.24px' lengthAdjust='spacingAndGlyphs'>2.5</text><polyline points='29.63,10.03 63.99,9.58 ' style='stroke-width: 1.07; stroke-linecap: butt;' /><circle cx='63.99' cy='9.58' r='0.89' style='stroke-width: 0.71; fill: #000000;' /><circle cx='29.63' cy='10.03' r='0.89' style='stroke-width: 0.71; stroke: #A020F0; fill: #A020F0;' /><circle cx='63.99' cy='9.58' r='0.89' style='stroke-width: 0.71; stroke: #00FF00; fill: #00FF00;' /></g></svg></td></tr>
    <tr><td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">Saint-Maur-des-Fossés (94)</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">74.89</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><div>
  <div style="height: 20px;width:70px; background-color: #FF820033;border-radius:5px;)">
    <div style="height: 13px;width: 13px;background-color: #FF8200;display: inline-block;border-radius:4px;float:left;position:relative;top:17%;left:6%;"></div>
    <div style="display: inline-block;float:right;line-height:20px; font-weight: bold;padding: 0px 2.5px;">E</div>
  </div>
</div></td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">3.43</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">2.92</td>
<td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><?xml version='1.0' encoding='UTF-8' ?><svg xmlns='http://www.w3.org/2000/svg' xmlns:xlink='http://www.w3.org/1999/xlink' class='svglite' width='85.04pt' height='14.17pt' viewBox='0 0 85.04 14.17'><defs>  <style type='text/css'><![CDATA[    .svglite line, .svglite polyline, .svglite polygon, .svglite path, .svglite rect, .svglite circle {      fill: none;      stroke: #000000;      stroke-linecap: round;      stroke-linejoin: round;      stroke-miterlimit: 10.00;    }    .svglite text {      white-space: pre;    }  ]]></style></defs><rect width='100%' height='100%' style='stroke: none; fill: none;'/><defs>  <clipPath id='cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3'>    <rect x='0.00' y='0.00' width='85.04' height='14.17' />  </clipPath></defs><g clip-path='url(#cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3)'><text x='67.43' y='9.98' style='font-size: 5.69px; font-family: "Arial";' textLength='10.24px' lengthAdjust='spacingAndGlyphs'>2.9</text><polyline points='29.63,6.95 63.99,8.36 ' style='stroke-width: 1.07; stroke-linecap: butt;' /><circle cx='63.99' cy='8.36' r='0.89' style='stroke-width: 0.71; fill: #000000;' /><circle cx='63.99' cy='8.36' r='0.89' style='stroke-width: 0.71; stroke: #A020F0; fill: #A020F0;' /><circle cx='29.63' cy='6.95' r='0.89' style='stroke-width: 0.71; stroke: #00FF00; fill: #00FF00;' /></g></svg></td></tr>
    <tr><td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">Cannes (06)</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">74.15</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><div>
  <div style="height: 20px;width:70px; background-color: #FF580033;border-radius:5px;)">
    <div style="height: 13px;width: 13px;background-color: #FF5800;display: inline-block;border-radius:4px;float:left;position:relative;top:17%;left:6%;"></div>
    <div style="display: inline-block;float:right;line-height:20px; font-weight: bold;padding: 0px 2.5px;">F</div>
  </div>
</div></td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">2.92</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">2.50</td>
<td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><?xml version='1.0' encoding='UTF-8' ?><svg xmlns='http://www.w3.org/2000/svg' xmlns:xlink='http://www.w3.org/1999/xlink' class='svglite' width='85.04pt' height='14.17pt' viewBox='0 0 85.04 14.17'><defs>  <style type='text/css'><![CDATA[    .svglite line, .svglite polyline, .svglite polygon, .svglite path, .svglite rect, .svglite circle {      fill: none;      stroke: #000000;      stroke-linecap: round;      stroke-linejoin: round;      stroke-miterlimit: 10.00;    }    .svglite text {      white-space: pre;    }  ]]></style></defs><rect width='100%' height='100%' style='stroke: none; fill: none;'/><defs>  <clipPath id='cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3'>    <rect x='0.00' y='0.00' width='85.04' height='14.17' />  </clipPath></defs><g clip-path='url(#cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3)'><text x='67.43' y='11.10' style='font-size: 5.69px; font-family: "Arial";' textLength='10.24px' lengthAdjust='spacingAndGlyphs'>2.5</text><polyline points='29.63,8.34 63.99,9.47 ' style='stroke-width: 1.07; stroke-linecap: butt;' /><circle cx='63.99' cy='9.47' r='0.89' style='stroke-width: 0.71; fill: #000000;' /><circle cx='63.99' cy='9.47' r='0.89' style='stroke-width: 0.71; stroke: #A020F0; fill: #A020F0;' /><circle cx='29.63' cy='8.34' r='0.89' style='stroke-width: 0.71; stroke: #00FF00; fill: #00FF00;' /></g></svg></td></tr>
    <tr><td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">Antibes (06)</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">73.80</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><div>
  <div style="height: 20px;width:70px; background-color: #FF000033;border-radius:5px;)">
    <div style="height: 13px;width: 13px;background-color: #FF0000;display: inline-block;border-radius:4px;float:left;position:relative;top:17%;left:6%;"></div>
    <div style="display: inline-block;float:right;line-height:20px; font-weight: bold;padding: 0px 2.5px;">G</div>
  </div>
</div></td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">2.26</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">2.24</td>
<td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><?xml version='1.0' encoding='UTF-8' ?><svg xmlns='http://www.w3.org/2000/svg' xmlns:xlink='http://www.w3.org/1999/xlink' class='svglite' width='85.04pt' height='14.17pt' viewBox='0 0 85.04 14.17'><defs>  <style type='text/css'><![CDATA[    .svglite line, .svglite polyline, .svglite polygon, .svglite path, .svglite rect, .svglite circle {      fill: none;      stroke: #000000;      stroke-linecap: round;      stroke-linejoin: round;      stroke-miterlimit: 10.00;    }    .svglite text {      white-space: pre;    }  ]]></style></defs><rect width='100%' height='100%' style='stroke: none; fill: none;'/><defs>  <clipPath id='cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3'>    <rect x='0.00' y='0.00' width='85.04' height='14.17' />  </clipPath></defs><g clip-path='url(#cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3)'><text x='67.43' y='11.82' style='font-size: 5.69px; font-family: "Arial";' textLength='10.24px' lengthAdjust='spacingAndGlyphs'>2.2</text><polyline points='29.63,10.14 63.99,10.20 ' style='stroke-width: 1.07; stroke-linecap: butt;' /><circle cx='63.99' cy='10.20' r='0.89' style='stroke-width: 0.71; fill: #000000;' /><circle cx='63.99' cy='10.20' r='0.89' style='stroke-width: 0.71; stroke: #A020F0; fill: #A020F0;' /><circle cx='29.63' cy='10.14' r='0.89' style='stroke-width: 0.71; stroke: #00FF00; fill: #00FF00;' /></g></svg></td></tr>
    <tr><td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">Mérignac (33)</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">70.32</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><div>
  <div style="height: 20px;width:70px; background-color: #FFA50033;border-radius:5px;)">
    <div style="height: 13px;width: 13px;background-color: #FFA500;display: inline-block;border-radius:4px;float:left;position:relative;top:17%;left:6%;"></div>
    <div style="display: inline-block;float:right;line-height:20px; font-weight: bold;padding: 0px 2.5px;">D</div>
  </div>
</div></td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">3.75</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">3.31</td>
<td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><?xml version='1.0' encoding='UTF-8' ?><svg xmlns='http://www.w3.org/2000/svg' xmlns:xlink='http://www.w3.org/1999/xlink' class='svglite' width='85.04pt' height='14.17pt' viewBox='0 0 85.04 14.17'><defs>  <style type='text/css'><![CDATA[    .svglite line, .svglite polyline, .svglite polygon, .svglite path, .svglite rect, .svglite circle {      fill: none;      stroke: #000000;      stroke-linecap: round;      stroke-linejoin: round;      stroke-miterlimit: 10.00;    }    .svglite text {      white-space: pre;    }  ]]></style></defs><rect width='100%' height='100%' style='stroke: none; fill: none;'/><defs>  <clipPath id='cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3'>    <rect x='0.00' y='0.00' width='85.04' height='14.17' />  </clipPath></defs><g clip-path='url(#cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3)'><text x='67.43' y='8.90' style='font-size: 5.69px; font-family: "Arial";' textLength='10.24px' lengthAdjust='spacingAndGlyphs'>3.3</text><polyline points='29.63,6.09 63.99,7.28 ' style='stroke-width: 1.07; stroke-linecap: butt;' /><circle cx='63.99' cy='7.28' r='0.89' style='stroke-width: 0.71; fill: #000000;' /><circle cx='63.99' cy='7.28' r='0.89' style='stroke-width: 0.71; stroke: #A020F0; fill: #A020F0;' /><circle cx='29.63' cy='6.09' r='0.89' style='stroke-width: 0.71; stroke: #00FF00; fill: #00FF00;' /></g></svg></td></tr>
    <tr><td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">Colmar (68)</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">69.90</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><div>
  <div style="height: 20px;width:70px; background-color: #FFA50033;border-radius:5px;)">
    <div style="height: 13px;width: 13px;background-color: #FFA500;display: inline-block;border-radius:4px;float:left;position:relative;top:17%;left:6%;"></div>
    <div style="display: inline-block;float:right;line-height:20px; font-weight: bold;padding: 0px 2.5px;">D</div>
  </div>
</div></td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">3.40</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">3.15</td>
<td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><?xml version='1.0' encoding='UTF-8' ?><svg xmlns='http://www.w3.org/2000/svg' xmlns:xlink='http://www.w3.org/1999/xlink' class='svglite' width='85.04pt' height='14.17pt' viewBox='0 0 85.04 14.17'><defs>  <style type='text/css'><![CDATA[    .svglite line, .svglite polyline, .svglite polygon, .svglite path, .svglite rect, .svglite circle {      fill: none;      stroke: #000000;      stroke-linecap: round;      stroke-linejoin: round;      stroke-miterlimit: 10.00;    }    .svglite text {      white-space: pre;    }  ]]></style></defs><rect width='100%' height='100%' style='stroke: none; fill: none;'/><defs>  <clipPath id='cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3'>    <rect x='0.00' y='0.00' width='85.04' height='14.17' />  </clipPath></defs><g clip-path='url(#cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3)'><text x='67.43' y='9.36' style='font-size: 5.69px; font-family: "Arial";' textLength='10.24px' lengthAdjust='spacingAndGlyphs'>3.1</text><polyline points='29.63,7.06 63.99,7.73 ' style='stroke-width: 1.07; stroke-linecap: butt;' /><circle cx='63.99' cy='7.73' r='0.89' style='stroke-width: 0.71; fill: #000000;' /><circle cx='63.99' cy='7.73' r='0.89' style='stroke-width: 0.71; stroke: #A020F0; fill: #A020F0;' /><circle cx='29.63' cy='7.06' r='0.89' style='stroke-width: 0.71; stroke: #00FF00; fill: #00FF00;' /></g></svg></td></tr>
    <tr><td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">Saint-Nazaire (44)</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">69.72</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><div>
  <div style="height: 20px;width:70px; background-color: #FFA50033;border-radius:5px;)">
    <div style="height: 13px;width: 13px;background-color: #FFA500;display: inline-block;border-radius:4px;float:left;position:relative;top:17%;left:6%;"></div>
    <div style="display: inline-block;float:right;line-height:20px; font-weight: bold;padding: 0px 2.5px;">D</div>
  </div>
</div></td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">4.18</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">3.45</td>
<td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><?xml version='1.0' encoding='UTF-8' ?><svg xmlns='http://www.w3.org/2000/svg' xmlns:xlink='http://www.w3.org/1999/xlink' class='svglite' width='85.04pt' height='14.17pt' viewBox='0 0 85.04 14.17'><defs>  <style type='text/css'><![CDATA[    .svglite line, .svglite polyline, .svglite polygon, .svglite path, .svglite rect, .svglite circle {      fill: none;      stroke: #000000;      stroke-linecap: round;      stroke-linejoin: round;      stroke-miterlimit: 10.00;    }    .svglite text {      white-space: pre;    }  ]]></style></defs><rect width='100%' height='100%' style='stroke: none; fill: none;'/><defs>  <clipPath id='cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3'>    <rect x='0.00' y='0.00' width='85.04' height='14.17' />  </clipPath></defs><g clip-path='url(#cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3)'><text x='67.43' y='8.54' style='font-size: 5.69px; font-family: "Arial";' textLength='10.24px' lengthAdjust='spacingAndGlyphs'>3.4</text><polyline points='29.63,4.94 63.99,6.92 ' style='stroke-width: 1.07; stroke-linecap: butt;' /><circle cx='63.99' cy='6.92' r='0.89' style='stroke-width: 0.71; fill: #000000;' /><circle cx='63.99' cy='6.92' r='0.89' style='stroke-width: 0.71; stroke: #A020F0; fill: #A020F0;' /><circle cx='29.63' cy='4.94' r='0.89' style='stroke-width: 0.71; stroke: #00FF00; fill: #00FF00;' /></g></svg></td></tr>
    <tr><td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">Ajaccio (2A)</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">69.08</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><div>
  <div style="height: 20px;width:70px; background-color: #FF000033;border-radius:5px;)">
    <div style="height: 13px;width: 13px;background-color: #FF0000;display: inline-block;border-radius:4px;float:left;position:relative;top:17%;left:6%;"></div>
    <div style="display: inline-block;float:right;line-height:20px; font-weight: bold;padding: 0px 2.5px;">G</div>
  </div>
</div></td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">1.90</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">2.04</td>
<td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><?xml version='1.0' encoding='UTF-8' ?><svg xmlns='http://www.w3.org/2000/svg' xmlns:xlink='http://www.w3.org/1999/xlink' class='svglite' width='85.04pt' height='14.17pt' viewBox='0 0 85.04 14.17'><defs>  <style type='text/css'><![CDATA[    .svglite line, .svglite polyline, .svglite polygon, .svglite path, .svglite rect, .svglite circle {      fill: none;      stroke: #000000;      stroke-linecap: round;      stroke-linejoin: round;      stroke-miterlimit: 10.00;    }    .svglite text {      white-space: pre;    }  ]]></style></defs><rect width='100%' height='100%' style='stroke: none; fill: none;'/><defs>  <clipPath id='cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3'>    <rect x='0.00' y='0.00' width='85.04' height='14.17' />  </clipPath></defs><g clip-path='url(#cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3)'><text x='67.43' y='12.36' style='font-size: 5.69px; font-family: "Arial";' textLength='10.24px' lengthAdjust='spacingAndGlyphs'>2.0</text><polyline points='29.63,11.12 63.99,10.73 ' style='stroke-width: 1.07; stroke-linecap: butt;' /><circle cx='63.99' cy='10.73' r='0.89' style='stroke-width: 0.71; fill: #000000;' /><circle cx='29.63' cy='11.12' r='0.89' style='stroke-width: 0.71; stroke: #A020F0; fill: #A020F0;' /><circle cx='63.99' cy='10.73' r='0.89' style='stroke-width: 0.71; stroke: #00FF00; fill: #00FF00;' /></g></svg></td></tr>
    <tr><td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">Issy-les-Moulineaux (92)</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">68.39</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><div>
  <div style="height: 20px;width:70px; background-color: #FF820033;border-radius:5px;)">
    <div style="height: 13px;width: 13px;background-color: #FF8200;display: inline-block;border-radius:4px;float:left;position:relative;top:17%;left:6%;"></div>
    <div style="display: inline-block;float:right;line-height:20px; font-weight: bold;padding: 0px 2.5px;">E</div>
  </div>
</div></td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">2.91</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">2.81</td>
<td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><?xml version='1.0' encoding='UTF-8' ?><svg xmlns='http://www.w3.org/2000/svg' xmlns:xlink='http://www.w3.org/1999/xlink' class='svglite' width='85.04pt' height='14.17pt' viewBox='0 0 85.04 14.17'><defs>  <style type='text/css'><![CDATA[    .svglite line, .svglite polyline, .svglite polygon, .svglite path, .svglite rect, .svglite circle {      fill: none;      stroke: #000000;      stroke-linecap: round;      stroke-linejoin: round;      stroke-miterlimit: 10.00;    }    .svglite text {      white-space: pre;    }  ]]></style></defs><rect width='100%' height='100%' style='stroke: none; fill: none;'/><defs>  <clipPath id='cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3'>    <rect x='0.00' y='0.00' width='85.04' height='14.17' />  </clipPath></defs><g clip-path='url(#cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3)'><text x='67.43' y='10.27' style='font-size: 5.69px; font-family: "Arial";' textLength='10.24px' lengthAdjust='spacingAndGlyphs'>2.8</text><polyline points='29.63,8.38 63.99,8.65 ' style='stroke-width: 1.07; stroke-linecap: butt;' /><circle cx='63.99' cy='8.65' r='0.89' style='stroke-width: 0.71; fill: #000000;' /><circle cx='63.99' cy='8.65' r='0.89' style='stroke-width: 0.71; stroke: #A020F0; fill: #A020F0;' /><circle cx='29.63' cy='8.38' r='0.89' style='stroke-width: 0.71; stroke: #00FF00; fill: #00FF00;' /></g></svg></td></tr>
    <tr><td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">Noisy-le-Grand (93)</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">66.66</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><div>
  <div style="height: 20px;width:70px; background-color: #FF000033;border-radius:5px;)">
    <div style="height: 13px;width: 13px;background-color: #FF0000;display: inline-block;border-radius:4px;float:left;position:relative;top:17%;left:6%;"></div>
    <div style="display: inline-block;float:right;line-height:20px; font-weight: bold;padding: 0px 2.5px;">G</div>
  </div>
</div></td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">2.06</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">2.29</td>
<td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><?xml version='1.0' encoding='UTF-8' ?><svg xmlns='http://www.w3.org/2000/svg' xmlns:xlink='http://www.w3.org/1999/xlink' class='svglite' width='85.04pt' height='14.17pt' viewBox='0 0 85.04 14.17'><defs>  <style type='text/css'><![CDATA[    .svglite line, .svglite polyline, .svglite polygon, .svglite path, .svglite rect, .svglite circle {      fill: none;      stroke: #000000;      stroke-linecap: round;      stroke-linejoin: round;      stroke-miterlimit: 10.00;    }    .svglite text {      white-space: pre;    }  ]]></style></defs><rect width='100%' height='100%' style='stroke: none; fill: none;'/><defs>  <clipPath id='cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3'>    <rect x='0.00' y='0.00' width='85.04' height='14.17' />  </clipPath></defs><g clip-path='url(#cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3)'><text x='67.43' y='11.68' style='font-size: 5.69px; font-family: "Arial";' textLength='10.24px' lengthAdjust='spacingAndGlyphs'>2.3</text><polyline points='29.63,10.68 63.99,10.06 ' style='stroke-width: 1.07; stroke-linecap: butt;' /><circle cx='63.99' cy='10.06' r='0.89' style='stroke-width: 0.71; fill: #000000;' /><circle cx='29.63' cy='10.68' r='0.89' style='stroke-width: 0.71; stroke: #A020F0; fill: #A020F0;' /><circle cx='63.99' cy='10.06' r='0.89' style='stroke-width: 0.71; stroke: #00FF00; fill: #00FF00;' /></g></svg></td></tr>
    <tr><td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">Bourges (18)</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">65.56</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><div>
  <div style="height: 20px;width:70px; background-color: #FF820033;border-radius:5px;)">
    <div style="height: 13px;width: 13px;background-color: #FF8200;display: inline-block;border-radius:4px;float:left;position:relative;top:17%;left:6%;"></div>
    <div style="display: inline-block;float:right;line-height:20px; font-weight: bold;padding: 0px 2.5px;">E</div>
  </div>
</div></td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">3.13</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">2.75</td>
<td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><?xml version='1.0' encoding='UTF-8' ?><svg xmlns='http://www.w3.org/2000/svg' xmlns:xlink='http://www.w3.org/1999/xlink' class='svglite' width='85.04pt' height='14.17pt' viewBox='0 0 85.04 14.17'><defs>  <style type='text/css'><![CDATA[    .svglite line, .svglite polyline, .svglite polygon, .svglite path, .svglite rect, .svglite circle {      fill: none;      stroke: #000000;      stroke-linecap: round;      stroke-linejoin: round;      stroke-miterlimit: 10.00;    }    .svglite text {      white-space: pre;    }  ]]></style></defs><rect width='100%' height='100%' style='stroke: none; fill: none;'/><defs>  <clipPath id='cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3'>    <rect x='0.00' y='0.00' width='85.04' height='14.17' />  </clipPath></defs><g clip-path='url(#cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3)'><text x='67.43' y='10.43' style='font-size: 5.69px; font-family: "Arial";' textLength='10.24px' lengthAdjust='spacingAndGlyphs'>2.7</text><polyline points='29.63,7.77 63.99,8.81 ' style='stroke-width: 1.07; stroke-linecap: butt;' /><circle cx='63.99' cy='8.81' r='0.89' style='stroke-width: 0.71; fill: #000000;' /><circle cx='63.99' cy='8.81' r='0.89' style='stroke-width: 0.71; stroke: #A020F0; fill: #A020F0;' /><circle cx='29.63' cy='7.77' r='0.89' style='stroke-width: 0.71; stroke: #00FF00; fill: #00FF00;' /></g></svg></td></tr>
    <tr><td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">Vénissieux (69)</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">65.41</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><div>
  <div style="height: 20px;width:70px; background-color: #FF820033;border-radius:5px;)">
    <div style="height: 13px;width: 13px;background-color: #FF8200;display: inline-block;border-radius:4px;float:left;position:relative;top:17%;left:6%;"></div>
    <div style="display: inline-block;float:right;line-height:20px; font-weight: bold;padding: 0px 2.5px;">E</div>
  </div>
</div></td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">3.15</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">2.75</td>
<td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><?xml version='1.0' encoding='UTF-8' ?><svg xmlns='http://www.w3.org/2000/svg' xmlns:xlink='http://www.w3.org/1999/xlink' class='svglite' width='85.04pt' height='14.17pt' viewBox='0 0 85.04 14.17'><defs>  <style type='text/css'><![CDATA[    .svglite line, .svglite polyline, .svglite polygon, .svglite path, .svglite rect, .svglite circle {      fill: none;      stroke: #000000;      stroke-linecap: round;      stroke-linejoin: round;      stroke-miterlimit: 10.00;    }    .svglite text {      white-space: pre;    }  ]]></style></defs><rect width='100%' height='100%' style='stroke: none; fill: none;'/><defs>  <clipPath id='cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3'>    <rect x='0.00' y='0.00' width='85.04' height='14.17' />  </clipPath></defs><g clip-path='url(#cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3)'><text x='67.43' y='10.42' style='font-size: 5.69px; font-family: "Arial";' textLength='10.24px' lengthAdjust='spacingAndGlyphs'>2.8</text><polyline points='29.63,7.72 63.99,8.80 ' style='stroke-width: 1.07; stroke-linecap: butt;' /><circle cx='63.99' cy='8.80' r='0.89' style='stroke-width: 0.71; fill: #000000;' /><circle cx='63.99' cy='8.80' r='0.89' style='stroke-width: 0.71; stroke: #A020F0; fill: #A020F0;' /><circle cx='29.63' cy='7.72' r='0.89' style='stroke-width: 0.71; stroke: #00FF00; fill: #00FF00;' /></g></svg></td></tr>
    <tr><td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">La Seyne-sur-Mer (83)</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">64.62</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><div>
  <div style="height: 20px;width:70px; background-color: #FF000033;border-radius:5px;)">
    <div style="height: 13px;width: 13px;background-color: #FF0000;display: inline-block;border-radius:4px;float:left;position:relative;top:17%;left:6%;"></div>
    <div style="display: inline-block;float:right;line-height:20px; font-weight: bold;padding: 0px 2.5px;">G</div>
  </div>
</div></td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">1.45</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">2.05</td>
<td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><?xml version='1.0' encoding='UTF-8' ?><svg xmlns='http://www.w3.org/2000/svg' xmlns:xlink='http://www.w3.org/1999/xlink' class='svglite' width='85.04pt' height='14.17pt' viewBox='0 0 85.04 14.17'><defs>  <style type='text/css'><![CDATA[    .svglite line, .svglite polyline, .svglite polygon, .svglite path, .svglite rect, .svglite circle {      fill: none;      stroke: #000000;      stroke-linecap: round;      stroke-linejoin: round;      stroke-miterlimit: 10.00;    }    .svglite text {      white-space: pre;    }  ]]></style></defs><rect width='100%' height='100%' style='stroke: none; fill: none;'/><defs>  <clipPath id='cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3'>    <rect x='0.00' y='0.00' width='85.04' height='14.17' />  </clipPath></defs><g clip-path='url(#cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3)'><text x='67.43' y='12.32' style='font-size: 5.69px; font-family: "Arial";' textLength='10.24px' lengthAdjust='spacingAndGlyphs'>2.1</text><polyline points='29.63,12.34 63.99,10.70 ' style='stroke-width: 1.07; stroke-linecap: butt;' /><circle cx='63.99' cy='10.70' r='0.89' style='stroke-width: 0.71; fill: #000000;' /><circle cx='29.63' cy='12.34' r='0.89' style='stroke-width: 0.71; stroke: #A020F0; fill: #A020F0;' /><circle cx='63.99' cy='10.70' r='0.89' style='stroke-width: 0.71; stroke: #00FF00; fill: #00FF00;' /></g></svg></td></tr>
    <tr><td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">Cergy (95)</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">63.82</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><div>
  <div style="height: 20px;width:70px; background-color: #FFA50033;border-radius:5px;)">
    <div style="height: 13px;width: 13px;background-color: #FFA500;display: inline-block;border-radius:4px;float:left;position:relative;top:17%;left:6%;"></div>
    <div style="display: inline-block;float:right;line-height:20px; font-weight: bold;padding: 0px 2.5px;">D</div>
  </div>
</div></td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">3.45</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">3.20</td>
<td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><?xml version='1.0' encoding='UTF-8' ?><svg xmlns='http://www.w3.org/2000/svg' xmlns:xlink='http://www.w3.org/1999/xlink' class='svglite' width='85.04pt' height='14.17pt' viewBox='0 0 85.04 14.17'><defs>  <style type='text/css'><![CDATA[    .svglite line, .svglite polyline, .svglite polygon, .svglite path, .svglite rect, .svglite circle {      fill: none;      stroke: #000000;      stroke-linecap: round;      stroke-linejoin: round;      stroke-miterlimit: 10.00;    }    .svglite text {      white-space: pre;    }  ]]></style></defs><rect width='100%' height='100%' style='stroke: none; fill: none;'/><defs>  <clipPath id='cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3'>    <rect x='0.00' y='0.00' width='85.04' height='14.17' />  </clipPath></defs><g clip-path='url(#cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3)'><text x='67.43' y='9.21' style='font-size: 5.69px; font-family: "Arial";' textLength='10.24px' lengthAdjust='spacingAndGlyphs'>3.2</text><polyline points='29.63,6.91 63.99,7.59 ' style='stroke-width: 1.07; stroke-linecap: butt;' /><circle cx='63.99' cy='7.59' r='0.89' style='stroke-width: 0.71; fill: #000000;' /><circle cx='63.99' cy='7.59' r='0.89' style='stroke-width: 0.71; stroke: #A020F0; fill: #A020F0;' /><circle cx='29.63' cy='6.91' r='0.89' style='stroke-width: 0.71; stroke: #00FF00; fill: #00FF00;' /></g></svg></td></tr>
    <tr><td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">Levallois-Perret (92)</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">63.46</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><div>
  <div style="height: 20px;width:70px; background-color: #FF000033;border-radius:5px;)">
    <div style="height: 13px;width: 13px;background-color: #FF0000;display: inline-block;border-radius:4px;float:left;position:relative;top:17%;left:6%;"></div>
    <div style="display: inline-block;float:right;line-height:20px; font-weight: bold;padding: 0px 2.5px;">G</div>
  </div>
</div></td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">1.73</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">2.05</td>
<td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><?xml version='1.0' encoding='UTF-8' ?><svg xmlns='http://www.w3.org/2000/svg' xmlns:xlink='http://www.w3.org/1999/xlink' class='svglite' width='85.04pt' height='14.17pt' viewBox='0 0 85.04 14.17'><defs>  <style type='text/css'><![CDATA[    .svglite line, .svglite polyline, .svglite polygon, .svglite path, .svglite rect, .svglite circle {      fill: none;      stroke: #000000;      stroke-linecap: round;      stroke-linejoin: round;      stroke-miterlimit: 10.00;    }    .svglite text {      white-space: pre;    }  ]]></style></defs><rect width='100%' height='100%' style='stroke: none; fill: none;'/><defs>  <clipPath id='cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3'>    <rect x='0.00' y='0.00' width='85.04' height='14.17' />  </clipPath></defs><g clip-path='url(#cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3)'><text x='67.43' y='12.32' style='font-size: 5.69px; font-family: "Arial";' textLength='10.24px' lengthAdjust='spacingAndGlyphs'>2.1</text><polyline points='29.63,11.56 63.99,10.70 ' style='stroke-width: 1.07; stroke-linecap: butt;' /><circle cx='63.99' cy='10.70' r='0.89' style='stroke-width: 0.71; fill: #000000;' /><circle cx='29.63' cy='11.56' r='0.89' style='stroke-width: 0.71; stroke: #A020F0; fill: #A020F0;' /><circle cx='63.99' cy='10.70' r='0.89' style='stroke-width: 0.71; stroke: #00FF00; fill: #00FF00;' /></g></svg></td></tr>
    <tr><td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">Quimper (29)</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">63.41</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><div>
  <div style="height: 20px;width:70px; background-color: #FF580033;border-radius:5px;)">
    <div style="height: 13px;width: 13px;background-color: #FF5800;display: inline-block;border-radius:4px;float:left;position:relative;top:17%;left:6%;"></div>
    <div style="display: inline-block;float:right;line-height:20px; font-weight: bold;padding: 0px 2.5px;">F</div>
  </div>
</div></td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">2.65</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">2.60</td>
<td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><?xml version='1.0' encoding='UTF-8' ?><svg xmlns='http://www.w3.org/2000/svg' xmlns:xlink='http://www.w3.org/1999/xlink' class='svglite' width='85.04pt' height='14.17pt' viewBox='0 0 85.04 14.17'><defs>  <style type='text/css'><![CDATA[    .svglite line, .svglite polyline, .svglite polygon, .svglite path, .svglite rect, .svglite circle {      fill: none;      stroke: #000000;      stroke-linecap: round;      stroke-linejoin: round;      stroke-miterlimit: 10.00;    }    .svglite text {      white-space: pre;    }  ]]></style></defs><rect width='100%' height='100%' style='stroke: none; fill: none;'/><defs>  <clipPath id='cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3'>    <rect x='0.00' y='0.00' width='85.04' height='14.17' />  </clipPath></defs><g clip-path='url(#cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3)'><text x='67.43' y='10.83' style='font-size: 5.69px; font-family: "Arial";' textLength='10.24px' lengthAdjust='spacingAndGlyphs'>2.6</text><polyline points='29.63,9.09 63.99,9.21 ' style='stroke-width: 1.07; stroke-linecap: butt;' /><circle cx='63.99' cy='9.21' r='0.89' style='stroke-width: 0.71; fill: #000000;' /><circle cx='63.99' cy='9.21' r='0.89' style='stroke-width: 0.71; stroke: #A020F0; fill: #A020F0;' /><circle cx='29.63' cy='9.09' r='0.89' style='stroke-width: 0.71; stroke: #00FF00; fill: #00FF00;' /></g></svg></td></tr>
    <tr><td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">Valence (26)</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">62.48</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><div>
  <div style="height: 20px;width:70px; background-color: #FFA50033;border-radius:5px;)">
    <div style="height: 13px;width: 13px;background-color: #FFA500;display: inline-block;border-radius:4px;float:left;position:relative;top:17%;left:6%;"></div>
    <div style="display: inline-block;float:right;line-height:20px; font-weight: bold;padding: 0px 2.5px;">D</div>
  </div>
</div></td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">3.39</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">3.19</td>
<td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><?xml version='1.0' encoding='UTF-8' ?><svg xmlns='http://www.w3.org/2000/svg' xmlns:xlink='http://www.w3.org/1999/xlink' class='svglite' width='85.04pt' height='14.17pt' viewBox='0 0 85.04 14.17'><defs>  <style type='text/css'><![CDATA[    .svglite line, .svglite polyline, .svglite polygon, .svglite path, .svglite rect, .svglite circle {      fill: none;      stroke: #000000;      stroke-linecap: round;      stroke-linejoin: round;      stroke-miterlimit: 10.00;    }    .svglite text {      white-space: pre;    }  ]]></style></defs><rect width='100%' height='100%' style='stroke: none; fill: none;'/><defs>  <clipPath id='cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3'>    <rect x='0.00' y='0.00' width='85.04' height='14.17' />  </clipPath></defs><g clip-path='url(#cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3)'><text x='67.43' y='9.24' style='font-size: 5.69px; font-family: "Arial";' textLength='10.24px' lengthAdjust='spacingAndGlyphs'>3.2</text><polyline points='29.63,7.07 63.99,7.61 ' style='stroke-width: 1.07; stroke-linecap: butt;' /><circle cx='63.99' cy='7.61' r='0.89' style='stroke-width: 0.71; fill: #000000;' /><circle cx='63.99' cy='7.61' r='0.89' style='stroke-width: 0.71; stroke: #A020F0; fill: #A020F0;' /><circle cx='29.63' cy='7.07' r='0.89' style='stroke-width: 0.71; stroke: #00FF00; fill: #00FF00;' /></g></svg></td></tr>
    <tr><td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">Villeneuve-d'Ascq (59)</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">62.36</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><div>
  <div style="height: 20px;width:70px; background-color: #FF820033;border-radius:5px;)">
    <div style="height: 13px;width: 13px;background-color: #FF8200;display: inline-block;border-radius:4px;float:left;position:relative;top:17%;left:6%;"></div>
    <div style="display: inline-block;float:right;line-height:20px; font-weight: bold;padding: 0px 2.5px;">E</div>
  </div>
</div></td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">3.10</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">3.09</td>
<td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><?xml version='1.0' encoding='UTF-8' ?><svg xmlns='http://www.w3.org/2000/svg' xmlns:xlink='http://www.w3.org/1999/xlink' class='svglite' width='85.04pt' height='14.17pt' viewBox='0 0 85.04 14.17'><defs>  <style type='text/css'><![CDATA[    .svglite line, .svglite polyline, .svglite polygon, .svglite path, .svglite rect, .svglite circle {      fill: none;      stroke: #000000;      stroke-linecap: round;      stroke-linejoin: round;      stroke-miterlimit: 10.00;    }    .svglite text {      white-space: pre;    }  ]]></style></defs><rect width='100%' height='100%' style='stroke: none; fill: none;'/><defs>  <clipPath id='cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3'>    <rect x='0.00' y='0.00' width='85.04' height='14.17' />  </clipPath></defs><g clip-path='url(#cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3)'><text x='67.43' y='9.52' style='font-size: 5.69px; font-family: "Arial";' textLength='10.24px' lengthAdjust='spacingAndGlyphs'>3.1</text><polyline points='29.63,7.85 63.99,7.90 ' style='stroke-width: 1.07; stroke-linecap: butt;' /><circle cx='63.99' cy='7.90' r='0.89' style='stroke-width: 0.71; fill: #000000;' /><circle cx='63.99' cy='7.90' r='0.89' style='stroke-width: 0.71; stroke: #A020F0; fill: #A020F0;' /><circle cx='29.63' cy='7.85' r='0.89' style='stroke-width: 0.71; stroke: #00FF00; fill: #00FF00;' /></g></svg></td></tr>
    <tr><td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">Antony (92)</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">62.21</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><div>
  <div style="height: 20px;width:70px; background-color: #FF820033;border-radius:5px;)">
    <div style="height: 13px;width: 13px;background-color: #FF8200;display: inline-block;border-radius:4px;float:left;position:relative;top:17%;left:6%;"></div>
    <div style="display: inline-block;float:right;line-height:20px; font-weight: bold;padding: 0px 2.5px;">E</div>
  </div>
</div></td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">3.62</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">2.99</td>
<td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><?xml version='1.0' encoding='UTF-8' ?><svg xmlns='http://www.w3.org/2000/svg' xmlns:xlink='http://www.w3.org/1999/xlink' class='svglite' width='85.04pt' height='14.17pt' viewBox='0 0 85.04 14.17'><defs>  <style type='text/css'><![CDATA[    .svglite line, .svglite polyline, .svglite polygon, .svglite path, .svglite rect, .svglite circle {      fill: none;      stroke: #000000;      stroke-linecap: round;      stroke-linejoin: round;      stroke-miterlimit: 10.00;    }    .svglite text {      white-space: pre;    }  ]]></style></defs><rect width='100%' height='100%' style='stroke: none; fill: none;'/><defs>  <clipPath id='cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3'>    <rect x='0.00' y='0.00' width='85.04' height='14.17' />  </clipPath></defs><g clip-path='url(#cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3)'><text x='67.43' y='9.79' style='font-size: 5.69px; font-family: "Arial";' textLength='10.24px' lengthAdjust='spacingAndGlyphs'>3.0</text><polyline points='29.63,6.45 63.99,8.17 ' style='stroke-width: 1.07; stroke-linecap: butt;' /><circle cx='63.99' cy='8.17' r='0.89' style='stroke-width: 0.71; fill: #000000;' /><circle cx='63.99' cy='8.17' r='0.89' style='stroke-width: 0.71; stroke: #A020F0; fill: #A020F0;' /><circle cx='29.63' cy='6.45' r='0.89' style='stroke-width: 0.71; stroke: #00FF00; fill: #00FF00;' /></g></svg></td></tr>
    <tr><td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">Pessac (33)</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">61.86</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><div>
  <div style="height: 20px;width:70px; background-color: #FFA50033;border-radius:5px;)">
    <div style="height: 13px;width: 13px;background-color: #FFA500;display: inline-block;border-radius:4px;float:left;position:relative;top:17%;left:6%;"></div>
    <div style="display: inline-block;float:right;line-height:20px; font-weight: bold;padding: 0px 2.5px;">D</div>
  </div>
</div></td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">3.59</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">3.25</td>
<td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><?xml version='1.0' encoding='UTF-8' ?><svg xmlns='http://www.w3.org/2000/svg' xmlns:xlink='http://www.w3.org/1999/xlink' class='svglite' width='85.04pt' height='14.17pt' viewBox='0 0 85.04 14.17'><defs>  <style type='text/css'><![CDATA[    .svglite line, .svglite polyline, .svglite polygon, .svglite path, .svglite rect, .svglite circle {      fill: none;      stroke: #000000;      stroke-linecap: round;      stroke-linejoin: round;      stroke-miterlimit: 10.00;    }    .svglite text {      white-space: pre;    }  ]]></style></defs><rect width='100%' height='100%' style='stroke: none; fill: none;'/><defs>  <clipPath id='cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3'>    <rect x='0.00' y='0.00' width='85.04' height='14.17' />  </clipPath></defs><g clip-path='url(#cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3)'><text x='67.43' y='9.09' style='font-size: 5.69px; font-family: "Arial";' textLength='10.24px' lengthAdjust='spacingAndGlyphs'>3.2</text><polyline points='29.63,6.52 63.99,7.47 ' style='stroke-width: 1.07; stroke-linecap: butt;' /><circle cx='63.99' cy='7.47' r='0.89' style='stroke-width: 0.71; fill: #000000;' /><circle cx='63.99' cy='7.47' r='0.89' style='stroke-width: 0.71; stroke: #A020F0; fill: #A020F0;' /><circle cx='29.63' cy='6.52' r='0.89' style='stroke-width: 0.71; stroke: #00FF00; fill: #00FF00;' /></g></svg></td></tr>
    <tr><td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">Ivry-sur-Seine (94)</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">60.77</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><div>
  <div style="height: 20px;width:70px; background-color: #FF580033;border-radius:5px;)">
    <div style="height: 13px;width: 13px;background-color: #FF5800;display: inline-block;border-radius:4px;float:left;position:relative;top:17%;left:6%;"></div>
    <div style="display: inline-block;float:right;line-height:20px; font-weight: bold;padding: 0px 2.5px;">F</div>
  </div>
</div></td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">2.80</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">2.56</td>
<td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><?xml version='1.0' encoding='UTF-8' ?><svg xmlns='http://www.w3.org/2000/svg' xmlns:xlink='http://www.w3.org/1999/xlink' class='svglite' width='85.04pt' height='14.17pt' viewBox='0 0 85.04 14.17'><defs>  <style type='text/css'><![CDATA[    .svglite line, .svglite polyline, .svglite polygon, .svglite path, .svglite rect, .svglite circle {      fill: none;      stroke: #000000;      stroke-linecap: round;      stroke-linejoin: round;      stroke-miterlimit: 10.00;    }    .svglite text {      white-space: pre;    }  ]]></style></defs><rect width='100%' height='100%' style='stroke: none; fill: none;'/><defs>  <clipPath id='cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3'>    <rect x='0.00' y='0.00' width='85.04' height='14.17' />  </clipPath></defs><g clip-path='url(#cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3)'><text x='67.43' y='10.94' style='font-size: 5.69px; font-family: "Arial";' textLength='10.24px' lengthAdjust='spacingAndGlyphs'>2.6</text><polyline points='29.63,8.67 63.99,9.32 ' style='stroke-width: 1.07; stroke-linecap: butt;' /><circle cx='63.99' cy='9.32' r='0.89' style='stroke-width: 0.71; fill: #000000;' /><circle cx='63.99' cy='9.32' r='0.89' style='stroke-width: 0.71; stroke: #A020F0; fill: #A020F0;' /><circle cx='29.63' cy='8.67' r='0.89' style='stroke-width: 0.71; stroke: #00FF00; fill: #00FF00;' /></g></svg></td></tr>
    <tr><td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">Troyes (10)</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">60.64</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><div>
  <div style="height: 20px;width:70px; background-color: #FF580033;border-radius:5px;)">
    <div style="height: 13px;width: 13px;background-color: #FF5800;display: inline-block;border-radius:4px;float:left;position:relative;top:17%;left:6%;"></div>
    <div style="display: inline-block;float:right;line-height:20px; font-weight: bold;padding: 0px 2.5px;">F</div>
  </div>
</div></td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">2.53</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">2.55</td>
<td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><?xml version='1.0' encoding='UTF-8' ?><svg xmlns='http://www.w3.org/2000/svg' xmlns:xlink='http://www.w3.org/1999/xlink' class='svglite' width='85.04pt' height='14.17pt' viewBox='0 0 85.04 14.17'><defs>  <style type='text/css'><![CDATA[    .svglite line, .svglite polyline, .svglite polygon, .svglite path, .svglite rect, .svglite circle {      fill: none;      stroke: #000000;      stroke-linecap: round;      stroke-linejoin: round;      stroke-miterlimit: 10.00;    }    .svglite text {      white-space: pre;    }  ]]></style></defs><rect width='100%' height='100%' style='stroke: none; fill: none;'/><defs>  <clipPath id='cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3'>    <rect x='0.00' y='0.00' width='85.04' height='14.17' />  </clipPath></defs><g clip-path='url(#cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3)'><text x='67.43' y='10.98' style='font-size: 5.69px; font-family: "Arial";' textLength='10.24px' lengthAdjust='spacingAndGlyphs'>2.5</text><polyline points='29.63,9.39 63.99,9.35 ' style='stroke-width: 1.07; stroke-linecap: butt;' /><circle cx='63.99' cy='9.35' r='0.89' style='stroke-width: 0.71; fill: #000000;' /><circle cx='29.63' cy='9.39' r='0.89' style='stroke-width: 0.71; stroke: #A020F0; fill: #A020F0;' /><circle cx='63.99' cy='9.35' r='0.89' style='stroke-width: 0.71; stroke: #00FF00; fill: #00FF00;' /></g></svg></td></tr>
    <tr><td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">Neuilly-sur-Seine (92)</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">60.58</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><div>
  <div style="height: 20px;width:70px; background-color: #FF580033;border-radius:5px;)">
    <div style="height: 13px;width: 13px;background-color: #FF5800;display: inline-block;border-radius:4px;float:left;position:relative;top:17%;left:6%;"></div>
    <div style="display: inline-block;float:right;line-height:20px; font-weight: bold;padding: 0px 2.5px;">F</div>
  </div>
</div></td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">2.54</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">2.44</td>
<td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><?xml version='1.0' encoding='UTF-8' ?><svg xmlns='http://www.w3.org/2000/svg' xmlns:xlink='http://www.w3.org/1999/xlink' class='svglite' width='85.04pt' height='14.17pt' viewBox='0 0 85.04 14.17'><defs>  <style type='text/css'><![CDATA[    .svglite line, .svglite polyline, .svglite polygon, .svglite path, .svglite rect, .svglite circle {      fill: none;      stroke: #000000;      stroke-linecap: round;      stroke-linejoin: round;      stroke-miterlimit: 10.00;    }    .svglite text {      white-space: pre;    }  ]]></style></defs><rect width='100%' height='100%' style='stroke: none; fill: none;'/><defs>  <clipPath id='cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3'>    <rect x='0.00' y='0.00' width='85.04' height='14.17' />  </clipPath></defs><g clip-path='url(#cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3)'><text x='67.43' y='11.28' style='font-size: 5.69px; font-family: "Arial";' textLength='10.24px' lengthAdjust='spacingAndGlyphs'>2.4</text><polyline points='29.63,9.37 63.99,9.65 ' style='stroke-width: 1.07; stroke-linecap: butt;' /><circle cx='63.99' cy='9.65' r='0.89' style='stroke-width: 0.71; fill: #000000;' /><circle cx='63.99' cy='9.65' r='0.89' style='stroke-width: 0.71; stroke: #A020F0; fill: #A020F0;' /><circle cx='29.63' cy='9.37' r='0.89' style='stroke-width: 0.71; stroke: #00FF00; fill: #00FF00;' /></g></svg></td></tr>
    <tr><td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">Montauban (82)</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">60.44</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><div>
  <div style="height: 20px;width:70px; background-color: #FF580033;border-radius:5px;)">
    <div style="height: 13px;width: 13px;background-color: #FF5800;display: inline-block;border-radius:4px;float:left;position:relative;top:17%;left:6%;"></div>
    <div style="display: inline-block;float:right;line-height:20px; font-weight: bold;padding: 0px 2.5px;">F</div>
  </div>
</div></td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">2.76</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">2.65</td>
<td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><?xml version='1.0' encoding='UTF-8' ?><svg xmlns='http://www.w3.org/2000/svg' xmlns:xlink='http://www.w3.org/1999/xlink' class='svglite' width='85.04pt' height='14.17pt' viewBox='0 0 85.04 14.17'><defs>  <style type='text/css'><![CDATA[    .svglite line, .svglite polyline, .svglite polygon, .svglite path, .svglite rect, .svglite circle {      fill: none;      stroke: #000000;      stroke-linecap: round;      stroke-linejoin: round;      stroke-miterlimit: 10.00;    }    .svglite text {      white-space: pre;    }  ]]></style></defs><rect width='100%' height='100%' style='stroke: none; fill: none;'/><defs>  <clipPath id='cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3'>    <rect x='0.00' y='0.00' width='85.04' height='14.17' />  </clipPath></defs><g clip-path='url(#cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3)'><text x='67.43' y='10.70' style='font-size: 5.69px; font-family: "Arial";' textLength='10.24px' lengthAdjust='spacingAndGlyphs'>2.7</text><polyline points='29.63,8.78 63.99,9.08 ' style='stroke-width: 1.07; stroke-linecap: butt;' /><circle cx='63.99' cy='9.08' r='0.89' style='stroke-width: 0.71; fill: #000000;' /><circle cx='63.99' cy='9.08' r='0.89' style='stroke-width: 0.71; stroke: #A020F0; fill: #A020F0;' /><circle cx='29.63' cy='8.78' r='0.89' style='stroke-width: 0.71; stroke: #00FF00; fill: #00FF00;' /></g></svg></td></tr>
    <tr><td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">Clichy (92)</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">60.39</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><div>
  <div style="height: 20px;width:70px; background-color: #FF580033;border-radius:5px;)">
    <div style="height: 13px;width: 13px;background-color: #FF5800;display: inline-block;border-radius:4px;float:left;position:relative;top:17%;left:6%;"></div>
    <div style="display: inline-block;float:right;line-height:20px; font-weight: bold;padding: 0px 2.5px;">F</div>
  </div>
</div></td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">2.69</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">2.59</td>
<td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><?xml version='1.0' encoding='UTF-8' ?><svg xmlns='http://www.w3.org/2000/svg' xmlns:xlink='http://www.w3.org/1999/xlink' class='svglite' width='85.04pt' height='14.17pt' viewBox='0 0 85.04 14.17'><defs>  <style type='text/css'><![CDATA[    .svglite line, .svglite polyline, .svglite polygon, .svglite path, .svglite rect, .svglite circle {      fill: none;      stroke: #000000;      stroke-linecap: round;      stroke-linejoin: round;      stroke-miterlimit: 10.00;    }    .svglite text {      white-space: pre;    }  ]]></style></defs><rect width='100%' height='100%' style='stroke: none; fill: none;'/><defs>  <clipPath id='cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3'>    <rect x='0.00' y='0.00' width='85.04' height='14.17' />  </clipPath></defs><g clip-path='url(#cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3)'><text x='67.43' y='10.87' style='font-size: 5.69px; font-family: "Arial";' textLength='10.24px' lengthAdjust='spacingAndGlyphs'>2.6</text><polyline points='29.63,8.96 63.99,9.25 ' style='stroke-width: 1.07; stroke-linecap: butt;' /><circle cx='63.99' cy='9.25' r='0.89' style='stroke-width: 0.71; fill: #000000;' /><circle cx='63.99' cy='9.25' r='0.89' style='stroke-width: 0.71; stroke: #A020F0; fill: #A020F0;' /><circle cx='29.63' cy='8.96' r='0.89' style='stroke-width: 0.71; stroke: #00FF00; fill: #00FF00;' /></g></svg></td></tr>
    <tr><td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">Chambéry (73)</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">59.18</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><div>
  <div style="height: 20px;width:70px; background-color: #D4C80033;border-radius:5px;)">
    <div style="height: 13px;width: 13px;background-color: #D4C800;display: inline-block;border-radius:4px;float:left;position:relative;top:17%;left:6%;"></div>
    <div style="display: inline-block;float:right;line-height:20px; font-weight: bold;padding: 0px 2.5px;">C</div>
  </div>
</div></td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">4.36</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">3.76</td>
<td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><?xml version='1.0' encoding='UTF-8' ?><svg xmlns='http://www.w3.org/2000/svg' xmlns:xlink='http://www.w3.org/1999/xlink' class='svglite' width='85.04pt' height='14.17pt' viewBox='0 0 85.04 14.17'><defs>  <style type='text/css'><![CDATA[    .svglite line, .svglite polyline, .svglite polygon, .svglite path, .svglite rect, .svglite circle {      fill: none;      stroke: #000000;      stroke-linecap: round;      stroke-linejoin: round;      stroke-miterlimit: 10.00;    }    .svglite text {      white-space: pre;    }  ]]></style></defs><rect width='100%' height='100%' style='stroke: none; fill: none;'/><defs>  <clipPath id='cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3'>    <rect x='0.00' y='0.00' width='85.04' height='14.17' />  </clipPath></defs><g clip-path='url(#cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3)'><text x='67.43' y='7.70' style='font-size: 5.69px; font-family: "Arial";' textLength='10.24px' lengthAdjust='spacingAndGlyphs'>3.8</text><polyline points='29.63,4.44 63.99,6.08 ' style='stroke-width: 1.07; stroke-linecap: butt;' /><circle cx='63.99' cy='6.08' r='0.89' style='stroke-width: 0.71; fill: #000000;' /><circle cx='63.99' cy='6.08' r='0.89' style='stroke-width: 0.71; stroke: #A020F0; fill: #A020F0;' /><circle cx='29.63' cy='4.44' r='0.89' style='stroke-width: 0.71; stroke: #00FF00; fill: #00FF00;' /></g></svg></td></tr>
    <tr><td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">Niort (79)</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">59.01</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><div>
  <div style="height: 20px;width:70px; background-color: #FF820033;border-radius:5px;)">
    <div style="height: 13px;width: 13px;background-color: #FF8200;display: inline-block;border-radius:4px;float:left;position:relative;top:17%;left:6%;"></div>
    <div style="display: inline-block;float:right;line-height:20px; font-weight: bold;padding: 0px 2.5px;">E</div>
  </div>
</div></td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">3.23</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">2.95</td>
<td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><?xml version='1.0' encoding='UTF-8' ?><svg xmlns='http://www.w3.org/2000/svg' xmlns:xlink='http://www.w3.org/1999/xlink' class='svglite' width='85.04pt' height='14.17pt' viewBox='0 0 85.04 14.17'><defs>  <style type='text/css'><![CDATA[    .svglite line, .svglite polyline, .svglite polygon, .svglite path, .svglite rect, .svglite circle {      fill: none;      stroke: #000000;      stroke-linecap: round;      stroke-linejoin: round;      stroke-miterlimit: 10.00;    }    .svglite text {      white-space: pre;    }  ]]></style></defs><rect width='100%' height='100%' style='stroke: none; fill: none;'/><defs>  <clipPath id='cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3'>    <rect x='0.00' y='0.00' width='85.04' height='14.17' />  </clipPath></defs><g clip-path='url(#cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3)'><text x='67.43' y='9.89' style='font-size: 5.69px; font-family: "Arial";' textLength='10.24px' lengthAdjust='spacingAndGlyphs'>2.9</text><polyline points='29.63,7.50 63.99,8.27 ' style='stroke-width: 1.07; stroke-linecap: butt;' /><circle cx='63.99' cy='8.27' r='0.89' style='stroke-width: 0.71; fill: #000000;' /><circle cx='63.99' cy='8.27' r='0.89' style='stroke-width: 0.71; stroke: #A020F0; fill: #A020F0;' /><circle cx='29.63' cy='7.50' r='0.89' style='stroke-width: 0.71; stroke: #00FF00; fill: #00FF00;' /></g></svg></td></tr>
    <tr><td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">Lorient (56)</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">57.27</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><div>
  <div style="height: 20px;width:70px; background-color: #D4C80033;border-radius:5px;)">
    <div style="height: 13px;width: 13px;background-color: #D4C800;display: inline-block;border-radius:4px;float:left;position:relative;top:17%;left:6%;"></div>
    <div style="display: inline-block;float:right;line-height:20px; font-weight: bold;padding: 0px 2.5px;">C</div>
  </div>
</div></td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">4.05</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">3.59</td>
<td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><?xml version='1.0' encoding='UTF-8' ?><svg xmlns='http://www.w3.org/2000/svg' xmlns:xlink='http://www.w3.org/1999/xlink' class='svglite' width='85.04pt' height='14.17pt' viewBox='0 0 85.04 14.17'><defs>  <style type='text/css'><![CDATA[    .svglite line, .svglite polyline, .svglite polygon, .svglite path, .svglite rect, .svglite circle {      fill: none;      stroke: #000000;      stroke-linecap: round;      stroke-linejoin: round;      stroke-miterlimit: 10.00;    }    .svglite text {      white-space: pre;    }  ]]></style></defs><rect width='100%' height='100%' style='stroke: none; fill: none;'/><defs>  <clipPath id='cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3'>    <rect x='0.00' y='0.00' width='85.04' height='14.17' />  </clipPath></defs><g clip-path='url(#cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3)'><text x='67.43' y='8.15' style='font-size: 5.69px; font-family: "Arial";' textLength='10.24px' lengthAdjust='spacingAndGlyphs'>3.6</text><polyline points='29.63,5.28 63.99,6.53 ' style='stroke-width: 1.07; stroke-linecap: butt;' /><circle cx='63.99' cy='6.53' r='0.89' style='stroke-width: 0.71; fill: #000000;' /><circle cx='63.99' cy='6.53' r='0.89' style='stroke-width: 0.71; stroke: #A020F0; fill: #A020F0;' /><circle cx='29.63' cy='5.28' r='0.89' style='stroke-width: 0.71; stroke: #00FF00; fill: #00FF00;' /></g></svg></td></tr>
    <tr><td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">Beauvais (60)</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">56.02</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><div>
  <div style="height: 20px;width:70px; background-color: #FF580033;border-radius:5px;)">
    <div style="height: 13px;width: 13px;background-color: #FF5800;display: inline-block;border-radius:4px;float:left;position:relative;top:17%;left:6%;"></div>
    <div style="display: inline-block;float:right;line-height:20px; font-weight: bold;padding: 0px 2.5px;">F</div>
  </div>
</div></td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">2.52</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">2.61</td>
<td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><?xml version='1.0' encoding='UTF-8' ?><svg xmlns='http://www.w3.org/2000/svg' xmlns:xlink='http://www.w3.org/1999/xlink' class='svglite' width='85.04pt' height='14.17pt' viewBox='0 0 85.04 14.17'><defs>  <style type='text/css'><![CDATA[    .svglite line, .svglite polyline, .svglite polygon, .svglite path, .svglite rect, .svglite circle {      fill: none;      stroke: #000000;      stroke-linecap: round;      stroke-linejoin: round;      stroke-miterlimit: 10.00;    }    .svglite text {      white-space: pre;    }  ]]></style></defs><rect width='100%' height='100%' style='stroke: none; fill: none;'/><defs>  <clipPath id='cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3'>    <rect x='0.00' y='0.00' width='85.04' height='14.17' />  </clipPath></defs><g clip-path='url(#cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3)'><text x='67.43' y='10.81' style='font-size: 5.69px; font-family: "Arial";' textLength='10.24px' lengthAdjust='spacingAndGlyphs'>2.6</text><polyline points='29.63,9.43 63.99,9.19 ' style='stroke-width: 1.07; stroke-linecap: butt;' /><circle cx='63.99' cy='9.19' r='0.89' style='stroke-width: 0.71; fill: #000000;' /><circle cx='29.63' cy='9.43' r='0.89' style='stroke-width: 0.71; stroke: #A020F0; fill: #A020F0;' /><circle cx='63.99' cy='9.19' r='0.89' style='stroke-width: 0.71; stroke: #00FF00; fill: #00FF00;' /></g></svg></td></tr>
    <tr><td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">Hyères (83)</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">55.77</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><div>
  <div style="height: 20px;width:70px; background-color: #FF580033;border-radius:5px;)">
    <div style="height: 13px;width: 13px;background-color: #FF5800;display: inline-block;border-radius:4px;float:left;position:relative;top:17%;left:6%;"></div>
    <div style="display: inline-block;float:right;line-height:20px; font-weight: bold;padding: 0px 2.5px;">F</div>
  </div>
</div></td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">2.48</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">2.51</td>
<td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><?xml version='1.0' encoding='UTF-8' ?><svg xmlns='http://www.w3.org/2000/svg' xmlns:xlink='http://www.w3.org/1999/xlink' class='svglite' width='85.04pt' height='14.17pt' viewBox='0 0 85.04 14.17'><defs>  <style type='text/css'><![CDATA[    .svglite line, .svglite polyline, .svglite polygon, .svglite path, .svglite rect, .svglite circle {      fill: none;      stroke: #000000;      stroke-linecap: round;      stroke-linejoin: round;      stroke-miterlimit: 10.00;    }    .svglite text {      white-space: pre;    }  ]]></style></defs><rect width='100%' height='100%' style='stroke: none; fill: none;'/><defs>  <clipPath id='cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3'>    <rect x='0.00' y='0.00' width='85.04' height='14.17' />  </clipPath></defs><g clip-path='url(#cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3)'><text x='67.43' y='11.09' style='font-size: 5.69px; font-family: "Arial";' textLength='10.24px' lengthAdjust='spacingAndGlyphs'>2.5</text><polyline points='29.63,9.53 63.99,9.47 ' style='stroke-width: 1.07; stroke-linecap: butt;' /><circle cx='63.99' cy='9.47' r='0.89' style='stroke-width: 0.71; fill: #000000;' /><circle cx='29.63' cy='9.53' r='0.89' style='stroke-width: 0.71; stroke: #A020F0; fill: #A020F0;' /><circle cx='63.99' cy='9.47' r='0.89' style='stroke-width: 0.71; stroke: #00FF00; fill: #00FF00;' /></g></svg></td></tr>
    <tr><td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">Villejuif (94)</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">55.48</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><div>
  <div style="height: 20px;width:70px; background-color: #FF580033;border-radius:5px;)">
    <div style="height: 13px;width: 13px;background-color: #FF5800;display: inline-block;border-radius:4px;float:left;position:relative;top:17%;left:6%;"></div>
    <div style="display: inline-block;float:right;line-height:20px; font-weight: bold;padding: 0px 2.5px;">F</div>
  </div>
</div></td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">3.12</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">2.63</td>
<td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><?xml version='1.0' encoding='UTF-8' ?><svg xmlns='http://www.w3.org/2000/svg' xmlns:xlink='http://www.w3.org/1999/xlink' class='svglite' width='85.04pt' height='14.17pt' viewBox='0 0 85.04 14.17'><defs>  <style type='text/css'><![CDATA[    .svglite line, .svglite polyline, .svglite polygon, .svglite path, .svglite rect, .svglite circle {      fill: none;      stroke: #000000;      stroke-linecap: round;      stroke-linejoin: round;      stroke-miterlimit: 10.00;    }    .svglite text {      white-space: pre;    }  ]]></style></defs><rect width='100%' height='100%' style='stroke: none; fill: none;'/><defs>  <clipPath id='cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3'>    <rect x='0.00' y='0.00' width='85.04' height='14.17' />  </clipPath></defs><g clip-path='url(#cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3)'><text x='67.43' y='10.75' style='font-size: 5.69px; font-family: "Arial";' textLength='10.24px' lengthAdjust='spacingAndGlyphs'>2.6</text><polyline points='29.63,7.80 63.99,9.12 ' style='stroke-width: 1.07; stroke-linecap: butt;' /><circle cx='63.99' cy='9.12' r='0.89' style='stroke-width: 0.71; fill: #000000;' /><circle cx='63.99' cy='9.12' r='0.89' style='stroke-width: 0.71; stroke: #A020F0; fill: #A020F0;' /><circle cx='29.63' cy='7.80' r='0.89' style='stroke-width: 0.71; stroke: #00FF00; fill: #00FF00;' /></g></svg></td></tr>
    <tr><td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">Pantin (93)</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">55.34</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><div>
  <div style="height: 20px;width:70px; background-color: #FF820033;border-radius:5px;)">
    <div style="height: 13px;width: 13px;background-color: #FF8200;display: inline-block;border-radius:4px;float:left;position:relative;top:17%;left:6%;"></div>
    <div style="display: inline-block;float:right;line-height:20px; font-weight: bold;padding: 0px 2.5px;">E</div>
  </div>
</div></td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">3.04</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">2.76</td>
<td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><?xml version='1.0' encoding='UTF-8' ?><svg xmlns='http://www.w3.org/2000/svg' xmlns:xlink='http://www.w3.org/1999/xlink' class='svglite' width='85.04pt' height='14.17pt' viewBox='0 0 85.04 14.17'><defs>  <style type='text/css'><![CDATA[    .svglite line, .svglite polyline, .svglite polygon, .svglite path, .svglite rect, .svglite circle {      fill: none;      stroke: #000000;      stroke-linecap: round;      stroke-linejoin: round;      stroke-miterlimit: 10.00;    }    .svglite text {      white-space: pre;    }  ]]></style></defs><rect width='100%' height='100%' style='stroke: none; fill: none;'/><defs>  <clipPath id='cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3'>    <rect x='0.00' y='0.00' width='85.04' height='14.17' />  </clipPath></defs><g clip-path='url(#cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3)'><text x='67.43' y='10.39' style='font-size: 5.69px; font-family: "Arial";' textLength='10.24px' lengthAdjust='spacingAndGlyphs'>2.8</text><polyline points='29.63,8.01 63.99,8.77 ' style='stroke-width: 1.07; stroke-linecap: butt;' /><circle cx='63.99' cy='8.77' r='0.89' style='stroke-width: 0.71; fill: #000000;' /><circle cx='63.99' cy='8.77' r='0.89' style='stroke-width: 0.71; stroke: #A020F0; fill: #A020F0;' /><circle cx='29.63' cy='8.01' r='0.89' style='stroke-width: 0.71; stroke: #00FF00; fill: #00FF00;' /></g></svg></td></tr>
    <tr><td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">Maisons-Alfort (94)</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">55.29</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><div>
  <div style="height: 20px;width:70px; background-color: #FFA50033;border-radius:5px;)">
    <div style="height: 13px;width: 13px;background-color: #FFA500;display: inline-block;border-radius:4px;float:left;position:relative;top:17%;left:6%;"></div>
    <div style="display: inline-block;float:right;line-height:20px; font-weight: bold;padding: 0px 2.5px;">D</div>
  </div>
</div></td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">3.95</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">3.11</td>
<td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><?xml version='1.0' encoding='UTF-8' ?><svg xmlns='http://www.w3.org/2000/svg' xmlns:xlink='http://www.w3.org/1999/xlink' class='svglite' width='85.04pt' height='14.17pt' viewBox='0 0 85.04 14.17'><defs>  <style type='text/css'><![CDATA[    .svglite line, .svglite polyline, .svglite polygon, .svglite path, .svglite rect, .svglite circle {      fill: none;      stroke: #000000;      stroke-linecap: round;      stroke-linejoin: round;      stroke-miterlimit: 10.00;    }    .svglite text {      white-space: pre;    }  ]]></style></defs><rect width='100%' height='100%' style='stroke: none; fill: none;'/><defs>  <clipPath id='cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3'>    <rect x='0.00' y='0.00' width='85.04' height='14.17' />  </clipPath></defs><g clip-path='url(#cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3)'><text x='67.43' y='9.46' style='font-size: 5.69px; font-family: "Arial";' textLength='10.24px' lengthAdjust='spacingAndGlyphs'>3.1</text><polyline points='29.63,5.56 63.99,7.83 ' style='stroke-width: 1.07; stroke-linecap: butt;' /><circle cx='63.99' cy='7.83' r='0.89' style='stroke-width: 0.71; fill: #000000;' /><circle cx='63.99' cy='7.83' r='0.89' style='stroke-width: 0.71; stroke: #A020F0; fill: #A020F0;' /><circle cx='29.63' cy='5.56' r='0.89' style='stroke-width: 0.71; stroke: #00FF00; fill: #00FF00;' /></g></svg></td></tr>
    <tr><td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">Évry-Courcouronnes (91)</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">54.66</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><div>
  <div style="height: 20px;width:70px; background-color: #FF580033;border-radius:5px;)">
    <div style="height: 13px;width: 13px;background-color: #FF5800;display: inline-block;border-radius:4px;float:left;position:relative;top:17%;left:6%;"></div>
    <div style="display: inline-block;float:right;line-height:20px; font-weight: bold;padding: 0px 2.5px;">F</div>
  </div>
</div></td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">2.42</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">2.58</td>
<td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><?xml version='1.0' encoding='UTF-8' ?><svg xmlns='http://www.w3.org/2000/svg' xmlns:xlink='http://www.w3.org/1999/xlink' class='svglite' width='85.04pt' height='14.17pt' viewBox='0 0 85.04 14.17'><defs>  <style type='text/css'><![CDATA[    .svglite line, .svglite polyline, .svglite polygon, .svglite path, .svglite rect, .svglite circle {      fill: none;      stroke: #000000;      stroke-linecap: round;      stroke-linejoin: round;      stroke-miterlimit: 10.00;    }    .svglite text {      white-space: pre;    }  ]]></style></defs><rect width='100%' height='100%' style='stroke: none; fill: none;'/><defs>  <clipPath id='cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3'>    <rect x='0.00' y='0.00' width='85.04' height='14.17' />  </clipPath></defs><g clip-path='url(#cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3)'><text x='67.43' y='10.88' style='font-size: 5.69px; font-family: "Arial";' textLength='10.24px' lengthAdjust='spacingAndGlyphs'>2.6</text><polyline points='29.63,9.71 63.99,9.26 ' style='stroke-width: 1.07; stroke-linecap: butt;' /><circle cx='63.99' cy='9.26' r='0.89' style='stroke-width: 0.71; fill: #000000;' /><circle cx='29.63' cy='9.71' r='0.89' style='stroke-width: 0.71; stroke: #A020F0; fill: #A020F0;' /><circle cx='63.99' cy='9.26' r='0.89' style='stroke-width: 0.71; stroke: #00FF00; fill: #00FF00;' /></g></svg></td></tr>
    <tr><td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">Saint-Quentin (02)</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">54.44</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><div>
  <div style="height: 20px;width:70px; background-color: #FF000033;border-radius:5px;)">
    <div style="height: 13px;width: 13px;background-color: #FF0000;display: inline-block;border-radius:4px;float:left;position:relative;top:17%;left:6%;"></div>
    <div style="display: inline-block;float:right;line-height:20px; font-weight: bold;padding: 0px 2.5px;">G</div>
  </div>
</div></td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">1.85</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">2.25</td>
<td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><?xml version='1.0' encoding='UTF-8' ?><svg xmlns='http://www.w3.org/2000/svg' xmlns:xlink='http://www.w3.org/1999/xlink' class='svglite' width='85.04pt' height='14.17pt' viewBox='0 0 85.04 14.17'><defs>  <style type='text/css'><![CDATA[    .svglite line, .svglite polyline, .svglite polygon, .svglite path, .svglite rect, .svglite circle {      fill: none;      stroke: #000000;      stroke-linecap: round;      stroke-linejoin: round;      stroke-miterlimit: 10.00;    }    .svglite text {      white-space: pre;    }  ]]></style></defs><rect width='100%' height='100%' style='stroke: none; fill: none;'/><defs>  <clipPath id='cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3'>    <rect x='0.00' y='0.00' width='85.04' height='14.17' />  </clipPath></defs><g clip-path='url(#cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3)'><text x='67.43' y='11.78' style='font-size: 5.69px; font-family: "Arial";' textLength='10.24px' lengthAdjust='spacingAndGlyphs'>2.3</text><polyline points='29.63,11.26 63.99,10.16 ' style='stroke-width: 1.07; stroke-linecap: butt;' /><circle cx='63.99' cy='10.16' r='0.89' style='stroke-width: 0.71; fill: #000000;' /><circle cx='29.63' cy='11.26' r='0.89' style='stroke-width: 0.71; stroke: #A020F0; fill: #A020F0;' /><circle cx='63.99' cy='10.16' r='0.89' style='stroke-width: 0.71; stroke: #00FF00; fill: #00FF00;' /></g></svg></td></tr>
    <tr><td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">Chelles (77)</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">54.20</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><div>
  <div style="height: 20px;width:70px; background-color: #FF580033;border-radius:5px;)">
    <div style="height: 13px;width: 13px;background-color: #FF5800;display: inline-block;border-radius:4px;float:left;position:relative;top:17%;left:6%;"></div>
    <div style="display: inline-block;float:right;line-height:20px; font-weight: bold;padding: 0px 2.5px;">F</div>
  </div>
</div></td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">2.21</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">2.58</td>
<td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><?xml version='1.0' encoding='UTF-8' ?><svg xmlns='http://www.w3.org/2000/svg' xmlns:xlink='http://www.w3.org/1999/xlink' class='svglite' width='85.04pt' height='14.17pt' viewBox='0 0 85.04 14.17'><defs>  <style type='text/css'><![CDATA[    .svglite line, .svglite polyline, .svglite polygon, .svglite path, .svglite rect, .svglite circle {      fill: none;      stroke: #000000;      stroke-linecap: round;      stroke-linejoin: round;      stroke-miterlimit: 10.00;    }    .svglite text {      white-space: pre;    }  ]]></style></defs><rect width='100%' height='100%' style='stroke: none; fill: none;'/><defs>  <clipPath id='cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3'>    <rect x='0.00' y='0.00' width='85.04' height='14.17' />  </clipPath></defs><g clip-path='url(#cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3)'><text x='67.43' y='10.89' style='font-size: 5.69px; font-family: "Arial";' textLength='10.24px' lengthAdjust='spacingAndGlyphs'>2.6</text><polyline points='29.63,10.28 63.99,9.26 ' style='stroke-width: 1.07; stroke-linecap: butt;' /><circle cx='63.99' cy='9.26' r='0.89' style='stroke-width: 0.71; fill: #000000;' /><circle cx='29.63' cy='10.28' r='0.89' style='stroke-width: 0.71; stroke: #A020F0; fill: #A020F0;' /><circle cx='63.99' cy='9.26' r='0.89' style='stroke-width: 0.71; stroke: #00FF00; fill: #00FF00;' /></g></svg></td></tr>
    <tr><td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">La Roche-sur-Yon (85)</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">53.74</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><div>
  <div style="height: 20px;width:70px; background-color: #FFA50033;border-radius:5px;)">
    <div style="height: 13px;width: 13px;background-color: #FFA500;display: inline-block;border-radius:4px;float:left;position:relative;top:17%;left:6%;"></div>
    <div style="display: inline-block;float:right;line-height:20px; font-weight: bold;padding: 0px 2.5px;">D</div>
  </div>
</div></td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">3.58</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">3.23</td>
<td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><?xml version='1.0' encoding='UTF-8' ?><svg xmlns='http://www.w3.org/2000/svg' xmlns:xlink='http://www.w3.org/1999/xlink' class='svglite' width='85.04pt' height='14.17pt' viewBox='0 0 85.04 14.17'><defs>  <style type='text/css'><![CDATA[    .svglite line, .svglite polyline, .svglite polygon, .svglite path, .svglite rect, .svglite circle {      fill: none;      stroke: #000000;      stroke-linecap: round;      stroke-linejoin: round;      stroke-miterlimit: 10.00;    }    .svglite text {      white-space: pre;    }  ]]></style></defs><rect width='100%' height='100%' style='stroke: none; fill: none;'/><defs>  <clipPath id='cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3'>    <rect x='0.00' y='0.00' width='85.04' height='14.17' />  </clipPath></defs><g clip-path='url(#cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3)'><text x='67.43' y='9.12' style='font-size: 5.69px; font-family: "Arial";' textLength='10.24px' lengthAdjust='spacingAndGlyphs'>3.2</text><polyline points='29.63,6.55 63.99,7.50 ' style='stroke-width: 1.07; stroke-linecap: butt;' /><circle cx='63.99' cy='7.50' r='0.89' style='stroke-width: 0.71; fill: #000000;' /><circle cx='63.99' cy='7.50' r='0.89' style='stroke-width: 0.71; stroke: #A020F0; fill: #A020F0;' /><circle cx='29.63' cy='6.55' r='0.89' style='stroke-width: 0.71; stroke: #00FF00; fill: #00FF00;' /></g></svg></td></tr>
    <tr><td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">Cholet (49)</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">53.72</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><div>
  <div style="height: 20px;width:70px; background-color: #FF580033;border-radius:5px;)">
    <div style="height: 13px;width: 13px;background-color: #FF5800;display: inline-block;border-radius:4px;float:left;position:relative;top:17%;left:6%;"></div>
    <div style="display: inline-block;float:right;line-height:20px; font-weight: bold;padding: 0px 2.5px;">F</div>
  </div>
</div></td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">2.67</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">2.49</td>
<td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><?xml version='1.0' encoding='UTF-8' ?><svg xmlns='http://www.w3.org/2000/svg' xmlns:xlink='http://www.w3.org/1999/xlink' class='svglite' width='85.04pt' height='14.17pt' viewBox='0 0 85.04 14.17'><defs>  <style type='text/css'><![CDATA[    .svglite line, .svglite polyline, .svglite polygon, .svglite path, .svglite rect, .svglite circle {      fill: none;      stroke: #000000;      stroke-linecap: round;      stroke-linejoin: round;      stroke-miterlimit: 10.00;    }    .svglite text {      white-space: pre;    }  ]]></style></defs><rect width='100%' height='100%' style='stroke: none; fill: none;'/><defs>  <clipPath id='cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3'>    <rect x='0.00' y='0.00' width='85.04' height='14.17' />  </clipPath></defs><g clip-path='url(#cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3)'><text x='67.43' y='11.13' style='font-size: 5.69px; font-family: "Arial";' textLength='10.24px' lengthAdjust='spacingAndGlyphs'>2.5</text><polyline points='29.63,9.03 63.99,9.50 ' style='stroke-width: 1.07; stroke-linecap: butt;' /><circle cx='63.99' cy='9.50' r='0.89' style='stroke-width: 0.71; fill: #000000;' /><circle cx='63.99' cy='9.50' r='0.89' style='stroke-width: 0.71; stroke: #A020F0; fill: #A020F0;' /><circle cx='29.63' cy='9.03' r='0.89' style='stroke-width: 0.71; stroke: #00FF00; fill: #00FF00;' /></g></svg></td></tr>
    <tr><td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">Narbonne (11)</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">53.59</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><div>
  <div style="height: 20px;width:70px; background-color: #FF000033;border-radius:5px;)">
    <div style="height: 13px;width: 13px;background-color: #FF0000;display: inline-block;border-radius:4px;float:left;position:relative;top:17%;left:6%;"></div>
    <div style="display: inline-block;float:right;line-height:20px; font-weight: bold;padding: 0px 2.5px;">G</div>
  </div>
</div></td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">2.44</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">2.29</td>
<td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><?xml version='1.0' encoding='UTF-8' ?><svg xmlns='http://www.w3.org/2000/svg' xmlns:xlink='http://www.w3.org/1999/xlink' class='svglite' width='85.04pt' height='14.17pt' viewBox='0 0 85.04 14.17'><defs>  <style type='text/css'><![CDATA[    .svglite line, .svglite polyline, .svglite polygon, .svglite path, .svglite rect, .svglite circle {      fill: none;      stroke: #000000;      stroke-linecap: round;      stroke-linejoin: round;      stroke-miterlimit: 10.00;    }    .svglite text {      white-space: pre;    }  ]]></style></defs><rect width='100%' height='100%' style='stroke: none; fill: none;'/><defs>  <clipPath id='cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3'>    <rect x='0.00' y='0.00' width='85.04' height='14.17' />  </clipPath></defs><g clip-path='url(#cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3)'><text x='67.43' y='11.67' style='font-size: 5.69px; font-family: "Arial";' textLength='10.24px' lengthAdjust='spacingAndGlyphs'>2.3</text><polyline points='29.63,9.65 63.99,10.04 ' style='stroke-width: 1.07; stroke-linecap: butt;' /><circle cx='63.99' cy='10.04' r='0.89' style='stroke-width: 0.71; fill: #000000;' /><circle cx='63.99' cy='10.04' r='0.89' style='stroke-width: 0.71; stroke: #A020F0; fill: #A020F0;' /><circle cx='29.63' cy='9.65' r='0.89' style='stroke-width: 0.71; stroke: #00FF00; fill: #00FF00;' /></g></svg></td></tr>
    <tr><td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">Fontenay-sous-Bois (94)</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">53.42</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><div>
  <div style="height: 20px;width:70px; background-color: #FF820033;border-radius:5px;)">
    <div style="height: 13px;width: 13px;background-color: #FF8200;display: inline-block;border-radius:4px;float:left;position:relative;top:17%;left:6%;"></div>
    <div style="display: inline-block;float:right;line-height:20px; font-weight: bold;padding: 0px 2.5px;">E</div>
  </div>
</div></td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">3.24</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">2.94</td>
<td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><?xml version='1.0' encoding='UTF-8' ?><svg xmlns='http://www.w3.org/2000/svg' xmlns:xlink='http://www.w3.org/1999/xlink' class='svglite' width='85.04pt' height='14.17pt' viewBox='0 0 85.04 14.17'><defs>  <style type='text/css'><![CDATA[    .svglite line, .svglite polyline, .svglite polygon, .svglite path, .svglite rect, .svglite circle {      fill: none;      stroke: #000000;      stroke-linecap: round;      stroke-linejoin: round;      stroke-miterlimit: 10.00;    }    .svglite text {      white-space: pre;    }  ]]></style></defs><rect width='100%' height='100%' style='stroke: none; fill: none;'/><defs>  <clipPath id='cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3'>    <rect x='0.00' y='0.00' width='85.04' height='14.17' />  </clipPath></defs><g clip-path='url(#cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3)'><text x='67.43' y='9.93' style='font-size: 5.69px; font-family: "Arial";' textLength='10.24px' lengthAdjust='spacingAndGlyphs'>2.9</text><polyline points='29.63,7.49 63.99,8.31 ' style='stroke-width: 1.07; stroke-linecap: butt;' /><circle cx='63.99' cy='8.31' r='0.89' style='stroke-width: 0.71; fill: #000000;' /><circle cx='63.99' cy='8.31' r='0.89' style='stroke-width: 0.71; stroke: #A020F0; fill: #A020F0;' /><circle cx='29.63' cy='7.49' r='0.89' style='stroke-width: 0.71; stroke: #00FF00; fill: #00FF00;' /></g></svg></td></tr>
    <tr><td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">Vannes (56)</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">53.22</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><div>
  <div style="height: 20px;width:70px; background-color: #FF820033;border-radius:5px;)">
    <div style="height: 13px;width: 13px;background-color: #FF8200;display: inline-block;border-radius:4px;float:left;position:relative;top:17%;left:6%;"></div>
    <div style="display: inline-block;float:right;line-height:20px; font-weight: bold;padding: 0px 2.5px;">E</div>
  </div>
</div></td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">3.14</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">2.91</td>
<td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><?xml version='1.0' encoding='UTF-8' ?><svg xmlns='http://www.w3.org/2000/svg' xmlns:xlink='http://www.w3.org/1999/xlink' class='svglite' width='85.04pt' height='14.17pt' viewBox='0 0 85.04 14.17'><defs>  <style type='text/css'><![CDATA[    .svglite line, .svglite polyline, .svglite polygon, .svglite path, .svglite rect, .svglite circle {      fill: none;      stroke: #000000;      stroke-linecap: round;      stroke-linejoin: round;      stroke-miterlimit: 10.00;    }    .svglite text {      white-space: pre;    }  ]]></style></defs><rect width='100%' height='100%' style='stroke: none; fill: none;'/><defs>  <clipPath id='cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3'>    <rect x='0.00' y='0.00' width='85.04' height='14.17' />  </clipPath></defs><g clip-path='url(#cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3)'><text x='67.43' y='10.01' style='font-size: 5.69px; font-family: "Arial";' textLength='10.24px' lengthAdjust='spacingAndGlyphs'>2.9</text><polyline points='29.63,7.74 63.99,8.39 ' style='stroke-width: 1.07; stroke-linecap: butt;' /><circle cx='63.99' cy='8.39' r='0.89' style='stroke-width: 0.71; fill: #000000;' /><circle cx='63.99' cy='8.39' r='0.89' style='stroke-width: 0.71; stroke: #A020F0; fill: #A020F0;' /><circle cx='29.63' cy='7.74' r='0.89' style='stroke-width: 0.71; stroke: #00FF00; fill: #00FF00;' /></g></svg></td></tr>
    <tr><td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">Fréjus (83)</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">53.17</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><div>
  <div style="height: 20px;width:70px; background-color: #FF580033;border-radius:5px;)">
    <div style="height: 13px;width: 13px;background-color: #FF5800;display: inline-block;border-radius:4px;float:left;position:relative;top:17%;left:6%;"></div>
    <div style="display: inline-block;float:right;line-height:20px; font-weight: bold;padding: 0px 2.5px;">F</div>
  </div>
</div></td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">2.22</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">2.33</td>
<td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><?xml version='1.0' encoding='UTF-8' ?><svg xmlns='http://www.w3.org/2000/svg' xmlns:xlink='http://www.w3.org/1999/xlink' class='svglite' width='85.04pt' height='14.17pt' viewBox='0 0 85.04 14.17'><defs>  <style type='text/css'><![CDATA[    .svglite line, .svglite polyline, .svglite polygon, .svglite path, .svglite rect, .svglite circle {      fill: none;      stroke: #000000;      stroke-linecap: round;      stroke-linejoin: round;      stroke-miterlimit: 10.00;    }    .svglite text {      white-space: pre;    }  ]]></style></defs><rect width='100%' height='100%' style='stroke: none; fill: none;'/><defs>  <clipPath id='cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3'>    <rect x='0.00' y='0.00' width='85.04' height='14.17' />  </clipPath></defs><g clip-path='url(#cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3)'><text x='67.43' y='11.56' style='font-size: 5.69px; font-family: "Arial";' textLength='10.24px' lengthAdjust='spacingAndGlyphs'>2.3</text><polyline points='29.63,10.24 63.99,9.94 ' style='stroke-width: 1.07; stroke-linecap: butt;' /><circle cx='63.99' cy='9.94' r='0.89' style='stroke-width: 0.71; fill: #000000;' /><circle cx='29.63' cy='10.24' r='0.89' style='stroke-width: 0.71; stroke: #A020F0; fill: #A020F0;' /><circle cx='63.99' cy='9.94' r='0.89' style='stroke-width: 0.71; stroke: #00FF00; fill: #00FF00;' /></g></svg></td></tr>
    <tr><td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">Arles (13)</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">52.86</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><div>
  <div style="height: 20px;width:70px; background-color: #FF580033;border-radius:5px;)">
    <div style="height: 13px;width: 13px;background-color: #FF5800;display: inline-block;border-radius:4px;float:left;position:relative;top:17%;left:6%;"></div>
    <div style="display: inline-block;float:right;line-height:20px; font-weight: bold;padding: 0px 2.5px;">F</div>
  </div>
</div></td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">2.17</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">2.30</td>
<td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><?xml version='1.0' encoding='UTF-8' ?><svg xmlns='http://www.w3.org/2000/svg' xmlns:xlink='http://www.w3.org/1999/xlink' class='svglite' width='85.04pt' height='14.17pt' viewBox='0 0 85.04 14.17'><defs>  <style type='text/css'><![CDATA[    .svglite line, .svglite polyline, .svglite polygon, .svglite path, .svglite rect, .svglite circle {      fill: none;      stroke: #000000;      stroke-linecap: round;      stroke-linejoin: round;      stroke-miterlimit: 10.00;    }    .svglite text {      white-space: pre;    }  ]]></style></defs><rect width='100%' height='100%' style='stroke: none; fill: none;'/><defs>  <clipPath id='cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3'>    <rect x='0.00' y='0.00' width='85.04' height='14.17' />  </clipPath></defs><g clip-path='url(#cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3)'><text x='67.43' y='11.64' style='font-size: 5.69px; font-family: "Arial";' textLength='10.24px' lengthAdjust='spacingAndGlyphs'>2.3</text><polyline points='29.63,10.38 63.99,10.02 ' style='stroke-width: 1.07; stroke-linecap: butt;' /><circle cx='63.99' cy='10.02' r='0.89' style='stroke-width: 0.71; fill: #000000;' /><circle cx='29.63' cy='10.38' r='0.89' style='stroke-width: 0.71; stroke: #A020F0; fill: #A020F0;' /><circle cx='63.99' cy='10.02' r='0.89' style='stroke-width: 0.71; stroke: #00FF00; fill: #00FF00;' /></g></svg></td></tr>
    <tr><td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">Sartrouville (78)</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">52.65</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><div>
  <div style="height: 20px;width:70px; background-color: #FF580033;border-radius:5px;)">
    <div style="height: 13px;width: 13px;background-color: #FF5800;display: inline-block;border-radius:4px;float:left;position:relative;top:17%;left:6%;"></div>
    <div style="display: inline-block;float:right;line-height:20px; font-weight: bold;padding: 0px 2.5px;">F</div>
  </div>
</div></td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">2.85</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">2.62</td>
<td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><?xml version='1.0' encoding='UTF-8' ?><svg xmlns='http://www.w3.org/2000/svg' xmlns:xlink='http://www.w3.org/1999/xlink' class='svglite' width='85.04pt' height='14.17pt' viewBox='0 0 85.04 14.17'><defs>  <style type='text/css'><![CDATA[    .svglite line, .svglite polyline, .svglite polygon, .svglite path, .svglite rect, .svglite circle {      fill: none;      stroke: #000000;      stroke-linecap: round;      stroke-linejoin: round;      stroke-miterlimit: 10.00;    }    .svglite text {      white-space: pre;    }  ]]></style></defs><rect width='100%' height='100%' style='stroke: none; fill: none;'/><defs>  <clipPath id='cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3'>    <rect x='0.00' y='0.00' width='85.04' height='14.17' />  </clipPath></defs><g clip-path='url(#cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3)'><text x='67.43' y='10.77' style='font-size: 5.69px; font-family: "Arial";' textLength='10.24px' lengthAdjust='spacingAndGlyphs'>2.6</text><polyline points='29.63,8.55 63.99,9.15 ' style='stroke-width: 1.07; stroke-linecap: butt;' /><circle cx='63.99' cy='9.15' r='0.89' style='stroke-width: 0.71; fill: #000000;' /><circle cx='63.99' cy='9.15' r='0.89' style='stroke-width: 0.71; stroke: #A020F0; fill: #A020F0;' /><circle cx='29.63' cy='8.55' r='0.89' style='stroke-width: 0.71; stroke: #00FF00; fill: #00FF00;' /></g></svg></td></tr>
    <tr><td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">Clamart (92)</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">52.53</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><div>
  <div style="height: 20px;width:70px; background-color: #FF580033;border-radius:5px;)">
    <div style="height: 13px;width: 13px;background-color: #FF5800;display: inline-block;border-radius:4px;float:left;position:relative;top:17%;left:6%;"></div>
    <div style="display: inline-block;float:right;line-height:20px; font-weight: bold;padding: 0px 2.5px;">F</div>
  </div>
</div></td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">2.22</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">2.64</td>
<td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><?xml version='1.0' encoding='UTF-8' ?><svg xmlns='http://www.w3.org/2000/svg' xmlns:xlink='http://www.w3.org/1999/xlink' class='svglite' width='85.04pt' height='14.17pt' viewBox='0 0 85.04 14.17'><defs>  <style type='text/css'><![CDATA[    .svglite line, .svglite polyline, .svglite polygon, .svglite path, .svglite rect, .svglite circle {      fill: none;      stroke: #000000;      stroke-linecap: round;      stroke-linejoin: round;      stroke-miterlimit: 10.00;    }    .svglite text {      white-space: pre;    }  ]]></style></defs><rect width='100%' height='100%' style='stroke: none; fill: none;'/><defs>  <clipPath id='cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3'>    <rect x='0.00' y='0.00' width='85.04' height='14.17' />  </clipPath></defs><g clip-path='url(#cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3)'><text x='67.43' y='10.73' style='font-size: 5.69px; font-family: "Arial";' textLength='10.24px' lengthAdjust='spacingAndGlyphs'>2.6</text><polyline points='29.63,10.23 63.99,9.11 ' style='stroke-width: 1.07; stroke-linecap: butt;' /><circle cx='63.99' cy='9.11' r='0.89' style='stroke-width: 0.71; fill: #000000;' /><circle cx='29.63' cy='10.23' r='0.89' style='stroke-width: 0.71; stroke: #A020F0; fill: #A020F0;' /><circle cx='63.99' cy='9.11' r='0.89' style='stroke-width: 0.71; stroke: #00FF00; fill: #00FF00;' /></g></svg></td></tr>
    <tr><td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">Grasse (06)</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">50.68</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><div>
  <div style="height: 20px;width:70px; background-color: #FF000033;border-radius:5px;)">
    <div style="height: 13px;width: 13px;background-color: #FF0000;display: inline-block;border-radius:4px;float:left;position:relative;top:17%;left:6%;"></div>
    <div style="display: inline-block;float:right;line-height:20px; font-weight: bold;padding: 0px 2.5px;">G</div>
  </div>
</div></td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">2.04</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">2.20</td>
<td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><?xml version='1.0' encoding='UTF-8' ?><svg xmlns='http://www.w3.org/2000/svg' xmlns:xlink='http://www.w3.org/1999/xlink' class='svglite' width='85.04pt' height='14.17pt' viewBox='0 0 85.04 14.17'><defs>  <style type='text/css'><![CDATA[    .svglite line, .svglite polyline, .svglite polygon, .svglite path, .svglite rect, .svglite circle {      fill: none;      stroke: #000000;      stroke-linecap: round;      stroke-linejoin: round;      stroke-miterlimit: 10.00;    }    .svglite text {      white-space: pre;    }  ]]></style></defs><rect width='100%' height='100%' style='stroke: none; fill: none;'/><defs>  <clipPath id='cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3'>    <rect x='0.00' y='0.00' width='85.04' height='14.17' />  </clipPath></defs><g clip-path='url(#cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3)'><text x='67.43' y='11.91' style='font-size: 5.69px; font-family: "Arial";' textLength='10.24px' lengthAdjust='spacingAndGlyphs'>2.2</text><polyline points='29.63,10.72 63.99,10.28 ' style='stroke-width: 1.07; stroke-linecap: butt;' /><circle cx='63.99' cy='10.28' r='0.89' style='stroke-width: 0.71; fill: #000000;' /><circle cx='29.63' cy='10.72' r='0.89' style='stroke-width: 0.71; stroke: #A020F0; fill: #A020F0;' /><circle cx='63.99' cy='10.28' r='0.89' style='stroke-width: 0.71; stroke: #00FF00; fill: #00FF00;' /></g></svg></td></tr>
    <tr><td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">Bayonne (64)</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">50.59</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><div>
  <div style="height: 20px;width:70px; background-color: #FF580033;border-radius:5px;)">
    <div style="height: 13px;width: 13px;background-color: #FF5800;display: inline-block;border-radius:4px;float:left;position:relative;top:17%;left:6%;"></div>
    <div style="display: inline-block;float:right;line-height:20px; font-weight: bold;padding: 0px 2.5px;">F</div>
  </div>
</div></td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">2.06</td>
<td class="gt_row gt_right" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;">2.61</td>
<td class="gt_row gt_center" style="font-family: &#39;Source Sans Pro&#39;; font-weight: 400;"><?xml version='1.0' encoding='UTF-8' ?><svg xmlns='http://www.w3.org/2000/svg' xmlns:xlink='http://www.w3.org/1999/xlink' class='svglite' width='85.04pt' height='14.17pt' viewBox='0 0 85.04 14.17'><defs>  <style type='text/css'><![CDATA[    .svglite line, .svglite polyline, .svglite polygon, .svglite path, .svglite rect, .svglite circle {      fill: none;      stroke: #000000;      stroke-linecap: round;      stroke-linejoin: round;      stroke-miterlimit: 10.00;    }    .svglite text {      white-space: pre;    }  ]]></style></defs><rect width='100%' height='100%' style='stroke: none; fill: none;'/><defs>  <clipPath id='cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3'>    <rect x='0.00' y='0.00' width='85.04' height='14.17' />  </clipPath></defs><g clip-path='url(#cpMC4wMHw4NS4wNHwwLjAwfDE0LjE3)'><text x='67.43' y='10.81' style='font-size: 5.69px; font-family: "Arial";' textLength='10.24px' lengthAdjust='spacingAndGlyphs'>2.6</text><polyline points='29.63,10.67 63.99,9.18 ' style='stroke-width: 1.07; stroke-linecap: butt;' /><circle cx='63.99' cy='9.18' r='0.89' style='stroke-width: 0.71; fill: #000000;' /><circle cx='29.63' cy='10.67' r='0.89' style='stroke-width: 0.71; stroke: #A020F0; fill: #A020F0;' /><circle cx='63.99' cy='9.18' r='0.89' style='stroke-width: 0.71; stroke: #00FF00; fill: #00FF00;' /></g></svg></td></tr>
  </tbody>
  <tfoot class="gt_sourcenotes">
    <tr>
      <td class="gt_sourcenote" colspan="6">Source: <em>FUB</em></td>
    </tr>
  </tfoot>
  
</table>
</div>

Of course, we could continue editing the table, but with a few lines, we
already have a table that is production ready. This is enough to help people
know how likely it is that their bike will sleep in a garage.

# What now?

Well, first, as we can see, there is definitely room for improvement. There's
a lot of red in the grading. Hopefully, the 250M investement plan will be spent
wisely.

Second, by using code to build the table, it will be easy to update it. Right
now, as far as I know, the FUB data for 2021 is not yet available, but it will
be very easy to add a column for every year the survey is conducted. This will
probably help making the spark chart more informative. Two points is a bit too
little.

And, finally, as a concluding thought, if glancing at the table is not enough,
I can assure that a bike is definitely handy in Grenoble. The data that were
used do map onto something very concrete for a lot of people and surely it can
help design the world of tomorrow.

{{< figure src="img/fabe-collage-diqJhQtHHNk-unsplash.jpg" caption="A panoramic view of Grenoble. Photo by Fabe collage." >}}

[^1]: https://www.lemonde.fr/planete/article/2022/09/20/la-premiere-ministre-elisabeth-borne-plaide-pour-une-nation-velo_6142468_3244.html

[^2]: https://public.tableau.com/app/profile/fub4080/viz/2019Barometreresultatsfinal/
