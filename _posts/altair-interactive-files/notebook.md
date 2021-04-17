### What & why:

In [our recent work](link), we calculated the solvent effects on the NMR shielding constant of transition metal nuclei ($\sigma$) in multiple complexes, using two variants of the Frozen Density Embedding (FDE) method:

* canonical FDE with the density of the solvent kept fixed (denoted as `fde0`)
* FDE with the freeze-thaw procedure, in which the densities of the solute and solvent are relaxed in each other presence (referred to as `fdeN`)

In addition, we calculated the NMR shielding constants in:

* isolated solute molecules in their vacuum geometry (`vac`)
* isolated solute molecules in their relaxed geometries - as if they were parts of a complex (`isol`)
* full solvated complexes (`super`)

In this notebook I show how to do interactive plots in [Altair](https://altair-viz.github.io/index.html) that allow to use different plots or views to inspect this data.



#### Data description:

* *df_dirac* and *df_adf* are dataframes that collect the data obtained from calculations with DIRAC (with the DC Hamiltonian) and ADF (with the SO-ZORA Hamiltonian). The data is read from from dirac_data.csv and adf_data.csv files.

* some definitions used in plots:

    * useful to discuss solvent effects estimated by a given model:
        * $\delta_{isol} = \sigma_{isol} - \sigma_{vac}$
        * $\delta_{fde0} = \sigma_{fde0} - \sigma_{vac}$
        * $\delta_{fdeN} = \sigma_{fdeN} - \sigma_{vac}$
        * $\delta_{super} = \sigma_{super} - \sigma_{vac}$    
    
    * useful to discuss contributions to solvent effects:    
        * $\Delta_{isol} = \sigma_{isol} - \sigma_{vac} = \delta_{isol}$
        * $\Delta_{fde0} = \sigma_{fde0} - \sigma_{isol}$
        * $\Delta_{fdeN} = \sigma_{fdeN} - \sigma_{fde0}$
        * $\Delta_{super} = \sigma_{super} - \sigma_{fdeN}$       

    * useful for ....statistics....:
        * $d_{isol}  = (\sigma_{isol}  - \sigma_{super})/|\sigma_{vac}|$
        * $d_{fde0}  = (\sigma_{fde0}  - \sigma_{super})/|\sigma_{vac}|$
        * $d_{fdeN}  = (\sigma_{fdeN}  - \sigma_{super})/|\sigma_{vac}|$
        * $d_{super} = (\sigma_{super} - \sigma_{super})/|\sigma_{vac}|$        

* This notebook uses the actual data discussed in [this paper](link).


```python
import pandas as pd
import altair as alt
import altair_viewer
```


```python
def set_pandas_display_options() -> None:
    """Set pandas display options."""
    # Ref: https://stackoverflow.com/a/52432757/
    display = pd.options.display

    display.max_columns = 1000
    display.max_rows = 1000
    display.max_colwidth = 1000
    display.width = None
    display.precision = 2
    
set_pandas_display_options()
```


```python
def def_molprop():

    mol =  ['Ti',
            'V',
            'Cr',
            'Mn',
            'Fe',
            'Co',
            'Ni',
            'Cu',
            'Zn',
            'Nb',
            'Mo',
            'Tc',
            'Ru',
            'Pd',
            'Ag',
            'Cd',
            'Ta',
            'W',
            'Re',
            'Os',
            'Pt',
            'Hg'
    ]
 
    atomic_number_X={}
    atomic_number_X['Ti']=22
    atomic_number_X['V']=23
    atomic_number_X['Cr']=24
    atomic_number_X['Mn']=25
    atomic_number_X['Fe']=26
    atomic_number_X['Co']=27
    atomic_number_X['Ni']=28
    atomic_number_X['Cu']=29
    atomic_number_X['Zn']=30
    atomic_number_X['Nb']=41
    atomic_number_X['Mo']=42
    atomic_number_X['Tc']=43
    atomic_number_X['Ru']=44
    atomic_number_X['Pd']=46
    atomic_number_X['Ag']=47
    atomic_number_X['Cd']=48
    atomic_number_X['Ta']=73
    atomic_number_X['W']=74
    atomic_number_X['Re']=75
    atomic_number_X['Os']=76
    atomic_number_X['Pt']=78
    atomic_number_X['Hg']=80   
    
    solvent={} 
    solvent['Ti']='12TiCl$_{4}$'
    solvent['V']='6C$_6$H$_6$'
    solvent['Cr']='12H$_2$O'
    solvent['Mn']='12H$_2$O'
    solvent['Fe']='6C$_6$H$_6$'
    solvent['Co']='12H$_2$O'
    solvent['Ni']='6C$_6$H$_6$'
    solvent['Cu']='12CH$_3$CN'
    solvent['Zn']='12H$_2$O'
    solvent['Nb']='12CH$_3$CN'
    solvent['Mo']='12H$_2$O'
    solvent['Tc']='12H$_2$O'
    solvent['Ru']='12H$_2$O'
    solvent['Pd']='12H$_2$O'
    solvent['Ag']='12H$_2$O'
    solvent['Cd']='6Cd(CH$_3$)$_2$'
    solvent['Ta']='12CH$_3$CN'
    solvent['W']='12H$_2$O'
    solvent['Re']='12H$_2$O'
    solvent['Os']='12CCl$_4$'
    solvent['Pt']='12H$_2$O'
    solvent['Hg']='6Hg(CH$_3$)$_2$'

    charge={} 
    charge['Ti']=0
    charge['V']=0
    charge['Cr']=-2
    charge['Mn']=-1
    charge['Fe']=0
    charge['Co']=-3
    charge['Ni']=0
    charge['Cu']=1
    charge['Zn']=2
    charge['Nb']=-1
    charge['Mo']=-2
    charge['Tc']=-1
    charge['Ru']=-4
    charge['Pd']=-2
    charge['Ag']=1
    charge['Cd']=0
    charge['Ta']=-1
    charge['W']=-2
    charge['Re']=-1
    charge['Os']=0
    charge['Pt']=-2
    charge['Hg']=0
    
    row={} 
    row['Ti']=4
    row['V']=0
    row['Cr']=4
    row['Mn']=4
    row['Fe']=4
    row['Co']=4
    row['Ni']=4
    row['Cu']=4
    row['Zn']=4
    row['Nb']=5
    row['Mo']=5
    row['Tc']=5
    row['Ru']=5
    row['Pd']=5
    row['Ag']=5
    row['Cd']=5
    row['Ta']=6
    row['W']=6
    row['Re']=6
    row['Os']=6
    row['Pt']=6
    row['Hg']=6
      
    order_where=['DC','SO-ZORA']
```


```python
def prep_adf(df):
   
    df_se = df[['mol']].copy()
    df_se['x_labels'] = df_se.mol
    df_se['where'] = 'SO-ZORA'
    df_se['solveff']  = df['supermolecule']-df['isolated_vac']  
    df_se = df_se.drop(['mol'], axis=1)
    df_se = df_se.melt(id_vars =['x_labels', 'where'])     
    
    df_delta = df[['mol']].copy()
    df_delta['x_labels'] = df.mol
    df_delta['where'] = 'SO-ZORA' 
    df_delta['delta1']  = df['isolated_supergeom']-df['isolated_vac']
    df_delta['delta2']  = df['fde']-df['isolated_vac']
    df_delta['delta3']  = df['fnt']-df['isolated_vac']    
    df_delta['delta4']  = df['supermolecule']-df['isolated_vac']     
    df_delta=df_delta.drop(['mol'], axis=1)
    df_delta = df_delta.melt(id_vars =['x_labels', 'where']) 
    
    df_Delta = df[['mol']].copy()
    df_Delta['x_labels'] = df.mol
    df_Delta['where'] = 'SO-ZORA'
    df_Delta['Delta1']  = df['isolated_supergeom']-df['isolated_vac']
    df_Delta['Delta2']  = df['fde']-df['isolated_supergeom']
    df_Delta['Delta3']  = df['fnt']-df['fde']    
    df_Delta['Delta4']  = df['supermolecule']-df['fnt']   
    df_Delta=df_Delta.drop(['mol'], axis=1)
    df_Delta = df_Delta.melt(id_vars =['x_labels', 'where']) 

    df_d = df[['mol']].copy()
    df_d['x_labels'] = df.mol
    df_d['where'] = 'SO-ZORA'    
    df_d['d1']      = (df['isolated_supergeom']-df['supermolecule'])/df['isolated_vac']
    df_d['d2']      = (df['fde']-df['supermolecule'])/df['isolated_vac']
    df_d['d3']      = (df['fnt']-df['supermolecule'])/df['isolated_vac']
    df_d['d4']      = (df['supermolecule']-df['supermolecule'])/df['isolated_vac']
    df_d=df_d.drop(['mol'], axis=1)
    df_d = df_d.melt(id_vars =['x_labels', 'where'])
    
    df_all = pd.concat([df_se, df_delta, df_Delta, df_d],axis=1)
    
    return df_all, df_se, df_delta, df_Delta, df_d


def prep_dirac(df):
    
    df_se = df[['mol']].copy()
    df_se['x_labels'] = df_se.mol
    df_se['where'] = 'DC'
    df_se['solveff']  = df['supermolecule']-df['isolated_vac']
    df_se=df_se.drop(['mol'], axis=1)
    df_se = df_se.melt(id_vars =['x_labels', 'where'])

    df_nose = df[['mol']].copy()
    df_nose['x_labels'] = df.mol
    df_nose['where'] = 'DC'
    df_nose['delta1']  = df['isolated_supergeom_supergrid']-df['isolated_vac']
    df_nose['delta2']  = df['fde_vw11']-df['isolated_vac']
    df_nose['delta3']  = df['fnt_vw11']-df['isolated_vac']    
    df_nose['delta4']  = df['supermolecule']-df['isolated_vac']     
    df_nose['Delta1']  = df['isolated_supergeom_supergrid']-df['isolated_vac']
    df_nose['Delta2']  = df['fde_vw11']-df['isolated_supergeom_supergrid']
    df_nose['Delta3']  = df['fnt_vw11']-df['fde_vw11']    
    df_nose['Delta4']  = df['supermolecule']-df['fnt_vw11'] 
    df_nose['d1']      = (df['isolated_supergeom_supergrid']-df['supermolecule'])/df['isolated_vac']
    df_nose['d2']      = (df['fde_vw11']-df['supermolecule'])/df['isolated_vac']
    df_nose['d3']      = (df['fnt_vw11']-df['supermolecule'])/df['isolated_vac']
    df_nose['d4']      = (df['supermolecule']-df['supermolecule'])/df['isolated_vac']
    df_nose=df_nose.drop(['mol'], axis=1)
    df_nose = df_nose.melt(id_vars =['x_labels', 'where'])

    return df_nose, df_se
```


```python
df1=pd.read_csv('dirac_data.csv')
df2=pd.read_csv('adf_data.csv')
```

Let's have a look at the dataframe (DIRAC data):


```python
df1
```

And at the second dataframe (ADF data):


```python
df2
```


```python
df_dirac_se, df_dirac_delta, df_dirac_Delta, df_dirac_d = prep_dirac(df1)
```


```python
def def_molprop():

    mol =  ['Ti',
            'V',
            'Cr',
            'Mn',
            'Fe',
            'Co',
            'Ni',
            'Cu',
            'Zn',
            'Nb',
            'Mo',
            'Tc',
            'Ru',
            'Pd',
            'Ag',
            'Cd',
            'Ta',
            'W',
            'Re',
            'Os',
            'Pt',
            'Hg'
    ]
 
    atomic_number_X={}
    atomic_number_X['Ti']=22
    atomic_number_X['V']=23
    atomic_number_X['Cr']=24
    atomic_number_X['Mn']=25
    atomic_number_X['Fe']=26
    atomic_number_X['Co']=27
    atomic_number_X['Ni']=28
    atomic_number_X['Cu']=29
    atomic_number_X['Zn']=30
    atomic_number_X['Nb']=41
    atomic_number_X['Mo']=42
    atomic_number_X['Tc']=43
    atomic_number_X['Ru']=44
    atomic_number_X['Pd']=46
    atomic_number_X['Ag']=47
    atomic_number_X['Cd']=48
    atomic_number_X['Ta']=73
    atomic_number_X['W']=74
    atomic_number_X['Re']=75
    atomic_number_X['Os']=76
    atomic_number_X['Pt']=78
    atomic_number_X['Hg']=80   
    
    solvent={} 
    solvent['Ti']='12TiCl$_{4}$'
    solvent['V']='6C$_6$H$_6$'
    solvent['Cr']='12H$_2$O'
    solvent['Mn']='12H$_2$O'
    solvent['Fe']='6C$_6$H$_6$'
    solvent['Co']='12H$_2$O'
    solvent['Ni']='6C$_6$H$_6$'
    solvent['Cu']='12CH$_3$CN'
    solvent['Zn']='12H$_2$O'
    solvent['Nb']='12CH$_3$CN'
    solvent['Mo']='12H$_2$O'
    solvent['Tc']='12H$_2$O'
    solvent['Ru']='12H$_2$O'
    solvent['Pd']='12H$_2$O'
    solvent['Ag']='12H$_2$O'
    solvent['Cd']='6Cd(CH$_3$)$_2$'
    solvent['Ta']='12CH$_3$CN'
    solvent['W']='12H$_2$O'
    solvent['Re']='12H$_2$O'
    solvent['Os']='12CCl$_4$'
    solvent['Pt']='12H$_2$O'
    solvent['Hg']='6Hg(CH$_3$)$_2$'

    charge={} 
    charge['Ti']=0
    charge['V']=0
    charge['Cr']=-2
    charge['Mn']=-1
    charge['Fe']=0
    charge['Co']=-3
    charge['Ni']=0
    charge['Cu']=1
    charge['Zn']=2
    charge['Nb']=-1
    charge['Mo']=-2
    charge['Tc']=-1
    charge['Ru']=-4
    charge['Pd']=-2
    charge['Ag']=1
    charge['Cd']=0
    charge['Ta']=-1
    charge['W']=-2
    charge['Re']=-1
    charge['Os']=0
    charge['Pt']=-2
    charge['Hg']=0
    
    row={} 
    row['Ti']=4
    row['V']=4
    row['Cr']=4
    row['Mn']=4
    row['Fe']=4
    row['Co']=4
    row['Ni']=4
    row['Cu']=4
    row['Zn']=4
    row['Nb']=5
    row['Mo']=5
    row['Tc']=5
    row['Ru']=5
    row['Pd']=5
    row['Ag']=5
    row['Cd']=5
    row['Ta']=6
    row['W']=6
    row['Re']=6
    row['Os']=6
    row['Pt']=6
    row['Hg']=6
      
    order_where=['DC','SO-ZORA']
    return atomic_number_X, solvent, charge, row

def prep_adf(df):
    
    an,solvent,charge,row=def_molprop()
   
    df_se = df[['mol']].copy()
    df_se['x_labels'] = df_se.mol
    df_se['where'] = 'SO-ZORA'
    df_se['solveff']  = df['supermolecule']-df['isolated_vac']
    df_se['row'] = df_se['mol'].map(row)
    df_se['charge'] = df_se['mol'].map(charge)
    df_se['solvent'] = df_se['mol'].map(solvent)
    #df_se = df_se.drop(['mol'], axis=1)
    #df_se = df_se.melt(id_vars =['x_labels', 'where'])     
    
    df_delta = df[['mol']].copy()
    #df_delta['x_labels'] = df.mol
    #df_delta['where'] = 'SO-ZORA' 
    df_delta['delta1']  = df['isolated_supergeom']-df['isolated_vac']
    df_delta['delta2']  = df['fde']-df['isolated_vac']
    df_delta['delta3']  = df['fnt']-df['isolated_vac']    
    df_delta['delta4']  = df['supermolecule']-df['isolated_vac']     
    df_delta=df_delta.drop(['mol'], axis=1)
    #df_delta = df_delta.melt(id_vars =['x_labels', 'where']) 
    
    df_Delta = df[['mol']].copy()
    #df_Delta['x_labels'] = df.mol
    #df_Delta['where'] = 'SO-ZORA'
    df_Delta['Delta1']  = df['isolated_supergeom']-df['isolated_vac']
    df_Delta['Delta2']  = df['fde']-df['isolated_supergeom']
    df_Delta['Delta3']  = df['fnt']-df['fde']    
    df_Delta['Delta4']  = df['supermolecule']-df['fnt']   
    df_Delta=df_Delta.drop(['mol'], axis=1)
    #df_Delta = df_Delta.melt(id_vars =['x_labels', 'where']) 

    df_d = df[['mol']].copy()
    #df_d['x_labels'] = df.mol
    #df_d['where'] = 'SO-ZORA'    
    df_d['d1']      = (df['isolated_supergeom']-df['supermolecule'])/df['isolated_vac']
    df_d['d2']      = (df['fde']-df['supermolecule'])/df['isolated_vac']
    df_d['d3']      = (df['fnt']-df['supermolecule'])/df['isolated_vac']
    df_d['d4']      = (df['supermolecule']-df['supermolecule'])/df['isolated_vac']
    df_d=df_d.drop(['mol'], axis=1)
    #df_d = df_d.melt(id_vars =['x_labels', 'where'])
    
    df_all = pd.concat([df_se, df_delta, df_Delta, df_d],axis=1,sort=False)
    
    return df_all, df_se, df_delta, df_Delta, df_d

```


```python

df_adf_all, df_adf_se, df_adf_delta, df_adf_Delta, df_adf_d = prep_adf(df2)
```


```python
df_adf_all
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>mol</th>
      <th>x_labels</th>
      <th>where</th>
      <th>solveff</th>
      <th>row</th>
      <th>charge</th>
      <th>solvent</th>
      <th>delta1</th>
      <th>delta2</th>
      <th>delta3</th>
      <th>delta4</th>
      <th>Delta1</th>
      <th>Delta2</th>
      <th>Delta3</th>
      <th>Delta4</th>
      <th>d1</th>
      <th>d2</th>
      <th>d3</th>
      <th>d4</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Ti</td>
      <td>Ti</td>
      <td>SO-ZORA</td>
      <td>4.30</td>
      <td>4</td>
      <td>0</td>
      <td>12TiCl$_{4}$</td>
      <td>22.18</td>
      <td>21.68</td>
      <td>21.85</td>
      <td>4.30</td>
      <td>22.18</td>
      <td>-0.50</td>
      <td>0.17</td>
      <td>-17.55</td>
      <td>-2.18e-02</td>
      <td>-2.12e-02</td>
      <td>-2.14e-02</td>
      <td>-0.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>V</td>
      <td>V</td>
      <td>SO-ZORA</td>
      <td>-25.33</td>
      <td>4</td>
      <td>0</td>
      <td>6C$_6$H$_6$</td>
      <td>2.81</td>
      <td>1.02</td>
      <td>-0.11</td>
      <td>-25.33</td>
      <td>2.81</td>
      <td>-1.80</td>
      <td>-1.12</td>
      <td>-25.23</td>
      <td>-1.51e-02</td>
      <td>-1.42e-02</td>
      <td>-1.36e-02</td>
      <td>-0.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Cr</td>
      <td>Cr</td>
      <td>SO-ZORA</td>
      <td>63.86</td>
      <td>4</td>
      <td>-2</td>
      <td>12H$_2$O</td>
      <td>123.04</td>
      <td>104.04</td>
      <td>76.65</td>
      <td>63.86</td>
      <td>123.04</td>
      <td>-19.01</td>
      <td>-27.38</td>
      <td>-12.80</td>
      <td>-2.17e-02</td>
      <td>-1.47e-02</td>
      <td>-4.69e-03</td>
      <td>-0.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Mn</td>
      <td>Mn</td>
      <td>SO-ZORA</td>
      <td>-7.24</td>
      <td>4</td>
      <td>-1</td>
      <td>12H$_2$O</td>
      <td>35.49</td>
      <td>29.14</td>
      <td>15.23</td>
      <td>-7.24</td>
      <td>35.49</td>
      <td>-6.35</td>
      <td>-13.91</td>
      <td>-22.47</td>
      <td>-1.09e-02</td>
      <td>-9.25e-03</td>
      <td>-5.71e-03</td>
      <td>-0.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Fe</td>
      <td>Fe</td>
      <td>SO-ZORA</td>
      <td>47.11</td>
      <td>4</td>
      <td>0</td>
      <td>6C$_6$H$_6$</td>
      <td>67.43</td>
      <td>65.41</td>
      <td>64.57</td>
      <td>47.11</td>
      <td>67.43</td>
      <td>-2.02</td>
      <td>-0.84</td>
      <td>-17.46</td>
      <td>-8.72e-03</td>
      <td>-7.85e-03</td>
      <td>-7.49e-03</td>
      <td>-0.0</td>
    </tr>
    <tr>
      <th>5</th>
      <td>Co</td>
      <td>Co</td>
      <td>SO-ZORA</td>
      <td>1097.37</td>
      <td>4</td>
      <td>-3</td>
      <td>12H$_2$O</td>
      <td>1098.32</td>
      <td>1127.60</td>
      <td>1124.48</td>
      <td>1097.37</td>
      <td>1098.32</td>
      <td>29.28</td>
      <td>-3.12</td>
      <td>-27.11</td>
      <td>-1.65e-04</td>
      <td>-5.23e-03</td>
      <td>-4.69e-03</td>
      <td>-0.0</td>
    </tr>
    <tr>
      <th>6</th>
      <td>Ni</td>
      <td>Ni</td>
      <td>SO-ZORA</td>
      <td>-27.54</td>
      <td>4</td>
      <td>0</td>
      <td>6C$_6$H$_6$</td>
      <td>-9.36</td>
      <td>-17.95</td>
      <td>-21.46</td>
      <td>-27.54</td>
      <td>-9.36</td>
      <td>-8.59</td>
      <td>-3.51</td>
      <td>-6.09</td>
      <td>-1.12e-02</td>
      <td>-5.89e-03</td>
      <td>-3.73e-03</td>
      <td>-0.0</td>
    </tr>
    <tr>
      <th>7</th>
      <td>Cu</td>
      <td>Cu</td>
      <td>SO-ZORA</td>
      <td>-53.46</td>
      <td>4</td>
      <td>1</td>
      <td>12CH$_3$CN</td>
      <td>-23.30</td>
      <td>-44.89</td>
      <td>-47.87</td>
      <td>-53.46</td>
      <td>-23.30</td>
      <td>-21.59</td>
      <td>-2.98</td>
      <td>-5.59</td>
      <td>6.32e-02</td>
      <td>1.80e-02</td>
      <td>1.17e-02</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>8</th>
      <td>Zn</td>
      <td>Zn</td>
      <td>SO-ZORA</td>
      <td>-43.15</td>
      <td>4</td>
      <td>2</td>
      <td>12H$_2$O</td>
      <td>-29.68</td>
      <td>-30.29</td>
      <td>-30.12</td>
      <td>-43.15</td>
      <td>-29.68</td>
      <td>-0.61</td>
      <td>0.17</td>
      <td>-13.02</td>
      <td>7.03e-03</td>
      <td>6.72e-03</td>
      <td>6.80e-03</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>9</th>
      <td>Nb</td>
      <td>Nb</td>
      <td>SO-ZORA</td>
      <td>-19.51</td>
      <td>5</td>
      <td>-1</td>
      <td>12CH$_3$CN</td>
      <td>15.89</td>
      <td>3.21</td>
      <td>0.18</td>
      <td>-19.51</td>
      <td>15.89</td>
      <td>-12.68</td>
      <td>-3.03</td>
      <td>-19.69</td>
      <td>-1.11e-01</td>
      <td>-7.15e-02</td>
      <td>-6.19e-02</td>
      <td>-0.0</td>
    </tr>
    <tr>
      <th>10</th>
      <td>Mo</td>
      <td>Mo</td>
      <td>SO-ZORA</td>
      <td>16.26</td>
      <td>5</td>
      <td>-2</td>
      <td>12H$_2$O</td>
      <td>69.50</td>
      <td>57.48</td>
      <td>38.01</td>
      <td>16.26</td>
      <td>69.50</td>
      <td>-12.02</td>
      <td>-19.47</td>
      <td>-21.75</td>
      <td>-9.52e-02</td>
      <td>-7.37e-02</td>
      <td>-3.89e-02</td>
      <td>-0.0</td>
    </tr>
    <tr>
      <th>11</th>
      <td>Tc</td>
      <td>Tc</td>
      <td>SO-ZORA</td>
      <td>26.99</td>
      <td>5</td>
      <td>-1</td>
      <td>12H$_2$O</td>
      <td>22.69</td>
      <td>28.88</td>
      <td>23.90</td>
      <td>26.99</td>
      <td>22.69</td>
      <td>6.18</td>
      <td>-4.98</td>
      <td>3.09</td>
      <td>3.03e-03</td>
      <td>-1.33e-03</td>
      <td>2.18e-03</td>
      <td>-0.0</td>
    </tr>
    <tr>
      <th>12</th>
      <td>Ru</td>
      <td>Ru</td>
      <td>SO-ZORA</td>
      <td>787.76</td>
      <td>5</td>
      <td>-4</td>
      <td>12H$_2$O</td>
      <td>828.13</td>
      <td>831.76</td>
      <td>834.18</td>
      <td>787.76</td>
      <td>828.13</td>
      <td>3.64</td>
      <td>2.41</td>
      <td>-46.41</td>
      <td>-4.97e-02</td>
      <td>-5.42e-02</td>
      <td>-5.72e-02</td>
      <td>-0.0</td>
    </tr>
    <tr>
      <th>13</th>
      <td>Pd</td>
      <td>Pd</td>
      <td>SO-ZORA</td>
      <td>270.97</td>
      <td>5</td>
      <td>-2</td>
      <td>12H$_2$O</td>
      <td>202.93</td>
      <td>238.60</td>
      <td>270.48</td>
      <td>270.97</td>
      <td>202.93</td>
      <td>35.67</td>
      <td>31.89</td>
      <td>0.49</td>
      <td>4.30e-02</td>
      <td>2.05e-02</td>
      <td>3.07e-04</td>
      <td>-0.0</td>
    </tr>
    <tr>
      <th>14</th>
      <td>Ag</td>
      <td>Ag</td>
      <td>SO-ZORA</td>
      <td>-210.17</td>
      <td>5</td>
      <td>1</td>
      <td>12H$_2$O</td>
      <td>-175.04</td>
      <td>-174.27</td>
      <td>-173.43</td>
      <td>-210.17</td>
      <td>-175.04</td>
      <td>0.77</td>
      <td>0.84</td>
      <td>-36.73</td>
      <td>8.14e-03</td>
      <td>8.32e-03</td>
      <td>8.51e-03</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>15</th>
      <td>Cd</td>
      <td>Cd</td>
      <td>SO-ZORA</td>
      <td>54.90</td>
      <td>5</td>
      <td>0</td>
      <td>6Cd(CH$_3$)$_2$</td>
      <td>6.98</td>
      <td>22.72</td>
      <td>46.82</td>
      <td>54.90</td>
      <td>6.98</td>
      <td>15.73</td>
      <td>24.10</td>
      <td>8.08</td>
      <td>-1.39e-02</td>
      <td>-9.31e-03</td>
      <td>-2.34e-03</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>16</th>
      <td>Ta</td>
      <td>Ta</td>
      <td>SO-ZORA</td>
      <td>30.64</td>
      <td>6</td>
      <td>-1</td>
      <td>12CH$_3$CN</td>
      <td>11.59</td>
      <td>-6.85</td>
      <td>-11.60</td>
      <td>30.64</td>
      <td>11.59</td>
      <td>-18.44</td>
      <td>-4.75</td>
      <td>42.23</td>
      <td>-5.58e-03</td>
      <td>-1.10e-02</td>
      <td>-1.24e-02</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>17</th>
      <td>W</td>
      <td>W</td>
      <td>SO-ZORA</td>
      <td>-74.40</td>
      <td>6</td>
      <td>-2</td>
      <td>12H$_2$O</td>
      <td>85.21</td>
      <td>52.39</td>
      <td>25.10</td>
      <td>-74.40</td>
      <td>85.21</td>
      <td>-32.82</td>
      <td>-27.30</td>
      <td>-99.50</td>
      <td>5.32e-02</td>
      <td>4.22e-02</td>
      <td>3.31e-02</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>18</th>
      <td>Re</td>
      <td>Re</td>
      <td>SO-ZORA</td>
      <td>-52.82</td>
      <td>6</td>
      <td>-1</td>
      <td>12H$_2$O</td>
      <td>21.60</td>
      <td>11.56</td>
      <td>2.74</td>
      <td>-52.82</td>
      <td>21.60</td>
      <td>-10.05</td>
      <td>-8.82</td>
      <td>-55.56</td>
      <td>3.93e-02</td>
      <td>3.40e-02</td>
      <td>2.94e-02</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>19</th>
      <td>Os</td>
      <td>Os</td>
      <td>SO-ZORA</td>
      <td>121.18</td>
      <td>6</td>
      <td>0</td>
      <td>12CCl$_4$</td>
      <td>132.73</td>
      <td>135.56</td>
      <td>137.59</td>
      <td>121.18</td>
      <td>132.73</td>
      <td>2.83</td>
      <td>2.03</td>
      <td>-16.41</td>
      <td>9.49e-03</td>
      <td>1.18e-02</td>
      <td>1.35e-02</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>20</th>
      <td>Pt</td>
      <td>Pt</td>
      <td>SO-ZORA</td>
      <td>498.49</td>
      <td>6</td>
      <td>-2</td>
      <td>12H$_2$O</td>
      <td>292.29</td>
      <td>371.16</td>
      <td>428.06</td>
      <td>498.49</td>
      <td>292.29</td>
      <td>78.86</td>
      <td>56.90</td>
      <td>70.43</td>
      <td>-1.09e-01</td>
      <td>-6.75e-02</td>
      <td>-3.74e-02</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>21</th>
      <td>Hg</td>
      <td>Hg</td>
      <td>SO-ZORA</td>
      <td>40.08</td>
      <td>6</td>
      <td>0</td>
      <td>6Hg(CH$_3$)$_2$</td>
      <td>31.95</td>
      <td>67.20</td>
      <td>95.47</td>
      <td>40.08</td>
      <td>31.95</td>
      <td>35.25</td>
      <td>28.28</td>
      <td>-55.39</td>
      <td>-9.15e-04</td>
      <td>3.05e-03</td>
      <td>6.24e-03</td>
      <td>0.0</td>
    </tr>
  </tbody>
</table>
</div>



## Total solvent effects:



```python
scatter = alt.Chart(df_adf_se).mark_point(size=100).encode(
    x='x_labels:O',
    y=alt.Y('solveff:O',
            sort='descending',
            axis=alt.Axis(title="Solvent effects [ppm]", 
                          format=",.0f",
                          #values=list(range(1100,-250,-50)),
                          ),
            #scale=alt.Scale(domain=list(range(1100,-250,-50))),
           ),
    color=alt.Color('charge', type='nominal', scale=alt.Scale(scheme='dark2')),
    shape='solvent:O'
    #shape='row:O'
    #color=alt.condition(brush, 'Origin:N', alt.value('lightgray'))
#).add_selection(
#    brush
)

#text = scatter.mark_text(align="center", baseline="bottom").encode(
#       text=alt.Text('solveff:O', format=",.0f")
#    )

#chart = scatter + text
chart = scatter
chart.show()
```

    Displaying chart at http://localhost:22930/



```python
chart=alt.Chart(df_adf_all).mark_point().encode(
    alt.X(alt.repeat("column"), type='ordinal'),
    alt.Y(alt.repeat("row"), type='ordinal',
          sort='descending',
          #impute=alt.ImputeParams(),
          axis=alt.Axis(format=",.0f")
         ),
    color=alt.Color('charge', type='nominal', scale=alt.Scale(scheme='dark2'))
).properties(
    width=200,
    height=200
).repeat(
    row=['solveff', 'delta3'],
    column=['mol', 'solvent']
).interactive()


#chart.show()
chart
```





<div id="altair-viz-9cbd17f16cb442db9f622f996c34e2dc"></div>
<script type="text/javascript">
  (function(spec, embedOpt){
    let outputDiv = document.currentScript.previousElementSibling;
    if (outputDiv.id !== "altair-viz-9cbd17f16cb442db9f622f996c34e2dc") {
      outputDiv = document.getElementById("altair-viz-9cbd17f16cb442db9f622f996c34e2dc");
    }
    const paths = {
      "vega": "https://cdn.jsdelivr.net/npm//vega@5?noext",
      "vega-lib": "https://cdn.jsdelivr.net/npm//vega-lib?noext",
      "vega-lite": "https://cdn.jsdelivr.net/npm//vega-lite@4.8.1?noext",
      "vega-embed": "https://cdn.jsdelivr.net/npm//vega-embed@6?noext",
    };

    function loadScript(lib) {
      return new Promise(function(resolve, reject) {
        var s = document.createElement('script');
        s.src = paths[lib];
        s.async = true;
        s.onload = () => resolve(paths[lib]);
        s.onerror = () => reject(`Error loading script: ${paths[lib]}`);
        document.getElementsByTagName("head")[0].appendChild(s);
      });
    }

    function showError(err) {
      outputDiv.innerHTML = `<div class="error" style="color:red;">${err}</div>`;
      throw err;
    }

    function displayChart(vegaEmbed) {
      vegaEmbed(outputDiv, spec, embedOpt)
        .catch(err => showError(`Javascript Error: ${err.message}<br>This usually means there's a typo in your chart specification. See the javascript console for the full traceback.`));
    }

    if(typeof define === "function" && define.amd) {
      requirejs.config({paths});
      require(["vega-embed"], displayChart, err => showError(`Error loading script: ${err.message}`));
    } else if (typeof vegaEmbed === "function") {
      displayChart(vegaEmbed);
    } else {
      loadScript("vega")
        .then(() => loadScript("vega-lite"))
        .then(() => loadScript("vega-embed"))
        .catch(showError)
        .then(() => displayChart(vegaEmbed));
    }
  })({"config": {"view": {"continuousWidth": 400, "continuousHeight": 300}}, "repeat": {"column": ["mol", "solvent"], "row": ["solveff", "delta3"]}, "spec": {"data": {"name": "data-f8491dc3e7ef8465656fda26be902043"}, "mark": "point", "encoding": {"color": {"type": "nominal", "field": "charge", "scale": {"scheme": "dark2"}}, "x": {"type": "ordinal", "field": {"repeat": "column"}}, "y": {"type": "ordinal", "axis": {"format": ",.0f"}, "field": {"repeat": "row"}, "sort": "descending"}}, "height": 200, "selection": {"selector021": {"type": "interval", "bind": "scales", "encodings": ["x", "y"]}}, "width": 200}, "$schema": "https://vega.github.io/schema/vega-lite/v4.8.1.json", "datasets": {"data-f8491dc3e7ef8465656fda26be902043": [{"mol": "Ti", "x_labels": "Ti", "where": "SO-ZORA", "solveff": 4.2999999999999545, "row": 4, "charge": 0, "solvent": "12TiCl$_{4}$", "delta1": 22.184000000000083, "delta2": 21.684000000000083, "delta3": 21.854000000000042, "delta4": 4.2999999999999545, "Delta1": 22.184000000000083, "Delta2": -0.5, "Delta3": 0.16999999999995907, "Delta4": -17.554000000000087, "d1": -0.021763098138381527, "d2": -0.021154646501768317, "d3": -0.021361520058216756, "d4": -0.0}, {"mol": "V", "x_labels": "V", "where": "SO-ZORA", "solveff": -25.33199999999988, "row": 4, "charge": 0, "solvent": "6C$_6$H$_6$", "delta1": 2.8140000000003056, "delta2": 1.0170000000000528, "delta3": -0.10500000000001819, "delta4": -25.33199999999988, "Delta1": 2.8140000000003056, "Delta2": -1.7970000000002528, "Delta3": -1.122000000000071, "Delta4": -25.22699999999986, "d1": -0.015131346929647346, "d2": -0.014165276069397935, "d3": -0.013562086584033574, "d4": -0.0}, {"mol": "Cr", "x_labels": "Cr", "where": "SO-ZORA", "solveff": 63.858000000000175, "row": 4, "charge": -2, "solvent": "12H$_2$O", "delta1": 123.04400000000032, "delta2": 104.03900000000021, "delta3": 76.65400000000045, "delta4": 63.858000000000175, "Delta1": 123.04400000000032, "Delta2": -19.00500000000011, "Delta3": -27.384999999999764, "Delta4": -12.796000000000276, "d1": -0.021712884105380876, "d2": -0.014740739300481661, "d3": -0.004694320701051921, "d4": -0.0}, {"mol": "Mn", "x_labels": "Mn", "where": "SO-ZORA", "solveff": -7.242000000000189, "row": 4, "charge": -1, "solvent": "12H$_2$O", "delta1": 35.49200000000019, "delta2": 29.14099999999962, "delta3": 15.230999999999767, "delta4": -7.242000000000189, "Delta1": 35.49200000000019, "Delta2": -6.3510000000005675, "Delta3": -13.909999999999854, "Delta4": -22.472999999999956, "d1": -0.010860140180028253, "d2": -0.009246138441755099, "d3": -0.005711141720077042, "d4": -0.0}, {"mol": "Fe", "x_labels": "Fe", "where": "SO-ZORA", "solveff": 47.11200000000008, "row": 4, "charge": 0, "solvent": "6C$_6$H$_6$", "delta1": 67.43199999999979, "delta2": 65.41499999999996, "delta3": 64.57400000000007, "delta4": 47.11200000000008, "Delta1": 67.43199999999979, "Delta2": -2.0169999999998254, "Delta3": -0.8409999999998945, "Delta4": -17.46199999999999, "d1": -0.008717314895411402, "d2": -0.007852018431629732, "d3": -0.007491227987385629, "d4": -0.0}, {"mol": "Co", "x_labels": "Co", "where": "SO-ZORA", "solveff": 1097.3739999999998, "row": 4, "charge": -3, "solvent": "12H$_2$O", "delta1": 1098.3239999999996, "delta2": 1127.5999999999995, "delta3": 1124.4809999999998, "delta4": 1097.3739999999998, "Delta1": 1098.3239999999996, "Delta2": 29.27599999999984, "Delta3": -3.118999999999687, "Delta4": -27.10699999999997, "d1": -0.0001645170921136079, "d2": -0.005234414343396641, "d3": -0.004694278753604651, "d4": -0.0}, {"mol": "Ni", "x_labels": "Ni", "where": "SO-ZORA", "solveff": -27.544000000000096, "row": 4, "charge": 0, "solvent": "6C$_6$H$_6$", "delta1": -9.360000000000127, "delta2": -17.95199999999977, "delta3": -21.45699999999988, "delta4": -27.544000000000096, "Delta1": -9.360000000000127, "Delta2": -8.591999999999643, "Delta3": -3.505000000000109, "Delta4": -6.0870000000002165, "d1": -0.011156635879161785, "d2": -0.005885088613777148, "d3": -0.0037346261876627976, "d4": -0.0}, {"mol": "Cu", "x_labels": "Cu", "where": "SO-ZORA", "solveff": -53.464, "row": 4, "charge": 1, "solvent": "12CH$_3$CN", "delta1": -23.295999999999992, "delta2": -44.88799999999998, "delta3": -47.87099999999998, "delta4": -53.464, "Delta1": -23.295999999999992, "Delta2": -21.591999999999985, "Delta3": -2.983000000000004, "Delta4": -5.593000000000018, "d1": 0.06319613047294453, "d2": 0.01796506281278088, "d3": 0.01171625423412821, "d4": 0.0}, {"mol": "Zn", "x_labels": "Zn", "where": "SO-ZORA", "solveff": -43.14599999999996, "row": 4, "charge": 2, "solvent": "12H$_2$O", "delta1": -29.68100000000004, "delta2": -30.29199999999969, "delta3": -30.121000000000095, "delta4": -43.14599999999996, "Delta1": -29.68100000000004, "Delta2": -0.6109999999996489, "Delta3": 0.17099999999959437, "Delta4": -13.024999999999864, "d1": 0.007034567861245784, "d2": 0.006715360957182009, "d3": 0.006804697095635049, "d4": 0.0}, {"mol": "Nb", "x_labels": "Nb", "where": "SO-ZORA", "solveff": -19.514999999999986, "row": 5, "charge": -1, "solvent": "12CH$_3$CN", "delta1": 15.894000000000005, "delta2": 3.2099999999999795, "delta3": 0.17900000000003047, "delta4": -19.514999999999986, "Delta1": 15.894000000000005, "Delta2": -12.684000000000026, "Delta3": -3.030999999999949, "Delta4": -19.694000000000017, "d1": -0.11137042011203403, "d2": -0.07147597495116977, "d3": -0.061942699700257016, "d4": -0.0}, {"mol": "Mo", "x_labels": "Mo", "where": "SO-ZORA", "solveff": 16.257000000000062, "row": 5, "charge": -2, "solvent": "12H$_2$O", "delta1": 69.50000000000006, "delta2": 57.478999999999985, "delta3": 38.01100000000008, "delta4": 16.257000000000062, "Delta1": 69.50000000000006, "Delta2": -12.021000000000072, "Delta3": -19.467999999999904, "Delta4": -21.75400000000002, "d1": -0.09521501751650162, "d2": -0.07371773664266144, "d3": -0.03890290725642767, "d4": -0.0}, {"mol": "Tc", "x_labels": "Tc", "where": "SO-ZORA", "solveff": 26.989000000000033, "row": 5, "charge": -1, "solvent": "12H$_2$O", "delta1": 22.694999999999936, "delta2": 28.878999999999905, "delta3": 23.896999999999935, "delta4": 26.989000000000033, "Delta1": 22.694999999999936, "Delta2": 6.183999999999969, "Delta3": -4.981999999999971, "Delta4": 3.0920000000000982, "d1": 0.003025801759954068, "d2": -0.0013318037555455694, "d3": 0.002178802757749899, "d4": -0.0}, {"mol": "Ru", "x_labels": "Ru", "where": "SO-ZORA", "solveff": 787.761, "row": 5, "charge": -4, "solvent": "12H$_2$O", "delta1": 828.1270000000001, "delta2": 831.764, "delta3": 834.176, "delta4": 787.761, "Delta1": 828.1270000000001, "Delta2": 3.6370000000000005, "Delta3": 2.411999999999999, "Delta4": -46.415, "d1": -0.04974484294377672, "d2": -0.05422688212988671, "d3": -0.057199298549160095, "d4": -0.0}, {"mol": "Pd", "x_labels": "Pd", "where": "SO-ZORA", "solveff": 270.9690000000003, "row": 5, "charge": -2, "solvent": "12H$_2$O", "delta1": 202.92700000000013, "delta2": 238.59600000000023, "delta3": 270.48400000000015, "delta4": 270.9690000000003, "Delta1": 202.92700000000013, "Delta2": 35.669000000000096, "Delta3": 31.88799999999992, "Delta4": 0.48500000000012733, "d1": 0.04304883596306927, "d2": 0.020481760774704455, "d3": 0.0003068499668159965, "d4": -0.0}, {"mol": "Ag", "x_labels": "Ag", "where": "SO-ZORA", "solveff": -210.16699999999946, "row": 5, "charge": 1, "solvent": "12H$_2$O", "delta1": -175.03999999999996, "delta2": -174.26800000000003, "delta3": -173.433, "delta4": -210.16699999999946, "Delta1": -175.03999999999996, "Delta2": 0.7719999999999345, "Delta3": 0.8350000000000364, "Delta4": -36.73399999999947, "d1": 0.008140764518530916, "d2": 0.008319677326579011, "d3": 0.008513190532175098, "d4": 0.0}, {"mol": "Cd", "x_labels": "Cd", "where": "SO-ZORA", "solveff": 54.89800000000059, "row": 5, "charge": 0, "solvent": "6Cd(CH$_3$)$_2$", "delta1": 6.982000000000426, "delta2": 22.7170000000001, "delta3": 46.818000000000666, "delta4": 54.89800000000059, "Delta1": 6.982000000000426, "Delta2": 15.734999999999673, "Delta3": 24.101000000000568, "Delta4": 8.079999999999927, "d1": -0.013863777019912954, "d2": -0.009311090414012523, "d3": -0.002337826995594275, "d4": 0.0}, {"mol": "Ta", "x_labels": "Ta", "where": "SO-ZORA", "solveff": 30.63500000000022, "row": 6, "charge": -1, "solvent": "12CH$_3$CN", "delta1": 11.592999999999847, "delta2": -6.84900000000016, "delta3": -11.596000000000458, "delta4": 30.63500000000022, "Delta1": 11.592999999999847, "Delta2": -18.442000000000007, "Delta3": -4.747000000000298, "Delta4": 42.23100000000068, "d1": -0.005583268607645744, "d2": -0.010990612356317148, "d3": -0.012382471198901724, "d4": 0.0}, {"mol": "W", "x_labels": "W", "where": "SO-ZORA", "solveff": -74.40099999999984, "row": 6, "charge": -2, "solvent": "12H$_2$O", "delta1": 85.21000000000004, "delta2": 52.39400000000023, "delta3": 25.09900000000016, "delta4": -74.40099999999984, "Delta1": 85.21000000000004, "Delta2": -32.8159999999998, "Delta3": -27.295000000000073, "Delta4": -99.5, "d1": 0.05316830974068907, "d2": 0.04223691245321864, "d3": 0.03314462549071534, "d4": 0.0}, {"mol": "Re", "x_labels": "Re", "where": "SO-ZORA", "solveff": -52.82300000000032, "row": 6, "charge": -1, "solvent": "12H$_2$O", "delta1": 21.60499999999979, "delta2": 11.55600000000004, "delta3": 2.7369999999996253, "delta4": -52.82300000000032, "Delta1": 21.60499999999979, "Delta2": -10.04899999999975, "Delta3": -8.819000000000415, "Delta4": -55.559999999999945, "d1": 0.03932442385502783, "d2": 0.03401498204120556, "d3": 0.02935541717344737, "d4": 0.0}, {"mol": "Os", "x_labels": "Os", "where": "SO-ZORA", "solveff": 121.17599999999993, "row": 6, "charge": 0, "solvent": "12CCl$_4$", "delta1": 132.73399999999992, "delta2": 135.55999999999995, "delta3": 137.587, "delta4": 121.17599999999993, "Delta1": 132.73399999999992, "Delta2": 2.826000000000022, "Delta3": 2.0270000000000437, "Delta4": -16.411000000000058, "d1": 0.009486133567518316, "d2": 0.011805549855959828, "d3": 0.013469193457046525, "d4": 0.0}, {"mol": "Pt", "x_labels": "Pt", "where": "SO-ZORA", "solveff": 498.48800000000006, "row": 6, "charge": -2, "solvent": "12H$_2$O", "delta1": 292.2940000000001, "delta2": 371.1560000000002, "delta3": 428.05899999999997, "delta4": 498.48800000000006, "Delta1": 292.2940000000001, "Delta2": 78.86200000000008, "Delta3": 56.90299999999979, "Delta4": 70.42900000000009, "d1": -0.10937188371234462, "d2": -0.0675409599545101, "d3": -0.0373577911965272, "d4": 0.0}, {"mol": "Hg", "x_labels": "Hg", "where": "SO-ZORA", "solveff": 40.07600000000093, "row": 6, "charge": 0, "solvent": "6Hg(CH$_3$)$_2$", "delta1": 31.950000000000728, "delta2": 67.19500000000153, "delta3": 95.47099999999955, "delta4": 40.07600000000093, "Delta1": 31.950000000000728, "Delta2": 35.2450000000008, "Delta3": 28.27599999999802, "Delta4": -55.39499999999862, "d1": -0.0009148227516846377, "d2": 0.003053049249684422, "d3": 0.006236353227857244, "d4": 0.0}]}}, {"mode": "vega-lite"});
</script>




```python

```


```python
# Create dataframe
data = [[7, 10, 'Alex', 'Smith'],
        [12, 20, 'Bob', 'Jones'],
        [10, 30, 'Clive', 'Smith'],
        [42, 40,  'Alex', 'Johnson']]
df = pd.DataFrame(data,columns=['Favorite number', 'Age', 'First name', 'Last name'])

# Create selections
selection_first_name = alt.selection_multi(fields=['First name'], empty='none')
selection_last_name = alt.selection_multi(fields=['Last name'], empty='none')

# Create interactive scatter plot
scatter = alt.Chart(df).mark_point(size=100).encode(
    x='Favorite number:Q',
    y='Age:Q',
    color=alt.condition(selection_first_name & selection_last_name,
                        alt.Color('First name:N', legend=None),
                        alt.value('lightgray') ),
    shape=alt.Shape('Last name:N', legend=None),
    tooltip=['First name', 'Last name']
).add_selection(
    alt.selection_interval(bind='scales')
)

# Create interactive model name legend
legend_first_name = alt.Chart(df).mark_point(size=100).encode(
    y=alt.Y('First name:N', axis=alt.Axis(orient='right')),
    color=alt.condition(selection_first_name,
                        alt.Color('First name:N', legend=None),
                        alt.value('lightgray') ),
).add_selection(
    selection_first_name

)

# Create interactive model name legend
legend_last_name = alt.Chart(df).mark_point(size=100).encode(
    y=alt.Y('Last name:N', axis=alt.Axis(orient='right')),
    shape=alt.Shape('Last name:N', legend=None),
    color=alt.condition(selection_last_name,
                        alt.value('black'),
                        alt.value('lightgray') ),
).add_selection(
    selection_last_name
)

# Combine plotting elements
chart = scatter | legend_first_name | legend_last_name
#chart.save('test.html')
chart.show()
```


```python

```


```python

```


```python
df_plot = pd.concat([df1.set_index('mol'),
                     df2.set_index('mol')]).reset_index()
df_plot_te = pd.concat([df1_te.set_index('mol'),
                     df2_te.set_index('mol')]).reset_index()
```


```python
df_plot['variable'].replace({'delta1': '\u0394'+'(1)',
                             'delta2': '\u0394'+'(2)',
                             'delta3': '\u0394'+'(3)',
                             'delta4': '\u0394'+'(4)'
                            },inplace=True)
```


```python
df_plot_all = pd.merge(df_plot, df_plot_te, on=['mol','name'])
```


```python
order_mol=['Cr', 'Mn', 'Co', 'Zn', 'Mo', 'Tc', 'Ru', 'Pd', 'Ag', 'W', 'Re', 'Pt']
order_where=['set1','set2']


bars=alt.Chart(df_plot_all).mark_bar(size=15).encode(     

    # which field to group columns on
    x=alt.X('name:O',
            axis=alt.Axis(grid=True,labelFontSize=8),
            sort=order_where,
            title=None),

    # which field to use as Y values and how to calculate
    y=alt.Y('value_x:Q',
            axis=alt.Axis(grid=True,title=None)),

    # which field to color by & legend
    color=alt.Color('variable_x',
                    scale=alt.Scale(range=['#4381d1', '#47c488', '#ff6f69']),
                    legend=alt.Legend(title="Contributions",
                                      orient="right",
                                      direction="horizontal",
                                      offset=-200,
                                      titleFontSize=16,
                                      labelFontSize=14)),
                   
    # how to order the data on bars
    order=alt.Order('variable_x:Q', sort='ascending'))


# use separate marks for the 'total effect'
rules = alt.Chart(df_plot_all).mark_tick(color='black', 
                                         thickness=1.5,
                                         size=15
                                        ).encode(x=alt.X('name:O',axis=alt.Axis(grid=True,title=None)),
                                                 y=alt.Y('value_y:Q',axis=alt.Axis(grid=True,title=None)))


# combine all together
alt.layer(bars,rules).properties(height=450,width=50).facet(
   column=alt.Column('mol',
                     sort=order_mol,
                     header=alt.Header(title='Contributions to solvent shifts on NMR shieldings',
                                       orient='bottom',
                                       titleFontSize=20,
                                       labelFontSize=14,
                                       labelBaseline='line-top',
                                       labelAlign='center',
                                       labelAnchor='middle'))).resolve_scale(x='independent').configure_view(strokeOpacity=0)
```

#### Final note

The total effect is additionally marked by horizontal black lines.
It would be better to add these horizontal black lines to the legend, but from what I saw it is not straightfoward to do at this point.

    
#### Thanks:

Scripts used in this notebook are a combination of advice found (mostly) on stackoverlow, which I lost track of... So big thanks to all Altair experts out there!
