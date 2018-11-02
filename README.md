# CellView_server-modeの作成(2018.11.01~)

###目標
CellViewをindraやsiva内のfileを選択できるようにする。

###方法
#####既存のCellViewのコードを変更する
(1)フォークして(Git hubのmohanbolisetty/CellView からSuzuk1/CellViewへ)  
(2)indar側でgit clone  
(3)docker runでマウント元をcloneしてきたものにして実行  
(4)コードを変更して  
(5)フォークした先にgit push(Suzuk1/CellView)  
(6)またgit clone  
(7)また実行  
(8)また変更  
---(5)~(8)繰り返す---  
(9)完成したらGit publicにup  

###使用したもの
shiny
CellView
docker

####注意事項
* CellViewのみをrunApp()で直接起動しようとしてもremoteで動かす場合にはshiny serverが必要(これを含んだdockerが存在する)
* dockerから動かそうとするとマウントしたdataを使う際にpermissionの問題が生じる。
* CellViewはいくつかのRライブラリに依存しているので、それらのない環境では動かせない
* indra内にはCellViewが動くようにしてあるdocker imageがある(dkomura/cellview_shiny)



# CellView(fork元のREADME)


### A ShinyApp to visualize and explore single cell datasets

A live version of this app is hosted here: 
https://mbolisetty.shinyapps.io/CellView/

A preprint describing this software is available on 
[bioRxiv](https://www.biorxiv.org/content/early/2017/04/04/123810).

### Introduction

CellView reads expression, dimesionality reduction/clustering, and feature 
annotation data objects from an `.Rds` file, and provides functionality to 
quickly explore and interactively analyze single cell transcriptomic data.

- View 3D representation of your dataset.
- Analyze (co-)expression patterns within and among specific clusters.
- Compute differential gene expression analysis on the-fly using interactive 
  sample selection.

### Usage

The primary structure of the `.Rds` file comprises three dataframes, with the 
following object names and column names.  These structures will be updated in
the future to be more flexible but currently the following naming conventions
are *required*.

- `log2cpm` - Your (N x M) genes vs. cells expression matrix.  Gene names need 
  to be in Ensembl gene id format (e.g. ENSG,  ENSM).
- `tsne.data` – Your dimensionality reduction and sample clustering information.
  This dataframe contains `M` rows and 4 columns: `V1, V2, V3, dbCluster`.
  - `V1, V2, V3` store the 3 dimensional representation of your data, e.g. from
    t-SNE, PCA, etc.
  - `dbCluster` contains numerical cluster assignments.
  The row names of this data frame must correspond to the column names of your 
  expression matrix.
- `featuredata` - A dataframe representing gene annotations with row names in 
  ensembl gene id format.  The following 2 columns are required:
  - `Chromosome.Name` - integers representing chromosome numbers.
  - `Associated.Gene.Name` - Gene symbol
  Other columns are allowed but not utilized.  The number of rows can be larger 
  than the number of genes (N) in your   expression matrix, but beware of 
  duplicate gene names with unique ENSGIDs.  You can use one of the provided 
  files under 
  [CellView/FeatureData](https://github.com/mohanbolisetty/CellView/tree/master/Featuredata) 
  or create your own.


#### Sample code to generate an .Rds file for upload to CellView

```R
options(stringsAsFactors = FALSE, row.names = 1, as.is = T)
log2cpm <- read.csv('Data/Expression.csv', check.names = F)
featuredata <- read.csv('Databases/HG19_v74_FeatureData.csv', sep = ',')
tsne.data <- read.csv('Data/TNSE_dbscan.csv')

save(log2cpm, featuredata, tsne.data, file = 'Filename.Rds')
```
