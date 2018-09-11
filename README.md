# Non-metric Similarity Graphs for Maximum Inner Product Search

This is the implementation for ```ip-nsw``` from the following paper:

S. Morozov, A. Babenko. Non-metric Similarity Graphs for Maximum Inner Product Search. Advances in Neural Information Processing Systems 31 (NIPS 2018).

## Requirements
  - g++ with c++11 support
  - OpenMP installed

## Compilation
To download and compile the code type:
```
$ git clone https://github.com/stanis-morozov/ip-nsw.git
$ cd ip-nsw
$ cmake CMakeLists.txt
$ make
```
## Dataset
We introduce to the community the new dataset ```Music100```. The database of ```Music100``` consists of ```1,000,000``` vectors of dimensionality ```100``` and there is a random sample of ```10,000``` queries of the same dimensionality.

[Database Music100](https://yadi.sk/d/F85at7Df685hGA)

[Query Music100](https://yadi.sk/d/pdrfKf1dFkNAQA)

Groundtruth: [Top 1](https://yadi.sk/i/CtlFa-0GCSkvPA), [Top 10](https://yadi.sk/i/u0KaNC4KxW6g-g), [Top 100](https://yadi.sk/i/RJ_QGT9a1Ih5lw).

Both files are written in binary format. Vectors are stored consecutively, and numbers are represented in 4-bytes floating poing format (float in C/C++).

Example of reading ```database_music100.bin``` in C++:
```
size_t databaseSize = 1000 * 1000;
size_t dimension = 100;
std::vector <std::vector <float> > data(databaseSize, std::vector <float> (dimension));
std::ifstream input("database_music100.bin", std::ios::binary);
for (size_t i = 0; i < databaseSize; i++) {
    input.read((char*)data[i].data(), dimension * sizeof(float));
}
input.close();
```
Example of reading ```database_music100.bin``` in Python:
```
databaseSize = 10**6
dimension = 100
data = np.fromfile('database_music100.bin', dtype = np.float32).reshape(databaseSize, dimension)
```

## Run experiments
```
Usage: main [OPTIONS]
This tool supports two modes: to construct the graph index from the database and to retrieve top K maximum inner product vectors using the constructed index. Each mode has its own set of parameters.

  --mode            "database" or "query". Use "database" for
                    constructing index and "query" for top K
                    maximum inner product retrieval

Database mode supports the following options:
  --database        Database filename. Database should be stored in binary format.
                    Vectors are written consecutive and numbers are
                    represented in 4-bytes floating poing format (float in C/C++)
  --databaseSize    Number of vectors in the database
  --dimension       Dimension of vectors
  --outputGraph     Filename for the output index graph
  --efConstruction  efConstruction parameter. Default: 1024
  --M               M parameter. Default: 32

Query mode supports the following options:
  --query           Query filename. Queries should be stored in binary format.
                    Vectors are written consecutive and numbers are
                    represented in 4-bytes floating poing format (float in C/C++)
  --querySize       Number of queries
  --dimension       Dimension of vectors
  --inputGraph      Filename for the input index graph
  --efSearch        efSearch parameter. Default: 128
  --topK            Top size for retrieval. Default: 1
  --output          Filename to print the result. Default: stdout
```
Our best performed index graph from the NIPS paper is (we assume that our ```Music100``` dataset is located in the ```data``` folder):
```
./main --mode database --database data/database_music100.bin --databaseSize 1000000 --dimension 100 --outputGraph out_graph.hnsw --efConstruction 1024 --M 32
./main --mode query --query data/query_music100.bin --querySize 10000 --dimension 100 --inputGraph out_graph.hnsw --topK 10 --efSearch 128 --output result.txt
```
The above commands print the output to ```result.txt``` in the following format:
```
109133 121486 19537 659869 834299 626720 867593 793041 985613 780019 
981712 601975 355926 202115 294738 198925 679900 609952 19993 615818 
77873 764344 292930 982610 65402 80310 638008 446408 209176 974376 
615370 981712 768351 601975 19993 679900 521439 198925 867593 739598 
619843 567912 732469 560702 649392 34342 827097 18669 385074 690897 
473444 752040 342582 154589 881600 292375 171237 223102 370021 219806 
313256 36821 267804 507875 949307 270882 471300 70277 651590 111192 
19993 93160 361868 896032 202115 739598 58964 967143 205517 71338 
894914 943250 85319 431888 615156 428084 909129 687106 884369 942893 
480939 228514 630512 651287 197739 509842 745237 566816 881843 166116
...
```
where each line corresponds to the answer for the query and the numbers in the lines are zero-based IDs of vectors in the database.
The Recall for the above parameters is ```0.99458```.

## Related publication
```
@inproceedings{ip-nsw18,
    title={Non-metric Similarity Graphs for Maximum Inner Product Search}
    author={Stanislav Morozov and Artem Babenko},
    booktitle={Advances in Neural Information Processing Systems},
    year={2018}
}
```
