qsv is a command line program for indexing, slicing, analyzing, splitting
and joining CSV files. Commands should be simple, fast and composable:

1. Simple tasks should be easy.
2. Performance trade offs should be exposed in the CLI interface.
3. Composition should not come at the expense of performance.

This README contains information on how to
[install `qsv`](https://github.com/jqnatividad/qsv#installation), in addition to
a quick tour of several commands.

[![Windows build status](https://ci.appveyor.com/api/projects/status/github/jqnatividad/qsv?svg=true)](https://ci.appveyor.com/project/jqnatividad/qsv)

Dual-licensed under MIT or the [UNLICENSE](https://unlicense.org).

> :warning: **NOTE: qsv is a fork of the popular [xsv](https://github.com/BurntSushi/xsv) utility, merging several pending PRs [since xsv 0.13.0's release](https://github.com/BurntSushi/xsv/issues/267), along with additional features & commands for data-wrangling (_NEW/EXTENDED_ commands are marked accordingly).**

----

### Available commands

* **[cat](945d9cd8091505c554201defa0dc837f1e8c455e/src/cmd/cat.rs#L7)** - Concatenate CSV files by row or by column.
* **[count](945d9cd8091505c554201defa0dc837f1e8c455e/src/cmd/count.rs#L7)** - Count the rows in a CSV file. (Instantaneous with an index.)
* **[dedup](945d9cd8091505c554201defa0dc837f1e8c455e/src/cmd/dedup.rs#L13)** - Remove redundant rows _**(NEW)**_
* **[exclude](945d9cd8091505c554201defa0dc837f1e8c455e/src/cmd/exclude.rs#L17)** - Excludes the records in one CSV from another _**(NEW)**_
* **[fill](945d9cd8091505c554201defa0dc837f1e8c455e/src/cmd/fill.rs#L13)** - Fill empty values. _**(NEW)**_
* **[fixlengths](945d9cd8091505c554201defa0dc837f1e8c455e/src/cmd/fill.rs#L13)** - Force a CSV file to have same-length records by either
  padding or truncating them.
* **[flatten](945d9cd8091505c554201defa0dc837f1e8c455e/src/cmd/flatten.rs#L12)** - A flattened view of CSV records. Useful for viewing one record
  at a time. e.g., `qsv slice -i 5 data.csv | qsv flatten`.
* **[fmt](945d9cd8091505c554201defa0dc837f1e8c455e/src/cmd/fmt.rs#L7)** - Reformat CSV data with different delimiters, record terminators
  or quoting rules. (Supports ASCII delimited data.) _**(EXTENDED)**_
* **[frequency](945d9cd8091505c554201defa0dc837f1e8c455e/src/cmd/frequency.rs#L15)** - Build frequency tables of each column in CSV data. (Uses
  parallelism to go faster if an index is present.)
* **[headers](945d9cd8091505c554201defa0dc837f1e8c455e/src/cmd/headers.rs#L11)** - Show the headers of CSV data. Or show the intersection of all
  headers between many CSV files.
* **[index](945d9cd8091505c554201defa0dc837f1e8c455e/src/cmd/index.rs#L13)** - Create an index for a CSV file. This is very quick and provides
  constant time indexing into the CSV file.
* **[input](945d9cd8091505c554201defa0dc837f1e8c455e/src/cmd/input.rs#L7)** - Read CSV data with exotic quoting/escaping rules.
* **[join](945d9cd8091505c554201defa0dc837f1e8c455e/src/cmd/join.rs#L18)** - Inner, outer and cross joins. Uses a simple hash index to make it
  fast.
* **[partition](945d9cd8091505c554201defa0dc837f1e8c455e/src/cmd/partition.rs#L16)** - Partition CSV data based on a column value.
* **[rename](945d9cd8091505c554201defa0dc837f1e8c455e/src/cmd/rename.rs#L7)** -  Rename the columns of CSV data efficiently. _**(NEW)**_
* **[reverse](945d9cd8091505c554201defa0dc837f1e8c455e/src/cmd/reverse.rs#L7)** - Reverse order of rows in CSV data. _**(NEW)**_
* **[sample](945d9cd8091505c554201defa0dc837f1e8c455e/src/cmd/sample.rs#L15)** - Randomly draw rows from CSV data using reservoir sampling (i.e.,
  use memory proportional to the size of the sample). _**(EXTENDED)**_
* **[search](945d9cd8091505c554201defa0dc837f1e8c455e/src/cmd/search.rs#L10)** - Run a regex over CSV data. Applies the regex to each field
  individually and shows only matching rows.
* **[select](945d9cd8091505c554201defa0dc837f1e8c455e/src/cmd/select.rs#L8)** - Select or re-order columns from CSV data. _**(EXTENDED)**_
* **[slice](945d9cd8091505c554201defa0dc837f1e8c455e/src/cmd/slice.rs#L11)** - Slice rows from any part of a CSV file. When an index is present,
  this only has to parse the rows in the slice (instead of all rows leading up
  to the start of the slice).
* **[sort](945d9cd8091505c554201defa0dc837f1e8c455e/src/cmd/sort.rs#L13)** - Sort CSV data.
* **[split](945d9cd8091505c554201defa0dc837f1e8c455e/src/cmd/split.rs#L14)** - Split one CSV file into many CSV files of N chunks.
* **[stats](945d9cd8091505c554201defa0dc837f1e8c455e/src/cmd/stats.rs#L23)** - Show basic types and statistics of each column in the CSV file.
  (i.e., mean, standard deviation, variance, median, min/max, nullcount, etc.) _**(EXTENDED)**_
* **[table](945d9cd8091505c554201defa0dc837f1e8c455e/src/cmd/table.rs#L12)** - Show aligned output of any CSV data using
  [elastic tabstops](https://github.com/BurntSushi/tabwriter). _**(EXTENDED)**_
* **[transpose](945d9cd8091505c554201defa0dc837f1e8c455e/src/cmd/transpose.rs#L9)** - Transpose rows/columns of CSV data. _**(NEW)**_

----

### A whirlwind tour

Let's say you're playing with some of the data from the
[Data Science Toolkit](https://github.com/petewarden/dstkdata), which contains
several CSV files. Maybe you're interested in the population counts of each
city in the world. So grab the data and start examining it:

```bash
$ curl -LO https://burntsushi.net/stuff/worldcitiespop.csv
$ qsv headers worldcitiespop.csv
1   Country
2   City
3   AccentCity
4   Region
5   Population
6   Latitude
7   Longitude
```

The next thing you might want to do is get an overview of the kind of data that
appears in each column. The `stats` command will do this for you:

```bash
$ qsv stats worldcitiespop.csv --everything | qsv table
field       type     min            max            min_length  max_length  mean          stddev         median     mode         cardinality
Country     Unicode  ad             zw             2           2                                                   cn           234
City        Unicode   bab el ahmar  Þykkvibaer     1           91                                                  san jose     2351892
AccentCity  Unicode   Bâb el Ahmar  ïn Bou Chella  1           91                                                  San Antonio  2375760
Region      Unicode  00             Z9             0           2                                        13         04           397
Population  Integer  7              31480498       0           8           47719.570634  302885.559204  10779                   28754
Latitude    Float    -54.933333     82.483333      1           12          27.188166     21.952614      32.497222  51.15        1038349
Longitude   Float    -179.983333    180            1           14          37.08886      63.22301       35.28      23.8         1167162
```

The `qsv table` command takes any CSV data and formats it into aligned columns
using [elastic tabstops](https://github.com/BurntSushi/tabwriter). You'll
notice that it even gets alignment right with respect to Unicode characters.

So, this command takes about 12 seconds to run on my machine, but we can speed
it up by creating an index and re-running the command:

```bash
$ qsv index worldcitiespop.csv
$ qsv stats worldcitiespop.csv --everything | qsv table
...
```

Which cuts it down to about 8 seconds on my machine. (And creating the index
takes less than 2 seconds.)

Notably, the same type of "statistics" command in another
[CSV command line toolkit](https://csvkit.readthedocs.io/)
takes about 2 minutes to produce similar statistics on the same data set.

Creating an index gives us more than just faster statistics gathering. It also
makes slice operations extremely fast because *only the sliced portion* has to
be parsed. For example, let's say you wanted to grab the last 10 records:

```bash
$ qsv count worldcitiespop.csv
3173958
$ qsv slice worldcitiespop.csv -s 3173948 | qsv table
Country  City               AccentCity         Region  Population  Latitude     Longitude
zw       zibalonkwe         Zibalonkwe         06                  -19.8333333  27.4666667
zw       zibunkululu        Zibunkululu        06                  -19.6666667  27.6166667
zw       ziga               Ziga               06                  -19.2166667  27.4833333
zw       zikamanas village  Zikamanas Village  00                  -18.2166667  27.95
zw       zimbabwe           Zimbabwe           07                  -20.2666667  30.9166667
zw       zimre park         Zimre Park         04                  -17.8661111  31.2136111
zw       ziyakamanas        Ziyakamanas        00                  -18.2166667  27.95
zw       zizalisari         Zizalisari         04                  -17.7588889  31.0105556
zw       zuzumba            Zuzumba            06                  -20.0333333  27.9333333
zw       zvishavane         Zvishavane         07      79876       -20.3333333  30.0333333
```

These commands are *instantaneous* because they run in time and memory
proportional to the size of the slice (which means they will scale to
arbitrarily large CSV data).

Switching gears a little bit, you might not always want to see every column in
the CSV data. In this case, maybe we only care about the country, city and
population. So let's take a look at 10 random rows:

```bash
$ qsv select Country,AccentCity,Population worldcitiespop.csv \
  | qsv sample 10 \
  | qsv table
Country  AccentCity       Population
cn       Guankoushang
za       Klipdrift
ma       Ouled Hammou
fr       Les Gravues
la       Ban Phadèng
de       Lüdenscheid      80045
qa       Umm ash Shubrum
bd       Panditgoan
us       Appleton
ua       Lukashenkivske
```

Whoops! It seems some cities don't have population counts. How pervasive is
that?

```bash
$ qsv frequency worldcitiespop.csv --limit 5
field,value,count
Country,cn,238985
Country,ru,215938
Country,id,176546
Country,us,141989
Country,ir,123872
City,san jose,328
City,san antonio,320
City,santa rosa,296
City,santa cruz,282
City,san juan,255
AccentCity,San Antonio,317
AccentCity,Santa Rosa,296
AccentCity,Santa Cruz,281
AccentCity,San Juan,254
AccentCity,San Miguel,254
Region,04,159916
Region,02,142158
Region,07,126867
Region,03,122161
Region,05,118441
Population,(NULL),3125978
Population,2310,12
Population,3097,11
Population,983,11
Population,2684,11
Latitude,51.15,777
Latitude,51.083333,772
Latitude,50.933333,769
Latitude,51.116667,769
Latitude,51.133333,767
Longitude,23.8,484
Longitude,23.2,477
Longitude,23.05,476
Longitude,25.3,474
Longitude,23.1,459
```

(The `qsv frequency` command builds a frequency table for each column in the
CSV data. This one only took 5 seconds.)

So it seems that most cities do not have a population count associated with
them at all. No matter—we can adjust our previous command so that it only
shows rows with a population count:

```bash
$ qsv search -s Population '[0-9]' worldcitiespop.csv \
  | qsv select Country,AccentCity,Population \
  | qsv sample 10 \
  | tee sample.csv \
  | qsv table
Country  AccentCity       Population
es       Barañáin         22264
es       Puerto Real      36946
at       Moosburg         4602
hu       Hejobaba         1949
ru       Polyarnyye Zori  15092
gr       Kandíla          1245
is       Ólafsvík         992
hu       Decs             4210
bg       Sliven           94252
gb       Leatherhead      43544
```

> :warning: **NOTE:** The `tee` command reads from standard input and writes to both standard output and one or more files at the same time.
We do this so we can create the `sample.csv` file we need for the next step, and pipe it to the `qsv table` command.

Erk. Which country is `at`? No clue, but the Data Science Toolkit has a CSV
file called `countrynames.csv`. Let's grab it and do a join so we can see which
countries these are:

```bash
curl -LO https://gist.githubusercontent.com/anonymous/063cb470e56e64e98cf1/raw/98e2589b801f6ca3ff900b01a87fbb7452eb35c7/countrynames.csv
$ qsv headers countrynames.csv
1   Abbrev
2   Country
$ qsv join --no-case  Country sample.csv Abbrev countrynames.csv | qsv table
Country  AccentCity       Population  Abbrev  Country
es       Barañáin         22264       ES      Spain
es       Puerto Real      36946       ES      Spain
at       Moosburg         4602        AT      Austria
hu       Hejobaba         1949        HU      Hungary
ru       Polyarnyye Zori  15092       RU      Russian Federation | Russia
gr       Kandíla          1245        GR      Greece
is       Ólafsvík         992         IS      Iceland
hu       Decs             4210        HU      Hungary
bg       Sliven           94252       BG      Bulgaria
gb       Leatherhead      43544       GB      Great Britain | UK | England | Scotland | Wales | Northern Ireland | United Kingdom
```

Whoops, now we have two columns called `Country` and an `Abbrev` column that we
no longer need. This is easy to fix by re-ordering columns with the `qsv
select` command:

```bash
$ qsv join --no-case  Country sample.csv Abbrev countrynames.csv \
  | qsv select 'Country[1],AccentCity,Population' \
  | qsv table
Country                                                                              AccentCity       Population
Spain                                                                                Barañáin         22264
Spain                                                                                Puerto Real      36946
Austria                                                                              Moosburg         4602
Hungary                                                                              Hejobaba         1949
Russian Federation | Russia                                                          Polyarnyye Zori  15092
Greece                                                                               Kandíla          1245
Iceland                                                                              Ólafsvík         992
Hungary                                                                              Decs             4210
Bulgaria                                                                             Sliven           94252
Great Britain | UK | England | Scotland | Wales | Northern Ireland | United Kingdom  Leatherhead      43544
```

Perhaps we can do this with the original CSV data? Indeed we can—because
joins in `qsv` are fast.

```bash
$ qsv join --no-case Abbrev countrynames.csv Country worldcitiespop.csv \
  | qsv select '!Abbrev,Country[1]' \
  > worldcitiespop_countrynames.csv
$ qsv sample 10 worldcitiespop_countrynames.csv | qsv table
Country                      City                   AccentCity             Region  Population  Latitude    Longitude
Sri Lanka                    miriswatte             Miriswatte             36                  7.2333333   79.9
Romania                      livezile               Livezile               26      1985        44.512222   22.863333
Indonesia                    tawainalu              Tawainalu              22                  -4.0225     121.9273
Russian Federation | Russia  otar                   Otar                   45                  56.975278   48.305278
France                       le breuil-bois robert  le Breuil-Bois Robert  A8                  48.945567   1.717026
France                       lissac                 Lissac                 B1                  45.103094   1.464927
Albania                      lumalasi               Lumalasi               46                  40.6586111  20.7363889
China                        motzushih              Motzushih              11                  27.65       111.966667
Russian Federation | Russia  svakino                Svakino                69                  55.60211    34.559785
Romania                      tirgu pancesti         Tirgu Pancesti         38                  46.216667   27.1
```

The `!Abbrev,Country[1]` syntax means, "remove the `Abbrev` column and remove
the second occurrence of the `Country` column." Since we joined with
`countrynames.csv` first, the first `Country` name (fully expanded) is now
included in the CSV data.

This `qsv join` command takes about 7 seconds on my machine. The performance
comes from constructing a very simple hash index of one of the CSV data files
given. The `join` command does an inner join by default, but it also has left,
right and full outer join support too.


### Installation

Binaries for Windows, Linux and macOS are available [from Github](https://github.com/jqnatividad/qsv/releases/latest).

Alternatively, you can compile from source by
[installing Cargo](https://crates.io/install)
([Rust's](https://www.rust-lang.org/) package manager)
and installing `qsv` using Cargo:

```bash
cargo install qsv
```

Compiling from this repository also works similarly:

```bash
git clone git://github.com/jqnatividad/qsv
cd qsv
cargo build --release
```

Compilation will probably take a few minutes depending on your machine. The
binary will end up in `./target/release/qsv`.

If you want to squeeze more performance from your build, set this environment
variable before compiling:

```bash
export CARGO_BUILD_RUSTFLAGS='-C target-cpu=native'
```

Do note though that the resulting binary will only run on machines with the
same architecture as the machine you compiled from.  To find out your CPU 
architecture and other valid values for `target-cpu`:

```bash
rustc --print target-cpus
```

### Benchmarks

I've compiled some [very rough
benchmarks](https://github.com/jqnatividad/qsv/blob/master/BENCHMARKS.md) of
various `qsv` commands.

