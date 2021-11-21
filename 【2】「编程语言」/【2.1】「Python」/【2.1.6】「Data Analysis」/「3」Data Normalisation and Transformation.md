![截屏2021-02-01 上午11.43.37](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/img/%E6%88%AA%E5%B1%8F2021-02-01%20%E4%B8%8A%E5%8D%8811.43.37.png?token=AWS37JNFVGUUDDB7SM3CNL3BTIC3U)

![截屏2021-02-01 上午11.43.52](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/img/%E6%88%AA%E5%B1%8F2021-02-01%20%E4%B8%8A%E5%8D%8811.43.52.png?token=AWS37JORCWSCA3EJ2HSC2HTBTIC3Y)

![截屏2021-02-01 上午11.44.06](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/img/%E6%88%AA%E5%B1%8F2021-02-01%20%E4%B8%8A%E5%8D%8811.44.06.png?token=AWS37JJ66KNFBTOUXIHM4ITBTIC34)

![截屏2021-02-01 上午11.44.22](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/img/%E6%88%AA%E5%B1%8F2021-02-01%20%E4%B8%8A%E5%8D%8811.44.22.png?token=AWS37JNCXJMNEKAXVF2XE3TBTIC4M)

![截屏2021-02-01 上午11.44.40](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/img/%E6%88%AA%E5%B1%8F2021-02-01%20%E4%B8%8A%E5%8D%8811.44.40.png?token=AWS37JJWOMXUPD52H2HKEOTBTIC4S)

![截屏2021-02-01 上午11.44.52](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/img/%E6%88%AA%E5%B1%8F2021-02-01%20%E4%B8%8A%E5%8D%8811.44.52.png?token=AWS37JMGVBXCAMRKVILNN2DBTIC46)

![截屏2021-02-01 上午11.45.09](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-02-01 上午11.45.09.png)

![截屏2021-02-01 上午11.45.22](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/img/%E6%88%AA%E5%B1%8F2021-02-01%20%E4%B8%8A%E5%8D%8811.45.22.png?token=AWS37JML2NO5OGYODA7464TBTIC5Q)

![截屏2021-02-01 上午11.45.35](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/img/%E6%88%AA%E5%B1%8F2021-02-01%20%E4%B8%8A%E5%8D%8811.45.35.png?token=AWS37JIPFR5CLMR3HJBONZLBTIC5S)

![截屏2021-02-01 上午11.45.47](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/img/%E6%88%AA%E5%B1%8F2021-02-01%20%E4%B8%8A%E5%8D%8811.45.47.png?token=AWS37JI3NLKMSCKYTMTYQN3BTIC54)

![截屏2021-02-01 上午11.45.59](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/img/%E6%88%AA%E5%B1%8F2021-02-01%20%E4%B8%8A%E5%8D%8811.45.59.png?token=AWS37JNDEJ42SZRHWUTFEETBTIC6A)



![截屏2021-02-01 上午11.46.17](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/img/%E6%88%AA%E5%B1%8F2021-02-01%20%E4%B8%8A%E5%8D%8811.46.17.png?token=AWS37JO3XDODYOFWFKR4NT3BTIC6Q)



![截屏2021-02-01 上午11.46.31](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/img/%E6%88%AA%E5%B1%8F2021-02-01%20%E4%B8%8A%E5%8D%8811.46.31.png?token=AWS37JI4WLWQ6APSE77IWMLBTIC7C)



![截屏2021-02-01 上午11.46.49](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/img/%E6%88%AA%E5%B1%8F2021-02-01%20%E4%B8%8A%E5%8D%8811.46.49.png?token=AWS37JL4FYVPHOII72XBVV3BTIC7A)



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

![截屏2021-02-01 上午11.47.21](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/img/%E6%88%AA%E5%B1%8F2021-02-01%20%E4%B8%8A%E5%8D%8811.47.21.png?token=AWS37JPDTSMN5GYWK62FO4TBTIC7S)

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

![截屏2021-02-01 上午11.48.02](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/img/%E6%88%AA%E5%B1%8F2021-02-01%20%E4%B8%8A%E5%8D%8811.48.02.png?token=AWS37JLKPR3SAX6GAT5SJ4LBTIC7U)

![截屏2021-02-01 上午11.48.17](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/img/%E6%88%AA%E5%B1%8F2021-02-01%20%E4%B8%8A%E5%8D%8811.48.17.png?token=AWS37JNOFWXQU5VRCZK3OVDBTIC72)

![截屏2021-02-01 上午11.48.28](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-02-01 上午11.48.28.png)



## 3. Data Transformation:

Another way to reshape data is to perform data transformation. We will display an example of data that is with right skew (positive skew). We will need to compress large values. We first read the data used for this activity.



![截屏2021-02-01 上午11.48.50](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/img/%E6%88%AA%E5%B1%8F2021-02-01%20%E4%B8%8A%E5%8D%8811.48.50.png?token=AWS37JONCTC5HS2WZ2YKDRTBTIDAI)



```python
plt.scatter(data["BMR(W)"], data["Mass(g)"]) # before
```

![截屏2021-02-01 上午11.49.14](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/img/%E6%88%AA%E5%B1%8F2021-02-01%20%E4%B8%8A%E5%8D%8811.49.14.png?token=AWS37JKVPR65ZL4466CTMXDBTIDA2)

### So, which transformation type will suit this data?

In Tukey's ladder of power, we discussed different kind of transformation. Here you are going to compare the following three kinds of transformations

- Root transformation
- Square power transformation
- Log transformation

The implementation of Root transformation is given as follows. You need to finish the other two kinds of transformation.

### 3.1 Root transformation:[¶](http://localhost:8889/notebooks/Desktop/FIT5196 Data Wrangling/week 11/Tutorial/tutorial_11_A/Data Normalisation and Transformation.ipynb#3.1-Root-transformation:)

![截屏2021-02-01 上午11.49.39](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/img/%E6%88%AA%E5%B1%8F2021-02-01%20%E4%B8%8A%E5%8D%8811.49.39.png?token=AWS37JI2G6UXWZGHNNL6HPTBTIDBK)

![截屏2021-02-01 上午11.50.27](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-02-01 上午11.50.27.png)



![截屏2021-02-01 上午11.50.40](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/img/%E6%88%AA%E5%B1%8F2021-02-01%20%E4%B8%8A%E5%8D%8811.50.40.png?token=AWS37JKVYVQOIXQF2TAUNDLBTIDBQ)

![截屏2021-02-01 上午11.51.23](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/img/%E6%88%AA%E5%B1%8F2021-02-01%20%E4%B8%8A%E5%8D%8811.51.23.png?token=AWS37JLDNKFW4THVXKSIVBLBTIDBS)

![截屏2021-02-01 上午11.51.42](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/img/%E6%88%AA%E5%B1%8F2021-02-01%20%E4%B8%8A%E5%8D%8811.51.42.png?token=AWS37JLBKH7CXBKNYPGTEPDBTIDB4)

![截屏2021-02-01 上午11.52.11](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/img/%E6%88%AA%E5%B1%8F2021-02-01%20%E4%B8%8A%E5%8D%8811.52.11.png?token=AWS37JL7SHCRJO2HD4B7VYTBTIDCQ)

![截屏2021-02-01 上午11.52.21](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-02-01 上午11.52.21.png)

![截屏2021-02-01 上午11.52.35](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-02-01 上午11.52.35.png)

![截屏2021-02-01 上午11.52.46](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-02-01 上午11.52.46.png)

