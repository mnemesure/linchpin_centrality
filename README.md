# Linchpin Centrality

This R script can be used to generate a linchpin score for each node in the network.

## Quick Use Guide

Run the ```Linchpin.Centrality``` function in your R environment. Then use the code below to actually calculate the score for your network.

```outputdf = Linchpin.Centrality(links = edgelist_df, characteristics = characteristics_df, label_column_name = 'Specialty', weighted=FALSE, char_subset = c('gp', 'surgery'))```

```links``` is an edgelist with two columns, each representing IDs of an edge.
```characteristics``` is a df with each unique ID from the edgelist and some characteristic of interest.
```label_column_name``` is a string that is the name of the column in the characteristics file that is the characteristic of interest.
More detail on ```weighted``` and ```char_subset below```, typically you would use defaults here. 

## What is this measure?

The linchpin centrality is a measure how important a node in a network is to its local ties based on a characteristic of that node.
Take for example, a network of doctors where I am an oncologist. My linchpin score will depend on how many of my first order connections are connected
to another oncologist. If I am the only oncologist that many of my first order ties are connected to, I am very important because if I leave, they have 
no other oncologists that they could refer patients to.

## What does this look like?

Below is an example of linchpin calculation where the characteristics are represented by colors. The linchpin score represents the proportion of first order
ties that are not connected to another node with the same characteristic as myself.

![alt text](https://media.springernature.com/lw685/springer-static/image/art%3A10.1007%2Fs41109-021-00400-8/MediaObjects/41109_2021_400_Fig1_HTML.png?as=webp)

## How to use the code

The function ``` Linchpin.Centrality ``` takes 5 arguments.

### Edgelist

The first argument is an edgelist. It is required that the edgelist be a two column dataframe with data as IDS.

This is represented as ```links=edgelist``` where edgelist is the dataframe that looks as follows.

| ID1  | ID2 | weight
| ------------- | ------------- | ------------- |
| 1001  | 1002  | 5
| 1002  | 1003  | 4
| 1003  | 1001  | 3
| 1003  | 1005  | 10
| 1004  | 1005  | 2
| 1005  | 1001  | 1

### Characteristics

The second argument is a characteristics file. This contains a unique entry for each ID in the edgelist. There can be any number of columns representing
characteristics of the ID.

This is represented as the ```characteristics``` argument.

| ID  | Specialty | Hospital | Year|
| ------------- | ------------- | ------------- | ------------- |
| 1001  | surgery  | Hospital 1 | 2000
| 1002  | oncology  | Hospital 2 | 2001
| 1003  | gp  | Hospital 3 | 2002
| 1004  | surgery  | Hospital 2 | 2003
| 1005  | oncology  | Hospital 1 | 2000


### Weighting

The third argument is whether to weight the calculation based on edge weights. This works but is still being developed to better account for edge cases and other considerations.

The ```weighted``` argument is a bool of whether or not to use edgeweights.

### Characteristic of interest

The ```label_column_name``` argument is a string name of the column representing the characteristic of interest. In the case of the data above, if we were interested in ```Specialty``` the argument would be ```label_column_name='Specialty'```.

### Subsetting

If you are only interested in the linchpin score for a subset of characteristics in a particular column (i.e. only interested in surgery and gp) you can indicated this as a list in the ```char_subset``` argument. 

### Putting it all Together.

To run this, its as simple as indicating the arguments and running the function. 

```outputdf = Linchpin.Centrality(links = edgelist_df, characteristics = characteristics_df, label_column_name = 'Specialty', weighted=FALSE, char_subset = c('gp', 'surgery'))```


### Output

The function returns a list of 3 outputs. The first is a summary of each characteristic.

```outputdf[1][[1]]```

| surgery_n_actors  | surg_means | surg_variance | gp_n_actors| gp_means | gp_variance
| ------------- | ------------- | ------------- | ------------- | ------------- | ------------- |
| 2  | 0.5  | 0.2 | 1 | 0.75 | 0.33 

The second is a different format of the first output with the same information.

```outputdf[2][[1]]```

| Specialty  | n_actors | means | variance|
| ------------- | ------------- | ------------- | ------------- |
| surg  | 2  | 0.5 | 0.2
| gp  | 1  | 0.75 | 0.33

The third output contains the individual linkage scores for each node in the network by ID. Notice that specialty is listed as NA and linchpin score is listed as 0 for any IDs that had a specialty that wasnt in the ```char_subset=c('gp','surgery')``` argument.

```outputdf[3][[1]]```

| ID  | Specialty | Linchpin Score |
| ------------- | ------------- | ------------- |
| 1001  | surgery  | 0.5 
| 1002  | NA  | 0
| 1003  | gp  | 0.2
| 1004  | surgery  | 0.5
| 1005  | NA  | 0


