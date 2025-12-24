# Autodock-crankpep


본 설치&실행 가이드는 한국어 사용자를 위한 것
그리고, 사용하는 법을 숙지하게 하기 위한 가이드이므로, 보편적인 예시를 추가하였음.
Zhang, Y.; Sanner, M. F. AutoDock CrankPep: Combining Folding and Docking to Predict Protein-Peptide Complexes. Bioinformatics 2019.
논문을 참고하여 기본 지식을 쌓을 수 있음.


https://ccsb.scripps.edu/adcp/
위 사이트에서 참고하여 설치하면 됨

WSL 을 이용하여 윈도우 10 이상에서 Autodock Crankpep 을 구동하기 위한 방법.

### Installtion
Linux 환경
```
https://ccsb.scripps.edu/adfr/download/1038/
```
에서 ADFRsuite 1.0 Linux 64 용 tarball installer download

```
tar zxvf ADFRsuite_x86_64Darwin_1.0.tar.gz #unpacking
cd ADFRsuite_x86_64Darwin_1.0 #change directory
./install.sh -d myFolder -c 0 #-c option specifies how to compile the python files, 0 for pyc, 1 for pyo
```

### Docking simulation 실습
PDB ID: 3Q47
을 이용하여 실습을 진행할 예정임.

3Q47 파일은 receptor/ligand 가 함께 존재하는 file 이기 때문에,
3Q47_ligand
3Q47_receptor
로 나누어서 실행되는 것을 숙지하도록 하자

file download from link(below) for practice
   https://ccsb.scripps.edu/adcp/download/1063/

```
unzip ADCP_tutorial_data.zip #unpacking
cd ADCP_tutorial_data/3Q47 #change directory
# preprocess for docking (below)
reduce 3Q47_rec.pdb > 3Q47_recH.pdb
reduce 3Q47_pep.pdb > 3Q47_pepH.pdb
prepare_receptor -r 3Q47_recH.pdb
prepare_ligand -l 3Q47_pepH.pdb
```
도킹에 필요한 파일 준비 완료

아래 코드로 도킹 실행
```
agfr -r 3Q47_recH.pdbqt -l 3Q47_pepH.pdbqt -asv 1.1 -o 3Q47
```
위 코드의 output file 은 trg 파일임
-o option의 경우, output file name 을 지정한다

최종적으로, docking 은 아래의 코드로 이루어짐.
```
adcp -t 3Q47.trg -s npisdvd -N 20 -n 1000000 -o 3Q47_redocking -ref 3Q47_pepH.pdb
```

-s option의 경우에는 sequence 를 넣어주면 된다
-N, -n option의 경우에는 자신의 computer 사양을 숙지하고 실시 (모르면 default 값으로 놓아도 됨)


다른 단백질-펩타이드를 이용하여 도킹할 시, **단순히**파일만 바꿔주는 식으로 실행하면 된다.



### License
```
The ADFR software suite contains various software programs and components. Unless otherwise specified the software program and components are provided under the the LGPL2 open source license.

The only current exception is the MSMS software and MSLIB software component which is freely available for academic research but require a license for commercial use. This component is used to compute molecular surfaces and is not used by only used by the agfrgui program. Surface calculations and display . It is not used by adfr, agfr, autosite, adcp, prepare_ligand, or prepare_receptor.
```
### Citation
코드 실행 시 아래의 인용문이 뜨게 되는데, 반드시 숙지하도록 하자.
```
# P.A. Ravindranath S. Forli, D.S. Goodsell, A.J. Olson and     #
# M.F. Sanner                                                   #
# AutoDockFR: Advances in Protein-Ligand Docking with           #
# Explicitly Specified Binding Site Flexibility                 #
# PLoS Comput Biol 11(12): e1004586                             #
# DOI:10.1371/journal.pcbi.1004586                              #
#                                                               #
# P. Ananad Ravindranath and M.F. Sanner                        #
# AutoSite: an automated approach for pseudoligands prediction  #
# - From ligand binding sites identification to predicting key  #
# ligand atoms                                                  #
# Bioinformatics (2016)                                         #
# DOI:10.1093/bioinformatics/btw367                             #
```
