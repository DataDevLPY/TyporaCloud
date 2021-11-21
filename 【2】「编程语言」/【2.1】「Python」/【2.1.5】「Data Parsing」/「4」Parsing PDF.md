## Scraping data from PDFs with pdftables



```python
from pdftables import get_tables
pdfFile = './EN_FINAL_Table_2.pdf'
pdfobj = open(pdfFile, 'rb')
tables = get_tables(pdfobj)
```



```python
import pandas as pd

data = {}
for table in tables:
    for row in table[5:]:
        if row[2] == '':
            continue
        if row[0] == 'Zimbabwe':
            data[row[0]] = row[1:]
            break
        if 'THE STATE' in row[0]:
            break
        data[row[0]] = row[1:] 
data = pd.DataFrame(data)
data
```

```python
data = data.drop(3,0)
data = data.T
data.columns = range(12)
data.to_csv('./en_final_table_2_2.csv', sep='\t')
```

