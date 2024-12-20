Data Cleaning
================
mk4995
2024-11-18

Import the data.

``` r
diabetes_data = read_csv("data/diabetes_data.csv")
```

    ## Rows: 433323 Columns: 34
    ## ── Column specification ────────────────────────────────────────────────────────
    ## Delimiter: ","
    ## dbl (34): state, addepev3, asthma3, asthnow, bphigh6, cadult1, celsxbrt, chc...
    ## 
    ## ℹ Use `spec()` to retrieve the full column specification for this data.
    ## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.

The initial data contains 433,323 rows and 34 columns.

We want to start by recoding the values that are not clear answers,
“don’t knows”, refusals, and missing ALL as missing data.

``` r
# Define a helper function for recoding
recode_binary = function(var) {
  case_when(
    var == 1 ~ 1,
    var == 2 ~ 0,
    var %in% c(7, 9, NA) ~ NA
  )
}
```

``` r
diabetes_recoded_data = 
  diabetes_data |> 
  mutate(
    has_diabetes = case_when( # yes = 2, no/during pregnancy = 0, pre-diabetes = 1
      diabete4 == 1 ~ 2,
      diabete4 %in% c(2, 3) ~ 0,
      diabete4 == 4 ~ 1,
      diabete4 %in% c(7, 9, NA) ~ NA
    ),
    age_onset = case_when(
      diabage4 >= 1 & diabage4 <= 97 ~ diabage4, 
      diabage4 %in% c(98, 99, NA) ~ NA
    ),
    diab_type = case_when(
      diabtype == 1 ~ diabtype,
      diabtype == 2 ~ diabtype, 
      diabtype %in% c(7, 9, NA) ~ NA
    ),
    insulin = recode_binary(insulin1),
    high_chol = recode_binary(toldhi3),
    covid_test = recode_binary(covidpo1),
    any_exercise = case_when(
      exerany2 == 1 ~ 1,
      exerany2 == 2 ~ 0,
      exerany2 == 9 ~ NA
    ),
    heart_attack = recode_binary(cvdinfr4),
    chd = recode_binary(cvdcrhd4),
    stroke = recode_binary(cvdstrk3),
    asthma_ever = recode_binary(asthma3),
    asthma_now = recode_binary(asthnow),
    bronchitis = recode_binary(chccopd3),
    depression = recode_binary(addepev3),
    kidney_disease = recode_binary(chckdny2),
    pregnant = recode_binary(pregnant)
  ) |> 
  select(has_diabetes, age_onset, diab_type, insulin, high_chol, covid_test, any_exercise, heart_attack, chd, stroke, asthma_ever, asthma_now, bronchitis, depression, kidney_disease, pregnant)
```
