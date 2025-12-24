# C2Pred & MLCPP & Protparam
이용 방법 설명

## C2Pred

### 반드시 논문을 참고하여 모델 특성을 이해하고 사용할 것
Prediction of cell-penetrating peptides with feature selection techniques
https://doi.org/10.1016/j.bbrc.2016.06.035

### Website 접속
http://lin-group.cn/server/C2Pred

### 주의 사항
Input 구조는 아래와 같이 (fasta format)
```
> test1
sequence1
> test2
sequence2
```
### Output like
test1 CPP
test2 Non-CPP

(1) sequence 는 100개 이하로 제한
(2) 위와 같이 Input 구조를 명확히 할 것
(2) Illegal character 발견 시 멈춤 현상


## MLCPP
해당 웹사이트 서버 문제로 웹 자동화는 어렵기 때문에, 사용자가 직접 입력해주어야 함.
데이터 형식은
```
> test1
sequence1
> test2
sequence2
```
와 같으며, input.fasta 로도 입력 가능
시간이 조금 걸릴 가능성이 있으니 기다려주면서 실행

## Protparam
https://web.expasy.org/protparam/
peptide sequence 하나하나 입력하여 compute parameters 버튼으로 하나하나 결과 붙여넣을 것
