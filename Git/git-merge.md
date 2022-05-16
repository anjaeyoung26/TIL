# git merge

## Squash Merge

<img src="https://user-images.githubusercontent.com/61190690/168548829-79273fc6-8767-4276-bcd0-a1a356e17a18.png">

서브 브랜치의 커밋 히스토리를 하나의 커밋으로 합쳐서 병합한다.

```
git checkout master
git merge --squash feature
git commit -m "새로운 병합 메시지"
```

&nbsp;
## Rebase Merge

<img src="https://user-images.githubusercontent.com/61190690/168548825-40d59170-7bdd-4daa-9e71-34a1a71e0dcc.png">

병합한 커밋 내역이 남지않아 하나의 브랜치에서 작업한 것처럼 보인다.

```
git checkout feature
git rebase master
git checkout master
git merge feature
```

&nbsp;
## Fast Forword Merge

![fast-forward-merge](https://user-images.githubusercontent.com/61190690/168543950-613a2f75-f625-49c9-bfab-25b0e77dd81d.png)

`topic` 브랜치는 `master` 브랜치와 fast-forward 관계이다.

&nbsp;
## —ff

![ff](https://user-images.githubusercontent.com/61190690/168543954-168024eb-7e2c-4f56-9f28-6d09ed720de5.png)

기본 옵션으로 병합 대상인 브랜치가 fast-forward 관계인 경우, 병합 커밋을 만들지않고 브랜치 태깅만 변경한다.

```
git merge topic
```

&nbsp;
## —no-ff

![no-ff](https://user-images.githubusercontent.com/61190690/168545063-544b2b0b-1ee8-40bb-a785-1b05ae8ba48e.png)

병합 대상인 브랜치가 fast-forward 관계인 경우에도 병합 커밋을 만든다.

```
git merge --no-ff topic
```