# git merge

## Squash Merge

<img src="https://user-images.githubusercontent.com/61190690/167257765-32e10bba-aa6c-4306-b9fe-aaca9362bf92.png" width="400" height="200">

서브 브랜치의 커밋 히스토리를 하나의 커밋으로 합쳐서 병합한다.

```
git checkout master
git merge --squash feature
git commit -m "새로운 병합 메시지"
```

&nbsp;
## Rebase Merge

<img src="https://user-images.githubusercontent.com/61190690/167257764-cc206100-ccb6-4d79-bf8e-63c8c1478950.png" width="400" height="200">

병합한 커밋 내역이 남지않아 하나의 브랜치에서 작업한 것처럼 보인다.

```
git checkout feature
git rebase master
git checkout master
git merge feature
```

&nbsp;
## Fast Forword Merge

![fast-forward-merge](https://user-images.githubusercontent.com/61190690/167257759-0847b850-7772-42ee-8193-bb46f5a2bb9f.png)

`topic` 브랜치는 `master` 브랜치와 fast-forward 관계이다.

&nbsp;
## —ff

![ff](https://user-images.githubusercontent.com/61190690/167257761-6d9840d5-35db-47e6-90c8-7a8c26c2954d.png)

기본 옵션으로 병합 대상인 브랜치가 fast-forward 관계인 경우, 병합 커밋을 만들지않고 브랜치 태깅만 변경한다.

```
git merge topic
```

&nbsp;
## —no-ff

![no-ff](https://user-images.githubusercontent.com/61190690/167257763-4924f37d-4aad-4516-aafe-534422f52f21.png)

병합 대상인 브랜치가 fast-forward 관계인 경우에도 병합 커밋을 만든다.

```
git merge --no-ff topic
```