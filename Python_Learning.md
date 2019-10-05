# Python_Learning

pandas_profiling，注意一定要设置string类型的columns

```python
import pandas_profiling
import pandas as pd
from pathlib import Path

x=pd.read_csv(r"C:\Users\15487\Desktop\SRP\数据文件\raw_x.csv",header=None,usecols=[0,1])
# 需要将columns转换成string
# 否则报错 Can only use .str accessor with string values (i.e. inferred_type is 'string’
x.columns=['0','1']
profile=x.profile_report()
profile.to_file(output_file=Path(r"C:\Users\15487\Desktop\report.html"))
```

相应的，将list中的所有元素转换为string类型的方法

```python
li=list(range(10))
# 法一
li=[str(x) for x in li]
# 法二，map返回的是生成器
li=list(map(int,li))
```

