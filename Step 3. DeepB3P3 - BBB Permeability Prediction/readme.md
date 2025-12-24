# DeepB3P3
DeepB3P3 의 한국어 버전

## 반드시 아래의 원 제작자 github를 참고해서 할 것

https://github.com/horsepurve/DeepB3P3

### Installation
```
git clone https://github.com/horsepurve/DeepB3P3.git
pip install -r requirements.txt
```
설치 완료


### Code for Training
```
mkdir temp
python DeepB3P3.py \
    --train_path 'bbbp/d3_train_a1x8.txt' \
    --test_path 'bbbp/d3_test_a1x8.txt' \
    --result_path 'temp/d3_test.pred.txt' \
    --log_path 'temp/d3_test.txt.log' \
    --max_length 75 \
    --conv1_kernel 10 \
    --conv2_kernel 10 \
    --regCLASS --LR 0.001 --EVALUATE_ALL --NUM_EPOCHS 50
```



로 학습 가능하나, 원 제작자의
### Analysis
의 Google Drive 에서 제공되는 학습 파일을 다운받으면 빠르게 이용 가능함

### Prediction
탭 에서는 원 제작자의 Colab 으로 쉽게 이용 가능

### Citation & References
```
@article{ma2023prediction,
  title={A prediction model for blood-brain barrier penetrating peptides based on masked peptide transformers with dynamic routing},
  author={Ma, Chunwei and Wolfinger, Russ},
  journal={Briefings in Bioinformatics},
  volume={24},
  number={6},
  pages={bbad399},
  year={2023},
  publisher={Oxford University Press}
}
```
