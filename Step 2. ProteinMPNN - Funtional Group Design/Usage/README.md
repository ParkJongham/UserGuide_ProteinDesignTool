# ProteinMPNN_usage
한국어 버전
![ProteinMPNN](https://docs.google.com/drawings/d/e/2PACX-1vTtnMBDOq8TpHIctUfGN8Vl32x5ISNcPKlxjcQJF2q70PlaH2uFlj2Ac4s3khnZqG1YxppdMr0iTyk-/pub?w=889&h=358)

원 깃허브에 들어가서 코드를 숙지하고
https://github.com/dauparas/ProteinMPNN

아래 논문을 참고하여 기본 구조를 익힐 것
J. Dauparas et al. ,Robust deep learning–based protein sequence design using ProteinMPNN.Science378,49-56(2022).DOI:10.1126/science.add2187

Input: PDB
Output: Fasta
```
>3HTN, score=1.1705, global_score=1.2045, fixed_chains=['B'], designed_chains=['A', 'C'], model_name=v_48_020, git_hash=015ff820b9b5741ead6ba6795258f35a9c15e94b, seed=37
NMYSYKKIGNKYIVSINNHTEIVKALNAFCKEKGILSGSINGIGAIGELTLRFFNPKTKAYDDKTFREQMEISNLTGNISSMNEQVYLHLHITVGRSDYSALAGHLLSAIQNGAGEFVVEDYSERISRTYNPDLGLNIYDFER/NMYSYKKIGNKYIVSINNHTEIVKALNAFCKEKGILSGSINGIGAIGELTLRFFNPKTKAYDDKTFREQMEISNLTGNISSMNEQVYLHLHITVGRSDYSALAGHLLSAIQNGAGEFVVEDYSERISRTYNPDLGLNIYDFER
>T=0.1, sample=1, score=0.7291, global_score=0.9330, seq_recovery=0.5736
NMYSYKKIGNKYIVSINNHTEIVKALKKFCEEKNIKSGSVNGIGSIGSVTLKFYNLETKEEELKTFNANFEISNLTGFISMHDNKVFLDLHITIGDENFSALAGHLVSAVVNGTCELIVEDFNELVSTKYNEELGLWLLDFEK/NMYSYKKIGNKYIVSINNHTDIVTAIKKFCEDKKIKSGTINGIGQVKEVTLEFRNFETGEKEEKTFKKQFTISNLTGFISTKDGKVFLDLHITFGDENFSALAGHLISAIVDGKCELIIEDYNEEINVKYNEELGLYLLDFNK
>T=0.1, sample=2, score=0.7414, global_score=0.9355, seq_recovery=0.6075
NMYKYKKIGNKYIVSINNHTEIVKAIKEFCKEKNIKSGTINGIGQVGKVTLRFYNPETKEYTEKTFNDNFEISNLTGFISTYKNEVFLHLHITFGKSDFSALAGHLLSAIVNGICELIVEDFKENLSMKYDEKTGLYLLDFEK/NMYKYKKIGNKYVVSINNHTEIVEALKAFCEDKKIKSGTVNGIGQVSKVTLKFFNIETKESKEKTFNKNFEISNLTGFISEINGEVFLHLHITIGDENFSALAGHLLSAVVNGEAILIVEDYKEKVNRKYNEELGLNLLDFNL
```


### Installation
```git clone https://github.com/dauparas/ProteinMPNN.git```
을 이용하여 간단히 설치
```
conda create --name mlfold
\ source activate mlfold
\ conda install pytorch torchvision torchaudio cudatoolkit=11.3 -c pytorch
```
을 이용하여 conda environment 설정

### Usage (monomer)
code organization
```
python ~/ProteinMPNN/protein_mpnn_run.py --[options]
```
ex) 
```
python ~/ProteinMPNN/protein_mpnn_run.py --pdb_path 1qys.pdb #input path --out_folder ./ #output path --num_seq_per_target 1 #number sequence per target
```

Input PDB 파일: input.pdb


### Usage (bias)
residue bias
```
python ~/ProteinMPNN/helper_scripts/make_bias_AA.py --output_path output --AA_list "text" --bias_list "numeric"
```

### Usage (fixing residue)
residue fixing
```
python ~/ProteinMPNN/helper_scripts/parse_multiple_chains.py --input_path test --output_path testoutput
```
을 통해 준비
다음,
```
python ~/ProteinMPNN/helper_scripts/assign_fixed_chains.py --input_path testoutput --output_path testoutput2  --chain_list "X"
```
Output looks like:
`{"5TTA": [["A"], ["B"]], "3LIS": [["A"], ["B"]]}`

```
python ~/ProteinMPNN/helper_scripts/make_fixed_positions_dict.py --specify_non_fixed --position_list "1 3 4 ..."  --chain_list "X"  --input_path testoutput --output_path testoutput3
```
Output looks like:
`{"7JZM": {"A": [1, 2, 3, 7, ..., 33], "B": []}, ...}`

최종적으로
```
python ~/ProteinMPNN/protein_mpnn_run.py
--jsonl_path testoutput
--chain_id_jsonl testoutput2 # assinged file
--fixed_positions_jsonl testoutput3 # fixed filde
--out_folder 
--num_seq_per_target 10 # number of sequence per target
--sampling_temp "0.1" # default value 0.1
--seed 0 # 없어도됨
--batch_size 1 # batch size, 그래픽카드 사양에 따라
--save_score 1 # False=0, True=1, save score=-log_prob to npy files
							 # default=0
--save_probs 1 # save MPNN predicted probabilites per position
							 # default=0
```


### Options
Full protein backbone models
`vanilla_model_weights/v_48_002.pt, v_48_010.pt, v_48_020.pt, v_48_030.pt`, `soluble_model_weights/v_48_010.pt, v_48_020.pt`
CA only models
`ca_model_weights/v_48_002.pt, v_48_010.pt, v_48_020.pt`. Enable flag `**--ca_only**`


```
    argparser.add_argument("--suppress_print", type=int, default=0, help="0 for False, 1 for True")
    argparser.add_argument("--ca_only", action="store_true", default=False, help="Parse CA-only structures and use CA-only models (default: false)")
    argparser.add_argument("--path_to_model_weights", type=str, default="", help="Path to model weights folder;")
    argparser.add_argument("--model_name", type=str, default="v_48_020", help="ProteinMPNN model name: v_48_002, v_48_010, v_48_020, v_48_030; v_48_010=version with 48 edges 0.10A noise")
    argparser.add_argument("--use_soluble_model", action="store_true", default=False, help="Flag to load ProteinMPNN weights trained on soluble proteins only.")
    argparser.add_argument("--seed", type=int, default=0, help="If set to 0 then a random seed will be picked;")
    argparser.add_argument("--save_score", type=int, default=0, help="0 for False, 1 for True; save score=-log_prob to npy files")
    argparser.add_argument("--path_to_fasta", type=str, default="", help="score provided input sequence in a fasta format; e.g. GGGGGG/PPPPS/WWW for chains A, B, C sorted alphabetically and separated by /")
    argparser.add_argument("--save_probs", type=int, default=0, help="0 for False, 1 for True; save MPNN predicted probabilites per position")
    argparser.add_argument("--score_only", type=int, default=0, help="0 for False, 1 for True; score input backbone-sequence pairs")
    argparser.add_argument("--conditional_probs_only", type=int, default=0, help="0 for False, 1 for True; output conditional probabilities p(s_i given the rest of the sequence and backbone)")
    argparser.add_argument("--conditional_probs_only_backbone", type=int, default=0, help="0 for False, 1 for True; if true output conditional probabilities p(s_i given backbone)")
    argparser.add_argument("--unconditional_probs_only", type=int, default=0, help="0 for False, 1 for True; output unconditional probabilities p(s_i given backbone) in one forward pass")
    argparser.add_argument("--backbone_noise", type=float, default=0.00, help="Standard deviation of Gaussian noise to add to backbone atoms")
    argparser.add_argument("--num_seq_per_target", type=int, default=1, help="Number of sequences to generate per target")
    argparser.add_argument("--batch_size", type=int, default=1, help="Batch size; can set higher for titan, quadro GPUs, reduce this if running out of GPU memory")
    argparser.add_argument("--max_length", type=int, default=200000, help="Max sequence length")
    argparser.add_argument("--sampling_temp", type=str, default="0.1", help="A string of temperatures, 0.2 0.25 0.5. Sampling temperature for amino acids. Suggested values 0.1, 0.15, 0.2, 0.25, 0.3. Higher values will lead to more diversity.")
    argparser.add_argument("--out_folder", type=str, help="Path to a folder to output sequences, e.g. /home/out/")
    argparser.add_argument("--pdb_path", type=str, default='', help="Path to a single PDB to be designed")
    argparser.add_argument("--pdb_path_chains", type=str, default='', help="Define which chains need to be designed for a single PDB ")
    argparser.add_argument("--jsonl_path", type=str, help="Path to a folder with parsed pdb into jsonl")
    argparser.add_argument("--chain_id_jsonl",type=str, default='', help="Path to a dictionary specifying which chains need to be designed and which ones are fixed, if not specied all chains will be designed.")
    argparser.add_argument("--fixed_positions_jsonl", type=str, default='', help="Path to a dictionary with fixed positions")
    argparser.add_argument("--omit_AAs", type=list, default='X', help="Specify which amino acids should be omitted in the generated sequence, e.g. 'AC' would omit alanine and cystine.")
    argparser.add_argument("--bias_AA_jsonl", type=str, default='', help="Path to a dictionary which specifies AA composion bias if neededi, e.g. {A: -1.1, F: 0.7} would make A less likely and F more likely.")
    argparser.add_argument("--bias_by_res_jsonl", default='', help="Path to dictionary with per position bias.")
    argparser.add_argument("--omit_AA_jsonl", type=str, default='', help="Path to a dictionary which specifies which amino acids need to be omited from design at specific chain indices")
    argparser.add_argument("--pssm_jsonl", type=str, default='', help="Path to a dictionary with pssm")
    argparser.add_argument("--pssm_multi", type=float, default=0.0, help="A value between [0.0, 1.0], 0.0 means do not use pssm, 1.0 ignore MPNN predictions")
    argparser.add_argument("--pssm_threshold", type=float, default=0.0, help="A value between -inf + inf to restric per position AAs")
    argparser.add_argument("--pssm_log_odds_flag", type=int, default=0, help="0 for False, 1 for True")
    argparser.add_argument("--pssm_bias_flag", type=int, default=0, help="0 for False, 1 for True")
    argparser.add_argument("--tied_positions_jsonl", type=str, default='', help="Path to a dictionary with tied positions")
```

### Example files
These are provided `examples/`:
* `submit_example_1.sh` - 간단한 단량체
* `submit_example_2.sh` - 간단한 multi chain 예시
* `submit_example_3.sh` - directly from the .pdb path
* `submit_example_3_score_only.sh` - return score only (model's uncertainty)
* `submit_example_3_score_only_from_fasta.sh` - return score only (model's uncertainty) loading sequence from fasta files
* `submit_example_4.sh` - 잔기 고정
* `submit_example_4_non_fixed.sh` - 디자인 포지션 고정
* `submit_example_5.sh` - symmetry 구조
* `submit_example_6.sh` - 호모올리고머 예시
* `submit_example_7.sh` - return sequence unconditional probabilities (PSSM like)
* `submit_example_8.sh` - 잔기편향 예시
* `submit_example_pssm.sh` - use PSSM bias when designing sequences
이해가 가지 않는다면 sh 파일 내부를 참고할 것

### Citation
```
@article{dauparas2022robust,
  title={Robust deep learning--based protein sequence design using ProteinMPNN},
  author={Dauparas, Justas and Anishchenko, Ivan and Bennett, Nathaniel and Bai, Hua and Ragotte, Robert J and Milles, Lukas F and Wicky, Basile IM and Courbet, Alexis and de Haas, Rob J and Bethel, Neville and others},
  journal={Science},
  volume={378},
  number={6615},  
  pages={49--56},
  year={2022},
  publisher={American Association for the Advancement of Science}
}
```
