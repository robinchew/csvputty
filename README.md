# csvputty

_Command-fu with csv files_

A set of command line interfaces and python modules for easily manipulating, transforming and dealing with csv files quickly and effectively.

## CLI Usage

All csvputty commands start with `csvputty` and then the command you wish to perform.

### markup

This command will take the selected columns of a CSV file, process each row through a format string, and return the collective output. For example:

`$ csvputty markup 0 1 3 data.csv template.html rendered.html`

This parses each row of `data.csv` using the content of `template.html` as a format string and saves to `rendered.html`.

In the above example, `template.html` could be the following:

```html
<div class="row">
  <div class="col-sm-4">{}</div>
  <div class="col-sm-4">{}</div>
  <div class="col-sm-4">{}</div>
</div>
```

However csvputty really becomes useful in the full context of the command line. Take the following example:

`$ cat data1.csv data2.csv | csvputty markup 0 1 - template.html rendered.html`

This passes `data1.csv` and `data2.csv` through the same template and renders them together in a single file.

`csvputty markup 0 1 data.csv - -`

This opens stdin allowing you to enter the template via command line and prints the results to stdout.

### diff

Diff compares the input CSV against another CSV and outputs rows where selected columns fulfil the match condition.

`$ cat subtract1.csv subtract2.csv | csvputty diff -sc 18 source.csv - out.csv`

The above example returns columns from `source.csv` where column 18 is not found on column column 0 anywhere in `subtract1.csv` or `subtract2.csv`.

## Package Usage

Importing csvputty into your project allows you to use some features unavailable via the command line interface.

For example:

```python
import csvputty


def parse_row(row, row_index):
    for idx, col in enumerate(row):
        row[idx] = col.strip().replace("&", "&amp;")
    img_url = row[2].lower().replace(" ", "_").replace('&amp;', 'and')
    insta_url = row[4].replace("@", "")

    return (img_url, row[1], insta_url, row[3], row[6])


input = open('data.csv', 'r')
out = open('rendered.html', 'w')
template = open('template.html', 'r')

csvputty.markup.generate(custom_row_parser=parse_row, csv_file=input
                         template_file=template, out_file=out)
```

The above example allows me to strip whitespace and replace ampersands with html entities on all columns, and perform further processing on other columns.
