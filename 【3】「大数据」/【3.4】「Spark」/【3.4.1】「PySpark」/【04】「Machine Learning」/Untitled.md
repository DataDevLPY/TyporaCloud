

```python
import matplotlib.pylab as plt
import numpy as np
sample_data = df_memory.sample(fraction = 0.01).toPandas()
## First plot about attack and PID by using histogram
x1 = sample_data[sample_data.attack == 0]['PID']
x2 = sample_data[sample_data.attack == 1]['PID']
plt.hist([np.log10(x1+1),np.log10(x2+1)])

plt.legend(['attack = 0','attack = 1'])

plt.ylabel("PID")
plt.xlabel("attack")
plt.title('Relationship about attack and PID')
```

![截屏2021-01-18 下午6.58.43](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220110720.png?token=AWS37JMA6PRV5F6Y4TGHTRDBTJ62W)



```python
import matplotlib.pylab as plt
import numpy as np
sample_data = df_memory.sample(fraction = 0.01).toPandas()
plt.scatter(sample_data['MEM'], sample_data['attack'])
plt.ylabel("attack")
plt.xlabel("MEM")
plt.title('Relationship about attack and MEM')
```

![截屏2021-01-18 下午6.59.09](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220110515.png?token=AWS37JMK5EBENONBZPXPVMDBTJ626)

```python
import matplotlib.pylab as plt
import numpy as np
sample_data = df_process.sample(fraction = 0.01).toPandas()
## First plot about attack and PID by using boxplot
x1 = sample_data[sample_data.attack == 0]['PRI']
x2 = sample_data[sample_data.attack == 1]['PRI']
plt.boxplot([x1,x2])

plt.legend(['attack = 0','attack = 1'])

plt.ylabel("PRI")
plt.xlabel("attack")
plt.title('Relationship about attack and PRI')
```

![截屏2021-01-18 下午6.59.41](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220110150.png?token=AWS37JK7MTEJWTUJYHWNUGTBTJ63Q)





