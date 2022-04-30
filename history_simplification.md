History Simplification에 관한 정리 
================================

## 발단

```
git commit --amend
```

에 대한 2번 테스트를 진행하다가, Desktop에서 강제 Push한 것을 Notebook에서 Pull하여 기존의 수정된 커밋을 그대로 보유한 상태로 원격 저장소와 로컬 저장소를 Merge하게 되었다. 

그 상태에서 Notebook에서 다시 Push를 하니, 정상적으로 Push가 완료되었고, GitHub 상에서는 test.html 파일에 대한 Commit History가 의도했던대로 출력되지 않았다.

```
git commit --amend
```

를 통해 수정한 커밋이 아닌 수정 대상이었던 기존의 커밋이 최신 커밋으로서 File History에 나오게 된 것이다. 

따라서 원인을 찾아보게 되었는데, 실제로 

```
git log --graph
```

를 통해 출력한 History 상에서는

<hr>

M   <--- Merge Commit   
| &nbsp;&nbsp;\    
| &nbsp;&nbsp;&nbsp;A   <--- Amend Commit   
| &nbsp;&nbsp;/    
O   <--- Original Commit(modified by A commit)

<hr>

이런 식으로 기준 브랜치와 Merge 된 브랜치 상의 커밋이 모두 출력되었다. 

하지만 test.html을 대상으로 한 

```
git log --graph test.html
```

에서는 

<hr>

O   
|   
|   
|   
C   <--- Other Commit(previous of Original)

<hr>

이런 식으로 출력되었다.

중요한 것은, Merge Commit, Amend Commit이 생략되었다는 것이다.

이는 History Simplification 때문이다.


## History Simplification에 관한 References

1. __https://git-scm.com/docs/git-log#_history_simplification__

2. __https://stackoverflow.com/questions/48814114/git-history-for-branch-after-merge__

3. __https://errorsfixing.com/git-commit-history-of-a-file-showing-the-right-commits/__

4. __https://docs.microsoft.com/en-us/azure/devops/repos/git/git-log-history-simplification?view=azure-devops__

5. __http://ohyecloudy.com/pnotes/archives/git-file-log-history-simplification/__


### File History Simplification Default Mode
<hr>

History Simplification에는 Default Mode가 존재한다. git-scm의 설명은 이렇다. 


    Default mode
    Simplifies the history to the simplest history explaining the final state of the tree. Simplest because it prunes some side branches if the end result is the same (i.e. merging branches with the same content)

해당 트리의 최종 상태를 설명하는 가장 간단한 히스토리로 만든다는 것이다. 만약 최종 결과가 똑같다면 몇몇 사이드 Branch들을 쳐내기도 한다. 

쉽게 말해 어떠한 사이드 Branch를 쳐내더라도 최종 결과가 현재와 달라지지 않는다면 사이드 Branch를 쳐낸다.

하지만 어떠한 상황에서 최종 결과가 달라지지 않고 문제가 발생하지 않는지 아직까지는 정확하게 알 수 없다.


그렇다면 다른 문서를 보자.

<hr>

__microsoft azure__ 의 docs에서 설명하는 바에 따르면 merge시에 충돌이 발생하고 이에 따라 side branch의 커밋 수정 사항을 적용하지 않고 merge를 했을 때, File History는 Side Branch의 Commit과 Merge Commit을 모두 생략하게 된다.

이것으로 볼 때, 내 생각으로는 최종 결과가 똑같다면 몇몇 사이드 Branch들을 쳐낸다는 얘기는 결국, 그 사이드 Branch를 쳐냈을 때 현재 파일의 최종 결과에 영향이 없다면, 즉, 최종 결과와 동일하다면 사이드 Branch를 쳐낸다는 뜻이라고 본다. 

이것을 증명하기 위해서 이번에는 반대로 충돌이 있었을 때 기준 브랜치와 사이드 브랜치의 커밋으로 인한 파일 수정 내용을 모두 그대로 적용하고 Merge를 했는데, File History에서는 Merge Commit과 Side Branch Commit이 모두 출력되었다. 

만약 Merge Commit과 Branch Commit을 쳐내게 되면 당연히 파일의 현재 최종 상태와 차이가 발생하게 되므로 이것을 File History 상에 포함한 것으로 보인다. 

즉, 파일의 최종 상태에 영향을 주는 커밋은 포함하고 빼도 상관없는 커밋은 뺀다는 말이다. 

그래서 테스트를 한 번 더 진행해봤는데, 이번에는 충돌 시 기준 브랜치의 커밋으로 인한 수정 사항은 제외하고 사이드 브랜치의 커밋으로 인한 수정 사항은 적용해서 Merge 해봤다. 

이번에는 기존의 기준 브랜치의 커밋은 제외되고, 사이드 브랜치의 커밋이 포함되어 출력되었으며, Merge Commit은 제외되었다. 

또한 흥미로운 점은, 사이드 브랜치의 커밋이 마치 기준 브랜치의 원래 커밋이었던 것처럼 표시되었다는 점이다. 즉, 사이드 브랜치로 갈라지는 게 아닌 하나의 줄기로 사이드 브랜치의 커밋이 기준 브랜치의 커밋들과 이어져 있었다. 

S  
|  
|  
|  
A  
|  
|  
B

이런 형식으로 말이다. 

즉, File History Simplification은 철저하게 파일의 현재 최종 상태에 영향을 주는 Commit만 고르는 것이라는 사실을 알 수 있었다. 

그러므로 결론적으로, git-scm의 Default Mode에 관한 설명 중 첫 문장은 사실이었다.

이제 History Simplification의 원리를 쉽게 알아보기 위해 다른 문서를 보자.


__Stack Overflow의 답변__

<hr>

https://localcoder.org/git-history-for-branch-after-merge 를 통해서 볼 수도 있다.

    When you run git log file.ext, Git will deliberately skip any non-merge commit where the diff (as obtained by comparing parent to child) does not touch file.ext. That's natural enough: if you have a chain like:

즉, File History는 해당 File의 변경 사항이 존재하지 않는 커밋을 생략한다.

어떠한 branch의 모습이 아래와 같다고 하자.

    A--B--C--D--E   <-- master

또한 A와 E에서 file을 수정하였다. 

이 때 

    git log file

을 실행한다. 그러면 Git은 master의 HEAD가 가리키는 커밋인 E에서부터 처리를 시작한다. 

E와 D를 비교하면 file 수정 사항이 E에는 있지만 D에는 존재하지 않는다. 

따라서 D 커밋을 생략한다. 

이제 D로 넘어와서 D와 C를 비교한다. 

D에서도 변경이 없었고, C에서도 변경이 없었으므로 C 커밋 또한 생략된다. 

이런 식으로 B까지 도달했다.

A와 B를 비교한다. A에서는 변경이 있었고 B에서는 없었다. 아마 B는 이미 생략되어있을 것이다. 

따라서 A는 생략되지 않는다. 

이렇게 첫 커밋인 A까지 도달하였다. 

이런 식으로 Git은 History를 출력하기 위해서 최신 커밋부터 첫 커밋까지 커밋들을 거쳐간다. 


이 문서에서는 Merge Commit이 존재하는 경우에 대해서는 다루지 않았다. 하지만 Git이 내부적으로 History Simplification을 위해 커밋들을 하나씩 거쳐가며 서로 비교한다는 것을 알아냈다. 


### git-scm의 세부 설명

<hr>

__배경지식__

사실 가장 상세하게 설명되어 있는 것은 역시 git-scm이었다. 하지만 관련된 정보를 알지 못하는 상태에서 문서를 봤을 때 이해가 안되는 부분이 있었다. 그래서 배경 지식을 먼저 이해하고 다음 내용을 보는 것을 추천한다. 

1. git object model : https://medium.com/mindorks/what-is-git-object-model-6009c271ca66

2. tree object : https://git-scm.com/book/ko/v2/Git%EC%9D%98-%EB%82%B4%EB%B6%80-Git-%EA%B0%9C%EC%B2%B4

3. first-parent of merge commit : https://www.designcise.com/web/tutorial/what-is-meant-by-first-parent-in-a-git-commit

<hr>

__TREESAME__

Git object 중에서 tree object가 존재한다. tree는 자신에게 할당된 디렉터리의 정보를 가지고 있는 객체다. 

또한 모든 Git commit은 tree object의 hash 값을 참조하고 있다. 따라서 모든 Git commit은 커밋 당시의 저장소가 관리하는 디렉터리의 정보를 가지고 있다.

따라서 디렉터리에 포함되어있는 추적되는 파일, 서브 디렉터리에 대한 정보가 각각의 Commit에 Tree를 통해 저장되어 있는 것이다. 

따라서 Git이 History Simplification을 위해 자식과 부모 커밋을 비교할 때는, 그 커밋에 할당된 tree object의 hash값들을 비교하게 된다. 

    git log file

명령을 실행했을 때, file에 관한 log를 보는 것이 목적이므로, 관련된 commit들만 나타낼 수 있도록 해야한다. 

따라서 각 커밋의 tree object에서 해당 file로 여겨지는(혹은 할당된) blob object의 hash값만을 고려해야 한다.

따라서 tree가 참조하는 전체 object를 비교하지 않고 file에 해당하는 Blob object만 비교하게 된다. 

__git-scm__ 에서는, 이 비교를 통해서, 두 커밋간 file에 할당된 Blob object의 hash값이 동일할 경우, 이를 __TREESAME__ 이라고 하고 있다.

TREESAME이라는 말을 TREE object가 서로 동일한 때를 뜻하는 거라고 착각하면 안된다.

이렇게 TREESAME이라는 것이 무엇인지, 어떤 식으로 TREESAME을 판단하는지 알았으니, 그 다음은 쉽다. 

<hr>

__History Simplification 동작 원리__

History Simplification에서는 두 가지 상황이 있다. 

1. 현재 커밋이 Merge Commit일 때
2. 현재 커밋이 Non-Merge Commit일 때

2 번의 경우는 앞에서 설명한 것과 동일하다. 변경이 있는 것을 포함하고 없는 것을 생략한다. 이 것을 좀 더 본질적으로 설명하면 


__현재 커밋이 부모 커밋과 TREESAME하면 현재 커밋을 생략하고, 그렇지 않으면 포함한다.__

1번의 경우는 어떻게 될까 

기본적으로 Merge Commit은 부모 커밋이 여러 개 이므로, 모든 부모 커밋과 현재 커밋을 비교한다. 

그렇게 해서 하나라도 TREESAME한 부모 커밋이 존재할 경우, 현재 커밋은 생략된다. 

여기서 추가적으로, TREESAME하지 않은 부모 커밋을 제외한 나머지 부모 커밋들 또한 생략된다. 

__즉, 거쳐가지 않는다.__

또한 TREESAME한 부모 커밋이 존재하지 않을 경우, 즉, 모든 부모 커밋과도 file 내용이 다를 경우 현재 커밋을 포함하고 모든 부모 커밋을 생략하지 않는다. 

__즉, 모든 부모를 거쳐간다.__ 

이 두 가지 상황만 이해할 수 있으면 Git History Simplification의 기본은 모두 이해한 것이다. 

git-scm에서는 이러한 브랜치를 예로 들어 설명한다.

<hr>

__History Simplification 예시__

	  .-A---M---N---O---P---Q
	 /     /   /   /   /   /
	I     B   C   D   E   Y
	 \   /   /   /   /   /
	  `-------------'   X


커밋들에 관한 정보

I : master 브랜치의 first-commit.   
A-Q까지 같은 줄에 있는 커밋들 : master 브랜치의 커밋.   
B-E까지 같은 줄에 있는 커밋들 : 각각 I에서 갈라진 Branch에 존재하는 커밋.   
X : independent 브랜치의 first-commit.

현재 master 브랜치의 HEAD가 가리키는 커밋은 Q 커밋이다. 즉, 최신 커밋은 Q이다.

    git log file

을 실행했다. 

file 내용에 관하여 

__I__

    안녕하세요.

__A__

    안녕하세요. 반갑습니다.

__B__

    안녕하세요. 반갑냐?

__M__

    안녕하세요. 반갑냐?

__C__

    넌 뭐냐?

__N__

    집에 가고 싶다.

__D__

    안녕하세요. 반갑냐?

__O__

    집에 가고 싶다.

__E__

    좋습니다.

__P__

    안 좋습니다.

__X__

    ㅇ먀ㅓ랴애ㅓ랭ㅁ

__Y__

    ㅁㅁ야러매어래ㅑㅁ어래

__Q__

    안 좋습니다.


1. 현재 커밋 : Q 커밋

    Q 커밋의 부모 커밋은 P, Y 커밋이다. 

    그리고 P 커밋과 Y 커밋의 내용은 각각 '안 좋습니다.', 'ㅁㅁ야러매어래ㅑㅁ어래'이다. 

    따라서 Q 커밋은 P 커밋과 TREESAME하다. 

    앞에서 말한대로, TREESAME한 부모 커밋이 있을 경우 현재 커밋은 생략된다. 따라서 Q 커밋은 생략된다. 

    그리고 P 커밋을 제외한 나머지 부모 커밋들은 모두 생략된다. 

    따라서 다음 거쳐갈 커밋은 자연스럽게 P 커밋이 된다. 

2. 현재 커밋 : P 커밋

    P 커밋의 부모는 O, E 커밋이다. O 커밋과 E 커밋의 내용은 '집에 가고 싶다.', '좋습니다.'이다. 

    따라서 P 커밋과 TREESAME한 부모 커밋은 존재하지 않는다. 따라서 P커밋은 포함된다. 또한 모든 부모 커밋을 거쳐간다. 

    따라서 현재 포함된 커밋은 P 커밋이다. 거쳐갈 커밋은 O, E 커밋이다. 

3. 현재 커밋 : O 커밋

    O 커밋의 부모 커밋은 N, D 커밋이다. 내용은 각각 '집에 가고 싶다.', '안녕하세요. 반갑냐?'이다.

    따라서 O 커밋은 N 커밋과 TREESAME하다. 

    마찬가지로, O커밋은 생략되며 N 커밋을 거쳐가기로 결정하고 나머지 부모 커밋인 D 커밋은 생략된다. 

4. 현재 커밋 : E 커밋

    E 커밋의 부모는 I 커밋 뿐이다. 내용은 '안녕하세요.'이다. 

    따라서 E 커밋은 I 커밋과 TREESAME하지 않다. 고로 E 커밋은 포함된다. I 커밋은 거쳐가기로 결정한다. 

    현재 포함된 커밋은 P, E 커밋이다. 거쳐갈 커밋은 N, I 커밋이다. 

5. 현재 커밋 : N 커밋

    부모는 M, C 커밋이다. 내용은 각각 '안녕하세요. 반갑냐?', '넌 뭐냐?'이다.

    따라서 N 커밋과 TREESAME한 부모 커밋은 존재하지 않는다. 따라서 N 커밋은 포함된다. 또한 모든 부모 커밋을 거쳐간다. 

    따라서 현재 포함된 커밋은 P, E, N 커밋이다. 거쳐갈 커밋은 I, M, C 커밋이다. 

6. 현재 커밋 : I 커밋

    루트 커밋이므로 부모 커밋이 없다. 포함한다. 

    따라서 현재 포함된 커밋은 P, E, N, I 커밋이다. 거쳐갈 커밋은 M, C 커밋이다. 

7. 현재 커밋 : M 커밋

    부모는 A, B이다. 내용은 각각 '안녕하세요.', '안녕하세요. 반갑냐?'이다.

    따라서 M 커밋은 B 커밋과 TREESAME하다. 

    고로 M 커밋은 생략되고, B 커밋을 거쳐가기로 결정한다. A 커밋은 생략된다. 

8. 현재 커밋 : C 커밋

    부모는 I 커밋이다. TREESAME 하지 않다. C 커밋을 포함한다. I 커밋을 거쳐가기로 한다. 근데 이미 포함되어 있다. 딱히 거쳐가지 않아도 될 것 같다...

    따라서 현재 포함된 커밋은 P, E, N, C, I 커밋이다. 거쳐갈 커밋은 B 커밋이다. 

9. 현재 커밋 : B 커밋

    부모는 I 커밋...

    따라서 현재 포함된 커밋은 P, E, N, C, B, I 커밋이다. 거쳐갈 커밋은 없다!


고로 명령어의 실행 결과는 대략 이러하다. 

	  B---N---P
	 /   /   /   
	I   C   E   
	 \ /   /    
	  `----   

매우 이상한 모양새가 되었다. 기존의 A 커밋이 삭제 되고 그 자리를 B가 대신한 모습이며, N 커밋이 B 커밋의 자식이 된듯한 모습이다.

C 커밋은 여전히 I 커밋에서 갈라져 나온 모양새이지만 N 커밋이 B, C 커밋을 병합한 것처럼 느껴진다. 실제로는 M 커밋이 B와 TREESAME이므로, file 내용 기준으로는 B와 C를 병합하여 N이 생성되었다고 볼 수도 있다. 

P 커밋 또한 마찬가지로 N과 E를 병합한 것처럼 느껴진다. 이 것 또한 file 내용 상으로는 그렇게 볼 수도 있다. 

결과적으로

    git log file
    
명령어를 실행했을 때 대상으로 했던 file에 관한 흐름을 잘 나타내는 간략한 History가 탄생하였다. 

이런 식으로, History Simplification은 사용자가 어떠한 대상을 고려하는지에 따라 필요 없는 커밋은 과감하게 생략한다. 그래서 대상의 최종 상태에 영향을 준 커밋들만 나타내도록 한다. 

<hr>

__정리__

결국, __History Simplification__ 은

- 부모와 TREESAME한 커밋을 History 상에서 제외
- Merge Commit과 TREESAME한 부모 커밋만을 따라가기 위한 목적을 가지고 있는 로그 단순화 방법

이다.

또한 그 __목적__ 은 

- 대상의 최종 상태까지의 변화를 나타내는 최적의 커밋만을 보여주는 것

이다.

__History Simplification의 단점__

이것은 파일의 최종 상태에 영향을 주는 커밋만 걸러서 볼 수 있도록 해서 어떤 커밋이 유의미한 수정 사항을 만들어냈는지 아는 데에는 도움이 되지만, 사이드 브랜치와의 Merge와 파일에 적용된 전체 History를 일부 생략하는 단점이 존재한다. 

따라서 

```
git log --full-history
```

명령을 통해 전체 History를 볼 수 있다. File History에도 물론 

```
git log --full-history [file-name]
```

의 형식으로 적용할 수 있기 때문에 전체 History를 보기 원한다면 위의 명령어를 사용해야 한다. 

__Push된 커밋 메시지 수정 시 유의사항__

Push된 Commit을 Amend한 후 그 상태로 push를 하게 되면, 다른 저장소에서 pull한 후에, amended commit이 다시 file history 상에 등장할 수 있다. 

그러므로 Amend Commit을 통해 기존의 Commit Message를 수정할 것이라면, 그 수정 사항을 원격 저장소에 강제 Push할 때 다른 로컬 저장소에서도 

```
git reset --hard [commit id just before amended commit]
git pull
```

명령어 등을 통해 완벽하게 Amended Commit을 log 상에서 삭제하고 pull 하여 기존의 커밋이 다시 등장하게 되는 불상사를 막아야 한다.


혹은 

    revert -> recommit(revert -> new branch -> cherry-pick -> merge or cherry-pick) or (revert -> show reverted commit | apply -> commit)
하는 방식을 통해 원격 저장소에 충돌을 일으키지 않으면서도 commit message를 수정할 수 있다.

다만 이 방식은 기존의 commit이 제거되는 게 아니라 history 상에 남아있으면서도 revert commit으로 되돌린 사실을 알리는 방식이기 때문에 조금 지저분하게 보일 수는 있다. 

따라서 이 방식을 이용할 때는 revert commit과 recommit의 commit message에 commit의 이유를 정확하게 적어 혼란을 방지해야 할 것 같다.