# RFdiffusion Usage Guide

**RFdiffusion**은 단백질 또는 펩타이드의 백본을 디자인하는 알고리즘입니다.  
**RFdiffusion**을 활용하여 백본을 디자인할 경우, **Rfdiffusion**을 설치한 가상환경을 실행하여 디자인이 가능합니다.  
기본적으로 **RFdiffusion** 폴더로 이동하고, 하위에 있는 **scripts** 폴더의 `run_inference.py` 파일을 실행하여 알고리즘을 실행할 수 있습니다.  
일반적으로 다음과 같은 경로를 가집니다.  

> CODE: **`~/RFdiffusion/scripts/run_inference.py`**  

위 RFdiffusion 실행을 위한 파일을 뒤에 다양한 옵션을 입력하여 디자인할 수 있습니다.  


## RFdiffusion Options (Requirement!)  

많은 옵션이 있지만 기본적으로 다음과 같은 옵션은 필수적이며, 옵션 간 띄워쓰기를 통해 옵션을 구분합니다.   

1. 디자인하고자 하는 백본의 길이: **`'contigmap.contigs=[150-150]'`**  
2. 백본 디자인의 결과가 저장될 경로: **`inference.output_prefix="output/path/"`**  
3. 백본 디자인 개수: **`inference.num_designs=10`**  

> CODE EX: **`~/RFdiffusion/scripts/run_inference.py 'contigmap.contigs=[150-150]' inference.output_prefix=output/path/ inference.num_designs=10`**  

위 코드를 설명하자면 다음과 같습니다.

1. `~/RFdiffusion/scripts/run_inference.py`: run_inference.py 파일을 실행해서 백본을 디자인할 것인데,  
2. `'contigmap.contigs=[150-150]'`: 디자인할 백본의 길이는 150개로 고정하고,  
3. `inference.output_prefix="OUTPUT/DIRECTORY/PATH"`: 백본을 디자인하면 "OUTPUT/DIRECTORY/PATH" 경로에 저장하고,
4. `inference.num_designs=10`: 총 10가지의 백본을 디자인한다.


추가로 설명하자면  

2. `'contigmap.contigs=[150-150]'`:  
이 옵션에서 대괄호([, ]) 안의 값을 조정하면 길어 범위를 조절할 수 있습니다.  
고정된 범위(단일 길이)를 주고자한다면 대괄호([, ]) 안의 대쉬(-)를 기준으로 위 예시와 같은 앞과 뒤를 모두 같은 값으로 설정합니다.  

만약, 백본의 길이를 범위로 설정하고 싶을 경우 대괄호([, ]) 안의 대쉬(-)를 기준으로 앞은 백본 최소 길이, 뒤는 최대 길이를 입력하면 됩니다.  
이때, 범위 중 랜덤으로 길이를 선택하여 디자인합니다.  

> CODE EX: `'contigmap.contigs=[10-100]'`: 디자인할 백본의 길이를 10~100 중 랜덤으로 디자인  

이 외, **RFdiffusion**은 크게 4가지 유형의 백본을 디자인할 수 있습니다.

### 첫 째, Motif Scaffolding Design  
### 둘 째, Binder Design  
### 셋 째, Fold Conditioning  
### 넷 째, Symmetric Oligomers Generation  
---

그외 선택 옵션은 다음과 같으며, **RFdiffusion**의 다양한 설계 유형의 일반적으로 사용되는 옵션의 경우 아래에 예시와 같이 더욱 자세히 설명합니다.  

1. `inference.input_pdb=path/to/file.pdb`:
   **Motif Scaffolding**과 같이 **Reference PDB**를 사용하는 경우, 해당 **Reference PDB**의 경로를 설정합니다.  
2. `inference.ckpt_override_path=models/ActiveSite_ckpt.pt`:
   효소 활성 부위와 같은 매우 작은 부위를 스캐폴딩하고자 할 때, **RFdiffusion**을 미세 조정하여 더 작은 모티프를 잘 고정하도록하는 옵션입니다.  
3. `'contigmap.inpaint_seq=[A1/A30-40]'`: 
   **Motif Scaffolding**과 같이 **Reference PDB**를 사용할 때, **Motif**로 삼을 잔기의 위치정보(3차원 좌표 정보)는 차용하지만 시퀀스를 마스킹하는 옵션입니다.  
   이 경우 공간적 좌표정보는 유지하지만 기존의 시퀀스를 가져오지 않음으로써 노이즈를 부여하는 것과 같은 역할을 합니다. 즉, 다양성을 부여하는 기능입니다.  
   이러한 마스킹의 경우 백본 시퀀스인 **G(Gly)**으로 대체됩니다.  
4. `diffuser.partial_T=20`:
   보다 다양한 디자인을 생성하기 위해 노이즈를 강제로 부여하는 것으로, 해당 옵션을 주지 않을 때의 기본 값은 `diffuser.partial_T=50`으로 설정되어 있습니다.  
5. `'contigmap.provide_seq=[100-119, 200-205]'`:  
   `diffuser.partial_T=20` 옵션과 함께 사용하며, **Motif Scaffolding**과 같이 **Reference PDB** 구조의 특정 범위의 시퀀스를 차용합니다.  
   [100-119]와 같이 하나의 범위만 지정할 수 있으며, [100-119, 200-205]와 같이 콤마(,)로 구분하여 다양한 구간의 잔기 범위를 지정할 수 있습니다.  
6. `denoiser.noise_scale_ca=0.5 denoiser.noise_scale_frame=0.5`:  
   3번의 `'contigmap.inpaint_seq=[A1/A30-40]'`와 유사하게 노이즈를 통한 다양성 확보를 위한 옵션입니다.  
7. `'ppi.hotspot_res=[A30,A33,A34]'`:  
   **Hotspot** 정보에 대한 옵션입니다. 해당 옵션을 사용하여 **Binder Desing** 시 설계할 **Binder**가 결합하기를 원하는 **Hotspot**을 지정해줍니다.  
8. `inference.ckpt_override_path=models/Complex_beta_ckpt.pt`:  
   기본적으로 **RFdiffusion**은 **Linear**한 백본을 디자인하는 경향이 있습니다. 설계하려는 펩타이드 또는 단백질의 길이 범위에 따라 **Helix** 구조로 디자인되기도하지만, 연구자는 **Beta Sheet** 형태로 설계하고 싶을 수 있습니다.  
   이때, 위 옵션을 통해 **Beta Sheet**로 설계될 수 있도록 하는 옵션입니다.  
9. `--config-name symmetry`:  
   **Symmetric Oligomers** 유형의 백본을 설계하기 위한 옵션입니다. 
10. `inference.symmetry="type/of/Symmetric/Oligomers"`:  
    **Symmetric Oligomers**에 해당하는 여러 유형 중 어떤 유형을 설계할지 지정해주는 옵션입니다. **큰 따옴표(`"`)** 안에 어떤 유형의 **Symmetric Oligomers**를 설계한지 지정해줘야하며 `9.` 옵션과 반드시 함께 사용되어야 합니다.  
---

### Options for Using Auxiliary Potentials  

해당 옵션은 **Auxiliary Potentials(보조전위)** 사용에 관한 옵션입니다. **Symmetric Oligomers**를 설계할 때 함께 사용할 수 있는 옵션이며, 필수는 아닙니다.  
**Auxiliary Potentials**란 RFdiffusion이 대칭적 노이즈를 통해 대칭형 백본을 설계할 때, 외부 개입을 통해 백본 설계 시 구조를 유도하는 과정입니다.  
관련 옵션은 아래 **11. ~ 15.** 과 같으며, 여기서는 각 옵션에 대해 설명하기 위해 구분하였지만, 모두 함께 사용합니다.

> CODE EX: `'potentials.guiding_potentials=["type:olig_contacts,weight_intra:1,weight_inter:0.1"]' potentials.olig_intra_all=True potentials.olig_inter_all=True potentials.guide_scale=2.0 potentials.guide_decay="quadratic"`  


11. `'potentials.guiding_potentials=["type:olig_contacts,weight_intra:1,weight_inter:0.1"]'`:  
    **Auxiliary Potentials**를 활용하여 **Symmetric Oligomers**를 설계하겠다는 옵션입니다.  
    `weight_inter`는 외부, 즉 다른 것과 상호 관계에 해당하는 전위이며, `weight_intra`는 내부, 즉 설계할 **Symmetric Oligomers** 내부의 전위를 의미합니다.  
    `weight_intra`, `weight_inter`의 값은 사용자가 지정할 수 있으며, `weight_intra`의 값이 `weight_inter` 값 보다 반드시 높아야 합니다.  
    `weight_intra`는 정수값을 가지며, 최대 값은 없습니다. `weight_inter` 값은 `0~1 사이의 정수 값`을 가집니다.  
12. `potentials.olig_intra_all=True`:  
    11. 옵션의 `weight_intra` 옵션을 사용하겠다는 의미의 선언입니다.  
13. `potentials.olig_inter_all=True`:  
    11. 옵션의 `weight_inter` 옵션을 사용하겠다는 의미의 선언입니다.  
14. `potentials.guide_scale=2.0`:  
    **Auxiliary Potentials**를 스케일을 설정하는 옵션입니다. `2.0`에 해당하는 값은 임으로 변경하여 사용 가능합니다.  
15. `potentials.guide_decay="quadratic"`:  
    디자인되는 **Oligomers**의 형태에 해당하며, `quadratic` 외에 `constant`,`linear`,`quadratic`,`cubic` 로 사용할 수 있습니다.
---

16. `scaffoldguided.target_pdb=True`:  
    아래 `10.` 옵션과 함께 사용하는 옵션입니다. 특정 타겟에 대한 2차 레퍼런스 구조를 활용하겠다는 의미입니다.  
17. `scaffoldguided.target_path=examples/input_pdbs/insulin_target.pdb`:  
    특정 타겟에 대한 2차 구조 입력을 위한 스크립트를 활용하겠다는 옵션입니다. 반드시 `9.` 옵션과 함께 사용해야 합니다.  
18. `scaffoldguided.mask_loops=False`:    
    모든 잔기에 대해 정확한 2차 구조를 지정하지 않고도 추론 시 추가 잔기를 생성하기 위한 옵션입니다. 만약 3개의 긴 나선 번들을 만들고 싶을 경우 로프를 마스킹하는 식으로 활용할 수 있습니다.  
19. scaffoldguided.scaffold_dir=examples/ppi_scaffolds/: 
    


## Motif Scaffolding Design

**Motif Scaffolding Design**이란 원하는 모티프에서 파생된 기능을 가진 단백질을 구성하기 위한 스캐폴드의 구조를 설계하는 것입니다.  
쉽게 말하면 특정 구조를 가지며 타겟 단백질과 결합하여 연구자(또는 단백질 설계자)가 원하는 기능을 수행하지만, 타겟 단백질의 원래 기능을 변화시키지 않는 구조 단백질을 설계하는 것입니다.  
이러한 목표를 달성하기 위해 사용할 수 있는 옵션은 다음과 같습니다. 이러한 옵션을 필수 옵션과 선택적으로 사용가능한 옵션으로 구분됩니다.

**Requirement Option!**  
1. `inference.input_pdb=path/to/file.pdb`: **Scafold Motif**를 위한 레퍼런스 pdb 파일의 경로

**Options**
2. `inference.ckpt_override_path=models/ActiveSite_ckpt.pt`: 효소 활성 부위와 같은 매우 작은 부위를 스캐폴딩하고자 할 때, **RFdiffusion**을 미세 조정하여 더 작은 모티프를 잘 고정하도록하는 옵션입니다.


위 옵션에는 나와있지 않지만, **RFdiffusion**을 실행하기 위한 필수 옵션 중 백본의 길이 옵션을 다음과 같이 변경하여 활용할 수도 있습니다.

1. `'contigmap.contigs=[150-150]'`:  
   디자인 할 백본의 길이를 150 개로 고정  
2. `'contigmap.contigs=[10-150]'`:  
   디자인 할 백본의 길이를 10~150개의 범위로 설정  
3. `'contigmap.contigs=[5-15/A10-25/30-40/0 B1-100]'`:  
   디자인할 백본의 길이를 **Chain break**를 통해 구분하되, 하나의 **Chain**은 **A Chain**의 10~25번 잔기를 **Motif**로 고정하고, 기준으로 삼습니다.  
   기준으로 삼은 **A Chain**의 10~25 잔기의 왼쪽(N-term)의 길이를 5~15 범위로 디자인하고,  
   기준으로 삼은 **A Chain**의 10~25 잔기의 오른쪽(C-term)의 길이를 30~40 범위로 디자인합니다.  
   그리고 남은 다른 하나의 **Chain**은 **B Chain**의 1~100번 잔기를 Motif로 고정합니다.
   
   * 이러한 옵션은 Motif로 삼을 Reference 단백질의 PDB 파일을 필요로하기 때문에, Requirement Option의 레퍼런스 pdb 파일 경로 옵션이 필수적입니다.  
     기본적인 길이 범위 옵션과 마찬가지로, 고정된 범위(단일 길이)를 주고자한다면 대괄호([, ]) 안의 대쉬(-)를 기준으로 위 예시와 같은 앞과 뒤를 모두 같은 값으로 설정합니다.  

> CODE EX_1: `./scripts/run_inference.py inference.output_prefix=output/path inference.input_pdb=input/pdb/path.pdb 'contigmap.contigs=[150-150]' inference.ckpt_override_path=models/ActiveSite_ckpt.pt inference.num_designs=10`  
> CODE EX_2: `./scripts/run_inference.py inference.output_prefix=example_outputs/design_partialdiffusion inference.input_pdb=examples/input_pdbs/2KL8.pdb 'contigmap.contigs=[79-79]' inference.num_designs=10 diffuser.partial_T=10`  
> CODE EX_3: `./scripts/run_inference.py inference.output_prefix=example_outputs/design_partialdiffusion_peptidewithsequence inference.input_pdb=examples/input_pdbs/peptide_complex_ideal_helix.pdb 'contigmap.contigs=["172-172/0 34-34"]' diffuser.partial_T=10 inference.num_designs=10 'contigmap.provide_seq=[172-205]’`  
> CODE EX_4: `./scripts/run_inference.py inference.output_prefix=example_outputs/design_partialdiffusion_peptidewithmultiplesequence inference.input_pdb=examples/input_pdbs/peptide_complex_ideal_helix.pdb 'contigmap.contigs=["172-172/0 34-34"]' diffuser.partial_T=10 inference.num_designs=10 'contigmap.provide_seq=[172-177,200-205]’`  
> CODE EX_5: `./scripts/run_inference.py 'contigmap.contigs=[B1-100/0 100-100]' inference.output_prefix=test_outputs/binder_test inference.num_designs=10`


## Binder Design

**Binder Design**이란 타겟 단백질에 결합하는 단백질 또는 펩타이드를 디자인하는 과정입니다. 일반적으로 단백질-단백질 상호작용체는 세부적인 상호작용 부위가 존재하며, 이를 **Hotspot**라고 합니다.  
타겟 단백질의 이러한 **Hotspot**을 타겟으로 결합하는 **Binder**를 디자인하는 것은 단백질-단백질 상호작용 저해제를 디자인하는 가장 일반적이고 효율적인 방법이 될 수 있습니다.  
이러한 **Binder Design**을 위해서는 **Hotspot** 정보를 필요로하는 경우가 발생할 수 있기 때문에, 이 경우 타겟 단백질에 대한 **Reference PDB** 파일이 필수적입니다.  
만약 **Hotspot**에 대한 정보를 모를 경우 `'contigmap.contigs=[B1-100/0 100-100]'`와 같이 디자인하고자 하는 백본의 길이 정보에 특정 **Chain**을 입력(여기서는 **B Chain**의 `1~100`번 잔기)하고, **Chain Break**를 통해 특정 Chain에 결합하는 Binder를 디자인할 수 있습니다.  

즉, **Binder Design**을 위해서는 맨 위 언급한 **RFdiffusion** 실행을 위한 필수 옵션 3가지 외에, **Reference PDB**, **Hotspot** 정보에 대한 옵션을 선택적으로 사용할 수 있습니다.  
하지만 단백질-단백질 상호작용 저해를 위한 **Binder Design** 시 단백질 구조 분석을 통해 **Hotspot**을 예측, 식별하는 과정을 거치기 때문에, **Reference PDB**, **Hotspot** 옵션을 지정하는 것이 좋습니다.  

1. `denoiser.noise_scale_ca=0.5 denoiser.noise_scale_frame=0.5`: 
   `0.5`로 입력되어 있는 부분의 값은 `0~1` 사이의 값을 가지며, 노이즈의 양에 해당합니다. 따라서 값이 낮아질 수록 노이즈가 낮아지며 다양성이 그만큼 감소하게 됩니다.  
   일반적으로 가장 좋은 값은 중간 값이 `0.5`로 적당한 노이즈를 제공하여 다양성을 확보합니다. 만약 값을 너무 높게 설정하여 큰 노이즈를 준다면 다양성은 확보되는 반면, 통일성이 없어질 수 있습니다.  
2. `'ppi.hotspot_res=[A30,A33,A34]'`:  
    **Hotspot**에 해당하는 잔기의 **Chain**과 잔기 번호(시퀀스 서열 상 몇 번째 시퀀스인지)를 `A30`와 같이 입력합니다.  

> CODE EX_1: `./scripts/run_inference.py 'contigmap.contigs=[B1-100/0 100-100]' inference.output_prefix=test_outputs/binder_test inference.num_designs=10`  
> CODE EX_2: `./scripts/run_inference.py inference.output_prefix=example_outputs/design_ppi inference.input_pdb=examples/input_pdbs/insulin_target.pdb 'contigmap.contigs=[A1-150/0 70-100]' 'ppi.hotspot_res=[A59,A83,A91]' inference.num_designs=10 denoiser.noise_scale_ca=0 denoiser.noise_scale_frame=0`  


## Fold Conditioning

백본을 디자인하는 방법은 특정 **topologie**와 같은 조건을 충족하는 백본을 설계하는 것이 중요합니다. 이러한 목표를 달성하기위해 **2차 구조** 및 **Block adjacency** 정보를 활용합니다.  
**RFdiffusion**에서는 PDB 구조에서 직접 입력을 생성하는 방식을 사용하며, 이를 통해 **output topologie**의 저해상도 지정을 가능하게 합니다.

(작성해야 함)


## Symmetric Oligomers Generation  

**Symmetric Oligomers**는 서로 대칭을 이루는 구조의 백본을 디자인하고자 할 때 활용됩니다. 대표적인 예로 **Cyclic form**의 단백질 또는 펩타이드 설계가 해당됩니다.  
**RFdiffusion**에서는 3가지 유형의 **Symmetric Oligomers**를 설계할 수 있습니다.  

### 첫 째, Cyclic symmetry  
### 둘 째, Dihedral symmetry  
### 셋 재, Tetrahedral symmetry  

각 유형에 따른 기준 축은 다음과 같습니다.

**Cyclic symmetry: Z**
**Dihedral symmetry: Z**
**Tetrahedral symmetry: X**


1. `--config-name symmetry`:  
   앞서 설명한 것과 같이 **Symmetric Oligomers** 유형의 백본을 디자인하겠다는 의미의 옵션입니다.  
2. `inference.symmetry=type/of/Symmetric/Oligomers`:  
   **Symmetric Oligomers**의 유형 중 **Cyclic / Dihedral / Tetrahedral** 중 어떤 유형의 **Symmetric Oligomers**를 디자인할 지에 대해 선언해주는 옵션입니다.  
   만약 **Cyclic symmetry Oligomer**를 디자인하고자 한다면 `inference.symmetry=cyclic`와 같이 사용할 수 있습니다.  
   추가적으로, 단순히 **Cyclic symmetry Oligomer**가 아닌 **n면체 Cyclic symmetry Oligomer**를 디자인하고자 할 경우 `inference.symmetry=C6` 와 같이 사용할 수 있습니다.  여기서 `C`는 **Cyclic**의 약자이며, 뒤에 `6`은 6면으로 구성된 **Cyclic** 백본을 디자인하겠다는 의미입니다.  
   `6`은 사용자 임의로 변경할 수 있습니다. 이 때, 중요한 것은 백본의 길이를 지정하는 옵션인 `'contigmap.contigs=[12-12]’`가 `6`의 배수로 지정되어야 한다는 것입니다.

   > CODE EX_1: `./scripts/run_inference.py --config-name symmetry  inference.symmetry=Cyclic 'contigmap.contigs=[120-120]' inference.output_prefix=test_sample/tetrahedral inference.num_designs=1`  
   > CODE EX_2: `./scripts/run_inference.py --config-name=symmetry inference.symmetry="C6" inference.num_designs=10 inference.output_prefix="output_jp/C2_oligo" 'contigmap.contigs=[12-12]’`

   만약 **Dihedral symmetry Oligomer**를 디자인하고자 한다면 `inference.symmetry=Dihedral`와 같이 사용할 수 있습니다. 
   **n면체 Cyclic symmetry Oligomer**와 같이 **n면체 Dihedral symmetry Oligomer**를 디자인하고자 할 경우 `inference.symmetry=D2`와 같이 사용할 수 있습니다. 여기서 `D`는 **Dihedral**의 약자이며, 뒤에 `2`은 2면으로 구성된 **Dihedral** 백본을 디자인하겠다는 의미입니다.  
   `2`은 사용자 임의로 변경할 수 있습니다. 이 때, **n면체 Cyclic symmetry Oligomer**와 같이 백본의 길이를 지정하는 옵션인 `'contigmap.contigs=[12-12]’`가 `2`의 배수로 지정되어야 합니다.  

   > CODE EX_1: `./scripts/run_inference.py --config-name symmetry  inference.symmetry=Dihedral 'contigmap.contigs=[120-120]' inference.output_prefix=test_sample/tetrahedral inference.num_designs=1`  
   > CODE EX_2: `./scripts/run_inference.py --config-name=symmetry inference.symmetry="D2" inference.num_designs=10 inference.output_prefix="example_outputs/D2_oligo" 'contigmap.contigs=[12-12]'`  



   마지막으로 **Tetrahedral symmetry Oligomer**를 디자인하고자 한다면, `inference.symmetry=Tetrahedral`와 같이 사용할 수 있습니다.  
   마찬가지로, **n면체 Tetrahedral symmetry Oligomer**를 디자인하고자 할 경우 `inference.symmetry=T2`와 같이 사용할 수 있습니다. 여기서 `D`는 **Dihedral**의 약자이며, 뒤에 `2`은 2면으로 구성된 **Dihedral** 백본을 디자인하겠다는 의미입니다.  
   `2`은 사용자 임의로 변경할 수 있습니다. 이 때, **n면체 Dihedral symmetry Oligomer**와 같이 백본의 길이를 지정하는 옵션인 `'contigmap.contigs=[12-12]’`가 `2`의 배수로 지정되어야 합니다.  

   > CODE EX_1: `./scripts/run_inference.py --config-name symmetry  inference.symmetry=tetrahedral 'contigmap.contigs=[120-120]' inference.output_prefix=test_sample/tetrahedral inference.num_designs=1`
   > CODE EX_2: `./scripts/run_inference.py --config-name=symmetry inference.symmetry="T2" inference.num_designs=10 inference.output_prefix="example_outputs/T2_oligo" 'contigmap.contigs=[12-12]'`  

<br>
<br>
<br>

# ___ END