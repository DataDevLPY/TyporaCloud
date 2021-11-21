![截屏2021-02-01 上午11.43.37](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-02-01 上午11.43.37.png)

![截屏2021-02-01 上午11.43.52](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-02-01 上午11.43.52.png)

![截屏2021-02-01 上午11.44.06](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-02-01 上午11.44.06.png)

![截屏2021-02-01 上午11.44.22](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-02-01 上午11.44.22.png)

![截屏2021-02-01 上午11.44.40](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-02-01 上午11.44.40.png)

![截屏2021-02-01 上午11.44.52](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-02-01 上午11.44.52.png)

![截屏2021-02-01 上午11.45.09](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-02-01 上午11.45.09.png)

![截屏2021-02-01 上午11.45.22](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-02-01 上午11.45.22.png)

![截屏2021-02-01 上午11.45.35](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-02-01 上午11.45.35.png)

![截屏2021-02-01 上午11.45.47](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-02-01 上午11.45.47.png)

![截屏2021-02-01 上午11.45.59](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-02-01 上午11.45.59.png)



![截屏2021-02-01 上午11.46.17](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-02-01 上午11.46.17.png)



![截屏2021-02-01 上午11.46.31](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-02-01 上午11.46.31.png)



![截屏2021-02-01 上午11.46.49](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-02-01 上午11.46.49.png)



### 2.3 Plot the original, standardised and normalised data values.

```python
# and plot
%matplotlib inline

from matplotlib import pyplot as plt

def plot():
    f = plt.figure(figsize=(8,6))

    plt.scatter(df['Alcohol'], df['Malic acid'],
            color='green', label='input scale', alpha=0.5)

 #   plt.scatter(df_std[:,0], df_std[:,1], color='red',
 #           label='Standardized [$$N  (\mu=0, \; \sigma=1)$$]', alpha=0.3)
    plt.scatter(df_std[:,0], df_std[:,1], color='red',
             label='Standardized u=0, s=1', alpha=0.3) # can't print: μ = 0, σ = 0
    
    plt.scatter(df_minmax[:,0], df_minmax[:,1],
            color='blue', label='min-max scaled [min=0, max=1]', alpha=0.3)

    plt.title('Alcohol and Malic Acid content of the wine dataset')
    plt.xlabel('Alcohol')
    plt.ylabel('Malic Acid')
    plt.legend(loc='upper left')
    plt.grid()
    plt.tight_layout()
    #f.savefig("z_min_max.pdf", bbox_inches='tight')

plot()
plt.show()
```

![截屏2021-02-01 上午11.47.21](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-02-01 上午11.47.21.png)

#### The plot above includes the wine datapoints on all three different scales:

- the input scale where the alcohol content was measured in volume-percent (green),
- the standardized features (red), and
- the normalized features (blue).

#### In the following plot, we will zoom in into the three different axis-scales while dispalying class values.[¶](http://localhost:8889/notebooks/Desktop/FIT5196 Data Wrangling/week 11/Tutorial/tutorial_11_A/Data Normalisation and Transformation.ipynb#In-the-following-plot,-we-will-zoom-in-into-the-three-different-axis-scales-while-dispalying-class-values.)

```python
fig, ax = plt.subplots(3, figsize=(6,14))

for a,d,l in zip(range(len(ax)),
               (df[['Alcohol', 'Malic acid']].values, df_std, df_minmax),
               ('Input scale',
                'Standardized [u=0 s=1]',
                'min-max scaled [min=0, max=1]')
                ):
    for i,c in zip(range(1,4), ('red', 'blue', 'green')):
        ax[a].scatter(d[df['Class label'].values == i, 0],
                  d[df['Class label'].values == i, 1],
                  alpha=0.5,
                  color=c,
                  label='Class %s' %i
                  )
    ax[a].set_title(l)
    ax[a].set_xlabel('Alcohol')
    ax[a].set_ylabel('Malic Acid')
    ax[a].legend(loc='upper left')
    ax[a].grid()

plt.tight_layout()

plt.show()
```

![截屏2021-02-01 上午11.48.02](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-02-01 上午11.48.02.png)

![截屏2021-02-01 上午11.48.17](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-02-01 上午11.48.17.png)

![截屏2021-02-01 上午11.48.28](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-02-01 上午11.48.28.png)



## 3. Data Transformation:

Another way to reshape data is to perform data transformation. We will display an example of data that is with right skew (positive skew). We will need to compress large values. We first read the data used for this activity.



![截屏2021-02-01 上午11.48.50](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-02-01 上午11.48.50.png)



```python
plt.scatter(data["BMR(W)"], data["Mass(g)"]) # before
```

![截屏2021-02-01 上午11.49.14](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-02-01 上午11.49.14.png)

### So, which transformation type will suit this data?

In Tukey's ladder of power, we discussed different kind of transformation. Here you are going to compare the following three kinds of transformations

- Root transformation
- Square power transformation
- Log transformation

The implementation of Root transformation is given as follows. You need to finish the other two kinds of transformation.

### 3.1 Root transformation:[¶](http://localhost:8889/notebooks/Desktop/FIT5196 Data Wrangling/week 11/Tutorial/tutorial_11_A/Data Normalisation and Transformation.ipynb#3.1-Root-transformation:)

![截屏2021-02-01 上午11.49.39](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-02-01 上午11.49.39.png)

![截屏2021-02-01 上午11.50.27](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-02-01 上午11.50.27.png)



![截屏2021-02-01 上午11.50.40](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-02-01 上午11.50.40.png)

![截屏2021-02-01 上午11.51.23](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-02-01 上午11.51.23.png)

![截屏2021-02-01 上午11.51.42](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-02-01 上午11.51.42.png)

![截屏2021-02-01 上午11.52.11](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-02-01 上午11.52.11.png)

![截屏2021-02-01 上午11.52.21](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-02-01 上午11.52.21.png)

![截屏2021-02-01 上午11.52.35](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-02-01 上午11.52.35.png)

![截屏2021-02-01 上午11.52.46](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-02-01 上午11.52.46.png)

