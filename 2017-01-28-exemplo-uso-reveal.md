<div id="table-of-contents">
<h2>Table of Contents</h2>
<div id="text-table-of-contents">
<ul>
<li><a href="#org7270b11">Geração Automática de Slides</a></li>
</ul>
</div>
</div>


<a id="org7270b11"></a>

# Geração Automática de Slides


<a id="org2f62281"></a>

## Objetivo

O meu objetivo inicial vai ser testar se é mais eficiente gerar 
slides usando o orgmode. 


<a id="org6b9f912"></a>

## Inicialmente vamos preparar o sistema

    git clone https://github.com/ipython-books/cookbook-data.git
    cd cookbook-data
    unzip tennis.zip


<a id="org2ae5c4b"></a>

## Vamos testar o uso do python

Os primeiros passos são carregar os pacotes:

    import numpy as np
    import pandas as pd
    import matplotlib.pyplot as plt
    from  datetime import *

    datetime.datetime(2013, 3, 27, 23, 5)


<a id="org30fb93c"></a>

## Datafile

Criamos o *datafile* usando os arquivos do item anterior. 

    player = 'Roger Federer'
    filename = "./cookbook-data/data/{name}.csv".format(name=player.replace(' ', '-')) 
    df = pd.read_csv(filename)


<a id="orgbd5c173"></a>

## Determinamos quem Venceu

    df['win'] = df['winner'] == player
    df['win'].tail()

    1174    False
    1175     True
    1176     True
    1177     True
    1178    False
    Name: win, dtype: bool


<a id="orge95a866"></a>

## Agora geramos a fração de faltas duplas

    df['dblfaults'] = (df['player1 double faults'] / 
                       df['player1 total points total'])


<a id="orgefea2a2"></a>

## Podemos gerar um Report mais interessante.

     ("{player} has won {vic:.0f}% of his ATP matches.").format(
    player=player, vic=100*df['win'].mean())

    Roger Federer has won 82% of his ATP matches.

    df['dblfaults'].tail()

    1174    0.018116
    1175    0.000000
    1176    0.000000
    1177    0.011561
    1178         NaN
    Name: dblfaults, dtype: float64


<a id="org4ded04b"></a>

## Agrupamos os dados

Inicialmente por tipo de quadra

    df.groupby('surface')['win'].mean()

    surface
    Indoor: Carpet    0.736842
    Indoor: Clay      0.833333
    Indoor: Hard      0.836283
    Outdoor: Clay     0.779116
    Outdoor: Grass    0.871429
    Outdoor: Hard     0.842324
    Name: win, dtype: float64


<a id="org96ec475"></a>

## Agrupamos os dados

Agora por ano 

    gb = df.groupby('year')


<a id="org015936f"></a>

## Convertendo as datas

    df['date']=[datetime.strptime(date, '%d.%m.%Y') for date in df['start date']]

 A Conversão da base `gb` é mais complicada por que cada elemento
é uma base de dados. 

    dates=[datetime.strptime(date, '%d.%m.%Y') for date in gb['start date'].max()]


<a id="orgb24290f"></a>

## Representação dos Pontos

    %matplotlib inline
    from pytz import timezone
    fig=plt.figure(figsize=(12,6),facecolor="0.8")
    plt.plot_date(dates, gb['dblfaults'].mean(),  '-', lw=3)
    plt.plot_date(df['start date'], df['dblfaults'], alpha=.25, lw=0)
    plt.ylabel('Proportion of double faults per match.')
    plt.xlabel('Year')


<a id="org840a355"></a>

## Detalhes do orgmode

Note que o gráfico não é incluído no `exports` apenas o código:

    #+BEGIN_SRC ipython :session pandas :exports code  :file /Users/neylemke/Dropbox/laptop/bloggithub/LessOrMore-master/assets/pandatennis.png :tangle pandas.py

A Figura é incluída com um comando em HTML. Isso é uma gambiarra para consertar a
 gambiarra do `jeckyll` que exige que o path da figura comece com `/assets`
e não com `file://<path>`.

    #+CAPTION: Federer Results
    #+NAME:   fig:federer-results
    #+REVEAL_HTML:  <img src="/assets/pandatennis.png" alt="pandatennis.png" />


<a id="orge79f5b0"></a>

## Melhores Resultados

    gb['start date'].max().tail()

    year
    2008    28.07.2008
    2009    31.08.2009
    2010    30.08.2010
    2011    31.10.2011
    2012    27.08.2012
    Name: start date, dtype: object

