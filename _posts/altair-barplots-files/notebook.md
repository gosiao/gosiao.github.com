### What & why:


Recently we were investigating the solvent effects on the NMR shielding of transition metal nuclei in multiple complexes, for which we used few approximations within few computational techniques.  In one word - a lot of information to track.

It is not easy to find a recipe for effective visualization in this case, especially if one wants to capture all important data in one figure. Here I show how [Altair](https://altair-viz.github.io/index.html) can be used to plot the contributions to the solvent effects for the whole series and two calculation methods.



#### Data description:

* *df1* and *df2* collect the data from two calculation methods (read from data1.csv and data2.csv files)
* $v_1$ and $v_{ref}$ are two boundary reference values:
    * $v_1$   - corresponds to the property value with no effect included
    * $v_{ref}$ - corresponds to the property value with a total effect included
    * therefore this effect is estimated as: $v = v_{ref} - v_1$
    
* *v2* and *v3* are approximations to *vref*. We then define:
    * $\Delta(1) = v_2 - v_1$
    * $\Delta(2) = v_3 - v_2$
    * $\Delta(3) = v_{ref} - v_3$    
    In patricular, $\Delta(3)$ can be interpreted as the portion of the effect that is not described by approximations $v_2$ and $v_3$.
    
* I use fake data in this exercise


```python
import pandas as pd
import altair as alt
```


```python
def prep_data(df,name):      
    df['name'] = name
    df_te=df[['mol','name']].copy()
    
    df['delta1']  = df['v2']-df['v1']
    df['delta2']  = df['v3']-df['v2']
    df['delta3']  = df['vref']-df['v3']
    
    df_te['total_effect'] = df['vref']-df['v1']
    
    df = df.drop(['v1','v2','v3','vref'], axis=1)
    df = df.melt(id_vars =['mol', 'name'])
    
    df_te = df_te.melt(id_vars =['mol', 'name'])
    
    return df,df_te
```


```python
df1=pd.read_csv('data1.csv')
```

Let's have a look at the dataframe:


```python
df1
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
      <th>v1</th>
      <th>v2</th>
      <th>v3</th>
      <th>vref</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Cr</td>
      <td>-2944.2912</td>
      <td>-2813.8701</td>
      <td>-2796.4468</td>
      <td>-2863.6800</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Mn</td>
      <td>-4259.8771</td>
      <td>-4221.8368</td>
      <td>-4215.8657</td>
      <td>-4279.8494</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Co</td>
      <td>-6125.6798</td>
      <td>-4967.6189</td>
      <td>-4963.6009</td>
      <td>-4993.8201</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Zn</td>
      <td>1985.4178</td>
      <td>1947.7289</td>
      <td>1946.8668</td>
      <td>1939.1123</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Mo</td>
      <td>-403.3011</td>
      <td>-335.5684</td>
      <td>-315.6369</td>
      <td>-383.6767</td>
    </tr>
    <tr>
      <th>5</th>
      <td>Tc</td>
      <td>-1264.2636</td>
      <td>-1241.5628</td>
      <td>-1228.3957</td>
      <td>-1254.4530</td>
    </tr>
    <tr>
      <th>6</th>
      <td>Ru</td>
      <td>-762.8891</td>
      <td>69.6719</td>
      <td>204.9638</td>
      <td>74.8138</td>
    </tr>
    <tr>
      <th>7</th>
      <td>Pd</td>
      <td>-1768.4043</td>
      <td>-1550.3824</td>
      <td>-1533.2350</td>
      <td>-1477.3337</td>
    </tr>
    <tr>
      <th>8</th>
      <td>Ag</td>
      <td>4589.0627</td>
      <td>4408.0483</td>
      <td>4408.4061</td>
      <td>4377.6442</td>
    </tr>
    <tr>
      <th>9</th>
      <td>W</td>
      <td>4468.8709</td>
      <td>4547.5992</td>
      <td>4567.8561</td>
      <td>4436.9428</td>
    </tr>
    <tr>
      <th>10</th>
      <td>Re</td>
      <td>3414.5834</td>
      <td>3435.8192</td>
      <td>3446.8677</td>
      <td>3390.2474</td>
    </tr>
    <tr>
      <th>11</th>
      <td>Pt</td>
      <td>2557.4652</td>
      <td>3066.9424</td>
      <td>3125.3993</td>
      <td>2884.9378</td>
    </tr>
  </tbody>
</table>
</div>




```python
df1,df1_te=prep_data(df1,'set1')
```


```python
df2=pd.read_csv('data2.csv')
```


```python
df2, df2_te=prep_data(df2,'set2')
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





<div id="altair-viz-8f59e9329c624ce2a68aa7bd5aafdd2d"></div>
<script type="text/javascript">
  (function(spec, embedOpt){
    let outputDiv = document.currentScript.previousElementSibling;
    if (outputDiv.id !== "altair-viz-8f59e9329c624ce2a68aa7bd5aafdd2d") {
      outputDiv = document.getElementById("altair-viz-8f59e9329c624ce2a68aa7bd5aafdd2d");
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
  })({"config": {"view": {"continuousWidth": 400, "continuousHeight": 300, "strokeOpacity": 0}}, "data": {"name": "data-146825b39538986d6890b01e8453e687"}, "facet": {"column": {"type": "nominal", "field": "mol", "header": {"labelAlign": "center", "labelAnchor": "middle", "labelBaseline": "line-top", "labelFontSize": 14, "orient": "bottom", "title": "Contributions to solvent shifts on NMR shieldings", "titleFontSize": 20}, "sort": ["Cr", "Mn", "Co", "Zn", "Mo", "Tc", "Ru", "Pd", "Ag", "W", "Re", "Pt"]}}, "spec": {"layer": [{"mark": {"type": "bar", "size": 15}, "encoding": {"color": {"type": "nominal", "field": "variable_x", "legend": {"direction": "horizontal", "labelFontSize": 14, "offset": -200, "orient": "right", "title": "Contributions", "titleFontSize": 16}, "scale": {"range": ["#4381d1", "#47c488", "#ff6f69"]}}, "order": {"type": "quantitative", "field": "variable_x", "sort": "ascending"}, "x": {"type": "ordinal", "axis": {"grid": true, "labelFontSize": 8}, "field": "name", "sort": ["set1", "set2"], "title": null}, "y": {"type": "quantitative", "axis": {"grid": true, "title": null}, "field": "value_x"}}}, {"mark": {"type": "tick", "color": "black", "size": 15, "thickness": 1.5}, "encoding": {"x": {"type": "ordinal", "axis": {"grid": true, "title": null}, "field": "name"}, "y": {"type": "quantitative", "axis": {"grid": true, "title": null}, "field": "value_y"}}}], "height": 450, "width": 50}, "resolve": {"scale": {"x": "independent"}}, "$schema": "https://vega.github.io/schema/vega-lite/v4.8.1.json", "datasets": {"data-146825b39538986d6890b01e8453e687": [{"mol": "Cr", "name": "set1", "variable_x": "\u0394(1)", "value_x": 130.42110000000002, "variable_y": "total_effect", "value_y": 80.61120000000028}, {"mol": "Cr", "name": "set1", "variable_x": "\u0394(2)", "value_x": 17.423299999999927, "variable_y": "total_effect", "value_y": 80.61120000000028}, {"mol": "Cr", "name": "set1", "variable_x": "\u0394(3)", "value_x": -67.23319999999967, "variable_y": "total_effect", "value_y": 80.61120000000028}, {"mol": "Mn", "name": "set1", "variable_x": "\u0394(1)", "value_x": 38.04029999999966, "variable_y": "total_effect", "value_y": -19.97230000000036}, {"mol": "Mn", "name": "set1", "variable_x": "\u0394(2)", "value_x": 5.971099999999751, "variable_y": "total_effect", "value_y": -19.97230000000036}, {"mol": "Mn", "name": "set1", "variable_x": "\u0394(3)", "value_x": -63.98369999999977, "variable_y": "total_effect", "value_y": -19.97230000000036}, {"mol": "Co", "name": "set1", "variable_x": "\u0394(1)", "value_x": 1158.0608999999995, "variable_y": "total_effect", "value_y": 1131.8597}, {"mol": "Co", "name": "set1", "variable_x": "\u0394(2)", "value_x": 4.018000000000029, "variable_y": "total_effect", "value_y": 1131.8597}, {"mol": "Co", "name": "set1", "variable_x": "\u0394(3)", "value_x": -30.219199999999546, "variable_y": "total_effect", "value_y": 1131.8597}, {"mol": "Zn", "name": "set1", "variable_x": "\u0394(1)", "value_x": -37.688899999999876, "variable_y": "total_effect", "value_y": -46.30549999999994}, {"mol": "Zn", "name": "set1", "variable_x": "\u0394(2)", "value_x": -0.8621000000000549, "variable_y": "total_effect", "value_y": -46.30549999999994}, {"mol": "Zn", "name": "set1", "variable_x": "\u0394(3)", "value_x": -7.754500000000007, "variable_y": "total_effect", "value_y": -46.30549999999994}, {"mol": "Mo", "name": "set1", "variable_x": "\u0394(1)", "value_x": 67.73270000000002, "variable_y": "total_effect", "value_y": 19.624400000000037}, {"mol": "Mo", "name": "set1", "variable_x": "\u0394(2)", "value_x": 19.93149999999997, "variable_y": "total_effect", "value_y": 19.624400000000037}, {"mol": "Mo", "name": "set1", "variable_x": "\u0394(3)", "value_x": -68.03979999999996, "variable_y": "total_effect", "value_y": 19.624400000000037}, {"mol": "Tc", "name": "set1", "variable_x": "\u0394(1)", "value_x": 22.700800000000072, "variable_y": "total_effect", "value_y": 9.810600000000022}, {"mol": "Tc", "name": "set1", "variable_x": "\u0394(2)", "value_x": 13.167099999999891, "variable_y": "total_effect", "value_y": 9.810600000000022}, {"mol": "Tc", "name": "set1", "variable_x": "\u0394(3)", "value_x": -26.05729999999994, "variable_y": "total_effect", "value_y": 9.810600000000022}, {"mol": "Ru", "name": "set1", "variable_x": "\u0394(1)", "value_x": 832.5609999999999, "variable_y": "total_effect", "value_y": 837.7029}, {"mol": "Ru", "name": "set1", "variable_x": "\u0394(2)", "value_x": 135.2919, "variable_y": "total_effect", "value_y": 837.7029}, {"mol": "Ru", "name": "set1", "variable_x": "\u0394(3)", "value_x": -130.14999999999998, "variable_y": "total_effect", "value_y": 837.7029}, {"mol": "Pd", "name": "set1", "variable_x": "\u0394(1)", "value_x": 218.02189999999996, "variable_y": "total_effect", "value_y": 291.0706}, {"mol": "Pd", "name": "set1", "variable_x": "\u0394(2)", "value_x": 17.14740000000006, "variable_y": "total_effect", "value_y": 291.0706}, {"mol": "Pd", "name": "set1", "variable_x": "\u0394(3)", "value_x": 55.90129999999999, "variable_y": "total_effect", "value_y": 291.0706}, {"mol": "Ag", "name": "set1", "variable_x": "\u0394(1)", "value_x": -181.01440000000002, "variable_y": "total_effect", "value_y": -211.41850000000068}, {"mol": "Ag", "name": "set1", "variable_x": "\u0394(2)", "value_x": 0.3577999999997701, "variable_y": "total_effect", "value_y": -211.41850000000068}, {"mol": "Ag", "name": "set1", "variable_x": "\u0394(3)", "value_x": -30.761900000000423, "variable_y": "total_effect", "value_y": -211.41850000000068}, {"mol": "W", "name": "set1", "variable_x": "\u0394(1)", "value_x": 78.72829999999976, "variable_y": "total_effect", "value_y": -31.928100000000086}, {"mol": "W", "name": "set1", "variable_x": "\u0394(2)", "value_x": 20.256900000000314, "variable_y": "total_effect", "value_y": -31.928100000000086}, {"mol": "W", "name": "set1", "variable_x": "\u0394(3)", "value_x": -130.91330000000016, "variable_y": "total_effect", "value_y": -31.928100000000086}, {"mol": "Re", "name": "set1", "variable_x": "\u0394(1)", "value_x": 21.235799999999927, "variable_y": "total_effect", "value_y": -24.335999999999785}, {"mol": "Re", "name": "set1", "variable_x": "\u0394(2)", "value_x": 11.048499999999876, "variable_y": "total_effect", "value_y": -24.335999999999785}, {"mol": "Re", "name": "set1", "variable_x": "\u0394(3)", "value_x": -56.62029999999959, "variable_y": "total_effect", "value_y": -24.335999999999785}, {"mol": "Pt", "name": "set1", "variable_x": "\u0394(1)", "value_x": 509.4771999999998, "variable_y": "total_effect", "value_y": 327.47260000000006}, {"mol": "Pt", "name": "set1", "variable_x": "\u0394(2)", "value_x": 58.45690000000013, "variable_y": "total_effect", "value_y": 327.47260000000006}, {"mol": "Pt", "name": "set1", "variable_x": "\u0394(3)", "value_x": -240.4614999999999, "variable_y": "total_effect", "value_y": 327.47260000000006}, {"mol": "Cr", "name": "set2", "variable_x": "\u0394(1)", "value_x": -173.7458999999999, "variable_y": "total_effect", "value_y": 71.29509999999982}, {"mol": "Cr", "name": "set2", "variable_x": "\u0394(2)", "value_x": 293.4998999999998, "variable_y": "total_effect", "value_y": 71.29509999999982}, {"mol": "Cr", "name": "set2", "variable_x": "\u0394(3)", "value_x": -48.458900000000085, "variable_y": "total_effect", "value_y": 71.29509999999982}, {"mol": "Mn", "name": "set2", "variable_x": "\u0394(1)", "value_x": -389.3710000000001, "variable_y": "total_effect", "value_y": -10.177499999999782}, {"mol": "Mn", "name": "set2", "variable_x": "\u0394(2)", "value_x": 425.02030000000013, "variable_y": "total_effect", "value_y": -10.177499999999782}, {"mol": "Mn", "name": "set2", "variable_x": "\u0394(3)", "value_x": -45.82679999999982, "variable_y": "total_effect", "value_y": -10.177499999999782}, {"mol": "Co", "name": "set2", "variable_x": "\u0394(1)", "value_x": 443.2673999999997, "variable_y": "total_effect", "value_y": 922.8062999999997}, {"mol": "Co", "name": "set2", "variable_x": "\u0394(2)", "value_x": 498.0164999999997, "variable_y": "total_effect", "value_y": 922.8062999999997}, {"mol": "Co", "name": "set2", "variable_x": "\u0394(3)", "value_x": -18.47759999999971, "variable_y": "total_effect", "value_y": 922.8062999999997}, {"mol": "Zn", "name": "set2", "variable_x": "\u0394(1)", "value_x": 166.24489999999992, "variable_y": "total_effect", "value_y": -31.50739999999996}, {"mol": "Zn", "name": "set2", "variable_x": "\u0394(2)", "value_x": -197.47119999999995, "variable_y": "total_effect", "value_y": -31.50739999999996}, {"mol": "Zn", "name": "set2", "variable_x": "\u0394(3)", "value_x": -0.28109999999992397, "variable_y": "total_effect", "value_y": -31.50739999999996}, {"mol": "Mo", "name": "set2", "variable_x": "\u0394(1)", "value_x": 23.306699999999978, "variable_y": "total_effect", "value_y": 21.89580000000001}, {"mol": "Mo", "name": "set2", "variable_x": "\u0394(2)", "value_x": 47.7013, "variable_y": "total_effect", "value_y": 21.89580000000001}, {"mol": "Mo", "name": "set2", "variable_x": "\u0394(3)", "value_x": -49.11219999999997, "variable_y": "total_effect", "value_y": 21.89580000000001}, {"mol": "Tc", "name": "set2", "variable_x": "\u0394(1)", "value_x": -103.7686000000001, "variable_y": "total_effect", "value_y": 13.94659999999999}, {"mol": "Tc", "name": "set2", "variable_x": "\u0394(2)", "value_x": 132.8216000000001, "variable_y": "total_effect", "value_y": 13.94659999999999}, {"mol": "Tc", "name": "set2", "variable_x": "\u0394(3)", "value_x": -15.106400000000008, "variable_y": "total_effect", "value_y": 13.94659999999999}, {"mol": "Ru", "name": "set2", "variable_x": "\u0394(1)", "value_x": 683.3416, "variable_y": "total_effect", "value_y": 684.5394}, {"mol": "Ru", "name": "set2", "variable_x": "\u0394(2)", "value_x": 100.61930000000001, "variable_y": "total_effect", "value_y": 684.5394}, {"mol": "Ru", "name": "set2", "variable_x": "\u0394(3)", "value_x": -99.42150000000001, "variable_y": "total_effect", "value_y": 684.5394}, {"mol": "Pd", "name": "set2", "variable_x": "\u0394(1)", "value_x": 23.55950000000007, "variable_y": "total_effect", "value_y": 241.7672}, {"mol": "Pd", "name": "set2", "variable_x": "\u0394(2)", "value_x": 166.92759999999998, "variable_y": "total_effect", "value_y": 241.7672}, {"mol": "Pd", "name": "set2", "variable_x": "\u0394(3)", "value_x": 51.28009999999995, "variable_y": "total_effect", "value_y": 241.7672}, {"mol": "Ag", "name": "set2", "variable_x": "\u0394(1)", "value_x": 296.18319999999994, "variable_y": "total_effect", "value_y": -165.24900000000025}, {"mol": "Ag", "name": "set2", "variable_x": "\u0394(2)", "value_x": -442.5151000000001, "variable_y": "total_effect", "value_y": -165.24900000000025}, {"mol": "Ag", "name": "set2", "variable_x": "\u0394(3)", "value_x": -18.91710000000012, "variable_y": "total_effect", "value_y": -165.24900000000025}, {"mol": "W", "name": "set2", "variable_x": "\u0394(1)", "value_x": 520.5299, "variable_y": "total_effect", "value_y": -19.861700000000383}, {"mol": "W", "name": "set2", "variable_x": "\u0394(2)", "value_x": -440.3519000000001, "variable_y": "total_effect", "value_y": -19.861700000000383}, {"mol": "W", "name": "set2", "variable_x": "\u0394(3)", "value_x": -100.03970000000027, "variable_y": "total_effect", "value_y": -19.861700000000383}, {"mol": "Re", "name": "set2", "variable_x": "\u0394(1)", "value_x": 362.7828999999997, "variable_y": "total_effect", "value_y": -13.712200000000394}, {"mol": "Re", "name": "set2", "variable_x": "\u0394(2)", "value_x": -336.6327000000001, "variable_y": "total_effect", "value_y": -13.712200000000394}, {"mol": "Re", "name": "set2", "variable_x": "\u0394(3)", "value_x": -39.86239999999998, "variable_y": "total_effect", "value_y": -13.712200000000394}, {"mol": "Pt", "name": "set2", "variable_x": "\u0394(1)", "value_x": 721.3708000000001, "variable_y": "total_effect", "value_y": 271.25279999999975}, {"mol": "Pt", "name": "set2", "variable_x": "\u0394(2)", "value_x": -261.3442, "variable_y": "total_effect", "value_y": 271.25279999999975}, {"mol": "Pt", "name": "set2", "variable_x": "\u0394(3)", "value_x": -188.7738000000004, "variable_y": "total_effect", "value_y": 271.25279999999975}]}}, {"mode": "vega-lite"});
</script>



#### Final note

The total effect is additionally marked by horizontal black lines.
It would be better to add these horizontal black lines to the legend, but from what I saw it is not straightfoward to do at this point.

    
#### Thanks:

Scripts used in this notebook are a combination of advice found (mostly) on stackoverlow, which I lost track of... So big thanks to all Altair experts out there!
