### What & why:

The data describes the solvent effects on the NMR shielding constant of transition metal nuclei ($\sigma$) in multiple complexes, that were calculated with few models:

* in full solvated complexes ($\sigma_{super}$)
* in solute molecules with the solvent effects estimated with two variants of the Frozen Density Embedding (FDE) method:

    * canonical FDE with the density of the solvent kept fixed ($\sigma_{fde0}$)
    * FDE with the freeze-thaw procedure, in which the densities of the solute and solvent are relaxed in each other presence ($\sigma_{fdeN}$)
    
* isolated solute molecules in their relaxed geometries - as if they were parts of a complex ($\sigma_{isol}$)
* isolated solute molecules in their vacuum geometry ($\sigma_{vac}$)

$\sigma_{super}$ is the most accurate result within the quantum chemistry model used, while $\sigma_{vac}$ is the reference value (no solvent effects). $\sigma_{isol}$, $\sigma_{fde0}$, $\sigma_{fdeN}$ are approximations and should approach the $\sigma_{super}$ value.



#### Data description:

* *df_dirac* and *df_adf* are dataframes that collect the data obtained from calculations with DIRAC (with the DC Hamiltonian) and ADF (with the SO-ZORA Hamiltonian). The data is read from from dirac_data.csv and adf_data.csv files.

* some definitions used in this notebook:

    * $\delta$: useful to discuss solvent effects estimated by a given model:
        * $\delta1 \equiv \delta_{isol} = \sigma_{isol} - \sigma_{vac}$
        * $\delta2 \equiv \delta_{fde0} = \sigma_{fde0} - \sigma_{vac}$
        * $\delta3 \equiv \delta_{fdeN} = \sigma_{fdeN} - \sigma_{vac}$
        * $\delta4 \equiv \delta_{super} = \sigma_{super} - \sigma_{vac}$     
    
    * $\Delta$: useful to discuss contributions to solvent effects:    
        * $\Delta1 \equiv\Delta_{isol} = \sigma_{isol} - \sigma_{vac} = \delta_{isol}$
        * $\Delta2 \equiv\Delta_{fde0} = \sigma_{fde0} - \sigma_{isol}$
        * $\Delta3 \equiv\Delta_{fdeN} = \sigma_{fdeN} - \sigma_{fde0}$
        * $\Delta4 \equiv \Delta_{super} = \sigma_{super} - \sigma_{fdeN}$       
   


```python
import pandas as pd
import altair as alt
import altair_viewer
```


```python
df1=pd.read_csv('dirac_data.csv')
df2=pd.read_csv('adf_data.csv')
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
    solvent['Ti']='12TiCl₄'
    solvent['V']='6C₆H₆'
    solvent['Cr']='12H₂O'
    solvent['Mn']='12H₂O'
    solvent['Fe']='6C₆H₆'
    solvent['Co']='12H₂O'
    solvent['Ni']='6C₆H₆'
    solvent['Cu']='12CH₃CN'
    solvent['Zn']='12H₂O'
    solvent['Nb']='12CH₃CN'
    solvent['Mo']='12H₂O'
    solvent['Tc']='12H₂O'
    solvent['Ru']='12H₂O'
    solvent['Pd']='12H₂O'
    solvent['Ag']='12H₂O'
    solvent['Cd']='6Cd(CH₃)₂'
    solvent['Ta']='12CH₃CN'
    solvent['W']='12H₂O'
    solvent['Re']='12H₂O'
    solvent['Os']='12CCl₄'
    solvent['Pt']='12H₂O'
    solvent['Hg']='6Hg(CH₃)₂'
    
    cplx={} 
    cplx['Ti']='TiCl₄ + 12TiCl₄'
    cplx['V']='VOCl₃ + 6C₆H₆'
    cplx['Cr']='CrO₄²⁻ + 12H₂O'
    cplx['Mn']='MnO₄⁻ + 12H₂O'
    cplx['Fe']='Fe(CO)₅ + 6C₆H₆'
    cplx['Co']='Co(CN)₆³⁻ + 12H₂O'
    cplx['Ni']='Ni(CO)₄ + 6C₆H₆'
    cplx['Cu']='Cu(CH₃CN)₄⁺ + 12CH₃CN'
    cplx['Zn']='Zn(H₂O)₆²⁺ + 12H₂O'
    cplx['Nb']='NbCl₆⁻ + 12CH₃CN'
    cplx['Mo']='MoO₄²⁻ + 12H₂O'
    cplx['Tc']='TcO₄⁻ + 12H₂O'
    cplx['Ru']='Ru(CN)₆⁴⁻ + 12H₂O'
    cplx['Pd']='PdCl₆²⁻ + 12H₂O'
    cplx['Ag']='Ag(H₂O)₄⁺ + 12H₂O'
    cplx['Cd']='Cd(CH₃)₂ + 6Cd(CH₃)₂'
    cplx['Ta']='TaCl₆⁻ + 12CH₃CN'
    cplx['W']='WO₄²⁻ + 12H₂O'
    cplx['Re']='ReO₄⁻ + 12H₂O'
    cplx['Os']='OsO₄ + 12CCl₄'
    cplx['Pt']='PtCl₆²⁻ + 12H₂O'
    cplx['Hg']='Hg(CH₃)₂ + 6Hg(CH₃)₂'    

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
    return atomic_number_X, cplx, solvent, charge, row


def prep_adf(df):
    
    an,cplx,solvent,charge,row=def_molprop()
   
    df_se = df[['mol']].copy()
    df_se['x_labels'] = df_se.mol
    df_se['where'] = 'SO-ZORA'
    df_se['solveff']  = df['supermolecule']-df['isolated_vac']
    df_se['row'] = df_se['mol'].map(row)
    df_se['charge'] = df_se['mol'].map(charge)
    df_se['solvent'] = df_se['mol'].map(solvent)
    df_se['complex'] = df_se['mol'].map(cplx)
    
    df_delta = df[['mol']].copy()
    df_delta['delta1']  = df['isolated_supergeom']-df['isolated_vac']
    df_delta['delta2']  = df['fde']-df['isolated_vac']
    df_delta['delta3']  = df['fnt']-df['isolated_vac']    
    df_delta['delta4']  = df['supermolecule']-df['isolated_vac']     
    df_delta=df_delta.drop(['mol'], axis=1)
    
    df_Delta = df[['mol']].copy()
    df_Delta['Delta1']  = df['isolated_supergeom']-df['isolated_vac']
    df_Delta['Delta2']  = df['fde']-df['isolated_supergeom']
    df_Delta['Delta3']  = df['fnt']-df['fde']    
    df_Delta['Delta4']  = df['supermolecule']-df['fnt']   
    df_Delta=df_Delta.drop(['mol'], axis=1)

    df_d = df[['mol']].copy() 
    df_d['d1']      = (df['isolated_supergeom']-df['supermolecule'])/df['isolated_vac']
    df_d['d2']      = (df['fde']-df['supermolecule'])/df['isolated_vac']
    df_d['d3']      = (df['fnt']-df['supermolecule'])/df['isolated_vac']
    df_d['d4']      = (df['supermolecule']-df['supermolecule'])/df['isolated_vac']
    df_d=df_d.drop(['mol'], axis=1)
    
    df_all = pd.concat([df_se, df_delta, df_Delta, df_d],axis=1,sort=False)
    
    return df_all, df_se, df_delta, df_Delta, df_d


def prep_dirac(df):
    
    an,cplx,solvent,charge,row=def_molprop()
 
    df_se = df[['mol']].copy()
    df_se['x_labels'] = df_se.mol
    df_se['where'] = 'DC'
    df_se['solveff']  = df['supermolecule']-df['isolated_vac']
    df_se['row'] = df_se['mol'].map(row)
    df_se['charge'] = df_se['mol'].map(charge)
    df_se['solvent'] = df_se['mol'].map(solvent)
    df_se['complex'] = df_se['mol'].map(cplx)

    df_delta = df[['mol']].copy()
    df_delta['delta1']  = df['isolated_supergeom_supergrid']-df['isolated_vac']
    df_delta['delta2']  = df['fde_vw11']-df['isolated_vac']
    df_delta['delta3']  = df['fnt_vw11']-df['isolated_vac'] 
    df_delta['delta4']  = df['supermolecule']-df['isolated_vac']     
    df_delta=df_delta.drop(['mol'], axis=1)
    
    df_Delta = df[['mol']].copy()
    df_Delta['Delta1']  = df['isolated_supergeom_supergrid']-df['isolated_vac']
    df_Delta['Delta2']  = df['fde_vw11']-df['isolated_supergeom_supergrid']
    df_Delta['Delta3']  = df['fnt_vw11']-df['fde_vw11']
    df_Delta['Delta4']  = df['supermolecule']-df['fnt_vw11'] 
    df_Delta=df_Delta.drop(['mol'], axis=1)

    df_d = df[['mol']].copy() 
    df_d['d1']      = (df['isolated_supergeom_supergrid']-df['supermolecule'])/df['isolated_vac']
    df_d['d2']      = (df['fde_vw11']-df['supermolecule'])/df['isolated_vac']
    df_d['d3']      = (df['fnt_vw11']-df['supermolecule'])/df['isolated_vac']
    df_d['d4']      = (df['supermolecule']-df['supermolecule'])/df['isolated_vac']
    df_d=df_d.drop(['mol'], axis=1)
    
    df_all = pd.concat([df_se, df_delta, df_Delta, df_d],axis=1,sort=False)
    
    return df_all, df_se, df_delta, df_Delta, df_d
```


```python

df_adf_all, df_adf_se, df_adf_delta, df_adf_Delta, df_adf_d = prep_adf(df2)
```


```python
df_adf_all_melted=df_adf_all.melt(id_vars =['mol','x_labels', 'where', 'row', 'charge', 'solvent', 'complex'])
df_adf_all_melted_delta=df_adf_all.melt(id_vars =['mol','x_labels', 'where', 'row', 'charge', 'solvent', 'complex',
                                                 'Delta1','Delta2', 'Delta3', 'Delta4',
                                                 'd1', 'd2', 'd3', 'd4',
                                                 'solveff'])
```


```python

df_dirac_all, df_dirac_se, df_dirac_delta, df_dirac_Delta, df_dirac_d = prep_dirac(df1)
```


```python
df_dirac_all_melted=df_dirac_all.melt(id_vars =['mol','x_labels', 'where', 'row', 'charge', 'solvent', 'complex'])
df_dirac_all_melted_delta=df_dirac_all.melt(id_vars =['mol','x_labels', 'where', 'row', 'charge', 'solvent', 'complex',
                                                 'Delta1','Delta2', 'Delta3', 'Delta4',
                                                 'd1', 'd2', 'd3', 'd4',
                                                 'solveff'])
```

## Total solvent effects:


We measure solvent effects at a given approximation by $\delta$. If we want to focus on one approximation, for instance on $\delta_{super}$ (the total solvent effects), we can first show the dependence of $\delta_{super}$ on the row of the periodic table to which the nucleus of interest belongs, and on the solvent used. 

Let's try to apply some of the interactive Altair's hover methods ([full gallery](https://altair-viz.github.io/gallery/#interactive-charts)) to the results from ADF.


```python
def plot_chart(df, xData, yData, colorData, textData, yDataTitle):
    
    hover = alt.selection_single(on='mouseover', nearest=True, empty='none')

    bckcolor='lightgray'
    
    base = alt.Chart(df).encode(
        x=alt.X(xData,
                axis=alt.Axis(title="X",labelAngle=0)
                ),
        y=alt.Y(yData,
                axis=alt.Axis(title=yDataTitle, 
                              format=",.0f")
                ),
        color=alt.condition(hover,
                            colorData,
                            alt.value(bckcolor)
                           )
        ).properties(width=230,
                     height=190
                    )
    
    points = base.mark_point().add_selection(hover)

    text = base.mark_text(dy=-5).encode(text = textData,
                                        opacity = alt.condition(hover,
                                                                alt.value(1),
                                                                alt.value(0)
                                                                )
                                        )
    
    return points, text


def def_layer(points, text, xIndp, yIndp, plotSpacing, plotTitle):
    
    selected=alt.selection_multi(fields=['row'], bind='legend')
    
    layer=alt.layer(points, text).facet(facet=alt.Facet('row:N', title='row(X)'),
                                        spacing=plotSpacing,
                                        title=plotTitle
                                        )
    
    layer=layer.add_selection(selected).transform_filter(selected)
    
    if xIndp and yIndp:
        layer=layer.resolve_scale(x='independent', y='independent')
    elif xIndp:
        layer=layer.resolve_scale(x='independent')
    elif yIndp:
        layer=layer.resolve_scale(y='independent')
    
    return layer
```

In the plot below: we show the total solvent effects for all complexes; when we hover over the point, the color of that point indicates the solvent.


```python
delta_points, delta_text=plot_chart(df_adf_all, 
                                    'mol:O', 
                                    'solveff', 
                                    'solvent:N', 
                                    'complex:N', 
                                    "δ(super) [ppm]")
                                          
layer=def_layer(delta_points, 
                delta_text,
                True, False,
                -30,
                'Solvent effects on σ(X) [ppm]')

layer
```





<div id="altair-viz-d25ca4e825b2478a852fc716658cba9c"></div>
<script type="text/javascript">
  (function(spec, embedOpt){
    let outputDiv = document.currentScript.previousElementSibling;
    if (outputDiv.id !== "altair-viz-d25ca4e825b2478a852fc716658cba9c") {
      outputDiv = document.getElementById("altair-viz-d25ca4e825b2478a852fc716658cba9c");
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
  })({"config": {"view": {"continuousWidth": 400, "continuousHeight": 300}}, "data": {"name": "data-82ba65b3d3f73062f9b09b91098cd11b"}, "facet": {"type": "nominal", "field": "row", "title": "row(X)"}, "spec": {"layer": [{"mark": "point", "encoding": {"color": {"condition": {"type": "nominal", "field": "solvent", "selection": "selector001"}, "value": "lightgray"}, "x": {"type": "ordinal", "axis": {"labelAngle": 0, "title": "X"}, "field": "mol"}, "y": {"type": "quantitative", "axis": {"format": ",.0f", "title": "\u03b4(super) [ppm]"}, "field": "solveff"}}, "height": 190, "selection": {"selector001": {"type": "single", "on": "mouseover", "nearest": true, "empty": "none"}, "selector002": {"type": "multi", "fields": ["row"], "bind": "legend"}}, "width": 230}, {"mark": {"type": "text", "dy": -5}, "encoding": {"color": {"condition": {"type": "nominal", "field": "solvent", "selection": "selector001"}, "value": "lightgray"}, "opacity": {"condition": {"value": 1, "selection": "selector001"}, "value": 0}, "text": {"type": "nominal", "field": "complex"}, "x": {"type": "ordinal", "axis": {"labelAngle": 0, "title": "X"}, "field": "mol"}, "y": {"type": "quantitative", "axis": {"format": ",.0f", "title": "\u03b4(super) [ppm]"}, "field": "solveff"}}, "height": 190, "width": 230}]}, "resolve": {"scale": {"x": "independent"}}, "spacing": -30, "title": "Solvent effects on \u03c3(X) [ppm]", "transform": [{"filter": {"selection": "selector002"}}], "$schema": "https://vega.github.io/schema/vega-lite/v4.8.1.json", "datasets": {"data-82ba65b3d3f73062f9b09b91098cd11b": [{"mol": "Ti", "x_labels": "Ti", "where": "SO-ZORA", "solveff": 4.300000000000068, "row": 4, "charge": 0, "solvent": "12TiCl\u2084", "complex": "TiCl\u2084 + 12TiCl\u2084", "delta1": 22.184000000000083, "delta2": 21.684000000000083, "delta3": 21.854000000000042, "delta4": 4.300000000000068, "Delta1": 22.184000000000083, "Delta2": -0.5, "Delta3": 0.16999999999995907, "Delta4": -17.553999999999974, "d1": -0.021763098138381388, "d2": -0.021154646501768178, "d3": -0.02136152005821662, "d4": -0.0}, {"mol": "V", "x_labels": "V", "where": "SO-ZORA", "solveff": -25.33199999999988, "row": 4, "charge": 0, "solvent": "6C\u2086H\u2086", "complex": "VOCl\u2083 + 6C\u2086H\u2086", "delta1": 2.814000000000078, "delta2": 1.0170000000000528, "delta3": -0.10500000000001819, "delta4": -25.33199999999988, "Delta1": 2.814000000000078, "Delta2": -1.7970000000000255, "Delta3": -1.122000000000071, "Delta4": -25.22699999999986, "d1": -0.015131346929647224, "d2": -0.014165276069397935, "d3": -0.013562086584033574, "d4": -0.0}, {"mol": "Cr", "x_labels": "Cr", "where": "SO-ZORA", "solveff": 63.858000000000175, "row": 4, "charge": -2, "solvent": "12H\u2082O", "complex": "CrO\u2084\u00b2\u207b + 12H\u2082O", "delta1": 123.04400000000032, "delta2": 104.03900000000021, "delta3": 76.654, "delta4": 63.858000000000175, "Delta1": 123.04400000000032, "Delta2": -19.00500000000011, "Delta3": -27.38500000000022, "Delta4": -12.795999999999822, "d1": -0.021712884105380876, "d2": -0.014740739300481661, "d3": -0.004694320701051754, "d4": -0.0}, {"mol": "Mn", "x_labels": "Mn", "where": "SO-ZORA", "solveff": -7.241999999999734, "row": 4, "charge": -1, "solvent": "12H\u2082O", "complex": "MnO\u2084\u207b + 12H\u2082O", "delta1": 35.49200000000019, "delta2": 29.141000000000076, "delta3": 15.231000000000222, "delta4": -7.241999999999734, "Delta1": 35.49200000000019, "Delta2": -6.351000000000113, "Delta3": -13.909999999999854, "Delta4": -22.472999999999956, "d1": -0.010860140180028139, "d2": -0.009246138441755099, "d3": -0.005711141720077042, "d4": -0.0}, {"mol": "Fe", "x_labels": "Fe", "where": "SO-ZORA", "solveff": 47.11200000000008, "row": 4, "charge": 0, "solvent": "6C\u2086H\u2086", "complex": "Fe(CO)\u2085 + 6C\u2086H\u2086", "delta1": 67.43199999999979, "delta2": 65.41499999999996, "delta3": 64.57400000000007, "delta4": 47.11200000000008, "Delta1": 67.43199999999979, "Delta2": -2.0169999999998254, "Delta3": -0.8409999999998945, "Delta4": -17.46199999999999, "d1": -0.008717314895411402, "d2": -0.007852018431629732, "d3": -0.007491227987385629, "d4": -0.0}, {"mol": "Co", "x_labels": "Co", "where": "SO-ZORA", "solveff": 1097.3739999999998, "row": 4, "charge": -3, "solvent": "12H\u2082O", "complex": "Co(CN)\u2086\u00b3\u207b + 12H\u2082O", "delta1": 1098.3239999999996, "delta2": 1127.5999999999995, "delta3": 1124.4809999999998, "delta4": 1097.3739999999998, "Delta1": 1098.3239999999996, "Delta2": 29.27599999999984, "Delta3": -3.118999999999687, "Delta4": -27.10699999999997, "d1": -0.0001645170921136079, "d2": -0.005234414343396641, "d3": -0.004694278753604651, "d4": -0.0}, {"mol": "Ni", "x_labels": "Ni", "where": "SO-ZORA", "solveff": -27.54399999999987, "row": 4, "charge": 0, "solvent": "6C\u2086H\u2086", "complex": "Ni(CO)\u2084 + 6C\u2086H\u2086", "delta1": -9.3599999999999, "delta2": -17.951999999999998, "delta3": -21.45699999999988, "delta4": -27.54399999999987, "Delta1": -9.3599999999999, "Delta2": -8.592000000000098, "Delta3": -3.5049999999998818, "Delta4": -6.086999999999989, "d1": -0.011156635879161785, "d2": -0.005885088613776869, "d3": -0.0037346261876626584, "d4": -0.0}, {"mol": "Cu", "x_labels": "Cu", "where": "SO-ZORA", "solveff": -53.464, "row": 4, "charge": 1, "solvent": "12CH\u2083CN", "complex": "Cu(CH\u2083CN)\u2084\u207a + 12CH\u2083CN", "delta1": -23.295999999999992, "delta2": -44.88799999999998, "delta3": -47.87099999999998, "delta4": -53.464, "Delta1": -23.295999999999992, "Delta2": -21.591999999999985, "Delta3": -2.983000000000004, "Delta4": -5.593000000000018, "d1": 0.06319613047294453, "d2": 0.01796506281278088, "d3": 0.01171625423412821, "d4": 0.0}, {"mol": "Zn", "x_labels": "Zn", "where": "SO-ZORA", "solveff": -43.14599999999996, "row": 4, "charge": 2, "solvent": "12H\u2082O", "complex": "Zn(H\u2082O)\u2086\u00b2\u207a + 12H\u2082O", "delta1": -29.680999999999813, "delta2": -30.291999999999916, "delta3": -30.120999999999867, "delta4": -43.14599999999996, "Delta1": -29.680999999999813, "Delta2": -0.6110000000001037, "Delta3": 0.1710000000000491, "Delta4": -13.025000000000091, "d1": 0.007034567861245903, "d2": 0.00671536095718189, "d3": 0.0068046970956351675, "d4": 0.0}, {"mol": "Nb", "x_labels": "Nb", "where": "SO-ZORA", "solveff": -19.514999999999986, "row": 5, "charge": -1, "solvent": "12CH\u2083CN", "complex": "NbCl\u2086\u207b + 12CH\u2083CN", "delta1": 15.894000000000005, "delta2": 3.2100000000000364, "delta3": 0.17900000000003047, "delta4": -19.514999999999986, "Delta1": 15.894000000000005, "Delta2": -12.683999999999969, "Delta3": -3.031000000000006, "Delta4": -19.694000000000017, "d1": -0.11137042011203403, "d2": -0.07147597495116995, "d3": -0.061942699700257016, "d4": -0.0}, {"mol": "Mo", "x_labels": "Mo", "where": "SO-ZORA", "solveff": 16.257000000000062, "row": 5, "charge": -2, "solvent": "12H\u2082O", "complex": "MoO\u2084\u00b2\u207b + 12H\u2082O", "delta1": 69.5, "delta2": 57.478999999999985, "delta3": 38.01099999999997, "delta4": 16.257000000000062, "Delta1": 69.5, "Delta2": -12.021000000000015, "Delta3": -19.468000000000018, "Delta4": -21.753999999999905, "d1": -0.09521501751650152, "d2": -0.07371773664266144, "d3": -0.03890290725642746, "d4": -0.0}, {"mol": "Tc", "x_labels": "Tc", "where": "SO-ZORA", "solveff": 26.989000000000033, "row": 5, "charge": -1, "solvent": "12H\u2082O", "complex": "TcO\u2084\u207b + 12H\u2082O", "delta1": 22.694999999999936, "delta2": 28.878999999999905, "delta3": 23.896999999999935, "delta4": 26.989000000000033, "Delta1": 22.694999999999936, "Delta2": 6.183999999999969, "Delta3": -4.981999999999971, "Delta4": 3.0920000000000982, "d1": 0.003025801759954068, "d2": -0.0013318037555455694, "d3": 0.002178802757749899, "d4": -0.0}, {"mol": "Ru", "x_labels": "Ru", "where": "SO-ZORA", "solveff": 787.761, "row": 5, "charge": -4, "solvent": "12H\u2082O", "complex": "Ru(CN)\u2086\u2074\u207b + 12H\u2082O", "delta1": 828.1270000000001, "delta2": 831.764, "delta3": 834.176, "delta4": 787.761, "Delta1": 828.1270000000001, "Delta2": 3.6370000000000005, "Delta3": 2.411999999999999, "Delta4": -46.415, "d1": -0.04974484294377672, "d2": -0.05422688212988671, "d3": -0.057199298549160095, "d4": -0.0}, {"mol": "Pd", "x_labels": "Pd", "where": "SO-ZORA", "solveff": 270.96900000000005, "row": 5, "charge": -2, "solvent": "12H\u2082O", "complex": "PdCl\u2086\u00b2\u207b + 12H\u2082O", "delta1": 202.9269999999999, "delta2": 238.596, "delta3": 270.4839999999999, "delta4": 270.96900000000005, "Delta1": 202.9269999999999, "Delta2": 35.669000000000096, "Delta3": 31.88799999999992, "Delta4": 0.48500000000012733, "d1": 0.04304883596306927, "d2": 0.02048176077470446, "d3": 0.0003068499668159965, "d4": -0.0}, {"mol": "Ag", "x_labels": "Ag", "where": "SO-ZORA", "solveff": -210.16700000000037, "row": 5, "charge": 1, "solvent": "12H\u2082O", "complex": "Ag(H\u2082O)\u2084\u207a + 12H\u2082O", "delta1": -175.03999999999996, "delta2": -174.26800000000003, "delta3": -173.433, "delta4": -210.16700000000037, "Delta1": -175.03999999999996, "Delta2": 0.7719999999999345, "Delta3": 0.8350000000000364, "Delta4": -36.73400000000038, "d1": 0.008140764518531128, "d2": 0.008319677326579223, "d3": 0.008513190532175308, "d4": 0.0}, {"mol": "Cd", "x_labels": "Cd", "where": "SO-ZORA", "solveff": 54.89800000000014, "row": 5, "charge": 0, "solvent": "6Cd(CH\u2083)\u2082", "complex": "Cd(CH\u2083)\u2082 + 6Cd(CH\u2083)\u2082", "delta1": 6.981999999999971, "delta2": 22.7170000000001, "delta3": 46.817999999999756, "delta4": 54.89800000000014, "Delta1": 6.981999999999971, "Delta2": 15.735000000000127, "Delta3": 24.100999999999658, "Delta4": 8.080000000000382, "d1": -0.013863777019912952, "d2": -0.009311090414012391, "d3": -0.0023378269955944063, "d4": 0.0}, {"mol": "Ta", "x_labels": "Ta", "where": "SO-ZORA", "solveff": 30.634999999999764, "row": 6, "charge": -1, "solvent": "12CH\u2083CN", "complex": "TaCl\u2086\u207b + 12CH\u2083CN", "delta1": 11.592999999999847, "delta2": -6.84900000000016, "delta3": -11.596000000000004, "delta4": 30.634999999999764, "Delta1": 11.592999999999847, "Delta2": -18.442000000000007, "Delta3": -4.746999999999844, "Delta4": 42.23099999999977, "d1": -0.0055832686076456115, "d2": -0.010990612356317014, "d3": -0.012382471198901457, "d4": 0.0}, {"mol": "W", "x_labels": "W", "where": "SO-ZORA", "solveff": -74.40099999999984, "row": 6, "charge": -2, "solvent": "12H\u2082O", "complex": "WO\u2084\u00b2\u207b + 12H\u2082O", "delta1": 85.21000000000004, "delta2": 52.39400000000023, "delta3": 25.09900000000016, "delta4": -74.40099999999984, "Delta1": 85.21000000000004, "Delta2": -32.8159999999998, "Delta3": -27.295000000000073, "Delta4": -99.5, "d1": 0.05316830974068907, "d2": 0.04223691245321864, "d3": 0.03314462549071534, "d4": 0.0}, {"mol": "Re", "x_labels": "Re", "where": "SO-ZORA", "solveff": -52.822999999999865, "row": 6, "charge": -1, "solvent": "12H\u2082O", "complex": "ReO\u2084\u207b + 12H\u2082O", "delta1": 21.605000000000018, "delta2": 11.55600000000004, "delta3": 2.73700000000008, "delta4": -52.822999999999865, "Delta1": 21.605000000000018, "Delta2": -10.048999999999978, "Delta3": -8.81899999999996, "Delta4": -55.559999999999945, "d1": 0.03932442385502771, "d2": 0.03401498204120532, "d3": 0.029355417173447373, "d4": 0.0}, {"mol": "Os", "x_labels": "Os", "where": "SO-ZORA", "solveff": 121.17599999999993, "row": 6, "charge": 0, "solvent": "12CCl\u2084", "complex": "OsO\u2084 + 12CCl\u2084", "delta1": 132.73399999999992, "delta2": 135.55999999999995, "delta3": 137.587, "delta4": 121.17599999999993, "Delta1": 132.73399999999992, "Delta2": 2.826000000000022, "Delta3": 2.0270000000000437, "Delta4": -16.411000000000058, "d1": 0.009486133567518316, "d2": 0.011805549855959828, "d3": 0.013469193457046525, "d4": 0.0}, {"mol": "Pt", "x_labels": "Pt", "where": "SO-ZORA", "solveff": 498.48800000000006, "row": 6, "charge": -2, "solvent": "12H\u2082O", "complex": "PtCl\u2086\u00b2\u207b + 12H\u2082O", "delta1": 292.2940000000001, "delta2": 371.1559999999997, "delta3": 428.05899999999997, "delta4": 498.48800000000006, "Delta1": 292.2940000000001, "Delta2": 78.86199999999963, "Delta3": 56.90300000000025, "Delta4": 70.42900000000009, "d1": -0.10937188371234462, "d2": -0.06754095995451033, "d3": -0.0373577911965272, "d4": 0.0}, {"mol": "Hg", "x_labels": "Hg", "where": "SO-ZORA", "solveff": 40.07600000000093, "row": 6, "charge": 0, "solvent": "6Hg(CH\u2083)\u2082", "complex": "Hg(CH\u2083)\u2082 + 6Hg(CH\u2083)\u2082", "delta1": 31.950000000000728, "delta2": 67.19500000000153, "delta3": 95.47100000000137, "delta4": 40.07600000000093, "Delta1": 31.950000000000728, "Delta2": 35.2450000000008, "Delta3": 28.27599999999984, "Delta4": -55.39500000000044, "d1": -0.0009148227516846377, "d2": 0.003053049249684422, "d3": 0.006236353227857449, "d4": 0.0}]}}, {"mode": "vega-lite"});
</script>



Let's then try to show the data corresponding to all introduced approximations to the solvent effects (represented by all $\delta$s).

In the plot below, when we hover over the point, the color of the point indicates the solvent, and the text indicates the approximation to which the data point refers.


```python
delta_points, delta_text=plot_chart(df_adf_all_melted_delta, 
                                    'mol:O', 
                                    'value', 
                                    'solvent:N', 
                                    'variable:N', 
                                    "δ(fdeN) [ppm]")
                                          
layer=def_layer(delta_points, 
                delta_text,
                True, False,
                10,
                'Approximations to solvent effects on σ(X) [ppm]')

layer

```





<div id="altair-viz-19c66bc8cb404bab8925548552995766"></div>
<script type="text/javascript">
  (function(spec, embedOpt){
    let outputDiv = document.currentScript.previousElementSibling;
    if (outputDiv.id !== "altair-viz-19c66bc8cb404bab8925548552995766") {
      outputDiv = document.getElementById("altair-viz-19c66bc8cb404bab8925548552995766");
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
  })({"config": {"view": {"continuousWidth": 400, "continuousHeight": 300}}, "data": {"name": "data-e2958ed67ee61d3fe3755e8257c7988b"}, "facet": {"type": "nominal", "field": "row", "title": "row(X)"}, "spec": {"layer": [{"mark": "point", "encoding": {"color": {"condition": {"type": "nominal", "field": "solvent", "selection": "selector003"}, "value": "lightgray"}, "x": {"type": "ordinal", "axis": {"labelAngle": 0, "title": "X"}, "field": "mol"}, "y": {"type": "quantitative", "axis": {"format": ",.0f", "title": "\u03b4(fdeN) [ppm]"}, "field": "value"}}, "height": 190, "selection": {"selector003": {"type": "single", "on": "mouseover", "nearest": true, "empty": "none"}, "selector004": {"type": "multi", "fields": ["row"], "bind": "legend"}}, "width": 230}, {"mark": {"type": "text", "dy": -5}, "encoding": {"color": {"condition": {"type": "nominal", "field": "solvent", "selection": "selector003"}, "value": "lightgray"}, "opacity": {"condition": {"value": 1, "selection": "selector003"}, "value": 0}, "text": {"type": "nominal", "field": "variable"}, "x": {"type": "ordinal", "axis": {"labelAngle": 0, "title": "X"}, "field": "mol"}, "y": {"type": "quantitative", "axis": {"format": ",.0f", "title": "\u03b4(fdeN) [ppm]"}, "field": "value"}}, "height": 190, "width": 230}]}, "resolve": {"scale": {"x": "independent"}}, "spacing": 10, "title": "Approximations to solvent effects on \u03c3(X) [ppm]", "transform": [{"filter": {"selection": "selector004"}}], "$schema": "https://vega.github.io/schema/vega-lite/v4.8.1.json", "datasets": {"data-e2958ed67ee61d3fe3755e8257c7988b": [{"mol": "Ti", "x_labels": "Ti", "where": "SO-ZORA", "row": 4, "charge": 0, "solvent": "12TiCl\u2084", "complex": "TiCl\u2084 + 12TiCl\u2084", "Delta1": 22.184000000000083, "Delta2": -0.5, "Delta3": 0.16999999999995907, "Delta4": -17.553999999999974, "d1": -0.021763098138381388, "d2": -0.021154646501768178, "d3": -0.02136152005821662, "d4": -0.0, "solveff": 4.300000000000068, "variable": "delta1", "value": 22.184000000000083}, {"mol": "V", "x_labels": "V", "where": "SO-ZORA", "row": 4, "charge": 0, "solvent": "6C\u2086H\u2086", "complex": "VOCl\u2083 + 6C\u2086H\u2086", "Delta1": 2.814000000000078, "Delta2": -1.7970000000000255, "Delta3": -1.122000000000071, "Delta4": -25.22699999999986, "d1": -0.015131346929647224, "d2": -0.014165276069397935, "d3": -0.013562086584033574, "d4": -0.0, "solveff": -25.33199999999988, "variable": "delta1", "value": 2.814000000000078}, {"mol": "Cr", "x_labels": "Cr", "where": "SO-ZORA", "row": 4, "charge": -2, "solvent": "12H\u2082O", "complex": "CrO\u2084\u00b2\u207b + 12H\u2082O", "Delta1": 123.04400000000032, "Delta2": -19.00500000000011, "Delta3": -27.38500000000022, "Delta4": -12.795999999999822, "d1": -0.021712884105380876, "d2": -0.014740739300481661, "d3": -0.004694320701051754, "d4": -0.0, "solveff": 63.858000000000175, "variable": "delta1", "value": 123.04400000000032}, {"mol": "Mn", "x_labels": "Mn", "where": "SO-ZORA", "row": 4, "charge": -1, "solvent": "12H\u2082O", "complex": "MnO\u2084\u207b + 12H\u2082O", "Delta1": 35.49200000000019, "Delta2": -6.351000000000113, "Delta3": -13.909999999999854, "Delta4": -22.472999999999956, "d1": -0.010860140180028139, "d2": -0.009246138441755099, "d3": -0.005711141720077042, "d4": -0.0, "solveff": -7.241999999999734, "variable": "delta1", "value": 35.49200000000019}, {"mol": "Fe", "x_labels": "Fe", "where": "SO-ZORA", "row": 4, "charge": 0, "solvent": "6C\u2086H\u2086", "complex": "Fe(CO)\u2085 + 6C\u2086H\u2086", "Delta1": 67.43199999999979, "Delta2": -2.0169999999998254, "Delta3": -0.8409999999998945, "Delta4": -17.46199999999999, "d1": -0.008717314895411402, "d2": -0.007852018431629732, "d3": -0.007491227987385629, "d4": -0.0, "solveff": 47.11200000000008, "variable": "delta1", "value": 67.43199999999979}, {"mol": "Co", "x_labels": "Co", "where": "SO-ZORA", "row": 4, "charge": -3, "solvent": "12H\u2082O", "complex": "Co(CN)\u2086\u00b3\u207b + 12H\u2082O", "Delta1": 1098.3239999999996, "Delta2": 29.27599999999984, "Delta3": -3.118999999999687, "Delta4": -27.10699999999997, "d1": -0.0001645170921136079, "d2": -0.005234414343396641, "d3": -0.004694278753604651, "d4": -0.0, "solveff": 1097.3739999999998, "variable": "delta1", "value": 1098.3239999999996}, {"mol": "Ni", "x_labels": "Ni", "where": "SO-ZORA", "row": 4, "charge": 0, "solvent": "6C\u2086H\u2086", "complex": "Ni(CO)\u2084 + 6C\u2086H\u2086", "Delta1": -9.3599999999999, "Delta2": -8.592000000000098, "Delta3": -3.5049999999998818, "Delta4": -6.086999999999989, "d1": -0.011156635879161785, "d2": -0.005885088613776869, "d3": -0.0037346261876626584, "d4": -0.0, "solveff": -27.54399999999987, "variable": "delta1", "value": -9.3599999999999}, {"mol": "Cu", "x_labels": "Cu", "where": "SO-ZORA", "row": 4, "charge": 1, "solvent": "12CH\u2083CN", "complex": "Cu(CH\u2083CN)\u2084\u207a + 12CH\u2083CN", "Delta1": -23.295999999999992, "Delta2": -21.591999999999985, "Delta3": -2.983000000000004, "Delta4": -5.593000000000018, "d1": 0.06319613047294453, "d2": 0.01796506281278088, "d3": 0.01171625423412821, "d4": 0.0, "solveff": -53.464, "variable": "delta1", "value": -23.295999999999992}, {"mol": "Zn", "x_labels": "Zn", "where": "SO-ZORA", "row": 4, "charge": 2, "solvent": "12H\u2082O", "complex": "Zn(H\u2082O)\u2086\u00b2\u207a + 12H\u2082O", "Delta1": -29.680999999999813, "Delta2": -0.6110000000001037, "Delta3": 0.1710000000000491, "Delta4": -13.025000000000091, "d1": 0.007034567861245903, "d2": 0.00671536095718189, "d3": 0.0068046970956351675, "d4": 0.0, "solveff": -43.14599999999996, "variable": "delta1", "value": -29.680999999999813}, {"mol": "Nb", "x_labels": "Nb", "where": "SO-ZORA", "row": 5, "charge": -1, "solvent": "12CH\u2083CN", "complex": "NbCl\u2086\u207b + 12CH\u2083CN", "Delta1": 15.894000000000005, "Delta2": -12.683999999999969, "Delta3": -3.031000000000006, "Delta4": -19.694000000000017, "d1": -0.11137042011203403, "d2": -0.07147597495116995, "d3": -0.061942699700257016, "d4": -0.0, "solveff": -19.514999999999986, "variable": "delta1", "value": 15.894000000000005}, {"mol": "Mo", "x_labels": "Mo", "where": "SO-ZORA", "row": 5, "charge": -2, "solvent": "12H\u2082O", "complex": "MoO\u2084\u00b2\u207b + 12H\u2082O", "Delta1": 69.5, "Delta2": -12.021000000000015, "Delta3": -19.468000000000018, "Delta4": -21.753999999999905, "d1": -0.09521501751650152, "d2": -0.07371773664266144, "d3": -0.03890290725642746, "d4": -0.0, "solveff": 16.257000000000062, "variable": "delta1", "value": 69.5}, {"mol": "Tc", "x_labels": "Tc", "where": "SO-ZORA", "row": 5, "charge": -1, "solvent": "12H\u2082O", "complex": "TcO\u2084\u207b + 12H\u2082O", "Delta1": 22.694999999999936, "Delta2": 6.183999999999969, "Delta3": -4.981999999999971, "Delta4": 3.0920000000000982, "d1": 0.003025801759954068, "d2": -0.0013318037555455694, "d3": 0.002178802757749899, "d4": -0.0, "solveff": 26.989000000000033, "variable": "delta1", "value": 22.694999999999936}, {"mol": "Ru", "x_labels": "Ru", "where": "SO-ZORA", "row": 5, "charge": -4, "solvent": "12H\u2082O", "complex": "Ru(CN)\u2086\u2074\u207b + 12H\u2082O", "Delta1": 828.1270000000001, "Delta2": 3.6370000000000005, "Delta3": 2.411999999999999, "Delta4": -46.415, "d1": -0.04974484294377672, "d2": -0.05422688212988671, "d3": -0.057199298549160095, "d4": -0.0, "solveff": 787.761, "variable": "delta1", "value": 828.1270000000001}, {"mol": "Pd", "x_labels": "Pd", "where": "SO-ZORA", "row": 5, "charge": -2, "solvent": "12H\u2082O", "complex": "PdCl\u2086\u00b2\u207b + 12H\u2082O", "Delta1": 202.9269999999999, "Delta2": 35.669000000000096, "Delta3": 31.88799999999992, "Delta4": 0.48500000000012733, "d1": 0.04304883596306927, "d2": 0.02048176077470446, "d3": 0.0003068499668159965, "d4": -0.0, "solveff": 270.96900000000005, "variable": "delta1", "value": 202.9269999999999}, {"mol": "Ag", "x_labels": "Ag", "where": "SO-ZORA", "row": 5, "charge": 1, "solvent": "12H\u2082O", "complex": "Ag(H\u2082O)\u2084\u207a + 12H\u2082O", "Delta1": -175.03999999999996, "Delta2": 0.7719999999999345, "Delta3": 0.8350000000000364, "Delta4": -36.73400000000038, "d1": 0.008140764518531128, "d2": 0.008319677326579223, "d3": 0.008513190532175308, "d4": 0.0, "solveff": -210.16700000000037, "variable": "delta1", "value": -175.03999999999996}, {"mol": "Cd", "x_labels": "Cd", "where": "SO-ZORA", "row": 5, "charge": 0, "solvent": "6Cd(CH\u2083)\u2082", "complex": "Cd(CH\u2083)\u2082 + 6Cd(CH\u2083)\u2082", "Delta1": 6.981999999999971, "Delta2": 15.735000000000127, "Delta3": 24.100999999999658, "Delta4": 8.080000000000382, "d1": -0.013863777019912952, "d2": -0.009311090414012391, "d3": -0.0023378269955944063, "d4": 0.0, "solveff": 54.89800000000014, "variable": "delta1", "value": 6.981999999999971}, {"mol": "Ta", "x_labels": "Ta", "where": "SO-ZORA", "row": 6, "charge": -1, "solvent": "12CH\u2083CN", "complex": "TaCl\u2086\u207b + 12CH\u2083CN", "Delta1": 11.592999999999847, "Delta2": -18.442000000000007, "Delta3": -4.746999999999844, "Delta4": 42.23099999999977, "d1": -0.0055832686076456115, "d2": -0.010990612356317014, "d3": -0.012382471198901457, "d4": 0.0, "solveff": 30.634999999999764, "variable": "delta1", "value": 11.592999999999847}, {"mol": "W", "x_labels": "W", "where": "SO-ZORA", "row": 6, "charge": -2, "solvent": "12H\u2082O", "complex": "WO\u2084\u00b2\u207b + 12H\u2082O", "Delta1": 85.21000000000004, "Delta2": -32.8159999999998, "Delta3": -27.295000000000073, "Delta4": -99.5, "d1": 0.05316830974068907, "d2": 0.04223691245321864, "d3": 0.03314462549071534, "d4": 0.0, "solveff": -74.40099999999984, "variable": "delta1", "value": 85.21000000000004}, {"mol": "Re", "x_labels": "Re", "where": "SO-ZORA", "row": 6, "charge": -1, "solvent": "12H\u2082O", "complex": "ReO\u2084\u207b + 12H\u2082O", "Delta1": 21.605000000000018, "Delta2": -10.048999999999978, "Delta3": -8.81899999999996, "Delta4": -55.559999999999945, "d1": 0.03932442385502771, "d2": 0.03401498204120532, "d3": 0.029355417173447373, "d4": 0.0, "solveff": -52.822999999999865, "variable": "delta1", "value": 21.605000000000018}, {"mol": "Os", "x_labels": "Os", "where": "SO-ZORA", "row": 6, "charge": 0, "solvent": "12CCl\u2084", "complex": "OsO\u2084 + 12CCl\u2084", "Delta1": 132.73399999999992, "Delta2": 2.826000000000022, "Delta3": 2.0270000000000437, "Delta4": -16.411000000000058, "d1": 0.009486133567518316, "d2": 0.011805549855959828, "d3": 0.013469193457046525, "d4": 0.0, "solveff": 121.17599999999993, "variable": "delta1", "value": 132.73399999999992}, {"mol": "Pt", "x_labels": "Pt", "where": "SO-ZORA", "row": 6, "charge": -2, "solvent": "12H\u2082O", "complex": "PtCl\u2086\u00b2\u207b + 12H\u2082O", "Delta1": 292.2940000000001, "Delta2": 78.86199999999963, "Delta3": 56.90300000000025, "Delta4": 70.42900000000009, "d1": -0.10937188371234462, "d2": -0.06754095995451033, "d3": -0.0373577911965272, "d4": 0.0, "solveff": 498.48800000000006, "variable": "delta1", "value": 292.2940000000001}, {"mol": "Hg", "x_labels": "Hg", "where": "SO-ZORA", "row": 6, "charge": 0, "solvent": "6Hg(CH\u2083)\u2082", "complex": "Hg(CH\u2083)\u2082 + 6Hg(CH\u2083)\u2082", "Delta1": 31.950000000000728, "Delta2": 35.2450000000008, "Delta3": 28.27599999999984, "Delta4": -55.39500000000044, "d1": -0.0009148227516846377, "d2": 0.003053049249684422, "d3": 0.006236353227857449, "d4": 0.0, "solveff": 40.07600000000093, "variable": "delta1", "value": 31.950000000000728}, {"mol": "Ti", "x_labels": "Ti", "where": "SO-ZORA", "row": 4, "charge": 0, "solvent": "12TiCl\u2084", "complex": "TiCl\u2084 + 12TiCl\u2084", "Delta1": 22.184000000000083, "Delta2": -0.5, "Delta3": 0.16999999999995907, "Delta4": -17.553999999999974, "d1": -0.021763098138381388, "d2": -0.021154646501768178, "d3": -0.02136152005821662, "d4": -0.0, "solveff": 4.300000000000068, "variable": "delta2", "value": 21.684000000000083}, {"mol": "V", "x_labels": "V", "where": "SO-ZORA", "row": 4, "charge": 0, "solvent": "6C\u2086H\u2086", "complex": "VOCl\u2083 + 6C\u2086H\u2086", "Delta1": 2.814000000000078, "Delta2": -1.7970000000000255, "Delta3": -1.122000000000071, "Delta4": -25.22699999999986, "d1": -0.015131346929647224, "d2": -0.014165276069397935, "d3": -0.013562086584033574, "d4": -0.0, "solveff": -25.33199999999988, "variable": "delta2", "value": 1.0170000000000528}, {"mol": "Cr", "x_labels": "Cr", "where": "SO-ZORA", "row": 4, "charge": -2, "solvent": "12H\u2082O", "complex": "CrO\u2084\u00b2\u207b + 12H\u2082O", "Delta1": 123.04400000000032, "Delta2": -19.00500000000011, "Delta3": -27.38500000000022, "Delta4": -12.795999999999822, "d1": -0.021712884105380876, "d2": -0.014740739300481661, "d3": -0.004694320701051754, "d4": -0.0, "solveff": 63.858000000000175, "variable": "delta2", "value": 104.03900000000021}, {"mol": "Mn", "x_labels": "Mn", "where": "SO-ZORA", "row": 4, "charge": -1, "solvent": "12H\u2082O", "complex": "MnO\u2084\u207b + 12H\u2082O", "Delta1": 35.49200000000019, "Delta2": -6.351000000000113, "Delta3": -13.909999999999854, "Delta4": -22.472999999999956, "d1": -0.010860140180028139, "d2": -0.009246138441755099, "d3": -0.005711141720077042, "d4": -0.0, "solveff": -7.241999999999734, "variable": "delta2", "value": 29.141000000000076}, {"mol": "Fe", "x_labels": "Fe", "where": "SO-ZORA", "row": 4, "charge": 0, "solvent": "6C\u2086H\u2086", "complex": "Fe(CO)\u2085 + 6C\u2086H\u2086", "Delta1": 67.43199999999979, "Delta2": -2.0169999999998254, "Delta3": -0.8409999999998945, "Delta4": -17.46199999999999, "d1": -0.008717314895411402, "d2": -0.007852018431629732, "d3": -0.007491227987385629, "d4": -0.0, "solveff": 47.11200000000008, "variable": "delta2", "value": 65.41499999999996}, {"mol": "Co", "x_labels": "Co", "where": "SO-ZORA", "row": 4, "charge": -3, "solvent": "12H\u2082O", "complex": "Co(CN)\u2086\u00b3\u207b + 12H\u2082O", "Delta1": 1098.3239999999996, "Delta2": 29.27599999999984, "Delta3": -3.118999999999687, "Delta4": -27.10699999999997, "d1": -0.0001645170921136079, "d2": -0.005234414343396641, "d3": -0.004694278753604651, "d4": -0.0, "solveff": 1097.3739999999998, "variable": "delta2", "value": 1127.5999999999995}, {"mol": "Ni", "x_labels": "Ni", "where": "SO-ZORA", "row": 4, "charge": 0, "solvent": "6C\u2086H\u2086", "complex": "Ni(CO)\u2084 + 6C\u2086H\u2086", "Delta1": -9.3599999999999, "Delta2": -8.592000000000098, "Delta3": -3.5049999999998818, "Delta4": -6.086999999999989, "d1": -0.011156635879161785, "d2": -0.005885088613776869, "d3": -0.0037346261876626584, "d4": -0.0, "solveff": -27.54399999999987, "variable": "delta2", "value": -17.951999999999998}, {"mol": "Cu", "x_labels": "Cu", "where": "SO-ZORA", "row": 4, "charge": 1, "solvent": "12CH\u2083CN", "complex": "Cu(CH\u2083CN)\u2084\u207a + 12CH\u2083CN", "Delta1": -23.295999999999992, "Delta2": -21.591999999999985, "Delta3": -2.983000000000004, "Delta4": -5.593000000000018, "d1": 0.06319613047294453, "d2": 0.01796506281278088, "d3": 0.01171625423412821, "d4": 0.0, "solveff": -53.464, "variable": "delta2", "value": -44.88799999999998}, {"mol": "Zn", "x_labels": "Zn", "where": "SO-ZORA", "row": 4, "charge": 2, "solvent": "12H\u2082O", "complex": "Zn(H\u2082O)\u2086\u00b2\u207a + 12H\u2082O", "Delta1": -29.680999999999813, "Delta2": -0.6110000000001037, "Delta3": 0.1710000000000491, "Delta4": -13.025000000000091, "d1": 0.007034567861245903, "d2": 0.00671536095718189, "d3": 0.0068046970956351675, "d4": 0.0, "solveff": -43.14599999999996, "variable": "delta2", "value": -30.291999999999916}, {"mol": "Nb", "x_labels": "Nb", "where": "SO-ZORA", "row": 5, "charge": -1, "solvent": "12CH\u2083CN", "complex": "NbCl\u2086\u207b + 12CH\u2083CN", "Delta1": 15.894000000000005, "Delta2": -12.683999999999969, "Delta3": -3.031000000000006, "Delta4": -19.694000000000017, "d1": -0.11137042011203403, "d2": -0.07147597495116995, "d3": -0.061942699700257016, "d4": -0.0, "solveff": -19.514999999999986, "variable": "delta2", "value": 3.2100000000000364}, {"mol": "Mo", "x_labels": "Mo", "where": "SO-ZORA", "row": 5, "charge": -2, "solvent": "12H\u2082O", "complex": "MoO\u2084\u00b2\u207b + 12H\u2082O", "Delta1": 69.5, "Delta2": -12.021000000000015, "Delta3": -19.468000000000018, "Delta4": -21.753999999999905, "d1": -0.09521501751650152, "d2": -0.07371773664266144, "d3": -0.03890290725642746, "d4": -0.0, "solveff": 16.257000000000062, "variable": "delta2", "value": 57.478999999999985}, {"mol": "Tc", "x_labels": "Tc", "where": "SO-ZORA", "row": 5, "charge": -1, "solvent": "12H\u2082O", "complex": "TcO\u2084\u207b + 12H\u2082O", "Delta1": 22.694999999999936, "Delta2": 6.183999999999969, "Delta3": -4.981999999999971, "Delta4": 3.0920000000000982, "d1": 0.003025801759954068, "d2": -0.0013318037555455694, "d3": 0.002178802757749899, "d4": -0.0, "solveff": 26.989000000000033, "variable": "delta2", "value": 28.878999999999905}, {"mol": "Ru", "x_labels": "Ru", "where": "SO-ZORA", "row": 5, "charge": -4, "solvent": "12H\u2082O", "complex": "Ru(CN)\u2086\u2074\u207b + 12H\u2082O", "Delta1": 828.1270000000001, "Delta2": 3.6370000000000005, "Delta3": 2.411999999999999, "Delta4": -46.415, "d1": -0.04974484294377672, "d2": -0.05422688212988671, "d3": -0.057199298549160095, "d4": -0.0, "solveff": 787.761, "variable": "delta2", "value": 831.764}, {"mol": "Pd", "x_labels": "Pd", "where": "SO-ZORA", "row": 5, "charge": -2, "solvent": "12H\u2082O", "complex": "PdCl\u2086\u00b2\u207b + 12H\u2082O", "Delta1": 202.9269999999999, "Delta2": 35.669000000000096, "Delta3": 31.88799999999992, "Delta4": 0.48500000000012733, "d1": 0.04304883596306927, "d2": 0.02048176077470446, "d3": 0.0003068499668159965, "d4": -0.0, "solveff": 270.96900000000005, "variable": "delta2", "value": 238.596}, {"mol": "Ag", "x_labels": "Ag", "where": "SO-ZORA", "row": 5, "charge": 1, "solvent": "12H\u2082O", "complex": "Ag(H\u2082O)\u2084\u207a + 12H\u2082O", "Delta1": -175.03999999999996, "Delta2": 0.7719999999999345, "Delta3": 0.8350000000000364, "Delta4": -36.73400000000038, "d1": 0.008140764518531128, "d2": 0.008319677326579223, "d3": 0.008513190532175308, "d4": 0.0, "solveff": -210.16700000000037, "variable": "delta2", "value": -174.26800000000003}, {"mol": "Cd", "x_labels": "Cd", "where": "SO-ZORA", "row": 5, "charge": 0, "solvent": "6Cd(CH\u2083)\u2082", "complex": "Cd(CH\u2083)\u2082 + 6Cd(CH\u2083)\u2082", "Delta1": 6.981999999999971, "Delta2": 15.735000000000127, "Delta3": 24.100999999999658, "Delta4": 8.080000000000382, "d1": -0.013863777019912952, "d2": -0.009311090414012391, "d3": -0.0023378269955944063, "d4": 0.0, "solveff": 54.89800000000014, "variable": "delta2", "value": 22.7170000000001}, {"mol": "Ta", "x_labels": "Ta", "where": "SO-ZORA", "row": 6, "charge": -1, "solvent": "12CH\u2083CN", "complex": "TaCl\u2086\u207b + 12CH\u2083CN", "Delta1": 11.592999999999847, "Delta2": -18.442000000000007, "Delta3": -4.746999999999844, "Delta4": 42.23099999999977, "d1": -0.0055832686076456115, "d2": -0.010990612356317014, "d3": -0.012382471198901457, "d4": 0.0, "solveff": 30.634999999999764, "variable": "delta2", "value": -6.84900000000016}, {"mol": "W", "x_labels": "W", "where": "SO-ZORA", "row": 6, "charge": -2, "solvent": "12H\u2082O", "complex": "WO\u2084\u00b2\u207b + 12H\u2082O", "Delta1": 85.21000000000004, "Delta2": -32.8159999999998, "Delta3": -27.295000000000073, "Delta4": -99.5, "d1": 0.05316830974068907, "d2": 0.04223691245321864, "d3": 0.03314462549071534, "d4": 0.0, "solveff": -74.40099999999984, "variable": "delta2", "value": 52.39400000000023}, {"mol": "Re", "x_labels": "Re", "where": "SO-ZORA", "row": 6, "charge": -1, "solvent": "12H\u2082O", "complex": "ReO\u2084\u207b + 12H\u2082O", "Delta1": 21.605000000000018, "Delta2": -10.048999999999978, "Delta3": -8.81899999999996, "Delta4": -55.559999999999945, "d1": 0.03932442385502771, "d2": 0.03401498204120532, "d3": 0.029355417173447373, "d4": 0.0, "solveff": -52.822999999999865, "variable": "delta2", "value": 11.55600000000004}, {"mol": "Os", "x_labels": "Os", "where": "SO-ZORA", "row": 6, "charge": 0, "solvent": "12CCl\u2084", "complex": "OsO\u2084 + 12CCl\u2084", "Delta1": 132.73399999999992, "Delta2": 2.826000000000022, "Delta3": 2.0270000000000437, "Delta4": -16.411000000000058, "d1": 0.009486133567518316, "d2": 0.011805549855959828, "d3": 0.013469193457046525, "d4": 0.0, "solveff": 121.17599999999993, "variable": "delta2", "value": 135.55999999999995}, {"mol": "Pt", "x_labels": "Pt", "where": "SO-ZORA", "row": 6, "charge": -2, "solvent": "12H\u2082O", "complex": "PtCl\u2086\u00b2\u207b + 12H\u2082O", "Delta1": 292.2940000000001, "Delta2": 78.86199999999963, "Delta3": 56.90300000000025, "Delta4": 70.42900000000009, "d1": -0.10937188371234462, "d2": -0.06754095995451033, "d3": -0.0373577911965272, "d4": 0.0, "solveff": 498.48800000000006, "variable": "delta2", "value": 371.1559999999997}, {"mol": "Hg", "x_labels": "Hg", "where": "SO-ZORA", "row": 6, "charge": 0, "solvent": "6Hg(CH\u2083)\u2082", "complex": "Hg(CH\u2083)\u2082 + 6Hg(CH\u2083)\u2082", "Delta1": 31.950000000000728, "Delta2": 35.2450000000008, "Delta3": 28.27599999999984, "Delta4": -55.39500000000044, "d1": -0.0009148227516846377, "d2": 0.003053049249684422, "d3": 0.006236353227857449, "d4": 0.0, "solveff": 40.07600000000093, "variable": "delta2", "value": 67.19500000000153}, {"mol": "Ti", "x_labels": "Ti", "where": "SO-ZORA", "row": 4, "charge": 0, "solvent": "12TiCl\u2084", "complex": "TiCl\u2084 + 12TiCl\u2084", "Delta1": 22.184000000000083, "Delta2": -0.5, "Delta3": 0.16999999999995907, "Delta4": -17.553999999999974, "d1": -0.021763098138381388, "d2": -0.021154646501768178, "d3": -0.02136152005821662, "d4": -0.0, "solveff": 4.300000000000068, "variable": "delta3", "value": 21.854000000000042}, {"mol": "V", "x_labels": "V", "where": "SO-ZORA", "row": 4, "charge": 0, "solvent": "6C\u2086H\u2086", "complex": "VOCl\u2083 + 6C\u2086H\u2086", "Delta1": 2.814000000000078, "Delta2": -1.7970000000000255, "Delta3": -1.122000000000071, "Delta4": -25.22699999999986, "d1": -0.015131346929647224, "d2": -0.014165276069397935, "d3": -0.013562086584033574, "d4": -0.0, "solveff": -25.33199999999988, "variable": "delta3", "value": -0.10500000000001819}, {"mol": "Cr", "x_labels": "Cr", "where": "SO-ZORA", "row": 4, "charge": -2, "solvent": "12H\u2082O", "complex": "CrO\u2084\u00b2\u207b + 12H\u2082O", "Delta1": 123.04400000000032, "Delta2": -19.00500000000011, "Delta3": -27.38500000000022, "Delta4": -12.795999999999822, "d1": -0.021712884105380876, "d2": -0.014740739300481661, "d3": -0.004694320701051754, "d4": -0.0, "solveff": 63.858000000000175, "variable": "delta3", "value": 76.654}, {"mol": "Mn", "x_labels": "Mn", "where": "SO-ZORA", "row": 4, "charge": -1, "solvent": "12H\u2082O", "complex": "MnO\u2084\u207b + 12H\u2082O", "Delta1": 35.49200000000019, "Delta2": -6.351000000000113, "Delta3": -13.909999999999854, "Delta4": -22.472999999999956, "d1": -0.010860140180028139, "d2": -0.009246138441755099, "d3": -0.005711141720077042, "d4": -0.0, "solveff": -7.241999999999734, "variable": "delta3", "value": 15.231000000000222}, {"mol": "Fe", "x_labels": "Fe", "where": "SO-ZORA", "row": 4, "charge": 0, "solvent": "6C\u2086H\u2086", "complex": "Fe(CO)\u2085 + 6C\u2086H\u2086", "Delta1": 67.43199999999979, "Delta2": -2.0169999999998254, "Delta3": -0.8409999999998945, "Delta4": -17.46199999999999, "d1": -0.008717314895411402, "d2": -0.007852018431629732, "d3": -0.007491227987385629, "d4": -0.0, "solveff": 47.11200000000008, "variable": "delta3", "value": 64.57400000000007}, {"mol": "Co", "x_labels": "Co", "where": "SO-ZORA", "row": 4, "charge": -3, "solvent": "12H\u2082O", "complex": "Co(CN)\u2086\u00b3\u207b + 12H\u2082O", "Delta1": 1098.3239999999996, "Delta2": 29.27599999999984, "Delta3": -3.118999999999687, "Delta4": -27.10699999999997, "d1": -0.0001645170921136079, "d2": -0.005234414343396641, "d3": -0.004694278753604651, "d4": -0.0, "solveff": 1097.3739999999998, "variable": "delta3", "value": 1124.4809999999998}, {"mol": "Ni", "x_labels": "Ni", "where": "SO-ZORA", "row": 4, "charge": 0, "solvent": "6C\u2086H\u2086", "complex": "Ni(CO)\u2084 + 6C\u2086H\u2086", "Delta1": -9.3599999999999, "Delta2": -8.592000000000098, "Delta3": -3.5049999999998818, "Delta4": -6.086999999999989, "d1": -0.011156635879161785, "d2": -0.005885088613776869, "d3": -0.0037346261876626584, "d4": -0.0, "solveff": -27.54399999999987, "variable": "delta3", "value": -21.45699999999988}, {"mol": "Cu", "x_labels": "Cu", "where": "SO-ZORA", "row": 4, "charge": 1, "solvent": "12CH\u2083CN", "complex": "Cu(CH\u2083CN)\u2084\u207a + 12CH\u2083CN", "Delta1": -23.295999999999992, "Delta2": -21.591999999999985, "Delta3": -2.983000000000004, "Delta4": -5.593000000000018, "d1": 0.06319613047294453, "d2": 0.01796506281278088, "d3": 0.01171625423412821, "d4": 0.0, "solveff": -53.464, "variable": "delta3", "value": -47.87099999999998}, {"mol": "Zn", "x_labels": "Zn", "where": "SO-ZORA", "row": 4, "charge": 2, "solvent": "12H\u2082O", "complex": "Zn(H\u2082O)\u2086\u00b2\u207a + 12H\u2082O", "Delta1": -29.680999999999813, "Delta2": -0.6110000000001037, "Delta3": 0.1710000000000491, "Delta4": -13.025000000000091, "d1": 0.007034567861245903, "d2": 0.00671536095718189, "d3": 0.0068046970956351675, "d4": 0.0, "solveff": -43.14599999999996, "variable": "delta3", "value": -30.120999999999867}, {"mol": "Nb", "x_labels": "Nb", "where": "SO-ZORA", "row": 5, "charge": -1, "solvent": "12CH\u2083CN", "complex": "NbCl\u2086\u207b + 12CH\u2083CN", "Delta1": 15.894000000000005, "Delta2": -12.683999999999969, "Delta3": -3.031000000000006, "Delta4": -19.694000000000017, "d1": -0.11137042011203403, "d2": -0.07147597495116995, "d3": -0.061942699700257016, "d4": -0.0, "solveff": -19.514999999999986, "variable": "delta3", "value": 0.17900000000003047}, {"mol": "Mo", "x_labels": "Mo", "where": "SO-ZORA", "row": 5, "charge": -2, "solvent": "12H\u2082O", "complex": "MoO\u2084\u00b2\u207b + 12H\u2082O", "Delta1": 69.5, "Delta2": -12.021000000000015, "Delta3": -19.468000000000018, "Delta4": -21.753999999999905, "d1": -0.09521501751650152, "d2": -0.07371773664266144, "d3": -0.03890290725642746, "d4": -0.0, "solveff": 16.257000000000062, "variable": "delta3", "value": 38.01099999999997}, {"mol": "Tc", "x_labels": "Tc", "where": "SO-ZORA", "row": 5, "charge": -1, "solvent": "12H\u2082O", "complex": "TcO\u2084\u207b + 12H\u2082O", "Delta1": 22.694999999999936, "Delta2": 6.183999999999969, "Delta3": -4.981999999999971, "Delta4": 3.0920000000000982, "d1": 0.003025801759954068, "d2": -0.0013318037555455694, "d3": 0.002178802757749899, "d4": -0.0, "solveff": 26.989000000000033, "variable": "delta3", "value": 23.896999999999935}, {"mol": "Ru", "x_labels": "Ru", "where": "SO-ZORA", "row": 5, "charge": -4, "solvent": "12H\u2082O", "complex": "Ru(CN)\u2086\u2074\u207b + 12H\u2082O", "Delta1": 828.1270000000001, "Delta2": 3.6370000000000005, "Delta3": 2.411999999999999, "Delta4": -46.415, "d1": -0.04974484294377672, "d2": -0.05422688212988671, "d3": -0.057199298549160095, "d4": -0.0, "solveff": 787.761, "variable": "delta3", "value": 834.176}, {"mol": "Pd", "x_labels": "Pd", "where": "SO-ZORA", "row": 5, "charge": -2, "solvent": "12H\u2082O", "complex": "PdCl\u2086\u00b2\u207b + 12H\u2082O", "Delta1": 202.9269999999999, "Delta2": 35.669000000000096, "Delta3": 31.88799999999992, "Delta4": 0.48500000000012733, "d1": 0.04304883596306927, "d2": 0.02048176077470446, "d3": 0.0003068499668159965, "d4": -0.0, "solveff": 270.96900000000005, "variable": "delta3", "value": 270.4839999999999}, {"mol": "Ag", "x_labels": "Ag", "where": "SO-ZORA", "row": 5, "charge": 1, "solvent": "12H\u2082O", "complex": "Ag(H\u2082O)\u2084\u207a + 12H\u2082O", "Delta1": -175.03999999999996, "Delta2": 0.7719999999999345, "Delta3": 0.8350000000000364, "Delta4": -36.73400000000038, "d1": 0.008140764518531128, "d2": 0.008319677326579223, "d3": 0.008513190532175308, "d4": 0.0, "solveff": -210.16700000000037, "variable": "delta3", "value": -173.433}, {"mol": "Cd", "x_labels": "Cd", "where": "SO-ZORA", "row": 5, "charge": 0, "solvent": "6Cd(CH\u2083)\u2082", "complex": "Cd(CH\u2083)\u2082 + 6Cd(CH\u2083)\u2082", "Delta1": 6.981999999999971, "Delta2": 15.735000000000127, "Delta3": 24.100999999999658, "Delta4": 8.080000000000382, "d1": -0.013863777019912952, "d2": -0.009311090414012391, "d3": -0.0023378269955944063, "d4": 0.0, "solveff": 54.89800000000014, "variable": "delta3", "value": 46.817999999999756}, {"mol": "Ta", "x_labels": "Ta", "where": "SO-ZORA", "row": 6, "charge": -1, "solvent": "12CH\u2083CN", "complex": "TaCl\u2086\u207b + 12CH\u2083CN", "Delta1": 11.592999999999847, "Delta2": -18.442000000000007, "Delta3": -4.746999999999844, "Delta4": 42.23099999999977, "d1": -0.0055832686076456115, "d2": -0.010990612356317014, "d3": -0.012382471198901457, "d4": 0.0, "solveff": 30.634999999999764, "variable": "delta3", "value": -11.596000000000004}, {"mol": "W", "x_labels": "W", "where": "SO-ZORA", "row": 6, "charge": -2, "solvent": "12H\u2082O", "complex": "WO\u2084\u00b2\u207b + 12H\u2082O", "Delta1": 85.21000000000004, "Delta2": -32.8159999999998, "Delta3": -27.295000000000073, "Delta4": -99.5, "d1": 0.05316830974068907, "d2": 0.04223691245321864, "d3": 0.03314462549071534, "d4": 0.0, "solveff": -74.40099999999984, "variable": "delta3", "value": 25.09900000000016}, {"mol": "Re", "x_labels": "Re", "where": "SO-ZORA", "row": 6, "charge": -1, "solvent": "12H\u2082O", "complex": "ReO\u2084\u207b + 12H\u2082O", "Delta1": 21.605000000000018, "Delta2": -10.048999999999978, "Delta3": -8.81899999999996, "Delta4": -55.559999999999945, "d1": 0.03932442385502771, "d2": 0.03401498204120532, "d3": 0.029355417173447373, "d4": 0.0, "solveff": -52.822999999999865, "variable": "delta3", "value": 2.73700000000008}, {"mol": "Os", "x_labels": "Os", "where": "SO-ZORA", "row": 6, "charge": 0, "solvent": "12CCl\u2084", "complex": "OsO\u2084 + 12CCl\u2084", "Delta1": 132.73399999999992, "Delta2": 2.826000000000022, "Delta3": 2.0270000000000437, "Delta4": -16.411000000000058, "d1": 0.009486133567518316, "d2": 0.011805549855959828, "d3": 0.013469193457046525, "d4": 0.0, "solveff": 121.17599999999993, "variable": "delta3", "value": 137.587}, {"mol": "Pt", "x_labels": "Pt", "where": "SO-ZORA", "row": 6, "charge": -2, "solvent": "12H\u2082O", "complex": "PtCl\u2086\u00b2\u207b + 12H\u2082O", "Delta1": 292.2940000000001, "Delta2": 78.86199999999963, "Delta3": 56.90300000000025, "Delta4": 70.42900000000009, "d1": -0.10937188371234462, "d2": -0.06754095995451033, "d3": -0.0373577911965272, "d4": 0.0, "solveff": 498.48800000000006, "variable": "delta3", "value": 428.05899999999997}, {"mol": "Hg", "x_labels": "Hg", "where": "SO-ZORA", "row": 6, "charge": 0, "solvent": "6Hg(CH\u2083)\u2082", "complex": "Hg(CH\u2083)\u2082 + 6Hg(CH\u2083)\u2082", "Delta1": 31.950000000000728, "Delta2": 35.2450000000008, "Delta3": 28.27599999999984, "Delta4": -55.39500000000044, "d1": -0.0009148227516846377, "d2": 0.003053049249684422, "d3": 0.006236353227857449, "d4": 0.0, "solveff": 40.07600000000093, "variable": "delta3", "value": 95.47100000000137}, {"mol": "Ti", "x_labels": "Ti", "where": "SO-ZORA", "row": 4, "charge": 0, "solvent": "12TiCl\u2084", "complex": "TiCl\u2084 + 12TiCl\u2084", "Delta1": 22.184000000000083, "Delta2": -0.5, "Delta3": 0.16999999999995907, "Delta4": -17.553999999999974, "d1": -0.021763098138381388, "d2": -0.021154646501768178, "d3": -0.02136152005821662, "d4": -0.0, "solveff": 4.300000000000068, "variable": "delta4", "value": 4.300000000000068}, {"mol": "V", "x_labels": "V", "where": "SO-ZORA", "row": 4, "charge": 0, "solvent": "6C\u2086H\u2086", "complex": "VOCl\u2083 + 6C\u2086H\u2086", "Delta1": 2.814000000000078, "Delta2": -1.7970000000000255, "Delta3": -1.122000000000071, "Delta4": -25.22699999999986, "d1": -0.015131346929647224, "d2": -0.014165276069397935, "d3": -0.013562086584033574, "d4": -0.0, "solveff": -25.33199999999988, "variable": "delta4", "value": -25.33199999999988}, {"mol": "Cr", "x_labels": "Cr", "where": "SO-ZORA", "row": 4, "charge": -2, "solvent": "12H\u2082O", "complex": "CrO\u2084\u00b2\u207b + 12H\u2082O", "Delta1": 123.04400000000032, "Delta2": -19.00500000000011, "Delta3": -27.38500000000022, "Delta4": -12.795999999999822, "d1": -0.021712884105380876, "d2": -0.014740739300481661, "d3": -0.004694320701051754, "d4": -0.0, "solveff": 63.858000000000175, "variable": "delta4", "value": 63.858000000000175}, {"mol": "Mn", "x_labels": "Mn", "where": "SO-ZORA", "row": 4, "charge": -1, "solvent": "12H\u2082O", "complex": "MnO\u2084\u207b + 12H\u2082O", "Delta1": 35.49200000000019, "Delta2": -6.351000000000113, "Delta3": -13.909999999999854, "Delta4": -22.472999999999956, "d1": -0.010860140180028139, "d2": -0.009246138441755099, "d3": -0.005711141720077042, "d4": -0.0, "solveff": -7.241999999999734, "variable": "delta4", "value": -7.241999999999734}, {"mol": "Fe", "x_labels": "Fe", "where": "SO-ZORA", "row": 4, "charge": 0, "solvent": "6C\u2086H\u2086", "complex": "Fe(CO)\u2085 + 6C\u2086H\u2086", "Delta1": 67.43199999999979, "Delta2": -2.0169999999998254, "Delta3": -0.8409999999998945, "Delta4": -17.46199999999999, "d1": -0.008717314895411402, "d2": -0.007852018431629732, "d3": -0.007491227987385629, "d4": -0.0, "solveff": 47.11200000000008, "variable": "delta4", "value": 47.11200000000008}, {"mol": "Co", "x_labels": "Co", "where": "SO-ZORA", "row": 4, "charge": -3, "solvent": "12H\u2082O", "complex": "Co(CN)\u2086\u00b3\u207b + 12H\u2082O", "Delta1": 1098.3239999999996, "Delta2": 29.27599999999984, "Delta3": -3.118999999999687, "Delta4": -27.10699999999997, "d1": -0.0001645170921136079, "d2": -0.005234414343396641, "d3": -0.004694278753604651, "d4": -0.0, "solveff": 1097.3739999999998, "variable": "delta4", "value": 1097.3739999999998}, {"mol": "Ni", "x_labels": "Ni", "where": "SO-ZORA", "row": 4, "charge": 0, "solvent": "6C\u2086H\u2086", "complex": "Ni(CO)\u2084 + 6C\u2086H\u2086", "Delta1": -9.3599999999999, "Delta2": -8.592000000000098, "Delta3": -3.5049999999998818, "Delta4": -6.086999999999989, "d1": -0.011156635879161785, "d2": -0.005885088613776869, "d3": -0.0037346261876626584, "d4": -0.0, "solveff": -27.54399999999987, "variable": "delta4", "value": -27.54399999999987}, {"mol": "Cu", "x_labels": "Cu", "where": "SO-ZORA", "row": 4, "charge": 1, "solvent": "12CH\u2083CN", "complex": "Cu(CH\u2083CN)\u2084\u207a + 12CH\u2083CN", "Delta1": -23.295999999999992, "Delta2": -21.591999999999985, "Delta3": -2.983000000000004, "Delta4": -5.593000000000018, "d1": 0.06319613047294453, "d2": 0.01796506281278088, "d3": 0.01171625423412821, "d4": 0.0, "solveff": -53.464, "variable": "delta4", "value": -53.464}, {"mol": "Zn", "x_labels": "Zn", "where": "SO-ZORA", "row": 4, "charge": 2, "solvent": "12H\u2082O", "complex": "Zn(H\u2082O)\u2086\u00b2\u207a + 12H\u2082O", "Delta1": -29.680999999999813, "Delta2": -0.6110000000001037, "Delta3": 0.1710000000000491, "Delta4": -13.025000000000091, "d1": 0.007034567861245903, "d2": 0.00671536095718189, "d3": 0.0068046970956351675, "d4": 0.0, "solveff": -43.14599999999996, "variable": "delta4", "value": -43.14599999999996}, {"mol": "Nb", "x_labels": "Nb", "where": "SO-ZORA", "row": 5, "charge": -1, "solvent": "12CH\u2083CN", "complex": "NbCl\u2086\u207b + 12CH\u2083CN", "Delta1": 15.894000000000005, "Delta2": -12.683999999999969, "Delta3": -3.031000000000006, "Delta4": -19.694000000000017, "d1": -0.11137042011203403, "d2": -0.07147597495116995, "d3": -0.061942699700257016, "d4": -0.0, "solveff": -19.514999999999986, "variable": "delta4", "value": -19.514999999999986}, {"mol": "Mo", "x_labels": "Mo", "where": "SO-ZORA", "row": 5, "charge": -2, "solvent": "12H\u2082O", "complex": "MoO\u2084\u00b2\u207b + 12H\u2082O", "Delta1": 69.5, "Delta2": -12.021000000000015, "Delta3": -19.468000000000018, "Delta4": -21.753999999999905, "d1": -0.09521501751650152, "d2": -0.07371773664266144, "d3": -0.03890290725642746, "d4": -0.0, "solveff": 16.257000000000062, "variable": "delta4", "value": 16.257000000000062}, {"mol": "Tc", "x_labels": "Tc", "where": "SO-ZORA", "row": 5, "charge": -1, "solvent": "12H\u2082O", "complex": "TcO\u2084\u207b + 12H\u2082O", "Delta1": 22.694999999999936, "Delta2": 6.183999999999969, "Delta3": -4.981999999999971, "Delta4": 3.0920000000000982, "d1": 0.003025801759954068, "d2": -0.0013318037555455694, "d3": 0.002178802757749899, "d4": -0.0, "solveff": 26.989000000000033, "variable": "delta4", "value": 26.989000000000033}, {"mol": "Ru", "x_labels": "Ru", "where": "SO-ZORA", "row": 5, "charge": -4, "solvent": "12H\u2082O", "complex": "Ru(CN)\u2086\u2074\u207b + 12H\u2082O", "Delta1": 828.1270000000001, "Delta2": 3.6370000000000005, "Delta3": 2.411999999999999, "Delta4": -46.415, "d1": -0.04974484294377672, "d2": -0.05422688212988671, "d3": -0.057199298549160095, "d4": -0.0, "solveff": 787.761, "variable": "delta4", "value": 787.761}, {"mol": "Pd", "x_labels": "Pd", "where": "SO-ZORA", "row": 5, "charge": -2, "solvent": "12H\u2082O", "complex": "PdCl\u2086\u00b2\u207b + 12H\u2082O", "Delta1": 202.9269999999999, "Delta2": 35.669000000000096, "Delta3": 31.88799999999992, "Delta4": 0.48500000000012733, "d1": 0.04304883596306927, "d2": 0.02048176077470446, "d3": 0.0003068499668159965, "d4": -0.0, "solveff": 270.96900000000005, "variable": "delta4", "value": 270.96900000000005}, {"mol": "Ag", "x_labels": "Ag", "where": "SO-ZORA", "row": 5, "charge": 1, "solvent": "12H\u2082O", "complex": "Ag(H\u2082O)\u2084\u207a + 12H\u2082O", "Delta1": -175.03999999999996, "Delta2": 0.7719999999999345, "Delta3": 0.8350000000000364, "Delta4": -36.73400000000038, "d1": 0.008140764518531128, "d2": 0.008319677326579223, "d3": 0.008513190532175308, "d4": 0.0, "solveff": -210.16700000000037, "variable": "delta4", "value": -210.16700000000037}, {"mol": "Cd", "x_labels": "Cd", "where": "SO-ZORA", "row": 5, "charge": 0, "solvent": "6Cd(CH\u2083)\u2082", "complex": "Cd(CH\u2083)\u2082 + 6Cd(CH\u2083)\u2082", "Delta1": 6.981999999999971, "Delta2": 15.735000000000127, "Delta3": 24.100999999999658, "Delta4": 8.080000000000382, "d1": -0.013863777019912952, "d2": -0.009311090414012391, "d3": -0.0023378269955944063, "d4": 0.0, "solveff": 54.89800000000014, "variable": "delta4", "value": 54.89800000000014}, {"mol": "Ta", "x_labels": "Ta", "where": "SO-ZORA", "row": 6, "charge": -1, "solvent": "12CH\u2083CN", "complex": "TaCl\u2086\u207b + 12CH\u2083CN", "Delta1": 11.592999999999847, "Delta2": -18.442000000000007, "Delta3": -4.746999999999844, "Delta4": 42.23099999999977, "d1": -0.0055832686076456115, "d2": -0.010990612356317014, "d3": -0.012382471198901457, "d4": 0.0, "solveff": 30.634999999999764, "variable": "delta4", "value": 30.634999999999764}, {"mol": "W", "x_labels": "W", "where": "SO-ZORA", "row": 6, "charge": -2, "solvent": "12H\u2082O", "complex": "WO\u2084\u00b2\u207b + 12H\u2082O", "Delta1": 85.21000000000004, "Delta2": -32.8159999999998, "Delta3": -27.295000000000073, "Delta4": -99.5, "d1": 0.05316830974068907, "d2": 0.04223691245321864, "d3": 0.03314462549071534, "d4": 0.0, "solveff": -74.40099999999984, "variable": "delta4", "value": -74.40099999999984}, {"mol": "Re", "x_labels": "Re", "where": "SO-ZORA", "row": 6, "charge": -1, "solvent": "12H\u2082O", "complex": "ReO\u2084\u207b + 12H\u2082O", "Delta1": 21.605000000000018, "Delta2": -10.048999999999978, "Delta3": -8.81899999999996, "Delta4": -55.559999999999945, "d1": 0.03932442385502771, "d2": 0.03401498204120532, "d3": 0.029355417173447373, "d4": 0.0, "solveff": -52.822999999999865, "variable": "delta4", "value": -52.822999999999865}, {"mol": "Os", "x_labels": "Os", "where": "SO-ZORA", "row": 6, "charge": 0, "solvent": "12CCl\u2084", "complex": "OsO\u2084 + 12CCl\u2084", "Delta1": 132.73399999999992, "Delta2": 2.826000000000022, "Delta3": 2.0270000000000437, "Delta4": -16.411000000000058, "d1": 0.009486133567518316, "d2": 0.011805549855959828, "d3": 0.013469193457046525, "d4": 0.0, "solveff": 121.17599999999993, "variable": "delta4", "value": 121.17599999999993}, {"mol": "Pt", "x_labels": "Pt", "where": "SO-ZORA", "row": 6, "charge": -2, "solvent": "12H\u2082O", "complex": "PtCl\u2086\u00b2\u207b + 12H\u2082O", "Delta1": 292.2940000000001, "Delta2": 78.86199999999963, "Delta3": 56.90300000000025, "Delta4": 70.42900000000009, "d1": -0.10937188371234462, "d2": -0.06754095995451033, "d3": -0.0373577911965272, "d4": 0.0, "solveff": 498.48800000000006, "variable": "delta4", "value": 498.48800000000006}, {"mol": "Hg", "x_labels": "Hg", "where": "SO-ZORA", "row": 6, "charge": 0, "solvent": "6Hg(CH\u2083)\u2082", "complex": "Hg(CH\u2083)\u2082 + 6Hg(CH\u2083)\u2082", "Delta1": 31.950000000000728, "Delta2": 35.2450000000008, "Delta3": 28.27599999999984, "Delta4": -55.39500000000044, "d1": -0.0009148227516846377, "d2": 0.003053049249684422, "d3": 0.006236353227857449, "d4": 0.0, "solveff": 40.07600000000093, "variable": "delta4", "value": 40.07600000000093}]}}, {"mode": "vega-lite"});
</script>



To zoom into the plots for each row, we may keep the y-axes of all plots independent:


```python
delta_points, delta_text=plot_chart(df_adf_all_melted_delta, 
                                    'mol:O', 
                                    'value', 
                                    'solvent:N', 
                                    'variable:N', 
                                    "δ(fdeN) [ppm]")
                                          
layer=def_layer(delta_points, 
                delta_text,
                True, True,
                10,
                'Approximations to solvent effects on σ(X) [ppm] (note different y-scales)')

layer

```





<div id="altair-viz-88ba2c2deaba435c82eb62a26d213b89"></div>
<script type="text/javascript">
  (function(spec, embedOpt){
    let outputDiv = document.currentScript.previousElementSibling;
    if (outputDiv.id !== "altair-viz-88ba2c2deaba435c82eb62a26d213b89") {
      outputDiv = document.getElementById("altair-viz-88ba2c2deaba435c82eb62a26d213b89");
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
  })({"config": {"view": {"continuousWidth": 400, "continuousHeight": 300}}, "data": {"name": "data-e2958ed67ee61d3fe3755e8257c7988b"}, "facet": {"type": "nominal", "field": "row", "title": "row(X)"}, "spec": {"layer": [{"mark": "point", "encoding": {"color": {"condition": {"type": "nominal", "field": "solvent", "selection": "selector005"}, "value": "lightgray"}, "x": {"type": "ordinal", "axis": {"labelAngle": 0, "title": "X"}, "field": "mol"}, "y": {"type": "quantitative", "axis": {"format": ",.0f", "title": "\u03b4(fdeN) [ppm]"}, "field": "value"}}, "height": 190, "selection": {"selector005": {"type": "single", "on": "mouseover", "nearest": true, "empty": "none"}, "selector006": {"type": "multi", "fields": ["row"], "bind": "legend"}}, "width": 230}, {"mark": {"type": "text", "dy": -5}, "encoding": {"color": {"condition": {"type": "nominal", "field": "solvent", "selection": "selector005"}, "value": "lightgray"}, "opacity": {"condition": {"value": 1, "selection": "selector005"}, "value": 0}, "text": {"type": "nominal", "field": "variable"}, "x": {"type": "ordinal", "axis": {"labelAngle": 0, "title": "X"}, "field": "mol"}, "y": {"type": "quantitative", "axis": {"format": ",.0f", "title": "\u03b4(fdeN) [ppm]"}, "field": "value"}}, "height": 190, "width": 230}]}, "resolve": {"scale": {"x": "independent", "y": "independent"}}, "spacing": 10, "title": "Approximations to solvent effects on \u03c3(X) [ppm] (note different y-scales)", "transform": [{"filter": {"selection": "selector006"}}], "$schema": "https://vega.github.io/schema/vega-lite/v4.8.1.json", "datasets": {"data-e2958ed67ee61d3fe3755e8257c7988b": [{"mol": "Ti", "x_labels": "Ti", "where": "SO-ZORA", "row": 4, "charge": 0, "solvent": "12TiCl\u2084", "complex": "TiCl\u2084 + 12TiCl\u2084", "Delta1": 22.184000000000083, "Delta2": -0.5, "Delta3": 0.16999999999995907, "Delta4": -17.553999999999974, "d1": -0.021763098138381388, "d2": -0.021154646501768178, "d3": -0.02136152005821662, "d4": -0.0, "solveff": 4.300000000000068, "variable": "delta1", "value": 22.184000000000083}, {"mol": "V", "x_labels": "V", "where": "SO-ZORA", "row": 4, "charge": 0, "solvent": "6C\u2086H\u2086", "complex": "VOCl\u2083 + 6C\u2086H\u2086", "Delta1": 2.814000000000078, "Delta2": -1.7970000000000255, "Delta3": -1.122000000000071, "Delta4": -25.22699999999986, "d1": -0.015131346929647224, "d2": -0.014165276069397935, "d3": -0.013562086584033574, "d4": -0.0, "solveff": -25.33199999999988, "variable": "delta1", "value": 2.814000000000078}, {"mol": "Cr", "x_labels": "Cr", "where": "SO-ZORA", "row": 4, "charge": -2, "solvent": "12H\u2082O", "complex": "CrO\u2084\u00b2\u207b + 12H\u2082O", "Delta1": 123.04400000000032, "Delta2": -19.00500000000011, "Delta3": -27.38500000000022, "Delta4": -12.795999999999822, "d1": -0.021712884105380876, "d2": -0.014740739300481661, "d3": -0.004694320701051754, "d4": -0.0, "solveff": 63.858000000000175, "variable": "delta1", "value": 123.04400000000032}, {"mol": "Mn", "x_labels": "Mn", "where": "SO-ZORA", "row": 4, "charge": -1, "solvent": "12H\u2082O", "complex": "MnO\u2084\u207b + 12H\u2082O", "Delta1": 35.49200000000019, "Delta2": -6.351000000000113, "Delta3": -13.909999999999854, "Delta4": -22.472999999999956, "d1": -0.010860140180028139, "d2": -0.009246138441755099, "d3": -0.005711141720077042, "d4": -0.0, "solveff": -7.241999999999734, "variable": "delta1", "value": 35.49200000000019}, {"mol": "Fe", "x_labels": "Fe", "where": "SO-ZORA", "row": 4, "charge": 0, "solvent": "6C\u2086H\u2086", "complex": "Fe(CO)\u2085 + 6C\u2086H\u2086", "Delta1": 67.43199999999979, "Delta2": -2.0169999999998254, "Delta3": -0.8409999999998945, "Delta4": -17.46199999999999, "d1": -0.008717314895411402, "d2": -0.007852018431629732, "d3": -0.007491227987385629, "d4": -0.0, "solveff": 47.11200000000008, "variable": "delta1", "value": 67.43199999999979}, {"mol": "Co", "x_labels": "Co", "where": "SO-ZORA", "row": 4, "charge": -3, "solvent": "12H\u2082O", "complex": "Co(CN)\u2086\u00b3\u207b + 12H\u2082O", "Delta1": 1098.3239999999996, "Delta2": 29.27599999999984, "Delta3": -3.118999999999687, "Delta4": -27.10699999999997, "d1": -0.0001645170921136079, "d2": -0.005234414343396641, "d3": -0.004694278753604651, "d4": -0.0, "solveff": 1097.3739999999998, "variable": "delta1", "value": 1098.3239999999996}, {"mol": "Ni", "x_labels": "Ni", "where": "SO-ZORA", "row": 4, "charge": 0, "solvent": "6C\u2086H\u2086", "complex": "Ni(CO)\u2084 + 6C\u2086H\u2086", "Delta1": -9.3599999999999, "Delta2": -8.592000000000098, "Delta3": -3.5049999999998818, "Delta4": -6.086999999999989, "d1": -0.011156635879161785, "d2": -0.005885088613776869, "d3": -0.0037346261876626584, "d4": -0.0, "solveff": -27.54399999999987, "variable": "delta1", "value": -9.3599999999999}, {"mol": "Cu", "x_labels": "Cu", "where": "SO-ZORA", "row": 4, "charge": 1, "solvent": "12CH\u2083CN", "complex": "Cu(CH\u2083CN)\u2084\u207a + 12CH\u2083CN", "Delta1": -23.295999999999992, "Delta2": -21.591999999999985, "Delta3": -2.983000000000004, "Delta4": -5.593000000000018, "d1": 0.06319613047294453, "d2": 0.01796506281278088, "d3": 0.01171625423412821, "d4": 0.0, "solveff": -53.464, "variable": "delta1", "value": -23.295999999999992}, {"mol": "Zn", "x_labels": "Zn", "where": "SO-ZORA", "row": 4, "charge": 2, "solvent": "12H\u2082O", "complex": "Zn(H\u2082O)\u2086\u00b2\u207a + 12H\u2082O", "Delta1": -29.680999999999813, "Delta2": -0.6110000000001037, "Delta3": 0.1710000000000491, "Delta4": -13.025000000000091, "d1": 0.007034567861245903, "d2": 0.00671536095718189, "d3": 0.0068046970956351675, "d4": 0.0, "solveff": -43.14599999999996, "variable": "delta1", "value": -29.680999999999813}, {"mol": "Nb", "x_labels": "Nb", "where": "SO-ZORA", "row": 5, "charge": -1, "solvent": "12CH\u2083CN", "complex": "NbCl\u2086\u207b + 12CH\u2083CN", "Delta1": 15.894000000000005, "Delta2": -12.683999999999969, "Delta3": -3.031000000000006, "Delta4": -19.694000000000017, "d1": -0.11137042011203403, "d2": -0.07147597495116995, "d3": -0.061942699700257016, "d4": -0.0, "solveff": -19.514999999999986, "variable": "delta1", "value": 15.894000000000005}, {"mol": "Mo", "x_labels": "Mo", "where": "SO-ZORA", "row": 5, "charge": -2, "solvent": "12H\u2082O", "complex": "MoO\u2084\u00b2\u207b + 12H\u2082O", "Delta1": 69.5, "Delta2": -12.021000000000015, "Delta3": -19.468000000000018, "Delta4": -21.753999999999905, "d1": -0.09521501751650152, "d2": -0.07371773664266144, "d3": -0.03890290725642746, "d4": -0.0, "solveff": 16.257000000000062, "variable": "delta1", "value": 69.5}, {"mol": "Tc", "x_labels": "Tc", "where": "SO-ZORA", "row": 5, "charge": -1, "solvent": "12H\u2082O", "complex": "TcO\u2084\u207b + 12H\u2082O", "Delta1": 22.694999999999936, "Delta2": 6.183999999999969, "Delta3": -4.981999999999971, "Delta4": 3.0920000000000982, "d1": 0.003025801759954068, "d2": -0.0013318037555455694, "d3": 0.002178802757749899, "d4": -0.0, "solveff": 26.989000000000033, "variable": "delta1", "value": 22.694999999999936}, {"mol": "Ru", "x_labels": "Ru", "where": "SO-ZORA", "row": 5, "charge": -4, "solvent": "12H\u2082O", "complex": "Ru(CN)\u2086\u2074\u207b + 12H\u2082O", "Delta1": 828.1270000000001, "Delta2": 3.6370000000000005, "Delta3": 2.411999999999999, "Delta4": -46.415, "d1": -0.04974484294377672, "d2": -0.05422688212988671, "d3": -0.057199298549160095, "d4": -0.0, "solveff": 787.761, "variable": "delta1", "value": 828.1270000000001}, {"mol": "Pd", "x_labels": "Pd", "where": "SO-ZORA", "row": 5, "charge": -2, "solvent": "12H\u2082O", "complex": "PdCl\u2086\u00b2\u207b + 12H\u2082O", "Delta1": 202.9269999999999, "Delta2": 35.669000000000096, "Delta3": 31.88799999999992, "Delta4": 0.48500000000012733, "d1": 0.04304883596306927, "d2": 0.02048176077470446, "d3": 0.0003068499668159965, "d4": -0.0, "solveff": 270.96900000000005, "variable": "delta1", "value": 202.9269999999999}, {"mol": "Ag", "x_labels": "Ag", "where": "SO-ZORA", "row": 5, "charge": 1, "solvent": "12H\u2082O", "complex": "Ag(H\u2082O)\u2084\u207a + 12H\u2082O", "Delta1": -175.03999999999996, "Delta2": 0.7719999999999345, "Delta3": 0.8350000000000364, "Delta4": -36.73400000000038, "d1": 0.008140764518531128, "d2": 0.008319677326579223, "d3": 0.008513190532175308, "d4": 0.0, "solveff": -210.16700000000037, "variable": "delta1", "value": -175.03999999999996}, {"mol": "Cd", "x_labels": "Cd", "where": "SO-ZORA", "row": 5, "charge": 0, "solvent": "6Cd(CH\u2083)\u2082", "complex": "Cd(CH\u2083)\u2082 + 6Cd(CH\u2083)\u2082", "Delta1": 6.981999999999971, "Delta2": 15.735000000000127, "Delta3": 24.100999999999658, "Delta4": 8.080000000000382, "d1": -0.013863777019912952, "d2": -0.009311090414012391, "d3": -0.0023378269955944063, "d4": 0.0, "solveff": 54.89800000000014, "variable": "delta1", "value": 6.981999999999971}, {"mol": "Ta", "x_labels": "Ta", "where": "SO-ZORA", "row": 6, "charge": -1, "solvent": "12CH\u2083CN", "complex": "TaCl\u2086\u207b + 12CH\u2083CN", "Delta1": 11.592999999999847, "Delta2": -18.442000000000007, "Delta3": -4.746999999999844, "Delta4": 42.23099999999977, "d1": -0.0055832686076456115, "d2": -0.010990612356317014, "d3": -0.012382471198901457, "d4": 0.0, "solveff": 30.634999999999764, "variable": "delta1", "value": 11.592999999999847}, {"mol": "W", "x_labels": "W", "where": "SO-ZORA", "row": 6, "charge": -2, "solvent": "12H\u2082O", "complex": "WO\u2084\u00b2\u207b + 12H\u2082O", "Delta1": 85.21000000000004, "Delta2": -32.8159999999998, "Delta3": -27.295000000000073, "Delta4": -99.5, "d1": 0.05316830974068907, "d2": 0.04223691245321864, "d3": 0.03314462549071534, "d4": 0.0, "solveff": -74.40099999999984, "variable": "delta1", "value": 85.21000000000004}, {"mol": "Re", "x_labels": "Re", "where": "SO-ZORA", "row": 6, "charge": -1, "solvent": "12H\u2082O", "complex": "ReO\u2084\u207b + 12H\u2082O", "Delta1": 21.605000000000018, "Delta2": -10.048999999999978, "Delta3": -8.81899999999996, "Delta4": -55.559999999999945, "d1": 0.03932442385502771, "d2": 0.03401498204120532, "d3": 0.029355417173447373, "d4": 0.0, "solveff": -52.822999999999865, "variable": "delta1", "value": 21.605000000000018}, {"mol": "Os", "x_labels": "Os", "where": "SO-ZORA", "row": 6, "charge": 0, "solvent": "12CCl\u2084", "complex": "OsO\u2084 + 12CCl\u2084", "Delta1": 132.73399999999992, "Delta2": 2.826000000000022, "Delta3": 2.0270000000000437, "Delta4": -16.411000000000058, "d1": 0.009486133567518316, "d2": 0.011805549855959828, "d3": 0.013469193457046525, "d4": 0.0, "solveff": 121.17599999999993, "variable": "delta1", "value": 132.73399999999992}, {"mol": "Pt", "x_labels": "Pt", "where": "SO-ZORA", "row": 6, "charge": -2, "solvent": "12H\u2082O", "complex": "PtCl\u2086\u00b2\u207b + 12H\u2082O", "Delta1": 292.2940000000001, "Delta2": 78.86199999999963, "Delta3": 56.90300000000025, "Delta4": 70.42900000000009, "d1": -0.10937188371234462, "d2": -0.06754095995451033, "d3": -0.0373577911965272, "d4": 0.0, "solveff": 498.48800000000006, "variable": "delta1", "value": 292.2940000000001}, {"mol": "Hg", "x_labels": "Hg", "where": "SO-ZORA", "row": 6, "charge": 0, "solvent": "6Hg(CH\u2083)\u2082", "complex": "Hg(CH\u2083)\u2082 + 6Hg(CH\u2083)\u2082", "Delta1": 31.950000000000728, "Delta2": 35.2450000000008, "Delta3": 28.27599999999984, "Delta4": -55.39500000000044, "d1": -0.0009148227516846377, "d2": 0.003053049249684422, "d3": 0.006236353227857449, "d4": 0.0, "solveff": 40.07600000000093, "variable": "delta1", "value": 31.950000000000728}, {"mol": "Ti", "x_labels": "Ti", "where": "SO-ZORA", "row": 4, "charge": 0, "solvent": "12TiCl\u2084", "complex": "TiCl\u2084 + 12TiCl\u2084", "Delta1": 22.184000000000083, "Delta2": -0.5, "Delta3": 0.16999999999995907, "Delta4": -17.553999999999974, "d1": -0.021763098138381388, "d2": -0.021154646501768178, "d3": -0.02136152005821662, "d4": -0.0, "solveff": 4.300000000000068, "variable": "delta2", "value": 21.684000000000083}, {"mol": "V", "x_labels": "V", "where": "SO-ZORA", "row": 4, "charge": 0, "solvent": "6C\u2086H\u2086", "complex": "VOCl\u2083 + 6C\u2086H\u2086", "Delta1": 2.814000000000078, "Delta2": -1.7970000000000255, "Delta3": -1.122000000000071, "Delta4": -25.22699999999986, "d1": -0.015131346929647224, "d2": -0.014165276069397935, "d3": -0.013562086584033574, "d4": -0.0, "solveff": -25.33199999999988, "variable": "delta2", "value": 1.0170000000000528}, {"mol": "Cr", "x_labels": "Cr", "where": "SO-ZORA", "row": 4, "charge": -2, "solvent": "12H\u2082O", "complex": "CrO\u2084\u00b2\u207b + 12H\u2082O", "Delta1": 123.04400000000032, "Delta2": -19.00500000000011, "Delta3": -27.38500000000022, "Delta4": -12.795999999999822, "d1": -0.021712884105380876, "d2": -0.014740739300481661, "d3": -0.004694320701051754, "d4": -0.0, "solveff": 63.858000000000175, "variable": "delta2", "value": 104.03900000000021}, {"mol": "Mn", "x_labels": "Mn", "where": "SO-ZORA", "row": 4, "charge": -1, "solvent": "12H\u2082O", "complex": "MnO\u2084\u207b + 12H\u2082O", "Delta1": 35.49200000000019, "Delta2": -6.351000000000113, "Delta3": -13.909999999999854, "Delta4": -22.472999999999956, "d1": -0.010860140180028139, "d2": -0.009246138441755099, "d3": -0.005711141720077042, "d4": -0.0, "solveff": -7.241999999999734, "variable": "delta2", "value": 29.141000000000076}, {"mol": "Fe", "x_labels": "Fe", "where": "SO-ZORA", "row": 4, "charge": 0, "solvent": "6C\u2086H\u2086", "complex": "Fe(CO)\u2085 + 6C\u2086H\u2086", "Delta1": 67.43199999999979, "Delta2": -2.0169999999998254, "Delta3": -0.8409999999998945, "Delta4": -17.46199999999999, "d1": -0.008717314895411402, "d2": -0.007852018431629732, "d3": -0.007491227987385629, "d4": -0.0, "solveff": 47.11200000000008, "variable": "delta2", "value": 65.41499999999996}, {"mol": "Co", "x_labels": "Co", "where": "SO-ZORA", "row": 4, "charge": -3, "solvent": "12H\u2082O", "complex": "Co(CN)\u2086\u00b3\u207b + 12H\u2082O", "Delta1": 1098.3239999999996, "Delta2": 29.27599999999984, "Delta3": -3.118999999999687, "Delta4": -27.10699999999997, "d1": -0.0001645170921136079, "d2": -0.005234414343396641, "d3": -0.004694278753604651, "d4": -0.0, "solveff": 1097.3739999999998, "variable": "delta2", "value": 1127.5999999999995}, {"mol": "Ni", "x_labels": "Ni", "where": "SO-ZORA", "row": 4, "charge": 0, "solvent": "6C\u2086H\u2086", "complex": "Ni(CO)\u2084 + 6C\u2086H\u2086", "Delta1": -9.3599999999999, "Delta2": -8.592000000000098, "Delta3": -3.5049999999998818, "Delta4": -6.086999999999989, "d1": -0.011156635879161785, "d2": -0.005885088613776869, "d3": -0.0037346261876626584, "d4": -0.0, "solveff": -27.54399999999987, "variable": "delta2", "value": -17.951999999999998}, {"mol": "Cu", "x_labels": "Cu", "where": "SO-ZORA", "row": 4, "charge": 1, "solvent": "12CH\u2083CN", "complex": "Cu(CH\u2083CN)\u2084\u207a + 12CH\u2083CN", "Delta1": -23.295999999999992, "Delta2": -21.591999999999985, "Delta3": -2.983000000000004, "Delta4": -5.593000000000018, "d1": 0.06319613047294453, "d2": 0.01796506281278088, "d3": 0.01171625423412821, "d4": 0.0, "solveff": -53.464, "variable": "delta2", "value": -44.88799999999998}, {"mol": "Zn", "x_labels": "Zn", "where": "SO-ZORA", "row": 4, "charge": 2, "solvent": "12H\u2082O", "complex": "Zn(H\u2082O)\u2086\u00b2\u207a + 12H\u2082O", "Delta1": -29.680999999999813, "Delta2": -0.6110000000001037, "Delta3": 0.1710000000000491, "Delta4": -13.025000000000091, "d1": 0.007034567861245903, "d2": 0.00671536095718189, "d3": 0.0068046970956351675, "d4": 0.0, "solveff": -43.14599999999996, "variable": "delta2", "value": -30.291999999999916}, {"mol": "Nb", "x_labels": "Nb", "where": "SO-ZORA", "row": 5, "charge": -1, "solvent": "12CH\u2083CN", "complex": "NbCl\u2086\u207b + 12CH\u2083CN", "Delta1": 15.894000000000005, "Delta2": -12.683999999999969, "Delta3": -3.031000000000006, "Delta4": -19.694000000000017, "d1": -0.11137042011203403, "d2": -0.07147597495116995, "d3": -0.061942699700257016, "d4": -0.0, "solveff": -19.514999999999986, "variable": "delta2", "value": 3.2100000000000364}, {"mol": "Mo", "x_labels": "Mo", "where": "SO-ZORA", "row": 5, "charge": -2, "solvent": "12H\u2082O", "complex": "MoO\u2084\u00b2\u207b + 12H\u2082O", "Delta1": 69.5, "Delta2": -12.021000000000015, "Delta3": -19.468000000000018, "Delta4": -21.753999999999905, "d1": -0.09521501751650152, "d2": -0.07371773664266144, "d3": -0.03890290725642746, "d4": -0.0, "solveff": 16.257000000000062, "variable": "delta2", "value": 57.478999999999985}, {"mol": "Tc", "x_labels": "Tc", "where": "SO-ZORA", "row": 5, "charge": -1, "solvent": "12H\u2082O", "complex": "TcO\u2084\u207b + 12H\u2082O", "Delta1": 22.694999999999936, "Delta2": 6.183999999999969, "Delta3": -4.981999999999971, "Delta4": 3.0920000000000982, "d1": 0.003025801759954068, "d2": -0.0013318037555455694, "d3": 0.002178802757749899, "d4": -0.0, "solveff": 26.989000000000033, "variable": "delta2", "value": 28.878999999999905}, {"mol": "Ru", "x_labels": "Ru", "where": "SO-ZORA", "row": 5, "charge": -4, "solvent": "12H\u2082O", "complex": "Ru(CN)\u2086\u2074\u207b + 12H\u2082O", "Delta1": 828.1270000000001, "Delta2": 3.6370000000000005, "Delta3": 2.411999999999999, "Delta4": -46.415, "d1": -0.04974484294377672, "d2": -0.05422688212988671, "d3": -0.057199298549160095, "d4": -0.0, "solveff": 787.761, "variable": "delta2", "value": 831.764}, {"mol": "Pd", "x_labels": "Pd", "where": "SO-ZORA", "row": 5, "charge": -2, "solvent": "12H\u2082O", "complex": "PdCl\u2086\u00b2\u207b + 12H\u2082O", "Delta1": 202.9269999999999, "Delta2": 35.669000000000096, "Delta3": 31.88799999999992, "Delta4": 0.48500000000012733, "d1": 0.04304883596306927, "d2": 0.02048176077470446, "d3": 0.0003068499668159965, "d4": -0.0, "solveff": 270.96900000000005, "variable": "delta2", "value": 238.596}, {"mol": "Ag", "x_labels": "Ag", "where": "SO-ZORA", "row": 5, "charge": 1, "solvent": "12H\u2082O", "complex": "Ag(H\u2082O)\u2084\u207a + 12H\u2082O", "Delta1": -175.03999999999996, "Delta2": 0.7719999999999345, "Delta3": 0.8350000000000364, "Delta4": -36.73400000000038, "d1": 0.008140764518531128, "d2": 0.008319677326579223, "d3": 0.008513190532175308, "d4": 0.0, "solveff": -210.16700000000037, "variable": "delta2", "value": -174.26800000000003}, {"mol": "Cd", "x_labels": "Cd", "where": "SO-ZORA", "row": 5, "charge": 0, "solvent": "6Cd(CH\u2083)\u2082", "complex": "Cd(CH\u2083)\u2082 + 6Cd(CH\u2083)\u2082", "Delta1": 6.981999999999971, "Delta2": 15.735000000000127, "Delta3": 24.100999999999658, "Delta4": 8.080000000000382, "d1": -0.013863777019912952, "d2": -0.009311090414012391, "d3": -0.0023378269955944063, "d4": 0.0, "solveff": 54.89800000000014, "variable": "delta2", "value": 22.7170000000001}, {"mol": "Ta", "x_labels": "Ta", "where": "SO-ZORA", "row": 6, "charge": -1, "solvent": "12CH\u2083CN", "complex": "TaCl\u2086\u207b + 12CH\u2083CN", "Delta1": 11.592999999999847, "Delta2": -18.442000000000007, "Delta3": -4.746999999999844, "Delta4": 42.23099999999977, "d1": -0.0055832686076456115, "d2": -0.010990612356317014, "d3": -0.012382471198901457, "d4": 0.0, "solveff": 30.634999999999764, "variable": "delta2", "value": -6.84900000000016}, {"mol": "W", "x_labels": "W", "where": "SO-ZORA", "row": 6, "charge": -2, "solvent": "12H\u2082O", "complex": "WO\u2084\u00b2\u207b + 12H\u2082O", "Delta1": 85.21000000000004, "Delta2": -32.8159999999998, "Delta3": -27.295000000000073, "Delta4": -99.5, "d1": 0.05316830974068907, "d2": 0.04223691245321864, "d3": 0.03314462549071534, "d4": 0.0, "solveff": -74.40099999999984, "variable": "delta2", "value": 52.39400000000023}, {"mol": "Re", "x_labels": "Re", "where": "SO-ZORA", "row": 6, "charge": -1, "solvent": "12H\u2082O", "complex": "ReO\u2084\u207b + 12H\u2082O", "Delta1": 21.605000000000018, "Delta2": -10.048999999999978, "Delta3": -8.81899999999996, "Delta4": -55.559999999999945, "d1": 0.03932442385502771, "d2": 0.03401498204120532, "d3": 0.029355417173447373, "d4": 0.0, "solveff": -52.822999999999865, "variable": "delta2", "value": 11.55600000000004}, {"mol": "Os", "x_labels": "Os", "where": "SO-ZORA", "row": 6, "charge": 0, "solvent": "12CCl\u2084", "complex": "OsO\u2084 + 12CCl\u2084", "Delta1": 132.73399999999992, "Delta2": 2.826000000000022, "Delta3": 2.0270000000000437, "Delta4": -16.411000000000058, "d1": 0.009486133567518316, "d2": 0.011805549855959828, "d3": 0.013469193457046525, "d4": 0.0, "solveff": 121.17599999999993, "variable": "delta2", "value": 135.55999999999995}, {"mol": "Pt", "x_labels": "Pt", "where": "SO-ZORA", "row": 6, "charge": -2, "solvent": "12H\u2082O", "complex": "PtCl\u2086\u00b2\u207b + 12H\u2082O", "Delta1": 292.2940000000001, "Delta2": 78.86199999999963, "Delta3": 56.90300000000025, "Delta4": 70.42900000000009, "d1": -0.10937188371234462, "d2": -0.06754095995451033, "d3": -0.0373577911965272, "d4": 0.0, "solveff": 498.48800000000006, "variable": "delta2", "value": 371.1559999999997}, {"mol": "Hg", "x_labels": "Hg", "where": "SO-ZORA", "row": 6, "charge": 0, "solvent": "6Hg(CH\u2083)\u2082", "complex": "Hg(CH\u2083)\u2082 + 6Hg(CH\u2083)\u2082", "Delta1": 31.950000000000728, "Delta2": 35.2450000000008, "Delta3": 28.27599999999984, "Delta4": -55.39500000000044, "d1": -0.0009148227516846377, "d2": 0.003053049249684422, "d3": 0.006236353227857449, "d4": 0.0, "solveff": 40.07600000000093, "variable": "delta2", "value": 67.19500000000153}, {"mol": "Ti", "x_labels": "Ti", "where": "SO-ZORA", "row": 4, "charge": 0, "solvent": "12TiCl\u2084", "complex": "TiCl\u2084 + 12TiCl\u2084", "Delta1": 22.184000000000083, "Delta2": -0.5, "Delta3": 0.16999999999995907, "Delta4": -17.553999999999974, "d1": -0.021763098138381388, "d2": -0.021154646501768178, "d3": -0.02136152005821662, "d4": -0.0, "solveff": 4.300000000000068, "variable": "delta3", "value": 21.854000000000042}, {"mol": "V", "x_labels": "V", "where": "SO-ZORA", "row": 4, "charge": 0, "solvent": "6C\u2086H\u2086", "complex": "VOCl\u2083 + 6C\u2086H\u2086", "Delta1": 2.814000000000078, "Delta2": -1.7970000000000255, "Delta3": -1.122000000000071, "Delta4": -25.22699999999986, "d1": -0.015131346929647224, "d2": -0.014165276069397935, "d3": -0.013562086584033574, "d4": -0.0, "solveff": -25.33199999999988, "variable": "delta3", "value": -0.10500000000001819}, {"mol": "Cr", "x_labels": "Cr", "where": "SO-ZORA", "row": 4, "charge": -2, "solvent": "12H\u2082O", "complex": "CrO\u2084\u00b2\u207b + 12H\u2082O", "Delta1": 123.04400000000032, "Delta2": -19.00500000000011, "Delta3": -27.38500000000022, "Delta4": -12.795999999999822, "d1": -0.021712884105380876, "d2": -0.014740739300481661, "d3": -0.004694320701051754, "d4": -0.0, "solveff": 63.858000000000175, "variable": "delta3", "value": 76.654}, {"mol": "Mn", "x_labels": "Mn", "where": "SO-ZORA", "row": 4, "charge": -1, "solvent": "12H\u2082O", "complex": "MnO\u2084\u207b + 12H\u2082O", "Delta1": 35.49200000000019, "Delta2": -6.351000000000113, "Delta3": -13.909999999999854, "Delta4": -22.472999999999956, "d1": -0.010860140180028139, "d2": -0.009246138441755099, "d3": -0.005711141720077042, "d4": -0.0, "solveff": -7.241999999999734, "variable": "delta3", "value": 15.231000000000222}, {"mol": "Fe", "x_labels": "Fe", "where": "SO-ZORA", "row": 4, "charge": 0, "solvent": "6C\u2086H\u2086", "complex": "Fe(CO)\u2085 + 6C\u2086H\u2086", "Delta1": 67.43199999999979, "Delta2": -2.0169999999998254, "Delta3": -0.8409999999998945, "Delta4": -17.46199999999999, "d1": -0.008717314895411402, "d2": -0.007852018431629732, "d3": -0.007491227987385629, "d4": -0.0, "solveff": 47.11200000000008, "variable": "delta3", "value": 64.57400000000007}, {"mol": "Co", "x_labels": "Co", "where": "SO-ZORA", "row": 4, "charge": -3, "solvent": "12H\u2082O", "complex": "Co(CN)\u2086\u00b3\u207b + 12H\u2082O", "Delta1": 1098.3239999999996, "Delta2": 29.27599999999984, "Delta3": -3.118999999999687, "Delta4": -27.10699999999997, "d1": -0.0001645170921136079, "d2": -0.005234414343396641, "d3": -0.004694278753604651, "d4": -0.0, "solveff": 1097.3739999999998, "variable": "delta3", "value": 1124.4809999999998}, {"mol": "Ni", "x_labels": "Ni", "where": "SO-ZORA", "row": 4, "charge": 0, "solvent": "6C\u2086H\u2086", "complex": "Ni(CO)\u2084 + 6C\u2086H\u2086", "Delta1": -9.3599999999999, "Delta2": -8.592000000000098, "Delta3": -3.5049999999998818, "Delta4": -6.086999999999989, "d1": -0.011156635879161785, "d2": -0.005885088613776869, "d3": -0.0037346261876626584, "d4": -0.0, "solveff": -27.54399999999987, "variable": "delta3", "value": -21.45699999999988}, {"mol": "Cu", "x_labels": "Cu", "where": "SO-ZORA", "row": 4, "charge": 1, "solvent": "12CH\u2083CN", "complex": "Cu(CH\u2083CN)\u2084\u207a + 12CH\u2083CN", "Delta1": -23.295999999999992, "Delta2": -21.591999999999985, "Delta3": -2.983000000000004, "Delta4": -5.593000000000018, "d1": 0.06319613047294453, "d2": 0.01796506281278088, "d3": 0.01171625423412821, "d4": 0.0, "solveff": -53.464, "variable": "delta3", "value": -47.87099999999998}, {"mol": "Zn", "x_labels": "Zn", "where": "SO-ZORA", "row": 4, "charge": 2, "solvent": "12H\u2082O", "complex": "Zn(H\u2082O)\u2086\u00b2\u207a + 12H\u2082O", "Delta1": -29.680999999999813, "Delta2": -0.6110000000001037, "Delta3": 0.1710000000000491, "Delta4": -13.025000000000091, "d1": 0.007034567861245903, "d2": 0.00671536095718189, "d3": 0.0068046970956351675, "d4": 0.0, "solveff": -43.14599999999996, "variable": "delta3", "value": -30.120999999999867}, {"mol": "Nb", "x_labels": "Nb", "where": "SO-ZORA", "row": 5, "charge": -1, "solvent": "12CH\u2083CN", "complex": "NbCl\u2086\u207b + 12CH\u2083CN", "Delta1": 15.894000000000005, "Delta2": -12.683999999999969, "Delta3": -3.031000000000006, "Delta4": -19.694000000000017, "d1": -0.11137042011203403, "d2": -0.07147597495116995, "d3": -0.061942699700257016, "d4": -0.0, "solveff": -19.514999999999986, "variable": "delta3", "value": 0.17900000000003047}, {"mol": "Mo", "x_labels": "Mo", "where": "SO-ZORA", "row": 5, "charge": -2, "solvent": "12H\u2082O", "complex": "MoO\u2084\u00b2\u207b + 12H\u2082O", "Delta1": 69.5, "Delta2": -12.021000000000015, "Delta3": -19.468000000000018, "Delta4": -21.753999999999905, "d1": -0.09521501751650152, "d2": -0.07371773664266144, "d3": -0.03890290725642746, "d4": -0.0, "solveff": 16.257000000000062, "variable": "delta3", "value": 38.01099999999997}, {"mol": "Tc", "x_labels": "Tc", "where": "SO-ZORA", "row": 5, "charge": -1, "solvent": "12H\u2082O", "complex": "TcO\u2084\u207b + 12H\u2082O", "Delta1": 22.694999999999936, "Delta2": 6.183999999999969, "Delta3": -4.981999999999971, "Delta4": 3.0920000000000982, "d1": 0.003025801759954068, "d2": -0.0013318037555455694, "d3": 0.002178802757749899, "d4": -0.0, "solveff": 26.989000000000033, "variable": "delta3", "value": 23.896999999999935}, {"mol": "Ru", "x_labels": "Ru", "where": "SO-ZORA", "row": 5, "charge": -4, "solvent": "12H\u2082O", "complex": "Ru(CN)\u2086\u2074\u207b + 12H\u2082O", "Delta1": 828.1270000000001, "Delta2": 3.6370000000000005, "Delta3": 2.411999999999999, "Delta4": -46.415, "d1": -0.04974484294377672, "d2": -0.05422688212988671, "d3": -0.057199298549160095, "d4": -0.0, "solveff": 787.761, "variable": "delta3", "value": 834.176}, {"mol": "Pd", "x_labels": "Pd", "where": "SO-ZORA", "row": 5, "charge": -2, "solvent": "12H\u2082O", "complex": "PdCl\u2086\u00b2\u207b + 12H\u2082O", "Delta1": 202.9269999999999, "Delta2": 35.669000000000096, "Delta3": 31.88799999999992, "Delta4": 0.48500000000012733, "d1": 0.04304883596306927, "d2": 0.02048176077470446, "d3": 0.0003068499668159965, "d4": -0.0, "solveff": 270.96900000000005, "variable": "delta3", "value": 270.4839999999999}, {"mol": "Ag", "x_labels": "Ag", "where": "SO-ZORA", "row": 5, "charge": 1, "solvent": "12H\u2082O", "complex": "Ag(H\u2082O)\u2084\u207a + 12H\u2082O", "Delta1": -175.03999999999996, "Delta2": 0.7719999999999345, "Delta3": 0.8350000000000364, "Delta4": -36.73400000000038, "d1": 0.008140764518531128, "d2": 0.008319677326579223, "d3": 0.008513190532175308, "d4": 0.0, "solveff": -210.16700000000037, "variable": "delta3", "value": -173.433}, {"mol": "Cd", "x_labels": "Cd", "where": "SO-ZORA", "row": 5, "charge": 0, "solvent": "6Cd(CH\u2083)\u2082", "complex": "Cd(CH\u2083)\u2082 + 6Cd(CH\u2083)\u2082", "Delta1": 6.981999999999971, "Delta2": 15.735000000000127, "Delta3": 24.100999999999658, "Delta4": 8.080000000000382, "d1": -0.013863777019912952, "d2": -0.009311090414012391, "d3": -0.0023378269955944063, "d4": 0.0, "solveff": 54.89800000000014, "variable": "delta3", "value": 46.817999999999756}, {"mol": "Ta", "x_labels": "Ta", "where": "SO-ZORA", "row": 6, "charge": -1, "solvent": "12CH\u2083CN", "complex": "TaCl\u2086\u207b + 12CH\u2083CN", "Delta1": 11.592999999999847, "Delta2": -18.442000000000007, "Delta3": -4.746999999999844, "Delta4": 42.23099999999977, "d1": -0.0055832686076456115, "d2": -0.010990612356317014, "d3": -0.012382471198901457, "d4": 0.0, "solveff": 30.634999999999764, "variable": "delta3", "value": -11.596000000000004}, {"mol": "W", "x_labels": "W", "where": "SO-ZORA", "row": 6, "charge": -2, "solvent": "12H\u2082O", "complex": "WO\u2084\u00b2\u207b + 12H\u2082O", "Delta1": 85.21000000000004, "Delta2": -32.8159999999998, "Delta3": -27.295000000000073, "Delta4": -99.5, "d1": 0.05316830974068907, "d2": 0.04223691245321864, "d3": 0.03314462549071534, "d4": 0.0, "solveff": -74.40099999999984, "variable": "delta3", "value": 25.09900000000016}, {"mol": "Re", "x_labels": "Re", "where": "SO-ZORA", "row": 6, "charge": -1, "solvent": "12H\u2082O", "complex": "ReO\u2084\u207b + 12H\u2082O", "Delta1": 21.605000000000018, "Delta2": -10.048999999999978, "Delta3": -8.81899999999996, "Delta4": -55.559999999999945, "d1": 0.03932442385502771, "d2": 0.03401498204120532, "d3": 0.029355417173447373, "d4": 0.0, "solveff": -52.822999999999865, "variable": "delta3", "value": 2.73700000000008}, {"mol": "Os", "x_labels": "Os", "where": "SO-ZORA", "row": 6, "charge": 0, "solvent": "12CCl\u2084", "complex": "OsO\u2084 + 12CCl\u2084", "Delta1": 132.73399999999992, "Delta2": 2.826000000000022, "Delta3": 2.0270000000000437, "Delta4": -16.411000000000058, "d1": 0.009486133567518316, "d2": 0.011805549855959828, "d3": 0.013469193457046525, "d4": 0.0, "solveff": 121.17599999999993, "variable": "delta3", "value": 137.587}, {"mol": "Pt", "x_labels": "Pt", "where": "SO-ZORA", "row": 6, "charge": -2, "solvent": "12H\u2082O", "complex": "PtCl\u2086\u00b2\u207b + 12H\u2082O", "Delta1": 292.2940000000001, "Delta2": 78.86199999999963, "Delta3": 56.90300000000025, "Delta4": 70.42900000000009, "d1": -0.10937188371234462, "d2": -0.06754095995451033, "d3": -0.0373577911965272, "d4": 0.0, "solveff": 498.48800000000006, "variable": "delta3", "value": 428.05899999999997}, {"mol": "Hg", "x_labels": "Hg", "where": "SO-ZORA", "row": 6, "charge": 0, "solvent": "6Hg(CH\u2083)\u2082", "complex": "Hg(CH\u2083)\u2082 + 6Hg(CH\u2083)\u2082", "Delta1": 31.950000000000728, "Delta2": 35.2450000000008, "Delta3": 28.27599999999984, "Delta4": -55.39500000000044, "d1": -0.0009148227516846377, "d2": 0.003053049249684422, "d3": 0.006236353227857449, "d4": 0.0, "solveff": 40.07600000000093, "variable": "delta3", "value": 95.47100000000137}, {"mol": "Ti", "x_labels": "Ti", "where": "SO-ZORA", "row": 4, "charge": 0, "solvent": "12TiCl\u2084", "complex": "TiCl\u2084 + 12TiCl\u2084", "Delta1": 22.184000000000083, "Delta2": -0.5, "Delta3": 0.16999999999995907, "Delta4": -17.553999999999974, "d1": -0.021763098138381388, "d2": -0.021154646501768178, "d3": -0.02136152005821662, "d4": -0.0, "solveff": 4.300000000000068, "variable": "delta4", "value": 4.300000000000068}, {"mol": "V", "x_labels": "V", "where": "SO-ZORA", "row": 4, "charge": 0, "solvent": "6C\u2086H\u2086", "complex": "VOCl\u2083 + 6C\u2086H\u2086", "Delta1": 2.814000000000078, "Delta2": -1.7970000000000255, "Delta3": -1.122000000000071, "Delta4": -25.22699999999986, "d1": -0.015131346929647224, "d2": -0.014165276069397935, "d3": -0.013562086584033574, "d4": -0.0, "solveff": -25.33199999999988, "variable": "delta4", "value": -25.33199999999988}, {"mol": "Cr", "x_labels": "Cr", "where": "SO-ZORA", "row": 4, "charge": -2, "solvent": "12H\u2082O", "complex": "CrO\u2084\u00b2\u207b + 12H\u2082O", "Delta1": 123.04400000000032, "Delta2": -19.00500000000011, "Delta3": -27.38500000000022, "Delta4": -12.795999999999822, "d1": -0.021712884105380876, "d2": -0.014740739300481661, "d3": -0.004694320701051754, "d4": -0.0, "solveff": 63.858000000000175, "variable": "delta4", "value": 63.858000000000175}, {"mol": "Mn", "x_labels": "Mn", "where": "SO-ZORA", "row": 4, "charge": -1, "solvent": "12H\u2082O", "complex": "MnO\u2084\u207b + 12H\u2082O", "Delta1": 35.49200000000019, "Delta2": -6.351000000000113, "Delta3": -13.909999999999854, "Delta4": -22.472999999999956, "d1": -0.010860140180028139, "d2": -0.009246138441755099, "d3": -0.005711141720077042, "d4": -0.0, "solveff": -7.241999999999734, "variable": "delta4", "value": -7.241999999999734}, {"mol": "Fe", "x_labels": "Fe", "where": "SO-ZORA", "row": 4, "charge": 0, "solvent": "6C\u2086H\u2086", "complex": "Fe(CO)\u2085 + 6C\u2086H\u2086", "Delta1": 67.43199999999979, "Delta2": -2.0169999999998254, "Delta3": -0.8409999999998945, "Delta4": -17.46199999999999, "d1": -0.008717314895411402, "d2": -0.007852018431629732, "d3": -0.007491227987385629, "d4": -0.0, "solveff": 47.11200000000008, "variable": "delta4", "value": 47.11200000000008}, {"mol": "Co", "x_labels": "Co", "where": "SO-ZORA", "row": 4, "charge": -3, "solvent": "12H\u2082O", "complex": "Co(CN)\u2086\u00b3\u207b + 12H\u2082O", "Delta1": 1098.3239999999996, "Delta2": 29.27599999999984, "Delta3": -3.118999999999687, "Delta4": -27.10699999999997, "d1": -0.0001645170921136079, "d2": -0.005234414343396641, "d3": -0.004694278753604651, "d4": -0.0, "solveff": 1097.3739999999998, "variable": "delta4", "value": 1097.3739999999998}, {"mol": "Ni", "x_labels": "Ni", "where": "SO-ZORA", "row": 4, "charge": 0, "solvent": "6C\u2086H\u2086", "complex": "Ni(CO)\u2084 + 6C\u2086H\u2086", "Delta1": -9.3599999999999, "Delta2": -8.592000000000098, "Delta3": -3.5049999999998818, "Delta4": -6.086999999999989, "d1": -0.011156635879161785, "d2": -0.005885088613776869, "d3": -0.0037346261876626584, "d4": -0.0, "solveff": -27.54399999999987, "variable": "delta4", "value": -27.54399999999987}, {"mol": "Cu", "x_labels": "Cu", "where": "SO-ZORA", "row": 4, "charge": 1, "solvent": "12CH\u2083CN", "complex": "Cu(CH\u2083CN)\u2084\u207a + 12CH\u2083CN", "Delta1": -23.295999999999992, "Delta2": -21.591999999999985, "Delta3": -2.983000000000004, "Delta4": -5.593000000000018, "d1": 0.06319613047294453, "d2": 0.01796506281278088, "d3": 0.01171625423412821, "d4": 0.0, "solveff": -53.464, "variable": "delta4", "value": -53.464}, {"mol": "Zn", "x_labels": "Zn", "where": "SO-ZORA", "row": 4, "charge": 2, "solvent": "12H\u2082O", "complex": "Zn(H\u2082O)\u2086\u00b2\u207a + 12H\u2082O", "Delta1": -29.680999999999813, "Delta2": -0.6110000000001037, "Delta3": 0.1710000000000491, "Delta4": -13.025000000000091, "d1": 0.007034567861245903, "d2": 0.00671536095718189, "d3": 0.0068046970956351675, "d4": 0.0, "solveff": -43.14599999999996, "variable": "delta4", "value": -43.14599999999996}, {"mol": "Nb", "x_labels": "Nb", "where": "SO-ZORA", "row": 5, "charge": -1, "solvent": "12CH\u2083CN", "complex": "NbCl\u2086\u207b + 12CH\u2083CN", "Delta1": 15.894000000000005, "Delta2": -12.683999999999969, "Delta3": -3.031000000000006, "Delta4": -19.694000000000017, "d1": -0.11137042011203403, "d2": -0.07147597495116995, "d3": -0.061942699700257016, "d4": -0.0, "solveff": -19.514999999999986, "variable": "delta4", "value": -19.514999999999986}, {"mol": "Mo", "x_labels": "Mo", "where": "SO-ZORA", "row": 5, "charge": -2, "solvent": "12H\u2082O", "complex": "MoO\u2084\u00b2\u207b + 12H\u2082O", "Delta1": 69.5, "Delta2": -12.021000000000015, "Delta3": -19.468000000000018, "Delta4": -21.753999999999905, "d1": -0.09521501751650152, "d2": -0.07371773664266144, "d3": -0.03890290725642746, "d4": -0.0, "solveff": 16.257000000000062, "variable": "delta4", "value": 16.257000000000062}, {"mol": "Tc", "x_labels": "Tc", "where": "SO-ZORA", "row": 5, "charge": -1, "solvent": "12H\u2082O", "complex": "TcO\u2084\u207b + 12H\u2082O", "Delta1": 22.694999999999936, "Delta2": 6.183999999999969, "Delta3": -4.981999999999971, "Delta4": 3.0920000000000982, "d1": 0.003025801759954068, "d2": -0.0013318037555455694, "d3": 0.002178802757749899, "d4": -0.0, "solveff": 26.989000000000033, "variable": "delta4", "value": 26.989000000000033}, {"mol": "Ru", "x_labels": "Ru", "where": "SO-ZORA", "row": 5, "charge": -4, "solvent": "12H\u2082O", "complex": "Ru(CN)\u2086\u2074\u207b + 12H\u2082O", "Delta1": 828.1270000000001, "Delta2": 3.6370000000000005, "Delta3": 2.411999999999999, "Delta4": -46.415, "d1": -0.04974484294377672, "d2": -0.05422688212988671, "d3": -0.057199298549160095, "d4": -0.0, "solveff": 787.761, "variable": "delta4", "value": 787.761}, {"mol": "Pd", "x_labels": "Pd", "where": "SO-ZORA", "row": 5, "charge": -2, "solvent": "12H\u2082O", "complex": "PdCl\u2086\u00b2\u207b + 12H\u2082O", "Delta1": 202.9269999999999, "Delta2": 35.669000000000096, "Delta3": 31.88799999999992, "Delta4": 0.48500000000012733, "d1": 0.04304883596306927, "d2": 0.02048176077470446, "d3": 0.0003068499668159965, "d4": -0.0, "solveff": 270.96900000000005, "variable": "delta4", "value": 270.96900000000005}, {"mol": "Ag", "x_labels": "Ag", "where": "SO-ZORA", "row": 5, "charge": 1, "solvent": "12H\u2082O", "complex": "Ag(H\u2082O)\u2084\u207a + 12H\u2082O", "Delta1": -175.03999999999996, "Delta2": 0.7719999999999345, "Delta3": 0.8350000000000364, "Delta4": -36.73400000000038, "d1": 0.008140764518531128, "d2": 0.008319677326579223, "d3": 0.008513190532175308, "d4": 0.0, "solveff": -210.16700000000037, "variable": "delta4", "value": -210.16700000000037}, {"mol": "Cd", "x_labels": "Cd", "where": "SO-ZORA", "row": 5, "charge": 0, "solvent": "6Cd(CH\u2083)\u2082", "complex": "Cd(CH\u2083)\u2082 + 6Cd(CH\u2083)\u2082", "Delta1": 6.981999999999971, "Delta2": 15.735000000000127, "Delta3": 24.100999999999658, "Delta4": 8.080000000000382, "d1": -0.013863777019912952, "d2": -0.009311090414012391, "d3": -0.0023378269955944063, "d4": 0.0, "solveff": 54.89800000000014, "variable": "delta4", "value": 54.89800000000014}, {"mol": "Ta", "x_labels": "Ta", "where": "SO-ZORA", "row": 6, "charge": -1, "solvent": "12CH\u2083CN", "complex": "TaCl\u2086\u207b + 12CH\u2083CN", "Delta1": 11.592999999999847, "Delta2": -18.442000000000007, "Delta3": -4.746999999999844, "Delta4": 42.23099999999977, "d1": -0.0055832686076456115, "d2": -0.010990612356317014, "d3": -0.012382471198901457, "d4": 0.0, "solveff": 30.634999999999764, "variable": "delta4", "value": 30.634999999999764}, {"mol": "W", "x_labels": "W", "where": "SO-ZORA", "row": 6, "charge": -2, "solvent": "12H\u2082O", "complex": "WO\u2084\u00b2\u207b + 12H\u2082O", "Delta1": 85.21000000000004, "Delta2": -32.8159999999998, "Delta3": -27.295000000000073, "Delta4": -99.5, "d1": 0.05316830974068907, "d2": 0.04223691245321864, "d3": 0.03314462549071534, "d4": 0.0, "solveff": -74.40099999999984, "variable": "delta4", "value": -74.40099999999984}, {"mol": "Re", "x_labels": "Re", "where": "SO-ZORA", "row": 6, "charge": -1, "solvent": "12H\u2082O", "complex": "ReO\u2084\u207b + 12H\u2082O", "Delta1": 21.605000000000018, "Delta2": -10.048999999999978, "Delta3": -8.81899999999996, "Delta4": -55.559999999999945, "d1": 0.03932442385502771, "d2": 0.03401498204120532, "d3": 0.029355417173447373, "d4": 0.0, "solveff": -52.822999999999865, "variable": "delta4", "value": -52.822999999999865}, {"mol": "Os", "x_labels": "Os", "where": "SO-ZORA", "row": 6, "charge": 0, "solvent": "12CCl\u2084", "complex": "OsO\u2084 + 12CCl\u2084", "Delta1": 132.73399999999992, "Delta2": 2.826000000000022, "Delta3": 2.0270000000000437, "Delta4": -16.411000000000058, "d1": 0.009486133567518316, "d2": 0.011805549855959828, "d3": 0.013469193457046525, "d4": 0.0, "solveff": 121.17599999999993, "variable": "delta4", "value": 121.17599999999993}, {"mol": "Pt", "x_labels": "Pt", "where": "SO-ZORA", "row": 6, "charge": -2, "solvent": "12H\u2082O", "complex": "PtCl\u2086\u00b2\u207b + 12H\u2082O", "Delta1": 292.2940000000001, "Delta2": 78.86199999999963, "Delta3": 56.90300000000025, "Delta4": 70.42900000000009, "d1": -0.10937188371234462, "d2": -0.06754095995451033, "d3": -0.0373577911965272, "d4": 0.0, "solveff": 498.48800000000006, "variable": "delta4", "value": 498.48800000000006}, {"mol": "Hg", "x_labels": "Hg", "where": "SO-ZORA", "row": 6, "charge": 0, "solvent": "6Hg(CH\u2083)\u2082", "complex": "Hg(CH\u2083)\u2082 + 6Hg(CH\u2083)\u2082", "Delta1": 31.950000000000728, "Delta2": 35.2450000000008, "Delta3": 28.27599999999984, "Delta4": -55.39500000000044, "d1": -0.0009148227516846377, "d2": 0.003053049249684422, "d3": 0.006236353227857449, "d4": 0.0, "solveff": 40.07600000000093, "variable": "delta4", "value": 40.07600000000093}]}}, {"mode": "vega-lite"});
</script>



To show the dependence of results on other parameters (e.g. charge of the complex), we can use the [repeat chart method](https://altair-viz.github.io/user_guide/compound_charts.html) of Altair.

For that we need just a small adjustment of our plot_chart function:


```python
def plot_chart2(df, colorData, rows, cols):
    
    hover = alt.selection_single(on='mouseover', nearest=True, empty='none')
    
    bckcolor='lightgray'
    
    base = alt.Chart(df).encode(
        
        x=alt.X(alt.repeat("column"),
                type='ordinal',
                ),
        y=alt.Y(alt.repeat("row"),
                type='ordinal',
                sort='descending',
                axis=alt.Axis(format=",.0f")
                ),
        color=alt.condition(hover,
                            colorData,
                            alt.value(bckcolor),
                            type='nominal',
                            scale=alt.Scale(scheme='dark2')
                           )
        ).properties(width=230,
                     height=190
                    )
    
    points = base.mark_point().encode().add_selection(hover).repeat(
        row=rows,
        column=cols)
    
    
    return points

```


```python
delta_points=plot_chart2(df_adf_all,
                         'charge',
                         ['solveff', 'delta1', 'delta2', 'delta3', 'delta4'],
                         ['mol', 'solvent']
                        )
                                          
delta_points
```





<div id="altair-viz-b7dc5f6caae2431da6af8f182f17f8c0"></div>
<script type="text/javascript">
  (function(spec, embedOpt){
    let outputDiv = document.currentScript.previousElementSibling;
    if (outputDiv.id !== "altair-viz-b7dc5f6caae2431da6af8f182f17f8c0") {
      outputDiv = document.getElementById("altair-viz-b7dc5f6caae2431da6af8f182f17f8c0");
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
  })({"config": {"view": {"continuousWidth": 400, "continuousHeight": 300}}, "repeat": {"column": ["mol", "solvent"], "row": ["solveff", "delta1", "delta2", "delta3", "delta4"]}, "spec": {"data": {"name": "data-82ba65b3d3f73062f9b09b91098cd11b"}, "mark": "point", "encoding": {"color": {"condition": {"type": "nominal", "field": "charge", "scale": {"scheme": "dark2"}, "selection": "selector007"}, "value": "lightgray"}, "x": {"type": "ordinal", "field": {"repeat": "column"}}, "y": {"type": "ordinal", "axis": {"format": ",.0f"}, "field": {"repeat": "row"}, "sort": "descending"}}, "height": 190, "selection": {"selector007": {"type": "single", "on": "mouseover", "nearest": true, "empty": "none"}}, "width": 230}, "$schema": "https://vega.github.io/schema/vega-lite/v4.8.1.json", "datasets": {"data-82ba65b3d3f73062f9b09b91098cd11b": [{"mol": "Ti", "x_labels": "Ti", "where": "SO-ZORA", "solveff": 4.300000000000068, "row": 4, "charge": 0, "solvent": "12TiCl\u2084", "complex": "TiCl\u2084 + 12TiCl\u2084", "delta1": 22.184000000000083, "delta2": 21.684000000000083, "delta3": 21.854000000000042, "delta4": 4.300000000000068, "Delta1": 22.184000000000083, "Delta2": -0.5, "Delta3": 0.16999999999995907, "Delta4": -17.553999999999974, "d1": -0.021763098138381388, "d2": -0.021154646501768178, "d3": -0.02136152005821662, "d4": -0.0}, {"mol": "V", "x_labels": "V", "where": "SO-ZORA", "solveff": -25.33199999999988, "row": 4, "charge": 0, "solvent": "6C\u2086H\u2086", "complex": "VOCl\u2083 + 6C\u2086H\u2086", "delta1": 2.814000000000078, "delta2": 1.0170000000000528, "delta3": -0.10500000000001819, "delta4": -25.33199999999988, "Delta1": 2.814000000000078, "Delta2": -1.7970000000000255, "Delta3": -1.122000000000071, "Delta4": -25.22699999999986, "d1": -0.015131346929647224, "d2": -0.014165276069397935, "d3": -0.013562086584033574, "d4": -0.0}, {"mol": "Cr", "x_labels": "Cr", "where": "SO-ZORA", "solveff": 63.858000000000175, "row": 4, "charge": -2, "solvent": "12H\u2082O", "complex": "CrO\u2084\u00b2\u207b + 12H\u2082O", "delta1": 123.04400000000032, "delta2": 104.03900000000021, "delta3": 76.654, "delta4": 63.858000000000175, "Delta1": 123.04400000000032, "Delta2": -19.00500000000011, "Delta3": -27.38500000000022, "Delta4": -12.795999999999822, "d1": -0.021712884105380876, "d2": -0.014740739300481661, "d3": -0.004694320701051754, "d4": -0.0}, {"mol": "Mn", "x_labels": "Mn", "where": "SO-ZORA", "solveff": -7.241999999999734, "row": 4, "charge": -1, "solvent": "12H\u2082O", "complex": "MnO\u2084\u207b + 12H\u2082O", "delta1": 35.49200000000019, "delta2": 29.141000000000076, "delta3": 15.231000000000222, "delta4": -7.241999999999734, "Delta1": 35.49200000000019, "Delta2": -6.351000000000113, "Delta3": -13.909999999999854, "Delta4": -22.472999999999956, "d1": -0.010860140180028139, "d2": -0.009246138441755099, "d3": -0.005711141720077042, "d4": -0.0}, {"mol": "Fe", "x_labels": "Fe", "where": "SO-ZORA", "solveff": 47.11200000000008, "row": 4, "charge": 0, "solvent": "6C\u2086H\u2086", "complex": "Fe(CO)\u2085 + 6C\u2086H\u2086", "delta1": 67.43199999999979, "delta2": 65.41499999999996, "delta3": 64.57400000000007, "delta4": 47.11200000000008, "Delta1": 67.43199999999979, "Delta2": -2.0169999999998254, "Delta3": -0.8409999999998945, "Delta4": -17.46199999999999, "d1": -0.008717314895411402, "d2": -0.007852018431629732, "d3": -0.007491227987385629, "d4": -0.0}, {"mol": "Co", "x_labels": "Co", "where": "SO-ZORA", "solveff": 1097.3739999999998, "row": 4, "charge": -3, "solvent": "12H\u2082O", "complex": "Co(CN)\u2086\u00b3\u207b + 12H\u2082O", "delta1": 1098.3239999999996, "delta2": 1127.5999999999995, "delta3": 1124.4809999999998, "delta4": 1097.3739999999998, "Delta1": 1098.3239999999996, "Delta2": 29.27599999999984, "Delta3": -3.118999999999687, "Delta4": -27.10699999999997, "d1": -0.0001645170921136079, "d2": -0.005234414343396641, "d3": -0.004694278753604651, "d4": -0.0}, {"mol": "Ni", "x_labels": "Ni", "where": "SO-ZORA", "solveff": -27.54399999999987, "row": 4, "charge": 0, "solvent": "6C\u2086H\u2086", "complex": "Ni(CO)\u2084 + 6C\u2086H\u2086", "delta1": -9.3599999999999, "delta2": -17.951999999999998, "delta3": -21.45699999999988, "delta4": -27.54399999999987, "Delta1": -9.3599999999999, "Delta2": -8.592000000000098, "Delta3": -3.5049999999998818, "Delta4": -6.086999999999989, "d1": -0.011156635879161785, "d2": -0.005885088613776869, "d3": -0.0037346261876626584, "d4": -0.0}, {"mol": "Cu", "x_labels": "Cu", "where": "SO-ZORA", "solveff": -53.464, "row": 4, "charge": 1, "solvent": "12CH\u2083CN", "complex": "Cu(CH\u2083CN)\u2084\u207a + 12CH\u2083CN", "delta1": -23.295999999999992, "delta2": -44.88799999999998, "delta3": -47.87099999999998, "delta4": -53.464, "Delta1": -23.295999999999992, "Delta2": -21.591999999999985, "Delta3": -2.983000000000004, "Delta4": -5.593000000000018, "d1": 0.06319613047294453, "d2": 0.01796506281278088, "d3": 0.01171625423412821, "d4": 0.0}, {"mol": "Zn", "x_labels": "Zn", "where": "SO-ZORA", "solveff": -43.14599999999996, "row": 4, "charge": 2, "solvent": "12H\u2082O", "complex": "Zn(H\u2082O)\u2086\u00b2\u207a + 12H\u2082O", "delta1": -29.680999999999813, "delta2": -30.291999999999916, "delta3": -30.120999999999867, "delta4": -43.14599999999996, "Delta1": -29.680999999999813, "Delta2": -0.6110000000001037, "Delta3": 0.1710000000000491, "Delta4": -13.025000000000091, "d1": 0.007034567861245903, "d2": 0.00671536095718189, "d3": 0.0068046970956351675, "d4": 0.0}, {"mol": "Nb", "x_labels": "Nb", "where": "SO-ZORA", "solveff": -19.514999999999986, "row": 5, "charge": -1, "solvent": "12CH\u2083CN", "complex": "NbCl\u2086\u207b + 12CH\u2083CN", "delta1": 15.894000000000005, "delta2": 3.2100000000000364, "delta3": 0.17900000000003047, "delta4": -19.514999999999986, "Delta1": 15.894000000000005, "Delta2": -12.683999999999969, "Delta3": -3.031000000000006, "Delta4": -19.694000000000017, "d1": -0.11137042011203403, "d2": -0.07147597495116995, "d3": -0.061942699700257016, "d4": -0.0}, {"mol": "Mo", "x_labels": "Mo", "where": "SO-ZORA", "solveff": 16.257000000000062, "row": 5, "charge": -2, "solvent": "12H\u2082O", "complex": "MoO\u2084\u00b2\u207b + 12H\u2082O", "delta1": 69.5, "delta2": 57.478999999999985, "delta3": 38.01099999999997, "delta4": 16.257000000000062, "Delta1": 69.5, "Delta2": -12.021000000000015, "Delta3": -19.468000000000018, "Delta4": -21.753999999999905, "d1": -0.09521501751650152, "d2": -0.07371773664266144, "d3": -0.03890290725642746, "d4": -0.0}, {"mol": "Tc", "x_labels": "Tc", "where": "SO-ZORA", "solveff": 26.989000000000033, "row": 5, "charge": -1, "solvent": "12H\u2082O", "complex": "TcO\u2084\u207b + 12H\u2082O", "delta1": 22.694999999999936, "delta2": 28.878999999999905, "delta3": 23.896999999999935, "delta4": 26.989000000000033, "Delta1": 22.694999999999936, "Delta2": 6.183999999999969, "Delta3": -4.981999999999971, "Delta4": 3.0920000000000982, "d1": 0.003025801759954068, "d2": -0.0013318037555455694, "d3": 0.002178802757749899, "d4": -0.0}, {"mol": "Ru", "x_labels": "Ru", "where": "SO-ZORA", "solveff": 787.761, "row": 5, "charge": -4, "solvent": "12H\u2082O", "complex": "Ru(CN)\u2086\u2074\u207b + 12H\u2082O", "delta1": 828.1270000000001, "delta2": 831.764, "delta3": 834.176, "delta4": 787.761, "Delta1": 828.1270000000001, "Delta2": 3.6370000000000005, "Delta3": 2.411999999999999, "Delta4": -46.415, "d1": -0.04974484294377672, "d2": -0.05422688212988671, "d3": -0.057199298549160095, "d4": -0.0}, {"mol": "Pd", "x_labels": "Pd", "where": "SO-ZORA", "solveff": 270.96900000000005, "row": 5, "charge": -2, "solvent": "12H\u2082O", "complex": "PdCl\u2086\u00b2\u207b + 12H\u2082O", "delta1": 202.9269999999999, "delta2": 238.596, "delta3": 270.4839999999999, "delta4": 270.96900000000005, "Delta1": 202.9269999999999, "Delta2": 35.669000000000096, "Delta3": 31.88799999999992, "Delta4": 0.48500000000012733, "d1": 0.04304883596306927, "d2": 0.02048176077470446, "d3": 0.0003068499668159965, "d4": -0.0}, {"mol": "Ag", "x_labels": "Ag", "where": "SO-ZORA", "solveff": -210.16700000000037, "row": 5, "charge": 1, "solvent": "12H\u2082O", "complex": "Ag(H\u2082O)\u2084\u207a + 12H\u2082O", "delta1": -175.03999999999996, "delta2": -174.26800000000003, "delta3": -173.433, "delta4": -210.16700000000037, "Delta1": -175.03999999999996, "Delta2": 0.7719999999999345, "Delta3": 0.8350000000000364, "Delta4": -36.73400000000038, "d1": 0.008140764518531128, "d2": 0.008319677326579223, "d3": 0.008513190532175308, "d4": 0.0}, {"mol": "Cd", "x_labels": "Cd", "where": "SO-ZORA", "solveff": 54.89800000000014, "row": 5, "charge": 0, "solvent": "6Cd(CH\u2083)\u2082", "complex": "Cd(CH\u2083)\u2082 + 6Cd(CH\u2083)\u2082", "delta1": 6.981999999999971, "delta2": 22.7170000000001, "delta3": 46.817999999999756, "delta4": 54.89800000000014, "Delta1": 6.981999999999971, "Delta2": 15.735000000000127, "Delta3": 24.100999999999658, "Delta4": 8.080000000000382, "d1": -0.013863777019912952, "d2": -0.009311090414012391, "d3": -0.0023378269955944063, "d4": 0.0}, {"mol": "Ta", "x_labels": "Ta", "where": "SO-ZORA", "solveff": 30.634999999999764, "row": 6, "charge": -1, "solvent": "12CH\u2083CN", "complex": "TaCl\u2086\u207b + 12CH\u2083CN", "delta1": 11.592999999999847, "delta2": -6.84900000000016, "delta3": -11.596000000000004, "delta4": 30.634999999999764, "Delta1": 11.592999999999847, "Delta2": -18.442000000000007, "Delta3": -4.746999999999844, "Delta4": 42.23099999999977, "d1": -0.0055832686076456115, "d2": -0.010990612356317014, "d3": -0.012382471198901457, "d4": 0.0}, {"mol": "W", "x_labels": "W", "where": "SO-ZORA", "solveff": -74.40099999999984, "row": 6, "charge": -2, "solvent": "12H\u2082O", "complex": "WO\u2084\u00b2\u207b + 12H\u2082O", "delta1": 85.21000000000004, "delta2": 52.39400000000023, "delta3": 25.09900000000016, "delta4": -74.40099999999984, "Delta1": 85.21000000000004, "Delta2": -32.8159999999998, "Delta3": -27.295000000000073, "Delta4": -99.5, "d1": 0.05316830974068907, "d2": 0.04223691245321864, "d3": 0.03314462549071534, "d4": 0.0}, {"mol": "Re", "x_labels": "Re", "where": "SO-ZORA", "solveff": -52.822999999999865, "row": 6, "charge": -1, "solvent": "12H\u2082O", "complex": "ReO\u2084\u207b + 12H\u2082O", "delta1": 21.605000000000018, "delta2": 11.55600000000004, "delta3": 2.73700000000008, "delta4": -52.822999999999865, "Delta1": 21.605000000000018, "Delta2": -10.048999999999978, "Delta3": -8.81899999999996, "Delta4": -55.559999999999945, "d1": 0.03932442385502771, "d2": 0.03401498204120532, "d3": 0.029355417173447373, "d4": 0.0}, {"mol": "Os", "x_labels": "Os", "where": "SO-ZORA", "solveff": 121.17599999999993, "row": 6, "charge": 0, "solvent": "12CCl\u2084", "complex": "OsO\u2084 + 12CCl\u2084", "delta1": 132.73399999999992, "delta2": 135.55999999999995, "delta3": 137.587, "delta4": 121.17599999999993, "Delta1": 132.73399999999992, "Delta2": 2.826000000000022, "Delta3": 2.0270000000000437, "Delta4": -16.411000000000058, "d1": 0.009486133567518316, "d2": 0.011805549855959828, "d3": 0.013469193457046525, "d4": 0.0}, {"mol": "Pt", "x_labels": "Pt", "where": "SO-ZORA", "solveff": 498.48800000000006, "row": 6, "charge": -2, "solvent": "12H\u2082O", "complex": "PtCl\u2086\u00b2\u207b + 12H\u2082O", "delta1": 292.2940000000001, "delta2": 371.1559999999997, "delta3": 428.05899999999997, "delta4": 498.48800000000006, "Delta1": 292.2940000000001, "Delta2": 78.86199999999963, "Delta3": 56.90300000000025, "Delta4": 70.42900000000009, "d1": -0.10937188371234462, "d2": -0.06754095995451033, "d3": -0.0373577911965272, "d4": 0.0}, {"mol": "Hg", "x_labels": "Hg", "where": "SO-ZORA", "solveff": 40.07600000000093, "row": 6, "charge": 0, "solvent": "6Hg(CH\u2083)\u2082", "complex": "Hg(CH\u2083)\u2082 + 6Hg(CH\u2083)\u2082", "delta1": 31.950000000000728, "delta2": 67.19500000000153, "delta3": 95.47100000000137, "delta4": 40.07600000000093, "Delta1": 31.950000000000728, "Delta2": 35.2450000000008, "Delta3": 28.27599999999984, "Delta4": -55.39500000000044, "d1": -0.0009148227516846377, "d2": 0.003053049249684422, "d3": 0.006236353227857449, "d4": 0.0}]}}, {"mode": "vega-lite"});
</script>



There's still a lot that could be done to improve the readibility of the plots, besides Altair has many other interactive options to offer in addition to simple hover. For instance, what could be done is to show the results from both programs on one plot or plot some more statistics. However, I will dive into this using larger data in the future.
