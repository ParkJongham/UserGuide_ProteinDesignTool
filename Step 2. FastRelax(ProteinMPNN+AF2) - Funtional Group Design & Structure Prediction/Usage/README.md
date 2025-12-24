# FASTRELAX Usage Guide

**FASTRELAX**는 **ProteinMPNN** 기반으로 작용기를 디자인함과 더불어 **AlphaFold2** 모델을 이용한 설계된 디자인을 스크리닝하는 과정까지 포함됩니다. **FASTRELAX**에서 제공하는 **AlphaFold2** 모델을 이용한 스크리닝은 초기 추측과 대상 템플릿을 사용하여 타겟 단백질과 바인더의 구조를 예측(필터링)할 수 있습니다.

기본적으로 **FASTREALX** 알고리즘은 **RFdiffusion**을 통해 디자인된 다량의 백본 PDB 파일을 한꺼번에 처리하기 위해 **Silent Tool**이라는 보조 도구를 이용하며, 이를통해 다량의 **PDB** 파일을 하나의 **silent** 파일로 변환하여 **ProteinMPNN** 알고리즘을 활용하여 작용기를 디자인, **AlphaFold2**를 통해 타겟 단백질과 바인더의 구조를 예측(필터링)합니다.  

작용기 디자인 및 필터링을 거친 **silent** 파일은 또다시 **Silent Tool** 보조 도구를 이용하여 개별 **PDB**파일로 변환합니다.  
즉, **Silent Tool** 보조 도구는 작업의 편의성을 위하여 다량의 **PDB**파일을 하나로 압축하여 작업을 수행한 후, 다시 압축을 해제하여 **PDB**파일로 변환해주는 보조 도구라고 할 수 있으며, **FASTRELAX**를 활용한 작용기 디자인의 작업 순서는 다음과 같습니다.  

1. **Silent Tool**을 이용하여 백본 **PDB**파일을 **Silent**파일로 변환  
2. 변환된 **Silnet**파일을 이용하여 **ProteinMPNN** 기반 작용기 디자인 (**Silent**파일로 결과가 출력됨.)  
3. **ProteinMPNN**을 통해 작용기 디자인된 파일을 **AlphaFold2** 모델을 이용하여 필터링 (**Silnet**파일로 결과가 출력됨.)  
4. **AlphaFold2** 모델 기반 구조 예측된 **Silent**파일을 **PDB**파일로 변환  

각 단계별 실행 코드는 다음과 같습니다.

1. **Silent Tool**을 이용하여 백본 **PDB**파일을 **Silent**파일로 변환  
> CODE: `<base_dir>/silentfrompdbs <path/to/PDB/folder>*.pdb > <path/to/dir>/<output_file_name>.silent`  

가장 먼저, **silentfrompdbs** 보조 도구를 이용하여 백본 디자인된 **PDB**파일을 **silent**파일로 변환해 줍니다. `<path/to/PDB/folder>` 부분에는 **PDB**파일이 있는 경로를 입력해주고, `<path/to/dir/<output_file_name>.silent` 부분에는 결과로 출력될 **silent**파일 경로 및 output file 이름을 입력해줍니다.  


2. 변환된 **Silnet**파일을 이용하여 **ProteinMPNN** 기반 작용기 디자인 (**Silent**파일로 결과가 출력됨.)  
> CODE: `<base_dir>/mpnn_fr/dl_interface_design.py -silent <path/to/dir>/<output_file_name>.silent`  

위 1번 과정을 통해 다량의 **PDB**파일을 하나의 **silent**파일로 변환하였다면, 해당 **silent**파일을 **ProteinMPNN**을 활용하여 작용기를 디자인할 수 있습니다. 여기서는 `dl_interface_design.py`라는 실행파일을 사용합니다. `-silent`는 입력값으로 **silent**파일을 사용한다는 의미이며, `<path/to/dir>/<output_file_name>.silent`는 입력값으로 사용될 **silent**파일의 경로입니다. 이 **silent**파일은 1번에서 변환한 **silent**파일의 경로를 입력해주면 됩니다.  

### Tips!  
이 과정을 통해 작용기 디자인된 결과 파일은 **out.silent**라는 파일명으로 출력되며, **출력 파일의 경로를 표시할 수 없기 때문에, 반드시 출력 파일이 저장되고자하는 폴더를 생성 후, 해당 폴더로 이동하여 명령어를 실행해 줍니다.**


3. **ProteinMPNN**을 통해 작용기 디자인된 파일을 **AlphaFold2** 모델을 이용하여 필터링 (**Silnet**파일로 결과가 출력됨.)  
> CODE: `<base_dir>/af2_initial_guess/predict.py -silent <path/to/dir>/<output_file_name>.silent`  

위 2번 과정을 통해 ProteinMPNN 기반 작용기 디자인을 했다면, 이번에는 Alphafold2 모델을 이용한 타겟 단백질과 바인더의 구조를 예측(필터링)을 수행하는 과정입니다. 타겟 단백질과 바인더의 구조를 예측(필터링)을 위해 `af2_initial_guess/predict.py`라는 실행파일을 사용합니다. 2번 과정과 동일하게 `-silent`는 입력값으로 **silent**파일을 사용한다는 의미이며, `<path/to/dir>/<output_file_name>.silent`는 입력값으로 사용될 **silent**파일의 경로입니다.  

### Tips!  
2번 과정과 동일하게 이 과정을 통해 작용기 디자인된 결과 파일은 **out.silent**라는 파일명으로 출력되며, **출력 파일의 경로를 표시할 수 없기 때문에, 반드시 출력 파일이 저장되고자하는 폴더를 생성 후, 해당 폴더로 이동하여 명령어를 실행해 줍니다.**


4. **AlphaFold2** 모델 기반 구조 예측된 **Silent**파일을 **PDB**파일로 변환  
> CODE: `<path/to/silet/tool/folder>/silentextract <path/to/dir>/<silent/file/name>.silent`  

마지막으로, 3번 과정을 통해 구조 예측된 최종 silent파일을 **silentextract**라는 보조 도구를 활용하여 PDB파일로 변환하는 과정입니다. 

### Tips!  
2번, 3번 과정과 동일하게 **출력 파일의 경로를 표시할 수 없기 때문에, 반드시 출력 파일이 저장되고자하는 폴더를 생성 후, 해당 폴더로 이동하여 명령어를 실행해 줍니다.**

<br>
<br>
<br>

# ___ END