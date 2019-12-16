
# Setup
install libraries and import it
PDFplumber tutorial: https://github.com/jsvine/pdfplumber/blob/master/examples/notebooks/extract-table-ca-warn-report.ipynb


```python
pip install pdfplumber

```

```python
import pdfplumber 
import pandas as pd
```


```python
from os import listdir
```

## Define function to get PDFs from directory


```python
def get_pdfs_from_dir(directory):
    pdfs = []
    for filename in listdir(directory):
        # FYI: this is case sensitive - only lowercase pdf, not if a filename ends with PDF
        if filename.endswith(".pdf"):
            pdfs.append(filename) 
    return(pdfs)
```


```python
# get_pdfs_from_dir('/Python Scripts')
```


## Function to extract table from a pdf and export it to excel


```python
# TODO - ADD COMMENT TO EXPLAIN WHAT THIS FUNCTION DOES
def extract_pdf_table(filename):
    # Open PDF file by a given filename
    pdf = pdfplumber.open(filename)
    
    # Get the first page of the PDF, because this PDF was only 1 page, doesn't matter but still works
    p0 = pdf.pages[0]
    
    # Use .extract_table to get the data from the largest table on the page
    table = p0.extract_table()
    
    # Display the records from the table through the designated row, here 5th row
    # table[:5]
    
    # Basic cleanup: use pandas to render the list as a DataFrames; for this column, replace each space between results with an "x" so it can be distributed across months later
    df = pd.DataFrame(table[1:], columns=table[0])
    for column in ["""Jan-18 Feb-18 Mar-18 Apr-18 May-18 Jun-18 Jul-18 Aug-18 Sep-18 Oct-18 Nov-18 Dec-18"""]:
        df[column] = df[column].str.replace(" ", "x")
    # df[:5]
    
    # Rename one long column of month/year to "MoYr"
    df.rename(columns = {'Jan-18 Feb-18 Mar-18 Apr-18 May-18 Jun-18 Jul-18 Aug-18 Sep-18 Oct-18 Nov-18 Dec-18' : "MoYr"}, inplace=True)
    # df[:5]
    
    # tutorial documentation for further cleanup: https://www.geeksforgeeks.org/split-a-text-column-into-two-columns-in-pandas-dataframe/
    print(df.MoYr.apply(lambda x:pd.Series(str(x).split("x"))))
    
    # attach split MoYr column back to rest of table
    df[['0','1', '2','3', '4','5','6','7','8','9','10','11']] = df.MoYr.apply(lambda x: pd.Series(str(x).split("x")))
    # df[:5]
    
    # export cleaned up table to excel
    df.to_excel(filename + ".xlsx")

```

## Let's do this 


```python
for filename in get_pdfs_from_dir('/Python Scripts'):
    extract_pdf_table('/Python Scripts/' + filename)    
```

